# SUPER configurations<a name="super-configurations"></a>

Note the following considerations of SUPER configurations when you use Amazon Redshift SUPER data type and PartiQL\.

## Lax and strict modes for SUPER<a name="lax-strict-modes"></a>

When you query SUPER data, the path expression may not match the actual SUPER data structure\. If you try to access a non\-existent member of an object or element of an array, Amazon Redshift returns a NULL value if your query is run in the default lax mode\. If you run your query in the strict mode, Amazon Redshift returns an error\. The following session parameters can be set to set the lax mode on or off\.

The following example uses session parameters to enable lax mode\.

```
SET navigate_super_null_on_error=ON;  --default lax mode for navigation

SET cast_super_null_on_error=ON;  --default lax mode for casting

SET parse_super_null_on_error=OFF;  --default strict mode for ingestion
```

## Accessing JSON fields with upper or mixed case field names or attributes<a name="upper-mixed-case"></a>

When your JSON fields are in upper cases or mixed cases, then you must configure the following setting to query data\.

```
SET downcase_delimited_identifier to FALSE;
 
-- Accessing JSON fields with uppercase and mixed-case names
SELECT json_table.data."ITEMS"."Name",
       json_table.data."price"
FROM
  (SELECT json_parse('{"ITEMS":{"Name":"TV"}, "price": 345}') AS data) AS json_table;

 Name | price
------+-------
 "TV" | 345
(1 row)
 
RESET downcase_delimited_identifier;
 
-- After resetting the above configuration, the following query accessing JSON fields with uppercase and mixed-case names should return null (if in lax mode).
SELECT json_table.data."ITEMS"."Name",
       json_table.data."price"
FROM
  (SELECT json_parse('{"ITEMS":{"Name":"TV"}, "price": 345}') AS data) AS json_table;

 name | price
------+-------
      | 345
(1 row)
```