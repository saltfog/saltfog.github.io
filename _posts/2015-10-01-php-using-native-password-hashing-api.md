---
layout: post
title: PHP - Using native password hashing API
description: Starting PHP version 5.5 onwards, password hashing in PHP application made easy as new native password hashing functions (built-in) have been introduced.
keywords: php 5.5, password hashing, native api, php functions
tags: [PHP, Password Hashing]
comments: true
---

## Introduction

As a developer, password hashing is one of the most basic security considerations that must be made when designing application that required us saving user credentials, especially user password. We must avoid implementing a weak design in hashing user password as the advancement in computer equipment, these kind of weak hashing algorithms such as `md5()` and `sha1()` are easy to "bruteforce" in seconds.

Luckily as of PHP 5.5, a native password hashing API was introduced to safely handle both hashing and verifying password in a secure manner. When hashing password, the two most important considerations are the computational expense and the salt. The more computationally expensive the hashing algorithm, the longer it will take to "bruteforce" its output. So, the suggested algorithm to use when hashing password is Blowfish, which is also the default hashing algorithm used by this password hashing API.

### Implementation of native password hashing API

The implementation consists of four functions:-

* `password_hash()` : To create a new password hash
* `password_verify()` : To confirm a given password matches with the hash
* `password_needs_rehash()` : To check if a password meets the desired hash settings (algorithm, cost)
* `password_get_info()` : To return information about the hash such as algorithm and cost

**NOTE:** `password_hash()` creates a new password hash using a strong one-way hashing algorithm and compatible with [**crypt()**](http://php.net/manual/en/function.crypt.php).

### Usage Examples

#### Example 1

`password_hash()` example with **PASSWORD_DEFAULT**

```php
<?php
/**
 * We just want to hash our password using the current DEFAULT algorithm.
 * This is presently BCRYPT, and will produce a 60 character result.
 *
 * Beware that DEFAULT may change over time, so you would want to prepare
 * By allowing your storage to expand past 60 characters (255 would be good)
 */
echo password_hash("KittenDies", PASSWORD_DEFAULT)."\n";
?>
```

Output:

```
$2y$10$kZ8eWlGS30qABEthikt.uOvdYETzS3azGCutmfOdtXMzEvFuNoMWe
```

#### Example 2

`password_hash()` example with setting **cost** manually

```php
<?php
/**
 * In this case, we want to increase the default cost for BCRYPT to 12.
 * Note that we also switched to BCRYPT, which will always be 60 characters.
 */
$options = [
    'cost' => 12,
];
echo password_hash("KittenDies", PASSWORD_BCRYPT, $options)."\n";
?>
```

Output:

```
$2y$12$gsF2.s.PkKLusBMsgHrQnOmB5mzG0bHGE97.SA5250aQ0ZTt0Klty
```

The role of the cost is to ensure that the passwords remain difficult to brute force even as hardware gets faster. You will need to continuously tweak your cost as your own hardware changes. However, if you are not careful and just crank it up to 11 (figuratively speaking) your passwords will take too long to generate, tying up web server threads and can easily lead to a DoS attack.

Here's the code to benchmark your server in order **to determine how high of a cost you can afford**:

```php
<?php

$timeTarget = 0.05; // 50 milliseconds

$cost = 8;
do {
    $cost++;
    $start = microtime(true);
    password_hash("test", PASSWORD_BCRYPT, ["cost" => $cost]);
    $end = microtime(true);
} while (($end - $start) < $timeTarget);

echo "Appropriate Cost Found: " . $cost . "\n";
?>
```

Output:

```
Appropriate Cost Found: 10
```

This is good if you want to set the highest cost that you can without slowing down your server too much. **8 - 10 is a good baseline**, and is good if your servers are fast enough. The code above aims for less than 50ms stretching time, which is a good baseline for systems handling interactive logins.

#### Example 3

`password_hash()` example with setting **salt** manually

```php
<?php
/**
 * Note that the salt here is randomly generated.
 * Never use a static salt or one that is not randomly generated.
 *
 * For the VAST majority of use-cases, let password_hash generate the salt randomly for you
 */
$options = [
    'cost' => 11,
    'salt' => mcrypt_create_iv(22, MCRYPT_DEV_URANDOM),
];
echo password_hash("KittenDies", PASSWORD_BCRYPT, $options)."\n";
?>
```

Output:

```
$2y$11$dtSMAuO41g5QZcAG76FqTehpk35Dcf0lkEpBffEW7dRqQsrR2E8VO
```

> **Caution:** It is strongly recommended that you do not generate your own salt for this function. It will create a secure salt automatically for you if you do not specify one.

#### Example 4

`password_verify()` example

```php
<?php

$hash = '$2y$11$dtSMAuO41g5QZcAG76FqTehpk35Dcf0lkEpBffEW7dRqQsrR2E8VO';

if (password_verify('KittenDies', $hash)) {
    echo 'Password is valid!';
} else {
    echo 'Invalid password.';
}
?>
```

Output:

```
Password is valid!
```

If you get incorrect false responses from `password_verify()` when manually including the hash variable (eg. for testing) and you know it should be correct, make sure you are enclosing the hash variable in single quotes (') and not double quotes (").

#### Example 5

`password_needs_rehash()` example

```php
<?php

$password = 'rasmuslerdorf';
$hash = '$2y$10$YCFsG6elYca568hBi2pZ0.3LDL5wjgxct1N8w/oLR/jfHsiQwCqTS';

// The cost parameter can change over time as hardware improves
$options = array('cost' => 11);

// Verify stored hash against plain-text password
if (password_verify($password, $hash)) {
    // Check if a newer hashing algorithm is available
    // or the cost has changed
    if (password_needs_rehash($hash, PASSWORD_DEFAULT, $options)) {
        // If so, create a new hash, and replace the old one
        $newHash = password_hash($password, PASSWORD_DEFAULT, $options);
    }

    // Log user in
}
?>
```

#### Example 6

`password_get_info()` example

```php
<?php

$hash = '$2y$11$dtSMAuO41g5QZcAG76FqTehpk35Dcf0lkEpBffEW7dRqQsrR2E8VO';
var_dump(password_get_info($hash));

?>
```

Output:

```
array (size=3)
  'algo' => int 1
  'algoName' => string 'bcrypt' (length=6)
  'options' =>
    array (size=1)
      'cost' => int 11
```

### Compatibility issue

If you are not using PHP 5.5, there is a [pure PHP compatibility library](https://github.com/ircmaxell/password_compat) available for PHP 5.3.7 and later.

### Salt

A cryptographic salt is a data which is applied during the hashing process in order to eliminate the possibility of the output being looked up in a list of pre-calculated pairs of hashes and their input, known as a rainbow table.

`password_hash()` will create a random salt if one is not provided, and this is generally the easiest and the most secure approach.

#### Storing the salt

When using `password_hash()` or `crypt()`, the return value includes the salt as part of the generated hash. This value should be stored verbatim in your database, as it includes information about the hash function that was used and can then be given directly to `password_verify()` or `crypt()` when verifying passwords.

![Password Hash](http://i.imgur.com/9cmcBRo.png)

The diagram above shows the format of a return value from `crypt()` or `password_hash()`. As you can see, they are self-contained, with all the information on the algorithm and salt required for future password verification.

### Conclusion

With this new [password extension](http://php.net/password), the password hashing becomes more easier as we don't need to create our custom-made algorithm class or use any other external password hashing class library in our application anymore.
