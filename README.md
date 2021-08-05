# postman-collection-for-voltcloud #

a Postman collection for VoltCloud.io (aka VoltServer)

This repository contains a [Postman](https://www.postman.com/) collection of REST requests against [VoltCloud.io](https://voltcloud.io/).

VoltCloud is a fairly cheap deployment server for web applications which also integrates basic user management and a simple key-value store. While originally been built for [NSB/AppStudio](https://www.nsbasic.com/), its REST interface allows it to be used from any platform which is able to handle REST requests (including cURL and Postman)

The contents of this Postman collection is based on [available documentation](https://docs.voltcloud.io/api/), answers to questions in the [developer forum](https://discuss.appstudio.dev/) ([here](https://discuss.appstudio.dev/t/deploying-to-volt-using-the-cli/742) and [there](https://discuss.appstudio.dev/t/how-to-get-a-list-of-registered-users/3136)) and my own research using the VoltCloud dashboard and my browser's developer tools (primarily its network log)

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

**Nota bene**: do not forget to "Save" your entries afterwards or they will not be considered!

## Requests ##

The requests found in this collection are grouped into those used by an application developer (who is also allowed to manage his/her customers and their key-value stores) and those used by a customer of a given application (which normally means that the application performs any requests on behalf of the - previously logged-in - customer)

In principle, you may submit the listed requests one after another - usually, former requests collect any information needed for later ones.

### Requests for Developers ###

#### Login ####

* **login developer**<br>just submit this request in order to authenticate yourself with the configured `developer_email_address` and `developer_password`. If successful, Postman will extract the `developer_access_token` needed to run all the other requests provided for developers

#### Application Management ####

* **list applications**<br>submit this request to get a (potentially empty) list of your currently registered applications together with their settings (shown in the response pane for this request). If any of these applications has a name (aka "subdomain") starting with "postman-", the URL and id of that application will be saved for the following requests. If you do not have such an application, simply create one as shown below
* **create new application**<br>this request registers a new application (and saves its id for further requests). Initially, the newly create application will have an arbitrary name (and URL) - for that reason, you should probably "update" it next
* **update application**<br>this request changes one or multiple settings for an application given by its id. In this example, the request simply changes the application's name to something beginning with "postman-" and ending with a series of digits (denoting the crrent time stamp)
* **upload application**<br>after creation, an application is still "empty", i.e., it contains no code. This request allows a developer to upload a ZIP archive containing all files that make up the actual application - including an `index.html` needed as the application's entry point. In this example, the ZIP archive just contains a simple dummy HTML file
* **inspect application**<br>submit this request in order to get a summary of the settings for a single registered application, given by its id
* **delete application**<br>this request unregisters a still registered application given by its id, but only if there are no more customers associated with this application (otherwise you will have to delete every customer first). At the same time, any entries of the associated key-value store will also be removed. Note: this request is *not idempotent* - trying to delete a non-existing application will result in an error

#### Application Storage Management ####

Every application is associated with a key-value store, whose entries may be created, read, written and deleted by the developer but only read by the application's customers. The following requests are those for the developer:

* **list application storage entries**<br>this request responds with a (potentially empty) JSON object containing all keys of the application store and their associated values
* **create or update application storage entry**<br>this request associates a given value (a string) with a given key in the application's key-value store - if the specfied entry does not exist, it will be created 
* **get application storage entry**<br>this request responds with a JSON string containing the value of a specific entry in the application's key-value store (given by its key). Trying to get the value of a non-existing key will result in an error
* **delete application storage entry**<br>this requets deletes an entry (given by its key) from the application's key-value store. Note: this request is *not idempotent* - trying to delete a non-existing entry will result in an error

#### Customer Management ####

A developer may associate an arbitrary number of users (aka "customers") with any registered application. These customers may register and login using their email address and a password. Internally, they are identified using an *id* which uniquely represents the combination of a customer and the application he/she registered for. As a consequence, the same email address may be used for multiple applications resulting in as many different user ids - even if the actual user is always the same

* **list customers**<br>
* **register customer**<br>
* **resend customer confirmation email**<br>
* **confirm customer**<br>
* **start customer password reset**<br>
* **reset customer password**<br>
* **delete customer**<br>

#### Customer Storage Management ####

Nota bene: yes, as a developer you have insight into every customer's storage entries. With regard to data privacy, you may therefore consider encrypting any storage keys and entries within your application!

* **list customer storage entries**<br>
* **create or update customer storage entry**<br>
* **get customer storage entry**<br>
* **delete customer storage entry**<br>

### Requests for Customers ###

The following requests will normally be submitted by an application on behalf of its user

### Account Setup ###

In order to sign up for an (application-specific) VoltCloud account, a customer should register, may ask for resending the confirmation email and should confirm his/her registration. In addition, he/she may ask for a password reset and perform that reset after having received the related email.

The requests needed for these operations are the same as those mentioned for the developer, since no authentication is needed for them.

* **register customer** - see "Customer Management" for Developers
* **resend customer confirmation email** - see "Customer Management" for Developers
* **confirm customer** - see "Customer Management" for Developers
* **start customer password reset** - see "Customer Management" for Developers
* **reset customer password** - see "Customer Management" for Developers

#### Login ####

* **login customer**<br>

#### Account Management ####

* **get customer settings**<br>
* **change customer email address**<br>
* **change customer password**<br>
* **change customer name**<br>
* **delete customer**<br>

#### Application Storage Management ####

* **list application storage entries**<br>
* **get application storage entry**<br>

#### Customer Storage Management ####

* **list customer storage entries**<br>
* **create or update customer storage entry**<br>
* **get customer storage entry**<br>
* **delete customer storage entry**<br>

## License ##

[MIT License](LICENSE.md)
