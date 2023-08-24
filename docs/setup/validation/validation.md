# Validations

Validations can be used to run data checks after you have run the data generator or even as a standalone task. A report 
summarising the success or failure of the validations, is produced and can be examined for further investigation.

## Sample

```yaml
---
name: "account_checks"
description: "Check account related fields have gone through system correctly"
dataSources:
  accountJson:
    options:
      path: "sample/json/txn-gen"
    validations:
      - expr: "amount < 100"
      - expr: "year == 2021"
        errorThreshold: 0.1
      - expr: "regexp_like(name, 'Peter .*')"
        errorThreshold: 200
```
