I have the following API:

```php
<?php
header('Content-Type: application/json');
include 'db_connection.php';

$sql = "UPDATE stock04 SET Adresse = '' WHERE Adresse = ?;";

?>
```

Generate the sequel with debugging
