# JWeb Interface

A web based GUI accessed by HTTP(S) and provides quick config wizards to configure aspects of Junos without using the CLI.

## Importance

- Allows easy deployment and maintenance as no additional software is needed just a standard browser.
- Offers multiple tabs for various aspects of operations and maintainance.
    - Dashboard: Offers a quick verification for system status
    - Configuration: Provides point and click configuration or CLI tools to edit the configuration file.
    - Monitor: Provides real time outputs and graphs; mostly the show commands and the monitor command in the CLI
    - Reports: Generates reports on demand and view them as HTML.
    - Administration: Perform software upgrades and file system maintainance. It also provides tools such as ping and traceroute.

## JWeb Login

JWeb uses the same authentication methods as the CLI - same username and password.

### Enable JWeb

```text
[edit]
user@router# set system services web-management http(s)
```
To use HTTPS, you need to specify the how the certificate is obtained.

Note: JWeb is preinstalled on SRX and vSRX platforms.