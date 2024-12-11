---
title: Helpers
---
# Introduction

This document will walk you through the helper functions implemented in <SwmPath>[src/functions.php](/src/functions.php)</SwmPath>.

The purpose of these helpers is to facilitate the generation of SQL queries and the handling of dataset parameters and types.

We will cover:

1. How SQL insert statements are generated.
2. How columns and placeholders are managed.
3. How dataset parameters and types are processed.

# Generating SQL insert statements

<SwmSnippet path="/src/functions.php" line="9">

---

The main function responsible for generating SQL insert statements is <SwmToken path="/src/functions.php" pos="9:2:2" line-data="function sql(AbstractPlatform $platform, Identifier $table, array $dataset): string">`sql`</SwmToken>. It constructs the SQL string by quoting columns, stringifying them, and generating placeholders for the values.

```
function sql(AbstractPlatform $platform, Identifier $table, array $dataset): string
{
    $columns = quote_columns($platform, extract_columns($dataset));

    $sql = sprintf(
        'INSERT INTO %s %s VALUES %s;',
        $table->getQuotedName($platform),
        stringify_columns($columns),
        generate_placeholders(count($columns), count($dataset))
    );

    return $sql;
}
```

---

</SwmSnippet>

# Extracting columns from dataset

<SwmSnippet path="/src/functions.php" line="23">

---

The <SwmToken path="/src/functions.php" pos="23:2:2" line-data="function extract_columns(array $dataset): array">`extract_columns`</SwmToken> function retrieves the column names from the first entry in the dataset. This is essential for knowing which columns to include in the SQL statement.

```
function extract_columns(array $dataset): array
{
    if (empty($dataset)) {
        return [];
    }

    $first = reset($dataset);

    return array_keys($first);
}
```

---

</SwmSnippet>

# Quoting columns

<SwmSnippet path="/src/functions.php" line="34">

---

The <SwmToken path="/src/functions.php" pos="34:2:2" line-data="function quote_columns(AbstractPlatform $platform, array $columns): array">`quote_columns`</SwmToken> function ensures that column names are properly quoted according to the platform's requirements. This prevents SQL injection and syntax errors.

```
function quote_columns(AbstractPlatform $platform, array $columns): array
{
    return array_map(static function (string $column) use ($platform): string {
        return (new Identifier($column))->getQuotedName($platform);
    }, $columns);
}
```

---

</SwmSnippet>

# Stringifying columns

<SwmSnippet path="/src/functions.php" line="41">

---

The <SwmToken path="/src/functions.php" pos="41:2:2" line-data="function stringify_columns(array $columns): string">`stringify_columns`</SwmToken> function formats the column names into a string suitable for an SQL query. This is used in the <SwmToken path="/src/functions.php" pos="9:2:2" line-data="function sql(AbstractPlatform $platform, Identifier $table, array $dataset): string">`sql`</SwmToken> function to create the column part of the insert statement.

```
function stringify_columns(array $columns): string
{
    return empty($columns) ? '' : sprintf('(%s)', implode(', ', $columns));
}
```

---

</SwmSnippet>

# Generating placeholders

<SwmSnippet path="/src/functions.php" line="46">

---

The <SwmToken path="/src/functions.php" pos="46:2:2" line-data="function generate_placeholders(int $columnsLength, int $datasetLength): string">`generate_placeholders`</SwmToken> function creates the necessary placeholders for the values in the SQL insert statement. It ensures that the correct number of placeholders is generated based on the number of columns and dataset entries.

```
function generate_placeholders(int $columnsLength, int $datasetLength): string
{
    // (?, ?, ?, ?)
    $placeholders = sprintf('(%s)', implode(', ', array_fill(0, $columnsLength, '?')));

    // (?, ?), (?, ?)
    return implode(', ', array_fill(0, $datasetLength, $placeholders));
}
```

---

</SwmSnippet>

# Processing dataset parameters

<SwmSnippet path="/src/functions.php" line="55">

---

The <SwmToken path="/src/functions.php" pos="55:2:2" line-data="function parameters(array $dataset): array">`parameters`</SwmToken> function flattens the dataset into a single array of values. This is useful for binding parameters to the SQL query.

```
function parameters(array $dataset): array
{
    return array_reduce($dataset, static function (array $flattenedValues, array $dataset): array {
        return array_merge($flattenedValues, array_values($dataset));
    }, []);
}
```

---

</SwmSnippet>

By understanding these helper functions, you can see how they work together to generate SQL insert statements and handle dataset parameters and types efficiently.

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBZG9jdHJpbmUtYnVsay1pbnNlcnQlM0ElM0FzZXJnZXlt" repo-name="doctrine-bulk-insert"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
