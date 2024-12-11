---
title: Query class
---
<SwmSnippet path="/src/Query.php" line="1">

---

Here we have a `Query` class that handles the execution of bulk insert operations. It takes a `Connection` object as a parameter in its constructor and provides two methods: `execute` and `transactional`. The `execute` method executes the bulk insert operation on the specified table using the given dataset and optional types. If the dataset is empty, it returns 0. The `transactional` method wraps the execution of the `execute` method in a transaction, returning the result of the execution.

```hack
<?php
declare(strict_types=1);

namespace Franzose\DoctrineBulkInsert;

use Doctrine\DBAL\Connection;
use Doctrine\DBAL\Schema\Identifier;

class Query
{
    private $connection;

    public function __construct(Connection $connection)
    {
        $this->connection = $connection;
    }

    public function execute(string $table, array $dataset, array $types = []): int
    {
        if (empty($dataset)) {
            return 0;
        }

        $sql = sql($this->connection->getDatabasePlatform(), new Identifier($table), $dataset);

        if (method_exists($this->connection, 'executeStatement')) {
            return $this->connection->executeStatement($sql, parameters($dataset), types($types, count($dataset)));
        }

        return $this->connection->executeUpdate($sql, parameters($dataset), types($types, count($dataset)));
    }

    public function transactional(string $table, array $dataset, array $types = []): int
    {
        return $this->connection->transactional(function () use ($table, $dataset, $types): int {
            return $this->execute($table, $dataset, $types);
        });
    }
}

```

---

</SwmSnippet>

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBZG9jdHJpbmUtYnVsay1pbnNlcnQlM0ElM0FzZXJnZXlt" repo-name="doctrine-bulk-insert"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
