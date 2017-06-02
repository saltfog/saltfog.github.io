---
layout: post
title: Commonly use PHP functions or code snippets in my PHP projects
description: Compilation of commonly use PHP functions or code snippets in my PHP projects and they are quite helpful in most of the time.
keywords: php, commonly use php functions, php code snippets, php projects, compilation of code snippets, helpful php functions
tags: [PHP]
comments: true
---

Few years ago, I had been actively developing stuffs in PHP and some of my PHP projects source code are backed up to my [GitHub](https://github.com/heiswayi). Here is a compilation of commonly use PHP functions in my PHP projects and they are quite helpful in most of the time.

### Hash user password

```php
<?php
function hashUserPassword($salt, $password) {
    return hash("SHA512", base64_encode(str_rot13(hash("SHA512", str_rot13($salt . $password)))));
}
?>
```

### Working with string inputs in secure manner

```php
<?php
// HTML encode string
function htmlEncodeString($str) {
    return htmlspecialchars($str, ENT_QUOTES, 'UTF-8');
}

// Trim whitespace including non-breaking space.
function trimString($str, $charlist = " \t\n\r\0\x0B\xC2\xA0") {
    return utf8_trim($str, $charlist);
}

// Convert \r\n and \r to \n
function linebreakString($str) {
    return str_replace(array(
        "\r\n",
        "\r"
    ), "\n", $str);
}

// Only accepts alphanumeric and underscore characters.
function isValidUsername($str) {
    return preg_match('/^[a-zA-Z0-9_]+$/', $str);
}

// Only accepts alphanumeric, underscore, dash, dot and whitespace characters.
function isStandardCharacters($str) {
    return preg_match('/^[a-zA-Z0-9_\-\.\s]+$/', $str);
}

// Save string input from textarea for address with new line format.
function address($str) {
    return nl2br(stripslashes(rtrim($str)));
}

// Normalize string.
function normalize($str) {
    return stripslashes(rtrim($str));
}

// Sanitize string.
function sanitize($string) {
    return stripslashes(rtrim(htmlspecialchars($string)));
}
?>
```

### Get Gravatar

```php
<?php
function getGravatar($email, $default = 'retro', $size = '50') {
    return "http://www.gravatar.com/avatar/" . md5(strtolower(trim($email))) . "?d=" . urlencode($default) . "&s=" . $size;
}
?>
```

### Get IP Address

```php
<?php
function getIp() {
    $ipaddress = '';
    if (!empty($_SERVER['HTTP_CLIENT_IP'])) {
        $ipaddress = $_SERVER['HTTP_CLIENT_IP'];
    } else if (!empty($_SERVER['HTTP_X_FORWARDED_FOR'])) {
        $ipaddress = $_SERVER['HTTP_X_FORWARDED_FOR'];
    } else if ($_SERVER['REMOTE_ADDR']) {
        $ipaddress = $_SERVER['REMOTE_ADDR'];
    } else {
        $ipaddress = 'UNKNOWN';
    }
    return $ipaddress;
}
?>
```

### Get current page URL

```php
<?php
function getCurrentPageUrl() {
    $s        = empty($_SERVER["HTTPS"]) ? '' : ($_SERVER["HTTPS"] == "on") ? "s" : "";
    $protocol = substr(strtolower($_SERVER["SERVER_PROTOCOL"]), 0, strpos(strtolower($_SERVER["SERVER_PROTOCOL"]), "/")) . $s;
    $port     = ($_SERVER["SERVER_PORT"] == "80") ? "" : (":" . $_SERVER["SERVER_PORT"]);
    return $protocol . "://" . $_SERVER['SERVER_NAME'] . $port . $_SERVER['REQUEST_URI'];
}
?>
```

### Convert timestamp to time-ago style

```php
<?php
function timeAgo($timestamp) {
    $timestamp    = (int) $timestamp;
    $current_time = time();
    $diff         = $current_time - $timestamp;
    $intervals    = array(
        'year' => 31556926,
        'month' => 2629744,
        'week' => 604800,
        'day' => 86400,
        'hour' => 3600,
        'minute' => 60
    );
    if ($diff == 0) {
        return 'just now';
    }
    if ($diff < 60) {
        return $diff == 1 ? $diff . ' second ago' : $diff . ' seconds ago';
    }
    if ($diff >= 60 && $diff < $intervals['hour']) {
        $diff = floor($diff / $intervals['minute']);
        return $diff == 1 ? $diff . ' minute ago' : $diff . ' minutes ago';
    }
    if ($diff >= $intervals['hour'] && $diff < $intervals['day']) {
        $diff = floor($diff / $intervals['hour']);
        return $diff == 1 ? $diff . ' hour ago' : $diff . ' hours ago';
    }
    if ($diff >= $intervals['day'] && $diff < $intervals['week']) {
        $diff = floor($diff / $intervals['day']);
        return $diff == 1 ? $diff . ' day ago' : $diff . ' days ago';
    }
    if ($diff >= $intervals['week'] && $diff < $intervals['month']) {
        $diff = floor($diff / $intervals['week']);
        return $diff == 1 ? $diff . ' week ago' : $diff . ' weeks ago';
    }
    if ($diff >= $intervals['month'] && $diff < $intervals['year']) {
        $diff = floor($diff / $intervals['month']);
        return $diff == 1 ? $diff . ' month ago' : $diff . ' months ago';
    }
    if ($diff >= $intervals['year']) {
        $diff = floor($diff / $intervals['year']);
        return $diff == 1 ? $diff . ' year ago' : $diff . ' years ago';
    }
}
?>
```

### Create HTML anchor to URL, @mention and #hashtag

```php
<?php
// Make URL, #mention, #hashtag clickable.
function makeClickable($input) {
    $output = preg_replace(array(
        '/(?i)\b((?:https?:\/\/|www\d{0,3}[.]|[a-z0-9.\-]+[.][a-z]{2,4}\/)(?:[^\s()<>]+|\(([^\s()<>]+|(\([^\s()<>]+\)))*\))+(?:\(([^\s()<>]+|(\([^\s()<>]+\)))*\)|[^\s`!()\[\]{};:\'".,<>?«»“”‘’]))/',
        '/(^|[^a-z0-9_])@([a-z0-9_]+)/i',
        '/(^|[^a-z0-9_])#([a-z0-9_]+)/i'
    ), array(
        '<a href="$1" target="_blank" rel="nofollow">$1</a>',
        ' <a href="profile.php?user=$2" target="_blank" rel="nofollow">@$2</a>',
        ' <a href="hashtag.php?tag=$2" target="_blank" rel="nofollow">#$2</a>'
    ), $input);
    return $output;
}
?>
```

### BB code style, emoticons conversion

```php
<?php
// (.*?) is a search pattern, will match with emoticon image filename.
function bbCode($var) {
    $search = array(
        '/\[\:s(.*?)\:\]/is',
        '/\[\:t(.*?)\:\]/is',
        '/\[\:o(.*?)\:\]/is',
        '/\[\:c(.*?)\:\]/is'
    );

    $replace = array(
        '<img src="img/smileys/s$1.png">',
        '<img src="img/tuzkiclub/t$1.gif">',
        '<img src="img/onionclub/o$1.gif">',
        '<img src="img/cutes/c$1.gif">'
    );

    $result = preg_replace($search, $replace, $var);
    return $result;
}
?>
```

### Generate or get random key

```php
<?php
// $len is a length of the random key.
function getRandomKey($len, $readable = false, $hash = false) {
    $key = '';

    if ($hash)
        $key = substr(sha1(uniqid(rand(), true)), 0, $len);
    else if ($readable) {
        $chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';

        for ($i = 0; $i < $len; ++$i)
            $key .= substr($chars, (mt_rand() % strlen($chars)), 1);
    } else
        for ($i = 0; $i < $len; ++$i)
            $key .= chr(mt_rand(33, 126));

    return $key;
}
?>
```

### Truncate a long text

```php
<?php
// $length = 0 will retain the original text length.
function truncateLongText($text, $length = 0) {
    if ($length > 0 && strlen($text) > $length) {
        $tmp = substr($text, 0, $length);
        $tmp = substr($tmp, 0, strrpos($tmp, ' '));
        if (strlen($tmp) >= $length - 3) {
            $tmp = substr($tmp, 0, strrpos($tmp, ' '));
        }
        $text = $tmp . '...';
    }
    return $text;
}
?>
```

###  Generate or get random password

```php
<?php
function generateRandomPassword($length = 9, $strength = 0) {
    $vowels     = 'aeuy';
    $consonants = 'bdghjmnpqrstvz';
    if ($strength >= 1) {
        $consonants .= 'BDGHJLMNPQRSTVWXZ';
    }
    if ($strength >= 2) {
        $vowels .= "AEUY";
    }
    if ($strength >= 4) {
        $consonants .= '23456789';
    }
    if ($strength >= 8) {
        $vowels .= '@#$%';
    }

    $password = '';
    $alt      = time() % 2;
    for ($i = 0; $i < $length; $i++) {
        if ($alt == 1) {
            $password .= $consonants[(rand() % strlen($consonants))];
            $alt = 0;
        } else {
            $password .= $vowels[(rand() % strlen($vowels))];
            $alt = 1;
        }
    }
    return $password;
}
?>
```

### Auto-link

```php
<?php
function autolink($message, $strip_tags = false) {
    if ($strip_tags) {
        $message = strip_tags($message);
    }

    // Convert all URLs to clickable links
    $message     = preg_replace('#([s|^])(www)#i', '$1http://$2', $message);
    $pattern     = '#((http|https|ftp|telnet|news|gopher|file|wais)://[^s]+)#i';
    $replacement = '<a href="$1" target="_blank">$1</a>';
    $message     = preg_replace($pattern, $replacement, $message);

    // Convert all email matches to appropriate HTML links
    $pattern = '#([0-9a-z]([-_.]?[0-9a-z])*@[0-9a-z]([-.]?[0-9a-z])*\.';
    $pattern .= '[a-wyz][a-z](fo|g|l|m|mes|o|op|pa|ro|seum|t|u|v|z)?)#i';
    $replacement = '<a href="mailto:\1">\1</a>';
    $message     = preg_replace($pattern, $replacement, $message);
    return $message;
}
?>
```

### Check if the URL is a valid URL

```php
<?php
function isValidUrl($url) {
    $regex = "((https?|ftp)\:\/\/)?"; // Scheme
    $regex .= "([a-z0-9+!*(),;?&=\$_.-]+(\:[a-z0-9+!*(),;?&=\$_.-]+)?@)?"; // User and Pass
    $regex .= "([a-z0-9-.]*)\.([a-z]{2,3})"; // Host or IP
    $regex .= "(\:[0-9]{2,5})?"; // Port
    $regex .= "(\/([a-z0-9+\$_-]\.?)+)*\/?"; // Path
    $regex .= "(\?[a-z+&\$_.-][a-z0-9;:@&%=+\/\$_.-]*)?"; // GET Query
    $regex .= "(#[a-z_.-][a-z0-9+\$_.-]*)?"; // Anchor
    return preg_match("/^$regex$/", $url);
}
?>
```

### Simple string encryption and decryption

```php
<?php
// Encrypt a plain string.
function encryptString($key, $string) {
    return base64_encode(mcrypt_encrypt(MCRYPT_RIJNDAEL_256, md5($key), $string, MCRYPT_MODE_CBC, md5(md5($key))));
}

// Decrypt an encrypted string.
function decryptString($key, $string) {
    return rtrim(mcrypt_decrypt(MCRYPT_RIJNDAEL_256, md5($key), base64_decode($string), MCRYPT_MODE_CBC, md5(md5($key))), "\0");
}
?>
```
