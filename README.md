# SETUP of PHPUNIT locally inside your WP Plugin

# Important

Use the **bash shell**, not _zhs_, which can be the reason for it to fail.

## Step 1: Create `/tests/` folder (`wp-config.php, bootstrap.php, test-sample.php`)

In your plugin folder, create:
`/tests/wp-config.php`
`/tests/bootstrap.php`

- Copy literally from `https://github.com/wp-phpunit/example-project/blob/master/tests/bootstrap.php`

- Create `/tests/test-sample.php`

  > any test should start by `test-*.php`

  Create a simple test like:

```
class Example_Test extends WP_UnitTestCase {
    function test_it_works() { $this->assertTrue( true ); }
}
```

## Step 2: `composer.json`

Copy from the repo: https://github.com/wp-phpunit/example-project/blob/master/composer.json
copy:

- From `composer.json` > the "require", "require-dev" and "scripts"
  - delete any `vendor/` folder and `composer.lock` if needed
  - Run `composer install`

I am not sure, but it's possible that other keys of the composer file are required, like the version of PHP, or the "allow-plugins". If you clone it completely, you are safe.

## Step 3: phpunit.xml.dist

- `phpunit.xml.dist`copy totally from `https://github.com/wp-phpunit/example-project/blob/master/phpunit.xml`

## Step 4: Create test DB cloning an existing clean WP db and remaning wp prefix

> This is not needed if you already have the test DB,
> and you dont need to change the prefix if you dont want to

If not created, create a clean DB in the same mysql host where WP runs. It should contain a clean WP installation. If you don't know how to get it, clone it from an existing DB

- let's assume it's called `wp_tests_todelete`
- Assuming that there is an existing DB called `local` with a clean WP installation, we clone it in our test DB:
  `mysqldump -uroot -p local > backup.sql`
  `mysql -uroot -p wp_tests_todelete < backup.sql`
  Then login to mysql to rename the prefix of the tables
  `mysql -u root -p`
  `use wp_tests_todelete;`

```
 RENAME TABLE wp_commentmeta TO wptest_commentmeta;
 RENAME TABLE wp_comments TO wptest_comments;
 RENAME TABLE wp_links TO wptest_links;
 RENAME TABLE wp_options TO wptest_options;
 RENAME TABLE wp_postmeta TO wptest_postmeta;
 RENAME TABLE wp_posts TO wptest_posts;
 RENAME TABLE wp_term_relationships TO wptest_term_relationships;
 RENAME TABLE wp_term_taxonomy TO wptest_term_taxonomy;
 RENAME TABLE wp_termmeta TO wptest_termmeta;
 RENAME TABLE wp_terms TO wptest_terms;
 RENAME TABLE wp_usermeta TO wptest_usermeta;
 RENAME TABLE wp_users TO wptest_users;
```

## Step 5: tests/wp-config.php again

Template for wp-config.php: `https://github.com/adeleyeayodeji/wordpress-test-plugin/blob/master/tests/wp-config.php`
Edit `tests/wp-config.php` to match the new DB connection:

```
define('DB_NAME', getenv('WP_DB_NAME') ?: 'wp_tests_todelete');
define('DB_USER', getenv('WP_DB_USER') ?: 'root');
define('DB_PASSWORD', getenv('WP_DB_PASS') ?: 'root');
define('DB_HOST', getenv('WP_DB_HOST') ?: 'localhost');
$table_prefix = 'wptest_'; # This is not needed if you didnt change it.
```

## Ready

Ready to run the test with

`composer test`  
or  
`./vendor/bin/phpunit tests/test-sample.php`, previously renaming the file and the class inside the php.
