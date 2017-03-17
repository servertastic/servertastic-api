# Servertastic APIv2 Documentation

* [APIv2 Documentation](/api2.md)
* [Encryption Everywhere API](/api2-ee.md)
* [Previous API Documentation](/api.md)
* [About Servertastic Reseller Program](https://www.servertastic.com/resellers/)
* [Reseller Dashboard](https://reseller.servertastic.com)
* [Servertastic Support](https://www.servertastic.com/support)

If you have issues with the API documentation please feel free to raise an issue in GitHub or email <team@servertastic.com>

The Servertastic APIv2 contains improvements over our first API. This opens up the API to all customers and not just resellers. Furture enhancements will only be made to APIv2.

API Endpoint: `https://api2.servertastic.com`

Test API Endpoint: `https://test-api2.servertastic.com`

Whitelabel API Endpoint: `https://api2.manage.cm`

## Target Audience
The target audience for this document are Servertastic resellers, or
developers working on their behalf. This document gives an overview
of the API GET and POST requests and return values that allow
resellers to place, review and manage their orders.

* [Overview](#Overview)
* [Using the API](#using-the-api)
	* [Returned Data](#returned-data)
	* [Success and Errors](#success-and-errors)
	* [Test Environment](#test-environment)
	* [Example Usage](#example-usage)
	* [Invite Routine](#invite-routine)
	* [Encryption Everywhere](#encryption-everywhere)
* [Order Web Service Details](#order-web-service-details)
	* [`place`](#place)
	* [`review`](#review)
	* [`cancel`](#cancel)
	* [`resendemail`](#resendemail)
	* [`changeapproveremail`](#changeapproveremail)
	* [`modifiedorders`](#modifiedorders)
	* [`expiring`](#expiring)
	* [`approverlist`](#approverlist)
	* [`changeauthmethod`](#changeauthmethod)
	* [`pollauth`](#pollauth)
	* [`generateToken`](#generatetoken)
	* [`showTokens`](#showtokens)
	* [`advanceorder`](#advanceorder)
* [Account Web Service Details](#account-web-service-details)
	* [`regenerateapikey`](#regenerateapikey)
	* [`review`](#reselleraccount-review)
	* [`pointsaudit`](#pointsaudit)
	* [`addtestpoints`](#addtestpoints)
	* [`emptytestpoints`](#emptytestpoints)
* [Product Web Service Details](#product-web-service-details)
	* [`listproduct`](#listproduct)
* [Further Field Definitions](#further-field-definitions)

## Overview
We offer a series of simple API calls that allow resellers to place, modify and review orders by constructing a URL that contains a number of parameters that allow Servertastic to return relevant information or perform an action on the resellers behalf. The returned information can be displayed as XML (default) or JSON.

The API is organised into three services - `order`, `account` and `products`. The `order` web service is the most commonly used at this point as it is concerned with all operations relating to an order. The `account` service concentrates on all actions related to the resellers account and will be developed further in the future. The `products` section simply allows all products along with related information to be pulled.

## Using the API
This API is intended to be used by Servertastic customers and resellers. Orders placed via Servertastic Retail site can be fulfilled using the API. For more information on becoming a reseller please contact <team@servertastic.com> or visit <https://www.servertastic.com/resellers>. 

### Resellers
Once you have become a reseller you will be provided with an `api_key` it is imperative that this is kept confidential at all times. The `api_key` is used to manage all functions related to the reseller account and to generate orders with an `order_token`. An `order_token` is used to manage all aspects of an order.

### Non Resellers
As a non-reseller you will need to place orders via the [Servertastic Retail website](https://www.servertastic.com). Once the order is fulfilled you will receive an `order_token`. This can be used to complete the order using the API.

API Method|Supported Functions
:--|:--
`api_key`|`regenerateapikey`,`review`,`addtestpoints`,`emptytestpoints`,`listproduct`,`generatetoken`,`generatedns`,`modifiedorders`,`expiring`,`showtokens`
`order_token`|`place`,`review`,`cancel`,`resendemail`,`changeapproveremail`,`approverlist`,`changeauthmethod`,`pollauth`


### Returned Data
By default, the data is returned in an unstyled XML format. If required the returned data can be returned using JSON by appending .json to the URL:

`https://api2.servertastic.com/order/generatetoken.json?st_product_code=RapidSSL-24&api_key=abc123&end_customer_email=customer@domain.com&reseller_unique_reference=xyz01`

### Success and Errors

The first XML element under `response` will let you know whether the request was a success or encountered an error. If an error is encountered a message explaining the problem and an error code is returned. Generally speaking, if the error code is in the 400 range then there was a problem with the API request, a 500 error message signifies a problem with the API. When an error occurs the XML is presented in the following format:

	<?xml version="1.0"?>
	<response>
	  <error>
	    <code></code>
	    <message></message>
	  </error>
	</response>
	
### Test Environment
A test environment has been set-up in order for users of the API to develop solutions in a secure environment. To call the test environment, replace the call URL of `https://api2.servertastic.com` with `https://test-api2.servertastic.com`.

If you would like a test account configuring or run into problems with your test account please don’t hesitate to get in touch with us.

In addition to the test API a sandbox environment for the web interface is available at <https://test-reseller.servertastic.com> in order to gain access please accept the Terms and Conditions at <https://reseller.servertastic.com>.

### Example Usage
Depending on how you would like to use the API, there are a couple of ways of getting access to the methods. The simplest is to visit the URL in the web browser, this will perform the action and display either and XML/JSON response.

If you would like to call the methods in your scripts then technologies such as CURL are recommended, especially as it allows the XML to be retrieved in the event of a 403 error. Below is a quick PHP example for placing an order, the `$response` variable contains XML/JSON string:

	$url = 'https://api2.servertastic.com/order/generatetoken?st_product_code=RapidSSLWildcard-24&api_key=abc123&end_customer_email=customer@domain.com&reseller_unique_reference=ref123';
	$ch = curl_init();
	curl_setopt($ch, CURLOPT_URL, $url);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
	$response = curl_exec($ch);
	curl_close($ch);
	
The place call in the Order web service now allows information to be posted to the URL. Below is an example:

	$data=array(
	‘st_product_code’=>’RapidSSLWildcard-24’,
	‘api_key’=>’abc123’,
	‘end_customer_email’=>’customer@domain.com’,
	‘reseller_unique_reference’=>’ref123’
	);
	$data=http_build_query($data);
	$url = 'https://api2.servertastic.com/order.xml/generatetoken';
	$ch = curl_init();
	curl_setopt($ch, CURLOPT_URL, $url);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
	curl_setopt($ch, CURLOPT_POST, 1);
	curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
	$response = curl_exec($ch);
	curl_close($ch);

## Order Web Service Details

Placing an order using the API requires that you first use the `generatetoken` call to create an `order_token`

### `generatetoken`

This call is used to generate an order token that can be used to place or take further actions on an order.

**`generatetoken` request**

`https://api2.servertastic.com/order/generatetoken?api_key=[Your API Key]&st_product_code=[Product Code]&reseller_unique_reference=[Unique order reference supplied by the reseller - 35 chars maximum]`

For full field information see [Fields](#fields)

**`generatetoken` response**

	<response>
	<success>Order placed</success>
	<reseller_order_id></reseller_order_id>
	<review_url></review_url>
	<order_token></order_token>
	</response>

### Completing Orders via Web Interface
When placing an order a review URL is generated. This URL can be used to either complete the order and/or view the current status of the order. To review the order using the web interface you should proceed to [API End Point]/token/[`order_token`] in browser.

Example using Servertastic branded interface: `https://api2.servertastic.com/token/abc123456789`

Example using white label interface: `https://api2.manage.cm/token/abc123456789`

With APIv2 no emails are sent to the end user with the order management link. You must supply this to the end user yourself if you want them to complete the order using the web interface.

### `place`
The `place` call allows an order to be placed using the API and an `order_token` rather than having to use the Web Interface.

This API call supports both the `POST` and `GET` method. If you are supplying a `csr` then you must use the `POST` method.

#### SSL Products
All orders can pass a `csr` or the API can create one using the organisation, admin contact email and `domain_name` fields. If the `csr` is to be supplied, it can only be sent using the `POST` method. If you do not supply the `csr` and instead supply only the `domain_name` then the `csr` and `private_key` will be returned in the success response. But for security reasons, this information is not stored and cannot be retrieved at a later time.

Domain validated orders can be approved in three ways, `EMAIL` (default), `FILE` and `DNS`. 

**`EMAIL` Authentication**

You must provide a valid email from the `approverlist` to use as an authentication.

**`FILE` Authentication**

The `dv_auth_file_contents` must be hosted on the domain at `/.well-known/pki-validation/fileauth.txt`

**`DNS` Authentication**

The `dv_auth_dns_string` must be placed in a TXT record on the domain.

**`place` order request**

`https://api2.servertastic.com/order/place?order_token=[order_token]`

Additional parameters will be required depending on product type.

For full field information see [Fields](#fields)

**`place` order response**

~~~xml
<?xml version="1.0"?>
<response>
  <success>Order placed</success>
  <reseller_order_id></reseller_order_id> *Used in all future calls
  <invite_url></invite_url>
  <csr></csr> *If CSR generated by API
  <private_key></private_key> *If CSR generated by API
  <dv_auth_file_contents></dv_auth_file_contents> *If generated by API
  <dv_auth_dns_string></dv_auth_dns_string> *If generated by API
</response>
~~~
	
### Fields

Field Name | Required | Further Information
:--|:--|:--
`st_product_code`| Required to generate `order_token`
`api_key` | Required to generate `order_token`
`end_customer_email` | Required to generate `order_token` | Email address of `none` accepted
`reseller_unique_reference` | Required to generate `order_token` | 35 character limit
`order_token` | Not Required when using `generatetoken` otherwise Required
`server_count` | Required for Symantec products
`san_count` | Required for MD products
`WebServerType` | Required for OV and EV Certificates | Accepted values `Other` or `IIS`
`approver_email_address` | Required for DV orders see [`approverlist`](#approverlist)
`tech_contact_first_name` | Required
`tech_contact_last_name` | Required
`tech_contact_phone` | Required
`tech_contact_email` | Required
`tech_contact_title` | Required
`tech_contact_organisation_name` | Required for OV and EV Certificates
`tech_contact_address_line1` | Required for OV and EV Certificates
`tech_contact_address_line2` | Required for OV and EV Certificates
`tech_contact_address_city` | Required for OV and EV Certificates
`tech_contact_address_region` | Required for OV and EV Certificates
`tech_contact_address_post_code` | Required for OV and EV Certificates
`tech_contact_address_country` | Required for OV and EV Certificates
`admin_contact_first_name` | Required
`admin_contact_last_name` |  Required
`admin_contact_phone` |  Required
`admin_contact_email` |  Required
`admin_contact_title` |  Required
`admin_contact_organisation_name` | Required for OV and EV Certificates
`admin_contact_address_line1` | Required for OV and EV Certificates
`admin_contact_address_line2` | Optional for OV and EV Certificates
`admin_contact_address_city` | Required for OV and EV Certificates
`admin_contact_address_region` | Required for OV and EV Certificates
`admin_contact_address_post_code` | Required for OV and EV Certificates
`admin_contact_address_country` | Required for OV and EV Certificates
`org_name` | Optional but required if the `csr` isn’t supplied
`org_division` | Optional but required if the `csr` isn’t supplied
`org_address_city` | Optional but required if the `csr` isn’t supplied
`org_address_region` | Optional but required if the `csr` isn’t supplied
`org_address_country` | Optional but required if the `csr` isn’t supplied
`domain_name` | Optional but required if the `csr` isn’t supplied
`csr` |  Optional but required if the `domain_name` isn’t supplied
`renewal` | Optional. | You can set if this order is a renewal `1` or `0`
`competitive_upgrade` | Optional. | If your domain has a certificate from a competitor you can set this to true. `1` or `0`
`dv_auth_method` | Required for DV Orders | Expected values `EMAIL` (default), `FILE` and `DNS`
`hashing_algorithm` | Optional. | Expected values `SHA2-256`(default) and `SHA2-256-FULL-CHAIN`
`san_domains`| Required for MD Orders | Comma seperated list of domains up to the `san_count` limit.

#### SmarterTools Products
The place call for the SmarterTools products works in the same was as for the SSL products above but the information that is required varies slightly. You must first use the `generatetoken` call to generate a token for the order which is then used for fulfilment.

When placing the order you must supply a `smartertools_email`. This is the email address to assign the licence to. If the email address does not have an account with SmarterTools then one will be created and the licence assigned.

Before using these API calls Resellers must login to the web interface at <https://reseller.servertastic.com> and activate the product group by accepting the Terms and Conditions.

**`place` order request**

`https://api2.servertastic.com/order/place?order_token=[Order Token]&smartertools_email=[email address to assign the licence to]`

**`place` order response**

The response details license information for the Bundle or product which can be retrieved at a later date using the review call. In addition to this an account password will be shown if the user is a new SmarterTools customer but left blank if they are one already. This information is not stored by Servertastic for security purposes.

	<?xml version="1.0"?>
	<response>
	  <success>Order placed</success>
	  <reseller_order_id></reseller_order_id> *Used in all future calls
	  <account_password></account_password>
	  <order_licenses>*May be multiple
	          <license>
	                <product_name></product_name>
	                <license_key></license_key>
	        </license>
	  </order_licenses>
	</response>

### `review`
The review call shows the status and all information that is currently available for an order. The call interrogates the Servertastic database that is synced with our providers every five minutes. This may mean that the information presented could be up to five minutes old.

#### SSL Orders

`https://api2.servertastic.com/order/review?order_token=[current order_token]`

**`review` order response**

Depending on the stage of the order, some information may be blank.

	<?xml version="1.0"?>
	<response>
	  <success>Review Order</success>
	  <reseller_order_id></reseller_order_id>
	  <provider_order_id></provider_order_id>
	  <st_product_code></st_product_code>
	  <order_status></order_status>
	  <order_state_further_info></order_state_further_info>
	  <domain_name></domain_name>
	  <san_count></san_count>
	  <additional_sans></additional_sans>
	  <server_count></server_count> *Only shows if relevant to product
	  <invite_url></invite_url>
	  <end_customer_email></end_customer_email>
	  <organisation_info>
	    <name></name>
	    <division></division>
	    <address>
	      <city></city>
	      <region></region>
	      <country></country>
	    </address>
	  </organisation_info>
	  <approver_notified_date></approver_notified_date>
	  <approver_email_address></approver_email_address>
	  <admin_contact>
	    <title></title>
	    <first_name></first_name>
	    <last_name></last_name>
	    <phone></phone>
	    <email></email>
	  </admin_contact>
	  <tech_contact>
	    <title></title>
	    <first_name></first_name>
	    <last_name></last_name>
	    <phone></phone>
	    <email></email>
	  </tech_contact>
	  <modifications>
	    <modification> *Could be multiple
	      <timestamp></timestamp>
	      <event_id></event_id>
	      <event_name></event_name>
	    </modification>
	  </modifications>
	  <certificate></certificate>
	  <reissued_certificates> *shown if multiple certs have been reissued
	        <certificate_info>*may be multiple
	                <hash></hash>
	                <encryption></encryption>
	                <certificate></certificate>
	        </certificate_info>
	  </reissued_certificates>
	  <ca_certs> *shown if CA certs are present
	        <certificate_info>*may be multiple
	                <type></type> *INTERMEDIATE or ROOT
	                <certificate></certificate>
	        </certificate_info>
	  </reissued_certificates>
	<expiry_date></expiry_date>
	</response>
	
#### Possible `order_status` values
`order_status` | Description 
:--|:--
Order Placed | Order has been placed
Awaiting Customer Verification | The invite link has been completed and the domain is awaiting an action by the customer
Awaiting Provider Approval | The certificate is undergoing moderation from the certificate provider
Queued | The order has been queued by the certificate issuer.
Completed | The order has been completed and has been sent to the end user.
Cancelled | The order was cancelled either by the reseller, domain approver or the certificate provider
Roll Back | There was a problem placing the order in the Servertastic systems, the order needs to be placed again.
Servertastic Review | The order has been flagged for review by Servertastic and the points will not be released until the issue has been resolved.

#### SmarterTools Orders

**`review` order request**

`https://api2.servertastic.com/order/review?order_token=[Order Token]`

**`review` order response**

	<?xml version="1.0"?>
	<response>
	  <success>Review Order</success>
	  <reseller_order_id></reseller_order_id>
	  <provider_order_id></provider_order_id>
	  <st_product_code></st_product_code>
	  <order_status></order_status>
	  <order_licenses>
	          <license> *May be multiple
	                <product_name></product_name>
	                <license_key></license_key>
	        </license>
	  </order_licenses>
	 </response>
 
### `cancel`
 
This call cancels an order that is placed by a reseller and reinstates the points used for that order. Orders can only be cancelled prior to approval and up to 30 days after completed. This call cannot be used at all on SmarterTools orders.

**`cancel` request**

`https://api2.servertastic.com/order/cancel?order_token=[Order Token]`

**`cancel` response**

	<?xml version="1.0"?>
	<response>
	  <success>Order cancelled - [order reference]</success>
	</response>
	
### `resendemail`

This call requests for the emails that have been sent to the end customers throughout the order process to be resent.  This call cannot be used at all on SmarterTools orders.

**`resendemail` request**

`https://api2.servertastic.com/order/resendemail?order_token=[Order Token]&email_type=[‘Approver’ | ‘Fulfillment’]`

**`resendemail` response**

	<?xml version="1.0"?>
	<response>
	  <success>Email resent</success>
	  <type>Fulfillment</type>
	  <email_sent_to>
	    <approver_email></approver_email>
	    <admin_contact_email></admin_contact_email>
	    <tech_contact_email></tech_contact_email>
	  </email_sent_to>
	</response>

### `changeapproveremail`

This call is used to change the approveremail that can be used to complete domain validated orders. See [`approverlist`](#approverlist)

`https://api2.servertastic.com/order/changeapproveremail?order_token=[Order Token]&email=[New domain approver email address]`

**`changeapproveremail` response**

	<?xml version="1.0"?>
	<response>
	  <success>Change Approver Email Reset</success>
	</response>
	
### `modifiedorders`

This call returns details of orders that have been modified since a given date. Due to the fact that the Servertastic information is updated every 5 minutes it is possible that we do not possess all information up to the specified time. As a result an extra piece of information is returned, ‘data_accuracy’, which will tell you the timestamp of the last modification in the Servertastic system for the returned data. If you are running this call in a script, please store this information and use it to make any subsequent calls. Please note, the date/time is GMT.

**`modifiedorders` request**

`https://api2.servertastic.com/order/modifiedorders?api_key=[Your API Key]&date=[YYYY-MM-DD 00:00:00 eg 2012-07-19 09:45:45]
`

**`modifiedorders` response**

	<?xml version="1.0"?>
	<response>
	  <success>Modified Orders</success>
	  <orders>
	        <order>*Maybe multiple
	  <reseller_order_id></reseller_order_id>
	  <provider_order_id></provider_order_id>*or
	  <truste_account_id></truste_account_id>
	  <st_product_code></st_product_code>
	  <order_status></order_status>
	  <order_state_further_info></order_state_further_info>
	  <domain_name></domain_name>
	  <san_count></san_count>
	  <additional_sans></additional_sans>
	  <server_count></server_count> *Only shows if relevant to product
	  <invite_url></invite_url>
	  <end_customer_email></end_customer_email>
	  <organisation_info>
	    <name></name>
	    <division></division>
	    <address>
	      <city></city>
	      <region></region>
	      <country></country>
	    </address>
	  </organisation_info>
	  <approver_notified_date></approver_notified_date>
	  <approver_email_address></approver_email_address>
	  <admin_contact>
	    <title></title>
	    <first_name></first_name>
	    <last_name></last_name>
	    <phone></phone>
	    <email></email>
	  </admin_contact>
	  <tech_contact>
	    <title></title>
	    <first_name></first_name>
	    <last_name></last_name>
	    <phone></phone>
	    <email></email>
	  </tech_contact>
	  <modifications>
	    <modification> *Could be multiple
	      <timestamp></timestamp>
	      <event_id></event_id>
	      <event_name></event_name>
	    </modification>
	  </modifications>
	  <certificate></certificate>
	<expiry_date></expiry_date>
	    </order>
	</orders>
	<data_accuracy></data_accuracy>
	</response>
	
### `expiring`

The expiring call allows you to return all orders expiring with a specified time period. If the domain has had the same product purchased for it that expires after the original the record won’t be returned.

#### SSL Orders

**`expiring` order request**

`https://api2.servertastic.com/order/expiring?api_key=[Reseller API key]&days=[Expiring time period. Defaults to 60]
`

**`expiring` order response**

	<?xml version="1.0"?>
	<response>
	  <success>Expiring Orders</success>
	  <orders>
	    <order> *Could be multiple
	       <reseller_order_id></reseller_order_id>
	       <order_status></order_status>
	       <order_date></order_date>
	          <domain_name></domain_name>
	  <order_points></order_points>
	  <expiry_date></expiry_date>
	  <st_product_code></st_product_code>
	     </order>
	   </orders>
	</response>
	
### `approverlist`

The approverlist call allows you to retrieve all acceptable domain approver email addresses for the specified domain name

**`approverlist` order request**

`https://api2.servertastic.com/order/approverlist?domain_name=[The domain name you want approvers for]`

**`approverlist` order response**

	<?xml version="1.0"?>
	<response>
	  <success>ApproverList</success>
	  <approver_email> *Could be multiple
	    <email></email>
	   </approver_email>
	</response>

### `changeauthmethod`

This call allows orders for domain validated products, that have either FILE or DNS authentication set, to change the authentication method back to Email. At this time it is not possible to swtich between FILE and DNS without cancelling and placing a new order.

**`changeauthmethod` request**

`https://api2.servertastic.com/order/changeauthmethod?order_token=[Order Token]`

**`changeauthmethod` response**

	<?xml version="1.0"?>
	<response>
	  <success>DV Auth Method Updated</success>
	</response>

### `pollAuth`

This call allows users to initiate an authentication check for domain validated products, that have either FILE or DNS authentication set. This should be ran after the file has been uploaded or the DNS records created.

**`pollAuth` request**

`https://api2.servertastic.com/order/pollauth?order_token=[Order Token]`

**`pollAuth` response**

	<?xml version="1.0"?>
	<response>
	  <success>Order Polled</success>
	  <status></status>*If FILE Auth
	</response>
	
### `generateToken`

This call allows resellers to generate an order token. The order token is then used to manage all aspects of the order. An API key is required to use this call.

The call requires the `end_customer_email` however we do not send any emails to this address. You need to supply the token to whoever needs it. This is simply for your own internal use in future to see who orders have been assigned to.

**`generateToken` request**

`https://api2.servertastic.com/order/generatetoken?api_key=[Your API Key]&st_product_code=[Product Code]&reseller_unique_reference=[Unique order reference supplied by the reseller - 35 chars maximum]&end_customer_email=[This can be any valid email address]`

**`generateToken` response**

	<?xml version="1.0"?>
	<response>
	  <success>Order token generated</success>
	  <order_token></order_token>
	  <product_code></product_code>
	</response>
	
### `showTokens`

This call allows resellers to keep track of tokens that have been generated and which ones have been used.

**`showTokens` request**

`https://api2.servertastic.com/order/showtokens?api_key=[Your API Key]`

**`showTokens` response**

	<?xml version="1.0"?>
	<response>
	  <success>Order tokens</success>
	  <unused_tokens>
	        <order_token> *could be multiple
	                <token></token>
	                <reseller_unique_reference></reseller_unique_reference>
	        </order_token
	  </unused_tokens>
	  <used_tokens>
	        <order_token> *could be multiple
	                <token></token>
	                <reseller_unique_reference></reseller_unique_reference>
	        </order_token
	  </unused_tokens>
	</response>
	
### `advanceorder`

This call is only valid on test orders. The order maybe queued for review within the system. Using this API call you can advance an order onto the next stage.

**`advanceorder` request**

`https://api2.servertastic.com/order/advanceorder?order_token=[Order Token]`

**`advanceorder` response**

	<?xml version="1.0"?>
	<response>
		<success>
		Order was pushed. Please wait up to 3 minutes for the status to update - STR_1_201611071123
		</success>
	</response>

## Account Web Service Details

### `regenerateapikey`

This call allows a reseller to regenerate their API key.

**`regenerateapikey` request**

`https://api2.servertastic.com/account/regenerateapikey?api_key=[Your API Key]`

**`regenerateapikey` response**

	<?xml version="1.0"?>
	<response>
	  <success>API Key reset</success>
	  <api_key></api_key>
	</response>
	
### `review`<a name="reselleraccount-review"></a>

This call returns information about the reseller associated with the provided API key, including the number of points that are available to spend and the details of each order that is currently in the Servertastic system that is not Complete or Cancelled.

**`review` request**

`https://api2.servertastic.com/account/review?api_key=[Your API Key]`

**`review` response**

	<?xml version="1.0"?>
	<response>
	  <success>Reseller Overview</success>
	  <purchased_points></purchased_points>
	  <active_orders>
	    <active_order> *Could be multiple
	       <reseller_order_id></reseller_order_id>
	       <order_status></order_status>
	       <order_date></order_date>
	          <domain_name></domain_name>
	  <order_points></order_points>
	  <expiry_date></expiry_date>
	  <st_product_code></st_product_code>
	     </active_order>
	   </active_orders>
	 <completed_orders>
	    <completed_order> *Could be multiple
	       <reseller_order_id></reseller_order_id>
	       <order_status></order_status>
	       <order_date></order_date>
	  <domain_name></domain_name>
	  <order_points></order_points>
	  <expiry_date></expiry_date>
	  <st_product_code></st_product_code>
	     </completed_order>
	   </completed_orders>
	</response>
	
### `pointsaudit`<a name="pointsaudit"></a>

This call returns details of all transactions that have affected the account points total. This includes Placed orders, Cancelled orders, Points Deposit, and adjustments by Servertastic.

**`pointsaudit` request**

`https://api2.servertastic.com/account/pointsaudit?api_key=[Your API Key]`

**`pointsaudit` response**

	<?xml version="1.0"?>
	<response>
		<success>PointsAudit</success>
		<record>
			<Timestamp></Timestamp>
			<PointsAdjustment></PointsAdjustment>
			<NewPointsTotal></NewPointsTotal>
			<Description></Description>
			<OrderDate/>
			<OrderReference/>
			<OrderToken/>
			<Notes></Notes>
		</record>
	</response>
	
### `addtestpoints`

This call allows resellers to add points to their account in order to aid testing. This call will only work within the test environment.

**`addtestpoints` request**

`https://test-api2.servertastic.com/account/addtestpoints?api_key=[Your API Key]&points=[The number of points you would like to add]
`

**`addtestpoints` response**

	<?xml version="1.0"?>
	<response>
	  <success>Points total updated</success>
	  <new_total></new_total>
	</response>
	
### `emptytestpoints`

This call allows resellers to set their point level to 0 for their account in order to aid testing. This call will only work within the test environment.

**`emptytestpoints` request**

`https://test-api2.servertastic.com/account/emptytestpoints?api_key=[Your API Key]`

**`emptytestpoints` response**

	<?xml version="1.0"?>
	<response>
	  <success>Points total updated</success>
	  <new_total>0</new_total>
	</response>
	
## Product Web Service Details

### `listproduct`

This call returns all the products available through the reseller system, along with associated information such as price and SAN count. 

**`listproduct` request**

`https://api2.servertastic.com/product/listproducts?api_key=[Your API Key]&product_group=[‘SSL’, ‘SmarterTools’ or ‘All’]
`

**`listproduct` response**

	<?xml version="1.0"?>
	<response>
	  <success>Product list</success>
	   <products>
	   <st_product_code></st_product_code>
	   <product_name></product_name>
	   <html_description></html_description>
	   <max_server_count></max_server_count>
	   <min_san_count></min_san_count>
	   <max_san_count></max_san_count>
	   <base_san_points></base_san_points>
	   <product_points></product_points>
	  </products>
	</response>
	
## Further Field Definitions

### RapidSSL

Product Code | Product Description | Server Count Values | SAN Values | Type
:--|:--|:--|:--|:--
`RapidSSL-12` | RapidSSL - 1 Years | Not Applicable | Not Applicable | DV
`RapidSSL-24` | RapidSSL - 2 Years | Not Applicable | Not Applicable | DV
`RapidSSL-36` | RapidSSL - 3 Years | Not Applicable | Not Applicable | DV
`RapidSSLWildcard-12` | RapidSSL Wildcard - 1 Years | Not Applicable | Not Applicable | DV
`RapidSSLWildcard-24` | RapidSSL Wildcard - 2 Years | Not Applicable | Not Applicable | DV
`RapidSSLWildcard-36` | RapidSSL Wildcard - 3 Years | Not Applicable | Not Applicable | DV

### Geotrust

Product Code | Product Description | Server Count Values | SAN Values | Type
:--|:--|:--|:--|:--
`QuickSSLPremium-12` | QuickSSL Premium - 1 Years | Not Applicable | Not Applicable | DV
`QuickSSLPremium-24` | QuickSSL Premium - 2 Years | Not Applicable | Not Applicable | DV
`QuickSSLPremium-36` | QuickSSL Premium - 3 Years | Not Applicable | Not Applicable | DV
`QuickSSLPremiumMD-12` | QuickSSL Premium Multi Domain - 1 Years | Not Applicable | 4-4 | DV
`QuickSSLPremiumMD-24` | QuickSSL Premium Multi Domain - 2 Years | Not Applicable | 4-4 | DV
`QuickSSLPremiumMD-36` | QuickSSL Premium Multi Domain - 3 Years | Not Applicable | 4-4 | DV
`TrueBizID-12`| True BusinessID - 1 Years | Not Applicable | Not Applicable | OV
`TrueBizID-24` | True BusinessID - 2 Years | Not Applicable | Not Applicable | OV
`TrueBizID-36` | True BusinessID - 3 Years | Not Applicable | Not Applicable | OV
`TrueBizIDWildcard-12` | True BusinessID Wildcard - 1 Years | Not Applicable | Not Applicable | OV
`TrueBizIDWildcard-24` | True BusinessID Wildcard - 2 Years | Not Applicable | Not Applicable | OV
`TrueBizIDWildcard-36` | True BusinessID Wildcard - 3 Years | Not Applicable | Not Applicable | OV
`TrueBizIDEV-12` | True BusinessID with EV - 1 Years | Not Applicable | Not Applicable | EV
`TrueBizIDEV-24` | True BusinessID with EV - 2 Years | Not Applicable | Not Applicable | EV
`TrueBizIDMD-12` | True Business ID with SAN - 1 Years | Not Applicable | 4-24 | OV
`TrueBizIDMD-24` | True Business ID with SAN - 2 Years | Not Applicable | 4-24 | OV
`TrueBizIDMD-36` | True Business ID with SAN - 3 Years | Not Applicable | 4-24 | OV
`TrueBizIDEVMD-12` | True BusinessID with EV with SAN - 1 Years | Not Applicable | 4-24 | EV
`TrueBizIDEVMD-24` | True BusinessID with EV with SAN - 2 Years | Not Applicable | 4-24 | EV
`AntiMalwareBasic-12` | Geotrust Anti-Malware Scan Basic - 1 Years | Not Applicable | Not Applicable | NA
`AntiMalwareBasic-24` | Geotrust Anti-Malware Scan Basic - 2 Years | Not Applicable | Not Applicable | NA
`AntiMalwareBasic-36` | Geotrust Anti-Malware Scan Basic - 3 Years | Not Applicable | Not Applicable | NA
`AntiMalware-12` | Geotrust Anti-Malware Scan - 1 Years | Not Applicable | Not Applicable | NA
`AntiMalware-24` | Geotrust Anti-Malware Scan - 2 Years | Not Applicable | Not Applicable | NA
`AntiMalware-36` | Geotrust Anti-Malware Scan - 3 Years | Not Applicable | Not Applicable | NA
- When purchasing a TrueBizID Multidomain product the price includes the first four SANs.
- When purchasing a QuickSSL Premium Multi Domain the price includes 4 SANs.

### Symantec

Product Code | Product Description | Server Count Values | SAN Values | Type
:--|:--|:--|:--|:--
`SecureSite-12` | Secure Site - 1 Years | 1 - 500 | 0-24 | OV
`SecureSite-24` | Secure Site - 2 Years | 1 - 500 | 0-24 | OV
`SecureSite-36` | Secure Site - 3 Years | 1 - 500 | 0-24 | OV
`SecureSiteEV-12` | Secure Site with EV - 1 Years | 1 - 500 | 0-24 | EV
`SecureSiteEV-24` | Secure Site with EV - 2 Years | 1 - 500 | 0-24 | EV
`SecureSitePro-12` | Secure Site Pro - 1 Years | 1 - 500 | 0-24 | OV
`SecureSitePro-24` | Secure Site Pro - 2 Years | 1 - 500 | 0-24 | OV
`SecureSitePro-36` | Secure Site Pro - 3 Years | 1 - 500 | 0-24 | OV
`SecureSiteProEV-12` | Secure Site Pro with EV - 1 Years | 1 - 500 | 0-24 | EV
`SecureSiteProEV-24` | Secure Site Pro with EV - 2 Years | 1 - 500 | 0-24 | EV
`SymantecSafeSite-12` | Symantec Safe Site - 1 Years | Not Applicable | Not Applicable | NA
`SymantecSafeSite-24` | Symantec Safe Site - 2 Years | Not Applicable | Not Applicable | NA
`SymantecSafeSite-36` | Symantec Safe Site - 3 Years | Not Applicable | Not Applicable | NA

### Thawte

Product Code | Product Description | Server Count Values | SAN Values | Type
:--|:--|:--|:--|:--
`SSLWebServer-12` | SSL Web Server - 1 Years | Not Applicable | 0-24 | OV
`SSLWebServer-24` | SSL Web Server - 2 Years | Not Applicable | 0-24 | OV
`SSLWebServer-36` | SSL Web Server - 3 Years | Not Applicable | 0-24 | OV
`SSLWebServerWildcard-12` | SSL Web Server Wildcard - 1 Years | Not Applicable | Not Applicable | OV
`SSLWebServerWildcard-24` | SSL Web Server Wildcard - 2 Years | Not Applicable | Not Applicable | OV
`SSLWebServerEV-12` | SSL Web Server with EV - 1 Years | Not Applicable | 0-24 | EV
`SSLWebServerEV-24` | SSL Web Server with EV - 2 Years | Not Applicable | 0-24 | EV
`SSL123-12` | SSL123 - 1 Years | Not Applicable | Not Applicable | DV
`SSL123-24` | SSL123 - 2 Years | Not Applicable | Not Applicable | DV
`SSL123-36` | SSL123 - 3 Years | Not Applicable | Not Applicable | DV

### SmarterTools Bundle

Product Code | Product Description
:--|:--
`SmarterBundle` | SmarterTools Bundle Pack


### SmarterTools SmarterMail

Product Code | Product Description
:--|:--
`SmarterMailPro-250` | SmarterMail Pro - 250 Mailboxes
`SmarterMailPro-500` | SmarterMail Pro - 500 Mailboxes
`SmarterMailPro-1000` | SmarterMail Pro - 1000 Mailboxes
`SmarterMailPro-2000` | SmarterMail Pro - 2000 Mailboxes
`SmarterMailPro-Unlimited` | SmarterMail Pro - Unlimited Mailboxes
`SmarterMailEnt-250` | SmarterMail Ent - 250 Mailboxes
`SmarterMailEnt-500` | SmarterMail Ent - 500 Mailboxes
`SmarterMailEnt-1000` | SmarterMail Ent - 1000 Mailboxes
`SmarterMailEnt-2000` | SmarterMail Ent - 2000 Mailboxes
`SmarterMailEnt-Unlimited` | SmarterMail Ent - Unlimited Mailboxes

### SmarterTools SmarterStats

Product Code | Product Description
:--|:--
`SmarterStatsPro-50` | SmarterStats Pro - 50 Sites
`SmarterStatsPro-250` | SmarterStats Pro -250 Sites
`SmarterStatsPro-1000` | SmarterStats Pro - 1000 Sites
`SmarterStatsPro-2500` | SmarterStats Pro - 2500 Sites
`SmarterStatsEnt-50` | SmarterStats Ent - 50 Sites
`SmarterStatsEnt-250` | SmarterStats Ent -250 Sites
`SmarterStatsEnt-1000` | SmarterStats Ent - 1000 Sites
`SmarterStatsEnt-2500` | SmarterStats Ent - 2500 Sites
`SmarterStatsEnt-5000` | SmarterStats Ent - 5000 Sites
`SmarterStatsEnt-10000` | SmarterStats Ent - 10000 Sites
`SmarterStatsEnt-20000` | SmarterStats Ent - 20000 Sites
`SmarterStatsEnt30000` | SmarterStats Ent - 300000 Sites

### SmarterTools SmarterTrack

Product Code | Product Description
:--|:--
`SmarterTrackPro-2` | SmarterTrack Pro - 2 Agents
`SmarterTrackPro-5` | SmarterTrack Pro - 5 Agents
`SmarterTrackPro-10` | SmarterTrack Pro - 10 Agents
`SmarterTrackEnt-2` | SmarterTrack Ent - 2 Agents
`SmarterTrackEnt-5` | SmarterTrack Ent - 5 Agents
`SmarterTrackEnt-10` | SmarterTrack Ent - 10 Agents
`SmarterTrackEnt-15` | SmarterTrack Ent - 15 Agents
`SmarterTrackEnt-20` | SmarterTrack Ent - 20 Agents
`SmarterTrackEnt-25` | SmarterTrack Ent - 25 Agents
`SmarterTrackEnt-50` | SmarterTrack Ent - 50 Agents
`SmarterTrackEnt-100` | SmarterTrack Ent - 100 Agents
`SmarterTrackEnt-200` | SmarterTrack Ent - 200 Agents
`SmarterTrackEnt-300` | SmarterTrack Ent - 300 Agents
`SmarterTrackEnt-400` | SmarterTrack Ent - 400 Agents
`SmarterTrackEnt-500` | SmarterTrack Ent - 500 Agents
`SmarterTrackEnt-600` | SmarterTrack Ent - 600 Agents
`SmarterTrackEnt-700` | SmarterTrack Ent - 700 Agents
`SmarterTrackEnt-800` | SmarterTrack Ent - 800 Agents
`SmarterTrackEnt-900` | SmarterTrack Ent - 900 Agents
`SmarterTrackEnt-1000` | SmarterTrack Ent - 1000 Agents

### SmarterTools Support

Product Code | Product Description
:--|:--
`SmarterToolsSingleEmailIncident` | SmarterTools Single Email Incident
`SmarterToolsSinglePhoneIncident` | SmarterTools Single Phone Incident
`SmarterToolsSingleCriticalIncident` | SmarterTools Single Critical Incident
`SmarterToolsSilverPackage` | SmarterTools Silver Package - 6 Email Incidents
`SmarterToolsGoldPackage` | SmarterTools Gold Package - 6 Email or Phone Incidents
`SmarterToolsPlatinumPackage` | SmarterTools Platinum Package - 6 Email or Phone Incidents - 2 (24x7) Phone Incidents
`SmarterToolsInstallationSupport` | SmarterTools Installation Supporta