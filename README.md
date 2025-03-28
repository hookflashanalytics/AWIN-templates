# AWIN Tag Templates
This repository contains two Google Tag Manager (GTM) templates that help solve common AWIN tracking issues:

AWIN Product-Level Tracking Template

AWIN Last Click Identifier (LCI) Fix

Both are offered under the MIT License, meaning you can use them at your own risk, with no warranty or liability on our part.

# AWIN Product-Level Tracking Template
# The Problem
When you promote your business via affiliate partners on AWIN, you often need Product-Level Tracking (PLT) so that each product in a user’s order is properly reported back to AWIN. By default, AWIN provides instructions that require developers to add hidden form elements or make application-level code changes.

For marketing teams or analysts using Google Tag Manager, this can be cumbersome. Our template simplifies the setup by:

Dynamically building the required product lines (AW:P|...) based on your dataLayer.

Injecting the hidden <form> and <textarea> before loading AWIN’s MasterTag script.

Minimizing development overhead so you can deploy via GTM rather than manually editing site code.

# How This Template Solves It
Reads your product data (SKU, name, price, quantity, etc.) from the dataLayer (or any GTM variable).

Builds the AWIN-friendly lines (AW:P|...) for each product, separated by the required \r\n.

Automatically creates the hidden form and <textarea id="aw_basket"> needed by AWIN.

Loads AWIN’s MasterTag (dwin1.com/...js) only after we’ve injected the product lines, ensuring AWIN sees the correct product data in time.

# Setup Instructions
Import Template

In GTM, go to Templates → Tag Templates → New.

Click the three-dot menu in the top right → Import.

Select the awin-product-level-tracking.tpl (or similarly named file) from this repo.

Configure Fields

MerchantId: Your AWIN advertiser program ID.

OrderReference: Your unique order ID (e.g., “ORDER-12345”).

AwinProductItems: A variable returning an array of product objects (see Example Product Data below).

Triggering

Set the tag to fire on your purchase confirmation (thank-you) page, or wherever the final transaction occurs.

Example Product Data
To ensure AWIN sees the correct product details, your AwinProductItems variable must produce an array of objects where each object has the following fields:

[
  {
    "productId": "SKU-12345",        
    "productName": "Red T-Shirt",    
    "productItemPrice": 19.99,       
    "productQuantity": 2,            
    "productSku": "REDTSHIRT-SKU",   
    "commissionGroupCode": "DEFAULT",
    "productCategory": "Clothing"    
  },
  {
    "productId": "SKU-67890",
    "productName": "Blue Jeans",
    "productItemPrice": 45.5,
    "productQuantity": 1,
    "productSku": "BLUEJEANS-SKU",
    "commissionGroupCode": "DEFAULT",
    "productCategory": "Denim"
  }
]

Required fields for AWIN:

productId: Unique identifier (e.g. “SKU-12345”)

productName: Product name

productItemPrice: Single-unit price (float, with a period for decimal)

productQuantity: Quantity purchased

productSku: (Optional) If you track SKUs separately. Otherwise, set to "".

commissionGroupCode: e.g. “DEFAULT” if you aren’t using advanced groupings

productCategory: (Optional) e.g. “Denim”, “Clothing,” etc.

With this data structure in place, the template will generate lines like:

vbnet
Copy
Edit
AW:P|12345|ORDER-12345|SKU-12345|Red T-Shirt|19.99|2|REDTSHIRT-SKU|DEFAULT|Clothing
…and inject them into AWIN’s tracking flow.

# AWIN Last Click Identifier (LCI) Fix
# The Problem
AWIN’s Last Click Identifier is a vital tag for affiliate attribution, determining whether a conversion gets credited to AWIN or another channel. However, the default tag template can lead to:

Cookie Overwriting: If the URL contains multiple source parameters, it sets the AWINChannelCookie based on whichever parameter appears last—sometimes overwriting a valid aw source.

Partial String Matching: It checks if a parameter contains aw, which might accidentally match other random strings (like “kawasaki” or “hawaii”).

These issues can lead to either incorrectly attributing conversions to AWIN or failing to credit AWIN when it should.

# How This Template Solves It
Stops searching once it finds aw in any source parameter (instead of continuing through all parameters).

Uses exact matching (===) instead of “contains.”

Ensures that if the previous session was AWIN, but the user comes back through a different source, the cookie gets properly updated to other.

# Setup Instructions
Import Template

Go to GTM → Templates → Tag Templates → New.

Click the three-dot menu in the top right → Import.

Select the awin-last-click-identifier-fix.tpl file in this repository.

Configure Fields

Used Source Parameters: The list of query parameters you want checked (e.g. source, utm_source, gclid, etc.).

Awin Source Values: Values that should set the cookie to AWIN (e.g. aw). This template uses exact matches, not contains.

Triggering

Typically, you want this LCI fix to fire early on every page load (e.g. an “All Pages” trigger), so the AwinChannelCookie is correct as soon as the page loads.

License & Disclaimer
This project is licensed under the MIT License.
We provide these templates as-is, with no guarantees or warranties. You assume all risk by using them, and we are not liable if something breaks or doesn’t behave as expected.

