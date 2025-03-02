=====================
Product Import Helper
=====================

This module adds methods to help on the import of products:

- validate EAN barcode
- detect double barcodes and double internal references
- create orderpoints, supplierinfo, product category, POS category,
- initialize stock level

Sample code
===========

Here is some sample code:

.. code::

  # parse Excel or CSV that contains the products to import in Odoo
  import_obj = self.env['import.helper']
  speedy = import_obj._prepare_speedy()
  line = 0
  for row in reader:  # loop on lines of the Excel
      line += 1
      vals = {
          'line': line,
          'name': row[0],
          'default_code': row[1],
          'barcode': row[2],
          'vat_rate': 200,
          'categ_name': row[4],
          'orderpoint_min_qty': row[5],
          'stock_qty': row[6],
          'create_date': row[10],  # in format %Y-%m-%d
          }
      import_obj._create_product(vals, speedy)
  action = import_obj._result_action(speedy)
  return action  # show import logs to the user


In the sample code above, ``vals`` is the dictionary that will be passed to ``create()`` of product.product, with few differences:

- it must contain a **'line'** key to indicate the Excel/CSV import ref in logs, which will be removed before calling ``create()``,
- it can contain a **'vat_rate'** key with the VAT rate x 10 as integer (20% -> 200, 10% -> 100, 5,5% -> 55, 2,1% -> 21) that will be used to set the fiscal classification,
- it can contain a **'supplier_id'** key with the ID of the supplier partner, along with the keys **'supplier_price'**, **'supplier_product_code'**, **'supplier_product_name'**, **'supplier_delay'**, **'supplier_currency'** (currency ISO code or ID),
- it can contain an **'orderpoint_min_qty'** key the min quantity of the reordering rule, along with the keys **'orderpoint_max_qty'** and **'orderpoint_trigger'** ('manual' or 'auto'),
- it can contain a **'categ_name'** key that will be used to match an existing product category or create a new one,
- it can contain a **'pos_categ_name'** key that will be used to match an existing POS category or create a new one,
- it can contain a **'stock_qty'** key that will be used to set the initial stock quantity of the product,
- it can contain a **'income_account_code'** or **'expense_account_code'** key that will be used to set the income and expense accounts (in the user's company),
- it can contain a **'route_codes'** key that contains a list of codes among the following codes: 'buy', 'manufacture' or 'mto' to set the routes.

Author
======

* Alexis de Lattre <alexis.delattre@akretion.com>
