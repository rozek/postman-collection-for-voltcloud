# postman-collection-for-voltcloud #

a Postman collection for VoltCloud.io (aka VoltServer)

This repository contains a [Postman](https://www.postman.com/) collection of REST requests against [VoltCloud.io](https://voltcloud.io/).

VoltCloud is a fairly cheap deployment server for web applications which also integrates basic user management and a simple key-value store. While originally been built for [NSB/AppStudio](https://www.nsbasic.com/), its REST interface allows it to be used from any platform which is able to handle REST requests (including cURL and Postman)

The contents of this Postman collection is based on [available documentation](https://docs.voltcloud.io/api/), answers to questions in the [developer forum](https://discuss.appstudio.dev/) ([here](https://discuss.appstudio.dev/t/deploying-to-volt-using-the-cli/742) and [there](https://discuss.appstudio.dev/t/how-to-get-a-list-of-registered-users/3136)) and my own research using the VoltCloud dashboard and my browser's developer tools (primarily its network log)

Just a small note: if you like this collection and plan to use it, consider "starring" this repository (you will find the "Star" button on the top right of this page), such that I know which of my repositories to take most care of.

## Prerequisites ##

If you plan to use this collection, you should

* have or create a [Postman Account](https://identity.getpostman.com/signup) (the [free plan](https://www.postman.com/pricing/) is sufficient),
* download, install and use the [Postman desktop application](https://www.postman.com/downloads/) for your operating system, and
* have or create a VoltCloud/VoltServer account (for historical reasons, this is bound to an [AppStudio subscription](https://www.nsbasic.com/i/Subscription/) - but don't worry: just think of getting two products for the price of one and simply ignore the other one).<br>&nbsp;<br>A free plan does not exist, but you may instead
  * download the [demo version of NSB/AppStudio](https://www.nsbasic.com/app/downloads/) and [create a VoltCloud account](https://wiki.appstudio.dev/Volt_for_appstudio) for it or
  * subscribe to NSB/AppStudio (do *not* choose the "one time" plan if you intend to use VoltCloud only) and use their "[30 day no hassle money back](https://wiki.appstudio.dev/NSB/App_Studio_FAQ#The_demo_period_has_expired.2C_but_I.27m_not_done_testing.)" guarantee if you decide not to use VoltCloud

If you plan to use VoltCloud for an application with customers, it may also be worth reading their [legal notes](https://www.nsbasic.com/app/legal/legal.php).

## Getting Started ##

Assuming that Postman is already up and running, simply import the [collection](https://raw.githubusercontent.com/rozek/postman-collection-for-voltcloud/main/VoltCloud%20Collection.json) from this repository by inserting the URL

&nbsp; `https://raw.githubusercontent.com/rozek/postman-collection-for-voltcloud/main/VoltCloud%20Collection.json`

into tab "Link" within the "Import" dialog. Postman will validate the file behind the given URL and want you to select the contained collection before it is actually imported.

One of the requests from the imported collection wants to upload a file to the VoltCloud server. In order to prepare this file, define a "working directory" in Postman by navigating to "Settings" > "General" and "Choose" the desired folder in section "Working directory". Next, download file "Postman-Dummy.zip" found at

&nbsp; [https://github.com/rozek/postman-collection-for-voltcloud/blob/main/Postman-Dummy.zip?raw=true](https://github.com/rozek/postman-collection-for-voltcloud/blob/main/Postman-Dummy.zip?raw=true)

into that directory.

Finally, select the imported collection and navigate to tab "Variables". The variables listed are those used by the various requests - many of them will be filled automatically upon receiving responses from the VoltCloud server, but two of them have to be explicitly set in order to get started

* **developer_email_address**<br>set its `initial value` or `current value` (or both) to the email address you use to log into the VoltServer
* **developer_password**<br>set its `current value` to your VoltServer password (note: set the `current value` only in order to avoid your password to accidentially leak to the Postman server or into any export file)

If you want to experiment with customer management, you should also configure a new customer:

* **customer_email_address**<br>set its `initial value` or `current value` (or both) to the email address of your new customer
* **customer_password**<br>set its `current value` to the intended password of your new customer. Because of restrictions implied by VoltCloud, the password must consist of 8 or more characters with at least one digit, one lower case letter and one upper case letter in order to be accepted
* **customer_confirmation**<br>VoltCloud uses this setting to detect accidental typing errors in the password. Normally, you should set the `current value` of this variable to the same value as `customer_password` - unless you want to test the behaviour of VoltCloud in case of  mismatches

**Nota bene**: do not forget to "Save" your entries afterwards or they will not be considered!

## Requests ##

The requests found in this collection are grouped into those used by an application developer (who is also allowed to manage his/her customers and their key-value stores) and those used by a customer of a given application (which normally means that the application performs any requests on behalf of the - previously logged-in - customer)

In principle, you may submit the listed requests one after another - usually, former requests collect any information needed for later ones.

### Requests for Developers ###

Many of the operations described below may be performed using the VoltCloud dashboard. From time to time, however, it may be useful to bypass the dashboard (e.g., for automated application deployment outside of AppStudio) or to implement a server that needs access to the key-value store associated with an application and its customers

#### Login ####

* **login developer**<br>just submit this request in order to authenticate yourself with the configured `developer_email_address` and `developer_password`. If successful, Postman will extract your `developer_id` and the `developer_access_token` needed to run all the other requests provided for developers

#### Application Management ####

* **list applications**<br>submit this request to get a (potentially empty) list of your currently registered applications together with their settings (shown in the response pane for this request). If any of these applications has a name (aka "subdomain") starting with "postman-", the URL and id of that application will be saved as `application_url` and `application_id` for the following requests. If you do not have such an application, simply create one as shown below
* **create new application**<br>this request registers a new application (and saves its URL and its id in `application_url` and `application_id` for further requests). Initially, the newly create application will have an arbitrary name (and URL) - for that reason, you should probably "update" it next
* **update application**<br>this request changes one or multiple settings for an application given by its id. In this example, the request simply changes the application's name to something beginning with "postman-" and ending with a series of digits (denoting the current time stamp). If successful, the new URL of the application is saved in `application_url` for further requests (the `application_id` remains unchanged)<br>&nbsp;<br>In addition, the request will define (dummy) URLs for customer confirmation and password reset emails - as a consequence, *be warned that the registration of a new customer or a change of his/her password will send an email to the configured address* (thus, **do not use the email addresses of other people!**)
* **upload application**<br>after creation, an application is still "empty", i.e., it contains no code. This request allows a developer to upload a ZIP archive containing all files that make up the actual application - including an "index.html" needed as the application's entry point. In this example, the ZIP archive just contains a simple dummy [HTML file](https://github.com/rozek/postman-collection-for-voltcloud/blob/main/Postman-Dummy.zip?raw=true), which is uploaded to the application given by `application_id`
* **inspect application**<br>submit this request in order to get a summary of the settings for the application given by `application_id`
* **delete application**<br>this request unregisters the application given by `application_id`, but only if no longer associated with any customers (otherwise you will have to delete all those customers first). At the same time, any entries of the associated key-value store will also be removed. Note: this request is *not idempotent* - trying to delete a non-existing application will result in an error

#### Application Storage Management ####

Every application is associated with a key-value store, whose entries may be created, read, written and deleted by the developer but only read by the application's customers. The following requests are those for the developer:

* **list application storage entries**<br>this request responds with a (potentially empty) JSON object containing all entries in the key-value store associated with the application given by `application_id` (this includes any keys and their bound values). If the JSON object is not empty, an arbitrary key from the set will be stored in `application_storage_key` for further requests - otherwise, `application_storage_key` will be set to "application-test"
* **create or update application storage entry**<br>this request addresses the key-value store for the application given by `application_id` and binds the key given by `application_storage_key` to a predefined value (derived from the current timestamp) - if the specfied entry does not exist, it will be created 
* **get application storage entry**<br>this request addresses the key-value store for the application given by `application_id` and responds with a JSON string containing the value the key given by `application_storage_key` is bound to. Trying to get the value of a non-existing key will result in an error
* **delete application storage entry**<br>this request addresses the key-value store for the application given by `application_id` and deletes the entry for the key given by `application_storage_key` together with the value that key is bound to. Note: this request is *not idempotent* - trying to delete a non-existing entry will result in an error

#### Customer Management ####

A developer may associate an arbitrary number of users (this collection calls them "customers") with any registered application. These customers may register and login using their email address and a password. Internally, they are identified using an *id* which uniquely represents the combination of a customer and the application he/she registered for. As a consequence, the same email address may be used for multiple applications resulting in as many different user ids - even if the actual person behind these users is always the same.

* **list customers**<br>use this request to get a (potentially empty) list of all currently registered customers (including their settings) for the application given by `application_id`
* **register customer**<br>this request registers a new customer with an email address given by `customer_email_address`, a password given by `customer_password` and its confirmation given by `customer_confirmation` for the application given by `application_id`. The request will fail, if the specified customer already exists. If successful, the id of the newly registered customer will be stored in `customer_id` for further requests<br>&nbsp;<br>**If configured, customer registration wil also send a "confirmation email" to the new customer's email address**. Its sender will be called "**Volt hosted App**", the topic "**Account Confirmation**" and the actual message will contain a link to your application which, when clicked, should allow your customer to confirm the given email address.<br>&nbsp;<br>For that reason, the suffix configured in the dashboard for your application under "Confirm Page" will be added to the application's base URL (ignore the prefix "<span>https</span><span>://dashboard.voltcloud.io</span>" shown there - it's just misleading), the placeholder `{{token}}` replaced by an actual confirmation token and the application invoked with the resulting URL. It will be your duty to extract the token from this URL again and to submit the "confirm customer" request shown below in order to actually confirm the customer's email address.<br>&nbsp;<br>For this collection, just copy the token into the `current value` of variable `customer_confirmation_token` manually
* **resend customer confirmation email**<br>this request submits another confirmation email to the address given by `customer_email_address` for a yet unconfirmed customer of the application given by `application_id`
* **confirm customer**<br>this request can be used to confirm the email address of a customer by providing the token that came with the confirmation email (entered into variable `customer_confirmation_token`)
* **start customer password reset**<br>this requests sends an email to the address given by `customer_email_address` for a customer of the application given by `application_id` which allows that customer to define a new password<br>&nbsp;<br>The sender will be called "**Volt hosted App**", the topic "**Password Reset**" and the actual message will contain a link to your application which, when clicked, should perform the password reset.<br>&nbsp;<br>For that reason, the configured suffix will be added to the application's base URL (ignore the prefix "<span>https</span><span>://dashboard.voltcloud.io</span>" shown there - it's just misleading), the placeholder `{{token}}` replaced by an actual password reset token and the application invoked with the resulting URL. It will be your duty to extract the token from this URL again, let the user provide a new password (and its confirmation) and to submit the "reset customer password" request shown below in order to actually set the new password.<br>&nbsp;<br>For this collection, just copy the token into the `current value` of variable `customer_reset_token` manually
* **reset customer password**<br>this request can be used to reset the password of a customer by providing the token that came with the password reset email (entered into variable `customer_reset_token`), a new password (given by `customer_password`) and its conformation (given by `customer_confirmation`)
* **delete customer**<br>this request allows an application developer to explicitly unregister a customer given by its `customer_id` (since VoltCloud user ids are globally unique, the affected application does not have to be explicitly specified). Deleting a customer will also remove the associated key-value store

#### Customer Storage Management ####

Every registered customer has his/her own key-value store associated with the application he/she registered for. The following requests allow the application developer to manage these stores and their entries.

Nota bene: yes, as a developer you have insight into every customer's storage entries. With regard to data privacy (and to calm your customers), you may therefore consider encrypting any storage keys and entries within your application!

* **list customer storage entries**<br>this request responds with a (potentially empty) JSON object containing all entries in the key-value store of the customer given by `customer_id` (this includes any keys and their bound values). Since `customer_id` is the id of a user who signed up for your application, the application itself does not have to be specified explicitly. If the received JSON object is not empty, an arbitrary key from the set will be stored in `customer_storage_key` for further requests - otherwise, `customer_storage_key` will be set to "customer-test"
* **create or update customer storage entry**<br>this request addresses the key-value store for the customer given by `customer_id` and binds the key given by `customer_storage_key` to a predefined value (derived from the current timestamp) - if the specfied entry does not exist, it will be created
* **get customer storage entry**<br>this request addresses the key-value store for the customer given by `customer_id` and responds with a JSON string containing the value the key given by `customer_storage_key` is bound to. Trying to get the value of a non-existing key will result in an error
* **delete customer storage entry**<br>this request addresses the key-value store for the customer given by `customer_id` and deletes the entry for the key given by `customer_storage_key` together with the value that key is bound to. Note: this request is *not idempotent* - trying to delete a non-existing entry will result in an error

### Requests for Customers ###

The following requests will normally be submitted by an application on behalf of its user.

### Account Setup ###

In order to sign up for an (application-specific) VoltCloud account, a customer should register, may ask for resending the confirmation email and should confirm his/her registration. In addition, he/she may ask for a password reset and perform that reset after having received the related email.

The requests needed for these operations are the same as those mentioned for the developer, since no authentication is needed for them.

* **register customer** - see "Customer Management" for Developers
* **resend customer confirmation email** - see "Customer Management" for Developers
* **confirm customer** - see "Customer Management" for Developers
* **start customer password reset** - see "Customer Management" for Developers
* **reset customer password** - see "Customer Management" for Developers

#### Login ####

For Customer Account and Storage Management to work, the customer has to log-in to the VoltCloud server using the email address and password configured for his/her account (the email address must have been confirmed before or the login will fail). As a result, the application will receive an "access token" which will have to be sent back to the server as part of any further request in order to authenticate it.

* **login customer**<br>submit this request in order to authenticate the customer with the configured `customer_email_address` and `customer_password` who registered for an application with the id `application_id` - the email address must have been confirmed before or the login will fail. If successful, Postman will extract the `customer_id` and the `customer_access_token` needed to run any requests listed under "Account Management" or "Application/Customer Storage Management"

#### Account Management ####

The following requests allow customers to inspect their own settings and change them. For those requests to work, the customer must have logged-in before and received a "token" to authenticate any request (in this collection, that token is taken from variable `customer_access_token`)

* **get customer settings**<br>this request responds with a JSON object containing any current settings (except password) for the customer given by `customer_id`
* **change customer email address**<br>this request allows a customer given by `customer_id` to change his/her email address to a new value given by `customer_email_address`. The new email address must not have been registered before or the request will fail. Surprisingly, *the new email address does not have to be confirmed and no confirmation mail will be sent to the new address*
* **change customer password**<br>this request changes the current password (given by `customer_password`) of a customer given by `customer_id` to a new value (given by `customer_new_password` and confirmed by `customer_confirmation`). The new password must adhere to the VoltCloud password rules, but both old and new password may be identical
* **change customer name**<br>this request may be used to define the actual name of a customer given by `customer_id` (in this collection, the new values have been hard-coded into the request body)
* **delete customer**<br>this request allows a customer given by `customer_id` to unregister him/herself. Note: this request is *not idempotent* - trying to delete a non-existing customer will result in an error

#### Application Storage Management ####

Every registered customer has read-only access to the key-value store the application he/she registered for is associated with. An application developer may use this store to publish some data to all users of the application.

* **list application storage entries**<br>this request responds with a (potentially empty) JSON object containing all entries in the key-value store associated with the application given by `application_id` (this includes any keys and their bound values). If the JSON object is not empty, an arbitrary key from the set will be stored in `application_storage_key` for further requests - otherwise, `application_storage_key` will be set to "application-test"
* **get application storage entry**<br>this request addresses the key-value store for the application given by `application_id` and responds with a JSON string containing the value the key given by `application_storage_key` is bound to. Trying to get the value of a non-existing key will result in an error

#### Customer Storage Management ####

Every registered customer has his/her own key-value store associated with the application he/she registered for. The following requests allow the customer to manage his/her store and its entries.

* **list customer storage entries**<br>this request responds with a (potentially empty) JSON object containing all entries in the key-value store of the customer given by `customer_id` (this includes any keys and their bound values). If the received JSON object is not empty, an arbitrary key from the set will be stored in `customer_storage_key` for further requests - otherwise, `customer_storage_key` will be set to "customer-test"
* **create or update customer storage entry**<br>this request addresses the key-value store for the customer given by `customer_id` and binds the key given by `customer_storage_key` to a predefined value (derived from the current timestamp) - if the specfied entry does not exist, it will be created
* **get customer storage entry**<br>this request addresses the key-value store for the customer given by `customer_id` and responds with a JSON string containing the value the key given by `customer_storage_key` is bound to. Trying to get the value of a non-existing key will result in an error
* **delete customer storage entry**<br>this request addresses the key-value store for the customer given by `customer_id` and deletes the entry for the key given by `customer_storage_key` together with the value that key is bound to. Note: this request is *not idempotent* - trying to delete a non-existing entry will result in an error

## License ##

[MIT License](LICENSE.md)
