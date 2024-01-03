### Extract XA-Miner Transactions as csv file
The script will only use SCP Transfer selection as rewards/deposit

```
import csv
import pandas as pd

def extract_transfer_data(input_file_path, output_file_path):
    with open(input_file_path, mode='r', encoding='utf-8') as file:
        reader = csv.DictReader(file)
        print("Column names:", reader.fieldnames)  # Print the column names

        with open(output_file_path, mode='w', newline='', encoding='utf-8') as output_file:
            fieldnames = ['Koinly Date', 'Amount', 'Currency', 'Label', 'TxHash']
            writer = csv.DictWriter(output_file, fieldnames=fieldnames)
            writer.writeheader()

            for row in reader:
                if row['Type'] == 'SCP Transfer':
                    # Split the date string before "GMT" and convert to datetime
                    date_time_str = row['DateTime'].split(" GMT")[0]
                    date_time = pd.to_datetime(date_time_str, format='%a %b %d %Y %H:%M:%S').strftime('%Y-%m-%d %H:%M UTC')
                    
                    writer.writerow({
                        'Koinly Date': date_time,
                        'Amount': row['Amount SCP'],
                        'Currency': 'SCP',
                        'Label': 'reward',
                        'TxHash': row['\ufeff"Transaction ID"'].strip('"')  # Strip the double quotes
                    })

# File paths
input_file_path = './XM-87b46d36.csv'
output_file_path = './koinly_formatted_transactions.csv'

# Extract transfer data
extract_transfer_data(input_file_path, output_file_path)

print("Conversion completed. The file is saved at:", output_file_path)
```
### Example CSV transaction
```
"Transaction ID","Type","Amount SCP","Amount SPF","Height","DateTime"
"0631f73334005ac288e894682b32cc2d2a22f8a627189a9cc15e4b3a6208d7a5","SCP Transfer","7.069424","7.069423691491678","272011","Tue Jan 02 2024 02:16:35 GMT+0100 (Mitteleuropäische Normalzeit)"
"17bd235d331ad7355b9fd617f4beb15cc6b3ce4c711a8065f9e2c805d400c852","Contract revision","-0.000039","-0.00003888","269312","Thu Dec 14 2023 13:03:38 GMT+0100 (Mitteleuropäische Normalzeit)"
"7081720ae7fded9ef9b9bd46b6e9734d44286d64c07326c9bb190b0e4b83d599","Renew file contract","-42.597086","-42.59708648619298","268959","Tue Dec 12 2023 00:18:31 GMT+0100 (Mitteleuropäische Normalzeit)"
"b0f2d8d54544f25d4a8f443a2507d66b006e52e96cfdd91442dc0bf592fcfc4e","SCP Transfer","7.366913","7.366913133902253","267494","Fri Dec 01 2023 18:14:00 GMT+0100 (Mitteleuropäische Normalzeit)"
"9f11a8d46d7b3d98bdc6b3265471f875a034e7e996826c3dd0fbdd8859286cc9","Contract revision","-0.000039","-0.00003888","265812","Mon Nov 20 2023 06:10:49 GMT+0100 (Mitteleuropäische Normalzeit)"
````
### Koinly:
```
Koinly Date,Amount,Currency,Label,TxHash
2024-01-02 02:16 UTC,7.069424,SCP,reward,0631f73334005ac288e894682b32cc2d2a22f8a627189a9cc15e4b3a6208d7a5
2023-12-01 18:14 UTC,7.366913,SCP,reward,b0f2d8d54544f25d4a8f443a2507d66b006e52e96cfdd91442dc0bf592fcfc4e
2023-11-01 18:12 UTC,4.631776,SCP,reward,f50734053a5caa0cb64f65619ccc40d945d250ec23ec69171f0355cdeb7e14f2
2023-10-02 02:35 UTC,0.455821,SCP,reward,6c6ecd251e734b1ea840032e2715446123472759362ed0f0976ad0c0b89711a2
2023-09-02 13:44 UTC,0.189839,SCP,reward,7f1b3d64311833e655e321929beec9cb92d88fd872cc4f78cab7130742e2ceef
```
