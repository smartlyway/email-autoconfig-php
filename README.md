# Email autoconfig
When you create a self-hosted email service, you have to inform your customers about the email protocol settings (commonly IMAP and SMTP servers, ports, security, usernames and passwords) so they can configure their accounts in their email clients. If those users are not tech savvy, they may call support or the IT manager to ask for help. This causes IT managers & support loose their wonderful time.

## Autoconfig: the solution
What most email providers do is to setup email autoconfigure so email clients accessing the service can guess using the email address' domain and the autoconfig settings which protocol settings have to use to use the mail service.

Basically, autoconfigure makes configuring email accounts easy for everyone.

### One client, one configuration
Despite that, every email client uses its own autoconfiguration protocols so it's required to setup each autoconfig protocol for each email client if you want the email service to autoconfigure in all email clients you support. 

#### Thunderbird
Setting up Thunderbird autoconfiguration is the easiest autoconfig setup. You just have to place an [XML config file](https://developer.mozilla.org/en-US/docs/Mozilla/Thunderbird/Autoconfiguration/FileFormat/HowTo) named `config-v1.1.xml` and place it in the autoconfig subdomain, inside a `mail` path.

So if you want to setup Thunderbird autoconfiguration for domain `example.org`, you have to write a config file visible in the following URL:

http://autoconfig.example.org/mail/config-v1.1.xml

The file `mail/config-v1.1.xml` in this repository is a tested configuration file that provides autoconfiguration for Thunderbird in the production environment.

##### Configuration guides:
- https://developer.mozilla.org/en-US/docs/Mozilla/Thunderbird/Autoconfiguration
- https://developer.mozilla.org/en-US/docs/Mozilla/Thunderbird/Autoconfiguration/FileFormat/HowTo
- https://wiki.mozilla.org/Thunderbird:Autoconfiguration:ConfigFileFormat

#### Microsoft Outlook
Things get complicated with Microsoft Outlook. 

1. **Create DNS record**
You must first create a DNS record:

|    Service    | Protocol | Value, Destination, Target | Port | Priority | Weight | TTL  |
|:-------------:|:--------:|:--------------------------:|:----:|:--------:|:------:|:----:|
| _autodiscover |   _tcp   |   {{domain}}   |  443 |     5    | 0      | 3600 |

2. **Create _autoconfig XML_ file**
It must by dynamic, it will receive a POST request and must reply with a valid XML given the email address requested. The URL of the XML service must be the following one:

https://{{domain}}/Autodiscover/Autodiscover.xml

> We use PHP, so we've created a folder named `Autodiscover.xml` and placed inside an `index.php` file so the browser can request the URL and the server executes PHP code

Contents of the XML request and response can be found at repository for generating the response and in the sources to check the request Microsoft Outlook triggers to perform autodiscover (autoconfig) protocol

##### Configuration guides:
Generic one at sources (archive.org)

##### Testing tools:
https://testconnectivity.microsoft.com

#### iOS / macOS mail application
To autoconfigure iOS and macOS default mail application, we must define a mobile configuration profile file (`.mobileconfig`) with the settings to install in the mail application. This file will be automatically generated for every email passed as input. You must then provide the user this file dynamically via a web form or once generated via email, instant messaging, etc... We've tested using a URL with a GET parameter set.

Sample file to generate it in PHP is available on `btcassessors.mobileconfig` directory

##### Configuration guides:
https://developer.apple.com/library/content/featuredarticles/iPhoneConfigurationProfileRef/Introduction/Introduction.html

# Sources
The information has been extracted generally from this URLs:
http://web.archive.org/web/20120828065248/http://moens.ch/2012/05/31/providing-email-client-autoconfiguration-information/
https://github.com/Tiliq/autodiscover.xml
