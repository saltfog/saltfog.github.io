---
layout: post
title: PHP - Encryption, Decryption and Password Hashing
description: Here how I encrypt-decrypt my data safely and implement secure password hashing in PHP using these PHP custom function.
keywords: php encryption function, php decryption function, secure password hashing using php
tags: [PHP, Encryption, Decryption, Password Hashing]
comments: true
---

These days, majority of people are doing their business in risky situation when they are using applications that are weakly built in protecting its user data or user confidential information. It does not matter how simple or complicated, small or big the application is, when it comes to security, the developer should be aware and do the best they can in delivering a safer product or service especially when keeping their user data.

In the web world, one of commonly used web programming is PHP. In PHP, there are few methods available to encrypt information data in a secure manner. These code snippets are the ones that I usually use in my web application or web development to ensure user confidential information are not easily been disclosed.

### Data encryption

This is a PHP function I use to encrypt any data string.

```php
<?php
function Encrypt($password, $data)
{

    $salt = substr(md5(mt_rand(), true), 8);

    $key = md5($password . $salt, true);
    $iv  = md5($key . $password . $salt, true);

    $ct = mcrypt_encrypt(MCRYPT_RIJNDAEL_128, $key, $data, MCRYPT_MODE_CBC, $iv);

    return base64_encode('Salted__' . $salt . $ct);
}
?>
```

**Example:**

```php
<?php
echo Encrypt('myPass123', 'Welcome to Flippancy 25');
// Output: U2FsdGVkX19LYv5Y5EDmFbjH8bGMDFwlid30h2x1ybibT1Dwp0vekJ0OT4tb7/j6
?>
```

The interesting feature here is, every time I execute this `Encrypt()` function, by using the **same** password and **same** data, the encrypted string is always changed as `mt_rand()` function is used for generating the salt.

Here are the example outputs of the encrypted string after I executed it for three times:-

```
// Output 1: U2FsdGVkX19LYv5Y5EDmFbjH8bGMDFwlid30h2x1ybibT1Dwp0vekJ0OT4tb7/j6
// Output 2: U2FsdGVkX1/3zxJCcE8p89t67nJNp8blNkezNxTVn4IDFQLM755K2+OSfFHewDLI
// Output 3: U2FsdGVkX18OQ8puUN8BBi+d6vAjEzDTZqM2WaKQD1atOykkYl9MY7NQM1DqI4Kw
```

Hence, even the encrypted string is changed each time I executed the function for the same input data, but it still be able to be decrypted and get back the original data string.

### Data decryption

To get back the original data string, I use this decryption function.

```php
<?php
function Decrypt($password, $data)
{

    $data = base64_decode($data);
    $salt = substr($data, 8, 8);
    $ct   = substr($data, 16);

    $key = md5($password . $salt, true);
    $iv  = md5($key . $password . $salt, true);

    $pt = mcrypt_decrypt(MCRYPT_RIJNDAEL_128, $key, $ct, MCRYPT_MODE_CBC, $iv);

    return $pt;
}
?>
```

**Example:**

```php
<?php
echo Decrypt('myPass123', 'U2FsdGVkX19LYv5Y5EDmFbjH8bGMDFwlid30h2x1ybibT1Dwp0vekJ0OT4tb7/j6');
echo Decrypt('myPass123', 'U2FsdGVkX1/3zxJCcE8p89t67nJNp8blNkezNxTVn4IDFQLM755K2+OSfFHewDLI');
echo Decrypt('myPass123', 'U2FsdGVkX18OQ8puUN8BBi+d6vAjEzDTZqM2WaKQD1atOykkYl9MY7NQM1DqI4Kw');
// All of three above will output the same decrypted data: Welcome to Flippancy 25
?>
```

**NOTE:** As you can see here, for each encryption and decryption requires the supplied _password_ to work as this is part of the security layers.

### Practical use of data encryption and decryption

Both of these PHP functions are useful when we want to securely encrypt user confidential information such as social security number, phone number, bank account number, credit card information, etc. where at some points of time, we need it back in a plain text.

For user password, you should not encrypt it using this encryption as this is reversible and totally not practical for user password. Instead, you should hash them.

### Secure password hashing using PHP custom function

Despite of data encryption and decryption, another part that plays very important role in our application development is password hashing. The best implementation in password hashing is to use **_one-way hashing technique_**. Meaning that, it is irreversible. Unlike the encryptions, they are formulated to be able to be decrypted it back. The password is encrypted and hashed for one-time only and it is no longer can be decrypted to a plain text anymore. You may want to check [password_hash](http://php.net/manual/en/function.password-hash.php) or [crypt](http://php.net/manual/en/function.crypt.php) for PHP one-way hashing algorithms.

However, in my projects, I am more likely to use this custom hashing function to hash the user password:

```php
<?php
function hashPassword($password, $salt) {
    $hash_password = hash("SHA512", base64_encode(str_rot13(hash("SHA512", str_rot13($salt . $password)))));
    return $hash_password;
}
?>
```

**Example:**

```php
<?php
echo hashPassword('myPa55w0rd', 'Flipp@ncy25');
// Output: 815890bb72e10a75a52087513a931afb6641a5d8d105365fa6f389f038dd81b45290a44cf94bb61e7741e073c6f4d59a16e9896bd197cc320f84f3a4d27cfb50
?>
```

That is it how I do the data encryption, data decryption and secure implementation of password hashing in PHP. If you are keen to learn more about password hashing, I recommend you to read [this article](https://crackstation.net/hashing-security.htm). It is a really good article talking about salted password hashing.
