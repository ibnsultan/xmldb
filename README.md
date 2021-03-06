# XMLDB
## XML as a Database | Store, retrieve & Manipulate data records in XML files

## Getting started
**Using composer** : coming soon
 
**Downloading the File** <br>
Download the File and Add it to your desired directory of your project

#### Connect to database
```
$db = xmlDb::connect('database');
```

If database file not exists it will be created automatically. Database files are stored in data directory.


#### Delete database

```
xmlDb::dropDatabase('database');

```

Delete the database file from data directory.

#### Chmod database file

```
xmlDb::chmod('database', 0755);

```

Change database file permissions. Default permissions are set to 0644.

#### Get database permissions

```
echo 'Database file permissions: ' . xmlDb::chmod('database');

```

#### Create database backup

```
// connect to database
$db = xmlDb::connect('database');

// create backup
$db->backup();

```

Backup files are stored in data directory with extension *.bak*

#### Restore database backup

```
// connect to database
$db = xmlDb::connect('database');

// restore database backup
$db->restore();

```

#### Add table to database

```
// connect to database
$db = xmlDb::connect('database');

// add table
$db->addTable('example_table');

// we can have unlimited number of tables in one database
$db->addTable('another_table');
$db->addTable('one_more_table');

```

#### Remove table from database

```
// connect to database
$db = xmlDb::connect('database');

// remove table and all its contents
$db->removeTable('another_table');

```

#### Get tables names

```
// connect to database
$db = xmlDb::connect('database');

// get tables names
$tables = $db->getTables();

// print table names
print_r($tables);

```

getTables() returns an array of table names

#### Add table column

```
// connect to database
$db = xmlDb::connect('database');

// add table
$db->addTable('example_table');

// for doing anything with table (adding columns, removing columns, selecting data)
// first we must select table by calling method from(table_name) or method in(table_name)

// add column "id" and set its value to 1
$db->in('example_table')->addColumn('id', 1);

// add first name and last name columns
$db->in('example_table')->addColumn('firstname', 'John');
$db->in('example_table')->addColumn('lastname', 'Doe');

```

addColumn() method is useful for extending the table structure, and it can be used when creating a new table for adding columns. <br /> If you add columns in newly created table on this way, *column id must be set.*

Better way for adding columns in newly created table is by using method [insert()](https://www.phpclasses.org/package/9314-PHP-Store-and-retrieve-data-records-in-XML-files.html#insert-data)

#### Remove table column

```
// connect to database
$db = xmlDb::connect('database');

// remove column
$db->from('example_table')->removeColumn('name');

```

#### Get columns names

```
// connect to database
$db = xmlDb::connect('database');

// you can get columns names like this
$columns = $db->from('example_table')->getColumns();

// print columns
print_r($columns);

// or like this
$columns = $db->getColumns('example_table');

print_r($columns);

```

getColumns() returns an array of columns names

#### Insert data

```
// connect to database
$db = xmlDb::connect('database');

// you can insert data in two different ways

// by passing an array with column names and their values
$db->in('example_table')->insert([
	'name'     => 'John',
	'lastname' => 'Doe'
]);

// or by using method bind(column_name, value)
$db->in('example_table')
   ->bind('name', 'John')
   ->bind('lastname', 'Doe')
   ->insert();

// example how to create a new table and insert data

// create table
$db->addTable('user_data');

// set data
$data = [
    'username' => 'my_user_name',
    'password' => md5('password'),
    'email'    => 'demo@example.com',
];

// insert data into table
$db->in('user_data')->insert($data);

```

If the table where you want to insert data doesn't have any columns (newly created table), columns will be automatically added. Notice that we aren't set the column id. Column id is added and incremented automatically.

#### Update data

```
// connect to database
$db = xmlDb::connect('database');

// like insert() method, update() method also supports two different ways of updating data

// by passing an array with column names and their values
$data = [
    'name'     => 'My name',
    'lastname' => 'My last name'
];

$db->in('example_table')->update($data);

// or by using method bind(column_name, value)
$db->in('example_table')
   ->bind('name', 'Johnny')
   ->bind('lastname', 'Test')
   ->update();

// using where()

$db->in('example_table')
   ->where('name', 'Johnny')
   ->bind('lastname', 'Doe')
   ->update();

// update column name in all rows where id is greater than 5 and less than 10
$db->in('example_table')
   ->where('id', 5, '>')
   ->where('id', 10, '<')
   ->bind('name', 'John')
   ->update();

// limiting update
$db->in('example_table')
   ->where('id', 3, '>')
   ->bind('name', 'Jane')
   ->limit(5)
   ->update();

// check how many rows is actually updated
echo 'Rows updated: ' . $db->affectedRows();

```

#### Delete data

```
// connect to database
$db = xmlDb::connect('database');

// this will delete all rows in table "example_table"
$db->from('example_table')->delete();

// using where()

// delete all rows where column "name" equal to "John"
$db->from('example_table')->where('name', 'John')->delete();

// limiting delete
$db->from('example_table')
   ->where('id', 3, '>')
   ->limit(5)
   ->delete();

echo 'Rows deleted: ' . $db->affectedRows();

```

#### Get one row

```
// connect to database
$db = xmlDb::connect('database');

// select table and columns
$db->from('example_table')->select('id, name, lastname');

// get row
$row = $db->getRow();

// print data
echo $row->id . '<br />';
echo $row->name . '<br />';
echo $row->lastname . '<br />';

```

getRow() returns an object with column names as properties, if there is no data returns false

#### Get all rows

```
// connect to database
$db = xmlDb::connect('database');

// select table and columns
$db->from('example_table')->select('id, name, lastname');

// get all rows
$rows = $db->getAll();

// print data
foreach ($rows as $row) {
    echo $row->id . '<br />';
    echo $row->name . '<br />';
    echo $row->lastname . '<br />';
}

```

getAll() returns an array of objects, if there is no data returns false

#### From

```
// select table to operate
$db->from('example_table');

```

#### In

```
// in is alias of from()
$db->in('example_table');

```

#### Join

Let's say we have two relational tables (users and users_data) in one database

```
<?xml version="1.0" encoding="utf-8"?>
<database>
	<users>
		<row>
			<id>1</id>
			<name>John</name>
		</row>
		<row>
			<id>2</id>
			<name>Jane</name>
		</row>
	</users>
	<users_data>
		<row>
			<id>1</id>
			<user_id>1</user_id>
			<lastname>Doe</lastname>
		</row>
		<row>
			<id>2</id>
			<user_id>2</user_id>
			<lastname>Dooe</lastname>
		</row>
	</users_data>
</database>

```

Getting the data

```
// conect to xmlDb
$db = xmlDb::connect('database');

// join tables
$db->from('users')->join('users_table' ,'id', 'user_id')->select('*');
// this will combine data from two tables where column "id" of "users" table euqals to column "user_id" of "users_data" table

$data = $db->getAll();

print_r($data);

// using where()

$db->from('users')
   ->join('users_table' ,'id', 'user_id')
   ->select('id, name, lastname')
   ->where('id', 2); // where "users" table "id" euqal 2

$user = $db->getRow();

echo $user->id . '<br />';
echo $user->name . '<br />';
echo $user->lastname;

```

#### Select columns

```
// connect to database
$db = xmlDb::connect('database');

// select all columns by using asterisk "*"
$db->from('example_table')->select('*');

// select columns we need by providing their names separated by comma
$db->from('example_table')->select('id, name, lastname');

```

#### Where

```
// connect to database
$db = xmlDb::connect('database');

$db->from('users')
   ->select('id, name, lastname')
   ->where('name', 'John');
   ->where('lastname', 'Doe');

$data = $db->getAll();

print_r($data);

// contains
$db->from('users')->where('name', 'Joh', 'contains');
// this will return all rows where name column contain string "Joh" (John, Johnny, etc)
$data = $db->getAll();

```

#### Or where

```
// connect to database
$db = xmlDb::connect('database');

// select id, name, lastname from users where name = John or name = Johnny
$db->from('users')
   ->select('id, name, lastname')
   ->where('name', 'John');
   ->orWhere('name', 'Johnny');

$data = $db->getAll();

print_r($data);

```

#### Limit result

```
// connect to database
$db = xmlDb::connect('database');

// limit result
$db->from('users')->select('*')->limit(10);

$data = $db->getAll();

echo 'Rows selected: ' . count($data);

// limit update
$db->in('users')->where('id', 3, '>')->limit(5)->update([
	'name' => 'Johnny'	
]);

echo 'Rows updated: ' . $db->affectedRows();

// limit delete
$db->in('example_table')
   ->where('id', 3, '>')
   ->limit(5)
   ->delete();

echo 'Rows deleted: ' . $xmlDb->affectedRows();

```

#### Affected rows

```
// connect to database
$db = xmlDb::connect('database');

$db->in('users')->where('id', 3)->update([
	'name' => 'Johnny'
]);

echo 'Rows updated: ' . $db->affectedRows();

```

affectedRows() is used with method update() and method delete() to get number of rows updated/deleted

#### Bind

```
// connect to database
$db = xmlDb::connect('database');

// binding column names and their values
$db->in('example_table')
   ->bind('name', 'John')
   ->bind('lastname', 'Doe')
   ->insert();

```

#### Order by

```
// connect to database
$db = xmlDb::connect('database');

// orderBy() method takes two params, column name and direction of sorting (asc or desc)
$db->from('example_table')->select('id, name, lastname')->orderBy('name', 'asc');

$data = $db->getAll();

print_r($data);

$db->from('example_table')
   ->select('id, name, lastname')
   ->where('id', 5, '>')
   ->limit(5)
   ->orderBy('id', 'desc');

$data = $db->getAll();

print_r($data);

```

#### Last insert id

```
// connect to database
$db = xmlDb::connect('database');

// select table
$db->in('example_table');

echo 'Last inserted id is ' . $db->lastId();
```
 ### credit: 
 -- Alaca, phpclasses
