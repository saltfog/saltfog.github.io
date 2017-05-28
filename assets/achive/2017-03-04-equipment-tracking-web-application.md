---
layout: post
title: Equipment Tracking web application
description: Simple CRUD application with basic user authentication system for internal equipment tracking.
keywords: cured application, php application, datatables, equipment tracking web app, crud ajax web application
tags: [PHP, CRUD, AJAX, DataTables, Web Application]
comments: true
---

A couple of weeks ago I was asked to gather and compile a list of equipment that have been borrowed by a group of engineers and the list I received is in MS Excel file. So, I thought instead of waiting them to send me the list file once in a year, I should create a simple web application where they can easily update the equipment info anywhere and anytime - live update. Then, I decided to spend few times to write this simple web application called Equipment Tracking (Etrac).

Technically, Etrac is built from scratch using [DataTables](https://datatables.net/), [jQuery](https://jquery.com/), [PHP](http://php.net/), [MySQL](https://www.mysql.com/) and [Bootstrap](http://getbootstrap.com/) with [Google-style theme](https://todc.github.io/todc-bootstrap/). Etrac uses a very simple user authentication module that I created before in PHP for use in other old projects. Now with this web app, whenever I need the latest list of borrowed equipment, I can just go to the app URL (set to intranet accessible only) and export or print the list as the user can easily update the equipment info to the latest info. Etrac supports exporting to PDF, Excel and CSV file format.

### Equipment Tracking (Etrac) v1.0 Screenshots

[![Screenshot1](http://i.imgur.com/dzJf6Wy.png)](http://i.imgur.com/dzJf6Wy.png)
*Figure 1 (above): List of equipment displayed using DataTables jQuery plugin without user login.*

[![Screenshot2](http://i.imgur.com/U0KobRL.png)](http://i.imgur.com/U0KobRL.png)
*Figure 2 (above): Login page.*

[![Screenshot3](http://i.imgur.com/Lsx4JkX.png)](http://i.imgur.com/Lsx4JkX.png)
*Figure 3 (above): List of equipment with logged user. If the user has the right permission given, the user can click CRUD buttons such as "Add new record", "Batch delete..." or "Update" button.*

[![Screenshot4](http://i.imgur.com/xyXCltl.png)](http://i.imgur.com/xyXCltl.png)
*Figure 4 (above): Adding new record via Modal dialog.*

[![Screenshot5](http://i.imgur.com/s2Sud2z.png)](http://i.imgur.com/s2Sud2z.png)
*Figure 5 (above): When user clicks on delete button, a confirmation will pop up to double-confirm before the delete action can be performed.*

[![Screenshot6](http://i.imgur.com/ulkAEDG.png)](http://i.imgur.com/ulkAEDG.png)
*Figure 6 (above): Search or filter can be performed easily when using this DataTables jQuery plugin.*

[![Screenshot7](http://i.imgur.com/TTdjjVV.png)](http://i.imgur.com/TTdjjVV.png)
*Figure 7 (above): Super simple user profile settings page.*

[![Screenshot8](http://i.imgur.com/sluB9Lk.png)](http://i.imgur.com/sluB9Lk.png)
*Figure 8 (above): List of registered users.*

### Etrac v1.0 DataTables Code Snippets

JavaScript:

```js
var table = $('.datatables-table').DataTable({
    fixedHeader: true,
    "processing": true,
    "serverSide": true,
    "ajax": {
        "url": "datatables.php",
        "type": "POST"
    },
    "columns": [{
            "data": "id"
        },
        {
            "data": "model"
        },
        {
            "data": "description"
        },
        {
            "data": "project"
        },
        {
            "data": "serial_number"
        },
        {
            "data": "owner"
        },
        {
            "data": "current_owner"
        },
        {
            "data": "remark"
        },
        {
            "data": "id"
        }
    ],
    "order": [
        [0, "desc"]
    ],
    aLengthMenu: [
        [10, 25, 50, 100, 200, -1],
        [10, 25, 50, 100, 200, "All"]
    ],
    iDisplayLength: 10, // default display
    // Enable mark.js search term highlighting
    mark: true,
    dom: 'lBfrtip',
    buttons: [{
            extend: 'copy',
            text: '<span class="hand-pointer" title="Copy to clipboard"><i class="fa fa-clipboard" aria-hidden="true"></i> Copy</span>',
            exportOptions: {
                columns: [1, 2, 3, 4, 5, 6, 7]
            }
        },
        {
            extend: 'csv',
            text: '<span class="hand-pointer" title="Export to CSV file"><i class="fa fa-file-text-o" aria-hidden="true"></i> CSV</span>',
            exportOptions: {
                columns: [1, 2, 3, 4, 5, 6, 7]
            }
        },
        {
            extend: 'excel',
            text: '<span class="hand-pointer" title="Export to Excel file"><i class="fa fa-file-excel-o" aria-hidden="true"></i> Excel</span>',
            exportOptions: {
                columns: [1, 2, 3, 4, 5, 6, 7]
            }
        },
        {
            extend: 'pdf',
            text: '<span class="hand-pointer" title="Export as PDF file"><i class="fa fa-file-pdf-o" aria-hidden="true"></i> PDF</span>',
            exportOptions: {
                columns: [1, 2, 3, 4, 5, 6, 7]
            }
        },
        {
            extend: 'print',
            text: '<span class="hand-pointer" title="Print"><i class="fa fa-print" aria-hidden="true"></i> Print</span>',
            exportOptions: {
                columns: [1, 2, 3, 4, 5, 6, 7]
            }
        },
        {
            text: '<span class="hand-pointer" title="Select all visible rows"><i class="fa fa-check-square-o" aria-hidden="true"></i> Select All</span>',
            action: function() {
                table.rows().select();
            }
        },
        {
            text: '<span class="hand-pointer" title="Deselect all"><i class="fa fa-square-o" aria-hidden="true"></i> Select None</span>',
            action: function() {
                table.rows().deselect();
            }
        }
    ],
    select: {
        style: 'multi'
    },
    columnDefs: [{
        targets: 8,
        render: function(data, type, full, meta) {
            if (type === 'display') {
                if (userlevel == 0 || userlevel == 1) {
                    data = '<div class="action-links"><button type="button" class="btn btn-default btn-xs disabled" data-eqid="' + data + '" id="btnUpdate"><i class="fa fa-pencil-square-o" aria-hidden="true"></i> Update</button> <button type="button" class="btn btn-default btn-xs disabled" data-eqid="' + data + '" id="btnDelete" title="Delete"><i class="fa fa-trash" aria-hidden="true"></i></button> <a href="equipment.php?id=' + data + '" class="btn btn-primary btn-xs hand-pointer" data-eqid="' + data + '" title="View details"><i class="fa fa-eye" aria-hidden="true"></i></a></div>';
                }
                if (userlevel == 2) {
                    data = '<div class="action-links"><button type="button" class="btn btn-info btn-xs btnUpdate hand-pointer" data-eqid="' + data + '" id="btnUpdate"><i class="fa fa-pencil-square-o" aria-hidden="true"></i> Update</button> <button type="button" class="btn btn-default btn-xs disabled" data-eqid="' + data + '" id="btnDelete" title="Delete"><i class="fa fa-trash" aria-hidden="true"></i></button> <a href="equipment.php?id=' + data + '" class="btn btn-primary btn-xs hand-pointer" data-eqid="' + data + '" title="View details"><i class="fa fa-eye" aria-hidden="true"></i></a></div>';
                }
                if (userlevel == 3 || userlevel == 4) {
                    data = '<div class="action-links"><button type="button" class="btn btn-info btn-xs btnUpdate hand-pointer" data-eqid="' + data + '" id="btnUpdate"><i class="fa fa-pencil-square-o" aria-hidden="true"></i> Update</button> <button type="button" class="btn btn-danger btn-xs btnDelete hand-pointer" data-eqid="' + data + '" id="btnDelete" title="Delete"><i class="fa fa-trash" aria-hidden="true"></i></button> <a href="equipment.php?id=' + data + '" class="btn btn-primary btn-xs hand-pointer" data-eqid="' + data + '" title="View details"><i class="fa fa-eye" aria-hidden="true"></i></a></div>';
                }
            }

            return data;
        }
    }],
    "language": {
        "search": "<strong>Search / filter records:</strong>",
        "lengthMenu": "<strong>Display:</strong> _MENU_ records per page",
        "zeroRecords": "Nothing found - sorry!",
        "info": "Showing page _PAGE_ of _PAGES_",
        "infoEmpty": "No records available",
        "infoFiltered": "(filtered from _MAX_ total records)"
    }

});
```

PHP (server-side):

```php
<?php

// Global config
require_once 'config.php';

/* * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
 * Easy set variables
 */

// DB table to use
$table = TABLE_EQUIPMENTS;

// Table's primary key
$primaryKey = 'id';

// Array of database columns which should be read and sent back to DataTables.
// The `db` parameter represents the column name in the database, while the `dt`
// parameter represents the DataTables column identifier. In this case object
// parameter names
$columns = array(
  array( 'db' => 'id',           'dt' => 'id'),
  array( 'db' => 'model',        'dt' => 'model' ),
  array( 'db' => 'description',  'dt' => 'description' ),
  array( 'db' => 'project',      'dt' => 'project' ),
  array( 'db' => 'serial_number','dt' => 'serial_number' ),
  array( 'db' => 'owner',        'dt' => 'owner' ),
  array( 'db' => 'current_owner','dt' => 'current_owner' ),
  array( 'db' => 'remark',       'dt' => 'remark' )
);

// SQL server connection information
$sql_details = array(
  'user' => DB_USER,
  'pass' => DB_PASS,
  'db'   => DB_NAME,
  'host' => DB_HOST
);


/* * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
 * If you just want to use the basic configuration for DataTables with PHP
 * server-side, there is no need to edit below this line.
 */

require( 'ssp.class.php' );

echo json_encode(
	SSP::complex( $_POST, $sql_details, $table, $primaryKey, $columns, null, 'deleted=0' )
);
```

Note: **ssp.class.php** is available on [DataTables' repo on GitHub here](https://github.com/DataTables/DataTables/blob/master/examples/server_side/scripts/ssp.class.php).

### Etrac v1.0 Full Source Code

Etrac v1.0 full source code will be available soon and I will update the link here later. If you're really interested and can't wait for it, you may just drop me an email at hnrird(at)gmail.com.
