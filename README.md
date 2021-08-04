# postman-collection-for-voltcloud #

a Postman collection for VoltCloud.io (aka VoltServer)

This repo contains a [Postman](https://www.postman.com/) collection of REST requests against [VoltCloud.io](https://voltcloud.io/).

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

Now select the imported collection and navigate to tab "Variables". The variables listed are those used by the various requests - many of them will be filled automatically upon receiving responses from the VoltCloud server, but two of them have to be explicitly set in order to get started

* **developer_email_address**<br>set its `initial value` or `current value` (or both) to the email address you use to log into the VoltServer
* **developer_password**<br>set its `current value` to your VoltServer password (note: set the `current value` only in order to avoid your password to accidentially leak to the Postman server or into any export file)

**Nota bene**: do not forget to "Save" your entries afterwards or they will not be considered!

## Requests ##

The requests found in this collection are grouped into those used by an application developer (who is also allowed to manage his/her customers and their key-value stores) and those used by a customer of a given application him/herself (which normally means that the application performs any requests on behalf of the - previously logged-in - customer)

In principle, you may submit the listed requests one after another - usually, former requests collect any information needed for later ones.

### Requests for Developers ###

#### Login ####

* **login developer**<br>just submit this request in order to authenticate yourself with the configured `developüer_email_address` and `developer_password`. If successful, Postman will extract the `developer_access_token` needed to run all the other requests provided for developers

#### Application Management ####

* **list applications**<br>submit this request to get a (potentially empty) list of your currently registered applications (shown in the response pane for this request). If any of these applications has a name (aka "subdomain") starting with "postman-", the URL and id of that application will be saved for the following requests. If you do not have such an application, simply create one as shown below
* **create new application**<br>this request registers a new application (and saves its id for further requests). Initially, the newly create application will have an arbitrary name (and URL) - for that reason, you should probably "update" it next
* **update application**<br>this request changes one or multiple settings for an application given by its id. In this example, the request simply changes the application's name to something beginning with "postman-" and ending with a series of digits (denoting the crrent time stamp)
* **upload application**<br>after creation, an application is still "empty", i.e., it contains no code. This request allows a developer to upload a ZIP archive containing all files that make up the actual application - including an `index.html` needed as the application's entry point. In this example, the ZIP archive just contains a simple dummy HTML file
* **inspect application**<br>
* **delete application**<br>

#### Application Storage Management ####

* **list application storage entries**<br>
* **create or update application storage entry**<br>
* **get application storage entry**<br>
* **delete application storage entry**<br>

#### Customer Management ####

* **list customers**<br>
* **register customer**<br>
* **resend customer confirmation email**<br>
* **confirm customer**<br>
* **start customer password reset**<br>
* **reset customer password**<br>
* **delete customer**<br>

#### Customer Storage Management ####

* **list customer storage entries**<br>
* **create or update customer storage entry**<br>
* **get customer storage entry**<br>
* **delete customer storage entry**<br>

### Requests for Customers ###

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
