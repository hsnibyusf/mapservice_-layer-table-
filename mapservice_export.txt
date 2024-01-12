import requests
import pandas as pd

service_url = "https://sampleserver6.arcgisonline.com/arcgis/rest/services/USA/MapServer"
response = requests.get(f"{service_url}?f=json")
service_info = response.json()


json_title = service_info.get("name", "Untitled")

with pd.ExcelWriter(f"{json_title}.xlsx") as writer:
        
    if service_info.get("layers"):  # Check for existence of "tables" key
        for layers in service_info["layers"]:
            layers_url = f"{service_url}/{layers['id']}/query?where=1=1&outFields=*&f=json"
            layers_response = requests.get(layers_url)
            layers_data = layers_response.json()
            df = pd.DataFrame.from_records(layers_data["features"])
            df.to_excel(writer, sheet_name=layers["name"], index=False)
    else:
        print("No layers found.")

    if service_info.get("tables"):  # Check for existence of "tables" key
        for table in service_info["tables"]:
            table_url = f"{service_url}/{table['id']}/query?where=1=1&outFields=*&f=json"
            table_response = requests.get(table_url)
            table_data = table_response.json()
            df = pd.DataFrame.from_records(table_data["features"])
            df.to_excel(writer, sheet_name=table["name"], index=False)
    else:
        print("No tables found.")
