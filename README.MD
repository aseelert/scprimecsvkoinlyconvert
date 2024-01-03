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
