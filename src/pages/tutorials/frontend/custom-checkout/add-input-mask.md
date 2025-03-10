---
title: Add Custom Input Mask for ZIP Code | Commerce PHP Extensions
description: Follow this tutorial to create a custom input mask to validate address ZIP codes in the Adobe Commerce and Magento Open Source checkout experience.
---

# Add custom input mask for ZIP code

This topic describes how a developer can add custom input masks.

When a shopper specifies the country and ZIP code in the shipping address during checkout or in the shopping cart, the application checks if the format of the entered code is valid for the specified country. This validation is implemented using the input masks for the ZIP code field. In Adobe Commerce and Magento Open Source, these input masks are regular expressions which define which format is allowed.

In Adobe Commerce and Magento Open Source the input masks for the **ZIP code** field are specified in the `<Magento_Directory_module_dir>/etc/zip_codes.xml`. Input masks are specified per country, and are entered in the form of regular expressions.
The syntax of defined by the [zip_code.xsd](https://github.com/magento/magento2/blob/2.4/app/code/Magento/Directory/etc/zip_codes.xsd) scheme.

The following table defines the `zip` node attributes:

 Attribute name | Required | Description
--- | --- | ---
`countryCode` | Yes | The country code (Alpha-2 format) for which the zip is defined

```xml
    <zip countryCode="US">
        <!-- Here we add the zip codes -->
    </zip>
```

The following table defines the `code` node attributes:

 Attribute name | Required | Description
--- | --- | ---
`id` | Yes | A random unique name within the same list.
`example` | Yes | An example of the allowed pattern.
`active` | No | Defines if this zip pattern is active or not.

You can define several zip `code` patterns for the same country, by passing a list of `codes`.

```xml
    <codes>
        <code id="pattern_1" active="true" example="12345">^[0-9]{5}$</code>
        <code id="pattern_2" active="true" example="AB1234">^[a-zA-z]{2}[0-9]{4}$</code>
    </codes>
```

For the sake of compatibility, upgradability, and easy maintenance, do not edit the default application code. Add your customizations in a separate, custom module. For your ZIP code input mask customization to be applied correctly, your custom module should depend on the `Magento_Directory` module. Do not use `Ui` for your custom module name, because `%Vendor%_Ui` notation, required when specifying paths, might cause issues.

To add custom ZIP code input masks or change the default ones, create a new `zip_codes.xml` in the `<your_module_dir>/etc` directory.

The content of the file should be similar to the following sample:

```xml
<?xml version="1.0"?>

<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Directory:etc/zip_codes.xsd">

    <!-- Specify the country ISO code-->
    <zip countryCode="US">
        <!-- You can specify several patterns for one country -->
        <codes>
            <code id="pattern_1" active="true" example="12345-6789">^[0-9]{5}\-[0-9]{4}$</code>
            <code id="pattern_2" active="true" example="12345">^[0-9]{5}$</code>
        </codes>
    </zip>
</config>
```

## Modify default values in existing mask

To change (override the default value) the existing mask:

1. Open `zip_codes.xml`.
1. Copy in the related nodes.
1. Change the regular expression defining the mask and the value of `example` correspondingly.

Example of changing the default input mask:

In the default `<Magento_Directory_module_dir>/etc/zip_codes.xml` the following mask is set for France:

```xml
<?xml version="1.0"?>

<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Directory:etc/zip_codes.xsd">
...
    <zip countryCode="FR">
        <codes>
            <code id="pattern_1" active="true" example="12345">^[0-9]{5}$</code>
        </codes>
    </zip>
...
</config>
```

To change this mask, add the following code in your `zip_codes.xml`:

```xml
<?xml version="1.0"?>

<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Directory:etc/zip_codes.xsd">
...
    <zip countryCode="FR">
        <codes>
            <!-- Changed the regexp defining the mask, and the value of "example" -->
            <code id="pattern_1" active="true" example="A123">^[a-zA-Z]{1}[0-9]{3}$</code>
        </codes>
    </zip>
...
</config>
```

## Remove a mask

To remove a mask, in your `zip_codes.xml` add the corresponding node and set `active` attribute of `<code/>` to `false`.
