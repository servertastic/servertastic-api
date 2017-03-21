# API documentation for Encryption Everywhere Resellers

* [APIv2 Documentation](/api2.md)
* [Encryption Everywhere API](/api2-ee.md)
* [Previous API Documentation](/api.md)
* [About Servertastic Reseller Program](https://www.servertastic.com/resellers/)
* [Reseller Dashboard](https://reseller.servertastic.com)
* [Servertastic Support](https://www.servertastic.com/support)

If you have issues with the API documentation please feel free to raise an issue in GitHub or email <team@servertastic.com>

The Encryption Everywhere (EE) functions are only available to resellers who have signed up to the Encryption Everywhere program.

To place an EE order you must first use the `generatedns` call to retrieve the required DNS string. Once the DNS string has been added to the domain you can then `place` the order. The domain validation is done during the place order. The place order request will either respond with a success and the certificate or fail. There is no pending order status. If an order fails a verbose response should identify the reasons.

All EE orders must use the `POST` method.

API Endpoint: `https://api2.servertastic.com`

Test API Endpoint: `https://test-api2.servertastic.com`

Whitelabel API Endpoint: `https://api2.manage.cm`

## EE API Calls

### `generatedns`
This call is used for Encryption Everywhere resellers to obtain the required DNS string BEFORE using the `placeee` call.

**`generatedns` request**

Must use `POST` method

`https://api2.servertastic.com/order/generatedns`

`'api_key'=>'[Your API Key]'`
`'csr' => '[Valid CSR that will be used during place]'`

Must use `POST` method. The returned `dv_auth_dns_string` should be added to the domain DNS as a TXT record.

`https://api2.servertastic.com/order/generatedns`

`'api_key'=>'[Your API Key]'`
`'csr' => '[Valid CSR that will be used during place]'`

**`generatedns` response**

	<?xml version="1.0"?>
	<response>
		<success>EE DNS</success>
		<dv_auth_dns_string>[computedstring]</dv_auth_dns_string>
	</response>
	
### `generatefile`

**`generatefile` request**

Must use `POST` method.

`https://api2.servertastic.com/order/generatefile`

`'api_key'=>'[Your API Key]'`
`'csr' => '[Valid CSR that will be used during place]'`

**`generatefile` response**

	<?xml version="1.0"?>
	<response>
  		<success>EE FILE</success>
  		<dv_auth_file_contents>[computedstring]</dv_auth_file_contents>
	</response>

### `placeee`

The `placeee` call is used to place an EE order. The DNS String must have been added to the domain BEFORE you use this call.

It is important to use the actual end customer details for tech and admin contact information. Otherwise your certificate may not be approved.

**`placeee` order request**

`https://api2.servertastic.com/order/placeee`

**`placeee` response**

<response>
<success>Order placed</success>
<reseller_order_id></reseller_order_id>
<certificate></certificate>
<pkcs7></pkcs7>
<intermediate></intermediate>
</response>

## Required Fields

Field | Description
:--|:--
`st_product_code`|Relevant EE Product Code
`api_key`|Your API Key
`end_customer_email`|Any valid email address
`reseller_unique_reference`|Unique order reference supplied by reseller - 35 chars maximum
`domain_name`|Fully Qualified Domain Name. Must match `csr`
`csr`|Generated CSR request
`tech_contact_first_name` | Customer Tech Contact
`tech_contact_last_name` | Customer Tech Contact
`tech_contact_phone` | Customer Tech Contact
`tech_contact_email` | Customer Tech Contact
`tech_contact_title` | Customer Tech Contact
`admin_contact_first_name` | Customer Admin Contact
`admin_contact_last_name` |  Customer Admin Contact
`admin_contact_phone` |  Customer Admin Contact
`admin_contact_email` |  Customer Admin Contact
`admin_contact_title` |  Customer Admin Contact
`ee_type` | `DNS` or `FILE`
	
	
## Further Field Definitions

### Encryption Everywhere Products

Product Code | Product Description | Server Count Values | SAN Values | Type
:--|:--|:--|:--|:--
`EESecureSiteStarter-12` | Basic DV - 1 Year | Not Applicable | Not Applicable  | DV