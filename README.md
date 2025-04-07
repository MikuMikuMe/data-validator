# data-validator

Creating a robust Python library for validating data schemas across JSON, CSV, and XML formats requires careful planning and consideration for different data structures and formats. Below is a basic implementation of a library named `data_validator`, which includes capabilities to validate data against predefined schemas in JSON, CSV, and XML formats.

Be sure to have the necessary libraries (e.g., `jsonschema`, `xmlschema`) installed. You can install these with `pip install jsonschema xmlschema`.

Below is the complete Python program:

```python
import json
import csv
import xmlschema
from jsonschema import validate, ValidationError as JsonValidationError
from typing import Union, List, Dict

class DataValidator:
    """
    A class to validate data against schemas in JSON, CSV, and XML formats.
    """

    @staticmethod
    def validate_json(data: Union[dict, str], schema: dict) -> bool:
        """
        Validate JSON data against a JSON schema.

        :param data: JSON data as a dictionary or a string.
        :param schema: JSON schema as a dictionary.
        :return: True if valid, raises exception if invalid.
        """
        try:
            # Load the JSON data if it's passed as a string
            if isinstance(data, str):
                data = json.loads(data)
            # Perform schema validation
            validate(instance=data, schema=schema)
            return True
        except JsonValidationError as e:
            print(f"JSON Validation Error: {e.message}")
            return False
        except json.JSONDecodeError as e:
            print(f"Invalid JSON format: {e.msg}")
            return False

    @staticmethod
    def validate_csv(file_path: str, headers: List[str]) -> bool:
        """
        Validate if the CSV file contains the required headers.

        :param file_path: The file path to the CSV file.
        :param headers: List of required headers.
        :return: True if valid, raises exception if invalid.
        """
        try:
            with open(file_path, mode='r', newline='') as csvfile:
                csv_reader = csv.reader(csvfile)
                actual_headers = next(csv_reader)
                if set(headers).issubset(set(actual_headers)):
                    return True
                else:
                    missing_headers = set(headers).difference(set(actual_headers))
                    print(f"CSV Validation Error: Missing headers {missing_headers}")
                    return False
        except FileNotFoundError:
            print(f"CSV file not found: {file_path}")
            return False
        except Exception as e:
            print(f"An error occurred while validating CSV: {e}")
            return False

    @staticmethod
    def validate_xml(file_path: str, schema_file: str) -> bool:
        """
        Validate XML data against an XML schema (XSD).

        :param file_path: The file path to the XML file.
        :param schema_file: The file path to the XSD (XML Schema Definition) file.
        :return: True if valid, raises exception if invalid.
        """
        try:
            schema = xmlschema.XMLSchema(schema_file)
            schema.validate(file_path)
            return True
        except xmlschema.XMLSchemaException as e:
            print(f"XML Validation Error: {e}")
            return False
        except FileNotFoundError:
            print("XML file or Schema not found.")
            return False
        except Exception as e:
            print(f"An error occurred while validating XML: {e}")
            return False

# Example usage:
if __name__ == "__main__":
    # JSON example
    json_data = '{"name": "John", "age": 30}'
    json_schema = {
        "type": "object",
        "properties": {
            "name": {"type": "string"},
            "age": {"type": "number"},
        },
        "required": ["name", "age"]
    }
    print("JSON Validation:", DataValidator.validate_json(json_data, json_schema))
    
    # CSV example
    csv_headers = ["name", "age", "email"]
    print("CSV Validation:", DataValidator.validate_csv('example.csv', csv_headers))

    # XML example
    xml_schema_file = "example.xsd"
    xml_file = "example.xml"
    print("XML Validation:", DataValidator.validate_xml(xml_file, xml_schema_file))
```

### Key Components:
- **JSON Validation**: Uses the `jsonschema` library to validate JSON data against a schema. It handles exceptions for invalid JSON formats and validation errors.
- **CSV Validation**: Verifies that the required headers are present in the CSV file. It handles file not found errors and generic exceptions.
- **XML Validation**: Uses the `xmlschema` library to validate XML data against an XSD schema. It handles file-related errors and XML validation errors.

### Error Handling:
- The program has comprehensive error handling for file operations, JSON decoding, and schema validation issues, enabling graceful failure and informative error messages.

### Dependencies:
- Make sure to install the required libraries using:
  ```
  pip install jsonschema xmlschema
  ```

This library serves as a starting point and can be expanded based on additional requirements, such as more complex schema validations, different file reading approaches, and logging capabilities.