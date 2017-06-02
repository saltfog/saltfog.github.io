---
layout: post
title: Simple way to minify CSS on-the-fly with PHP
description: Without using any PHP plugin or module, here's a simple trick to minify CSS code on-the-fly using PHP script.
keywords: web optimization, css compression, css minification, php script
tags: [PHP, Compression]
comments: true
---

CSS minification or compression is one of the rules for optimizing a web page loading performance. Here's a simple trick that we can do to minify a bunch of CSS files on the fly by using PHP script.

Assuming I have a bunch of CSS files such as grid.css, typography.css, button.css, form.css, table.css, etc. in a directory called "..\assets\css". Then I create a PHP file in the same directory and name it as _minified.css.php_.

Here is **minified.css.php** source code:

```php
<?php
header('Content-type: text/css');
ob_start("compress");

  function compress($buffer) {
    /* remove comments */
      $buffer = preg_replace('!/\*[^*]*\*+([^/][^*]*\*+)*/!', '', $buffer);

      /* remove tabs, spaces, newlines, etc. */
      $buffer = str_replace(array("\r\n", "\r", "\n", "\t", '  ', '    ', '    '), '', $buffer);

      return $buffer;
  }

  /* css files for compression */
  include('grid.css');
  include('typography.css');
  include('button.css');
  include('form.css');
  include('table.css');
  include('backgrounds.css');
  include('pagination.css');
  include('breadcrumbs.css');
  include('font.css');
  include('helpers.css');
  include('print.css');
  include('animation.css');
  include('responsive.css');

ob_end_flush();
?>
```

PHP code above will include all those CSS files, minify them, and output itself as a functional CSS-compressed file.

This is how we include _minified.css.php_ file in our HTML `<head>` section:

```HTML
<html lang="en">
  <head>
    <title>My Website</title>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=9">

    <link href="assets/css/minified.css.php" rel="stylesheet">

  </head>
  <body>
    <!-- website content -->
  </body>
</html>
```

Happy optimization!
