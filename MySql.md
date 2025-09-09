# Running mysql dump

`cd "C:\Program Files\MySQL\MySQL Server 8.4\bin"`

## create script for tables, storedproces-functions and data

`mysqldump -h 127.0.0.1 -P 33061 -u root -p --routines ScheApp > "C:\_bkp\Databases\backpac\db_scheapp.sql"`

## create script data onoloy

`mysqldump -h 127.0.0.1 -P 33061 -u root -p --no-create-info ScheApp > "C:\_bkp\Databases\backpac\db_scheapp_data.sql"`
