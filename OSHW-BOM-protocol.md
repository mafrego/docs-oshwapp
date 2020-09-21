# OSHW BOM protocol

[![version](https://img.shields.io/badge/version-0.0.2-blue)](https://github.com/mafrego/docs-oshwapp/blob/master/BOMCHANGELOG.md)

## Preliminary notes

- The **OSHW BOM protocol** defines the headers of a [comma-separated values](https://en.wikipedia.org/wiki/Comma-separated_values) file used as [bill of materials](https://en.wikipedia.org/wiki/Bill_of_materials) for any open source hardware product.
- Header 0. in the "BOM headers" section describes how each succesive header has to be understood. 
- The protocol follows the [Semantic Versioning](https://semver.org/) directives.
- The protocol is still under development: anyone interested in improving it can create an issue or write an email to [this address](mailto:marcofregonese@gmail.com).
- A [working example](https://github.com/mafrego/docs-oshwapp/blob/master/template/bom/miniHouse-bom.csv) of BOM file according to the **OSHW BOM protocol 0.0.2**. 

## BOM filename

- The file containing the BOM shall begin with the project name followed by "-bom".
- The file extension shall be ".csv".
- Example: "myopensourceproject-bom.csv".
- Filenames are case sensitive and blank spaces are not allowed.
- Filenames are recommended to be lower case.

## BOM headers

0. **headerName**:
    - allowed header format
    - corresponding regex
    - deonticity: required, recommended or optional
    - header description
---
1. **itemNumber**:
    - increasing integer starting from 1
    - `^[0-9]*[1-9][0-9]*$`
    - required
    - unique number associated to atom type
2. **name**:
    - alphanumeric string comprehensive of 62 characters (A-Z+a-z+0-9, case-sensitive, no blank spaces allowed) plus underscores and hyphens
    - `^[-0-9a-zA-Z_]+$`
    - required
    - unique atom name
3. **description**:
    - string including any character except for commas and semicolons
    - `^[^,;]+$`
    - required
    - brief atom description of its peculiarities
4. **moq**:
    - integer equal or greater than 1
    - `^[0-9]*[1-9][0-9]*$`
    - required
    - minimum order quantity
5. **quantity**:
    - integer equal or greater than 1
    - `^[0-9]*[1-9][0-9]*$`
    - required
    - number of identical atoms
6. **unitCost**:
    - floating point number
    - `^(?:[1-9]\d*|0)?(?:\.\d+)?$`
    - required
    - cost of a single atom if moq equals 1 otherwise cost of a moq
7. **totalCost**:
    - floating point number
    - `^(?:[1-9]\d*|0)?(?:\.\d+)?$`
    - required
    - sum of cost of all identical atoms if moq equals 1 otherwise unitCost times a multiple of respective moq plus 
8. **currency**:
    - 3 capital character length string according to currency designator ISO 4217
    - `[A-Z]{3}`
    - required
    - currency relative to unitCost and totalCost
9. **GTIN**:
    - 8, 12, 13, or 14 digit length number
    - `^(\d{8}|\d{12}|\d{13}|\d{14})$`
    - recommended if available
    - [Global Trade Item Number](https://en.wikipedia.org/wiki/Global_Trade_Item_Number)
10. **SKU**:
    - no standard defined: alphanumeric string with underscores, hyphens and blank spaces allowed
    - `^[-0-9a-zA-Z_. /]+$`
    - recommended
    - [Stock Keeping Unit](https://en.wikipedia.org/wiki/Stock_keeping_unit)
11. **vendorUrl**:
    - URL with HTTP string
    - `https?:\/\/(www\.)?[-a-zA-Z0-9@:%._+~#=]{1,256}\.[a-zA-Z0-9()]{1,6}\b([-a-zA-Z0-9()@:%_+.~#?&//=]*)`
    - required if relative link entry is blank
    - [Uniform Resource Locator of atom vendor](https://en.wikipedia.org/wiki/URL) 
12. **leadTime**:
    - alphanumerical upper case character string according to ISO 8601
    - `^P(?!$)(\d+(?:\.\d+)?Y)?(\d+(?:\.\d+)?M)?(\d+(?:\.\d+)?W)?(\d+(?:\.\d+)?D)?(T(?=\d)(\d+(?:\.\d+)?H)?(\d+(?:\.\d+)?M)?(\d+(?:\.\d+)?S)?)?$`
    - recommended
    - time required from the placement of an order to arrival of product at your doorstep
13. **link**:
    - URL with HTTP string
    - `https?:\/\/(www\.)?[-a-zA-Z0-9@:%._+~#=]{1,256}\.[a-zA-Z0-9()]{1,6}\b([-a-zA-Z0-9()@:%_+.~#?&//=]*)`
    - required if relative vendorUrl entry is blank
    - Uniform Resource Locator of atom if custom made or not for sale 
14. **notes**:
    - string including any character except for commas and semicolons
    - `^[^,;]+$`
    - not required
    - text ad libitum

## Supported languages

Any language with Latin-script alphabet, English preferred.

## System of measurement

[Metric system SI](https://en.wikipedia.org/wiki/International_System_of_Units)