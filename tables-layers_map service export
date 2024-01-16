import requests
import pandas as pd
import openpyxl

service_url = "https://sampleserver6.arcgisonline.com/arcgis/rest/services/USA/MapServer"
response = requests.get(f"{service_url}?f=json")
service_info = response.json()

layers = service_info["layers"]
tables = service_info["tables"]
json_title = service_info.get("documentInfo", {}).get("Title")

layer_df = pd.DataFrame({"Layer Name": [layer["name"] for layer in layers]})
table_df = pd.DataFrame({"Table Name": [table["name"] for table in tables]})

filename = f"{json_title or 'untitled'}.xlsx"
workbook = openpyxl.Workbook()

sheet_layers = workbook.active
sheet_layers.title = "Layers"
for row in layer_df.itertuples(index=False):
    sheet_layers.append(row)
    
sheet_tables = workbook.create_sheet("Tables")
for row in table_df.itertuples(index=False):
    sheet_tables.append(row)

workbook.save(filename)
