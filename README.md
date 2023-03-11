# Logging
#### _Way to expose contextual information along with the main application runtime_

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger.svg?branch=master)


### _LOGGING IMPROVES SOFTWARE MAINTAINABILITY_
> _Developers use logs to perform several crucial tasks like debugging, load testing, and performance testing. They almost always capture log errors and fatal exceptions (because those are usually the most important) and then export them to external services like Mezmo, formerly known as LogDNA, for further processing._

> _However, if you only log errors or fatal exceptions, you can miss significant information. To enable more extensive logging, you can utilize code libraries, which offer a proper set of methods and filters to configure the log information based on a level of sensitivity. Developers can then preconfigure apps to collect or store logs with a specific format and log level._

> _Utilizing different log levels in different places throughout the codebase allows developers to handle two important things. First, it allows them to configure the sensitivity of the information that gets stored to match the parameters of what they aim to capture. For example, they can include stack traces or detailed context using debug or trace log levels. Second, it allows developers to configure the sensitivity of the log collectors or external services to respond to events that satisfy specific criteria and then forward them into relevant channels._

# _Introducing ELK Stack_
_ELK is a collection of three open-source applications - [Elasticsearch](https://www.elastic.co/elasticsearch/), [Logstash](https://www.elastic.co/logstash/), and [Kibana](https://www.elastic.co/kibana/) from [Elastic](https://www.elastic.co/) that accepts data from any source or format, on which you can then perform search, analysis, and visualize that data._
> **Elasticsearch** — Elasticsearch stores and indexes the data. It is a NoSQL database based on Lucene's open-source search engine. Since Elasticsearch is developed using Java, therefore, it can run on different platforms. One particular aspect where it excels is indexing streams of data such as logs.

> **Kibana** — Kibana acts as an analytics and visualization layer on top of Elasticsearch. Kibana can be used to search, view, and interpret the data stored in Elasticsearch.

> **Logstash** — Logstash is a tool that integrates with a variety of deployments. It is used to collect, parse, transform, and buffer data from a variety of sources. The data collected by Logstash can be shipped to one or more targets like Elasticsearch.


***NOTE:exclamation:***
### All of the installation processes were made for Ubuntu


# Install Elasticsearch with Debian Package

######_The Debian package for Elasticsearch can be used to install Elasticsearch on any Debian-based system such as Debian and Ubuntu._

### _Download and install the Debian package manually_
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.6.1-amd64.deb
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.6.1-amd64.deb.sha512
shasum -a 512 -c elasticsearch-8.6.1-amd64.deb.sha512
sudo dpkg -i elasticsearch-8.6.1-amd64.deb
```
_The third line compares the SHA of the downloaded Debian package and the published checksum, which should output `elasticsearch-{version}-amd64.deb: OK.`_

### _Running Elasticsearch with systemd_
##_**`STEP #1`** | Configure Elasticsearch to start automatically_
_To configure Elasticsearch to start automatically when the system boots up, run the following command:_
```
sudo systemctl daemon-reload
```

##_**`STEP #2`** | Start Elasticsearch_ 
```
sudo systemctl enable elasticsearch.service
```


# _Install Kibana with Debian Package_
###### _The Debian package for Kibana can be used to install Kibana on any Debian-based system such as Debian and Ubuntu._

## _**`STEP #1`** | Download and install the Debian package manually_
_The Debian package for Kibana v8.6.1 can be downloaded from the website and installed as follows:_
```
wget https://artifacts.elastic.co/downloads/kibana/kibana-8.6.1-amd64.deb
shasum -a 512 kibana-8.6.1-amd64.deb 
sudo dpkg -i kibana-8.6.1-amd64.deb
```
_The second line compares the SHA produced by shasum with the published SHA._

## _**`STEP #2`** | Start Elasticsearch and generate an enrollment token for Kibana_
_When you start Elasticsearch for the first time, the following security configuration occurs automatically:_
* _Authentication and authorization are enabled, and a password is generated for the elastic built-in superuser._
* _Certificates and keys for TLS are generated for the transport and HTTP layer, and TLS is enabled and configured with these keys and certificates._

_The password and certificate and keys are output to your terminal._

_You can then generate an enrollment token for Kibana with the [elasticsearch-create-enrollment-token tool](https://www.elastic.co/guide/en/elasticsearch/reference/8.6/create-enrollment-token.html):_
```
/usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana
```
###### _Use this command only after installation of elasticsearch_
_Start Kibana and enter the enrollment token to securely connect Kibana with Elasticsearch._

_Elasticsearch can be started and stopped as follows:_
```
sudo systemctl start elasticsearch.service
sudo systemctl stop elasticsearch.service
```

## _Run Kibana with `systemd`_
##_**`STEP #1`** | Configure Kibanato start automatically_
_To configure Kibana to start automatically when the system starts, run the following command:_
```
sudo systemctl daemon-reload
```

##_**`STEP #2`** | Start Kibana_
```
sudo systemctl enable kibana.service
```

_Kibana can be started and stopped as follows:_
```
sudo systemctl start kibana.service
sudo systemctl stop kibana.service
```
_These commands provide no feedback as to whether Kibana was started successfully or not. Log information can be accessed via `journalctl -u kibana.service`._

## _Configure Kibana via the config file_
_Kibana loads its configuration from the /etc/kibana/kibana.yml file by default. The format of this config file is explained in [Configuring Kibana](https://www.elastic.co/guide/en/kibana/8.6/settings.html)._


# _Installing Logstash from Package Repositories_
## ***`STEP #1`*** | Preparing for installation
_Download and install the Public Signing Key:_
```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic-keyring.gpg
```
---
_You may need to install the apt-transport-https package on Debian before proceeding:_
```
sudo apt-get install apt-transport-https
```
---
_Save the repository definition to /etc/apt/sources.list.d/elastic-8.x.list:_
```
echo "deb [signed-by=/usr/share/keyrings/elastic-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-8.x.list
```
---
##_**`STEP #2`** | Downloading logstash_
_Run sudo apt-get update and the repository is ready for use. You can install it with:_
```
sudo apt-get update && sudo apt-get install logstash
```

###### A small configuration is required to start the logstash service, which is listed below..
## _**`STEP #1`** | Creating config file_
_Creating `logstash.conf` file into `/etc/logstash/`_
```
touch /etc/logstash/logstash.conf
```
---
##_**`STEP #2`** | Configuring created file to send logs to elasticsearch_
```
vim /etc/logstash/logstash.conf
```
---
_Copy and put text below within your config file_
```
input {
  file {
    type => "<TYPE>"
    path => "<PATH_TO_LOG_FILE>"
    codec => multiline {
        pattern => "^%{YEAR}-%{MONTHNUM}-%{MONTHDAY} %{TIME}.*"
        negate => "true"
        what => "previous"
    }
  }
}


output {
  stdout {}

  elasticsearch {
        ssl => "true"
        cloud_id => "<CLOUD_ID>"
        api_key => "<API_KEY>"
        index => "<PROJECT_NAME>-%{type}-%{+YYYY.MM.dd}"
  }
}
```
_Above has variables which you must change:_
> _**TYPE**=> types are used mainly for filter activation(you can use any word). The type is stored as part of the event itself, so you can also use the type to search for it in Kibana. If you try to set a type on an event that already has one (for example when you send an event from a shipper to an indexer) then a new input will not override the existing type. A type set at the shipper stays with that event for its life even when sent to another Logstash server._
> _**PATH_TO_LOG_FILE** => path must be absolute and cannot be relative._
> _**CLOUD_ID** => your existing cloud(Google,Auth0..) id_
> _**API_KEY** => api key for you cloud_
> _**PROJECT_NAME** => this variable works for differing all projects logs. Consequently Several projects logs can be saved in one place. Also they differ by type which explained above and create date._

## _**`STEP #3`** | Changing execute command of `logstash.service`_
_Type command below and change `ExecStart`_
```
vim /usr/lib/systemd/system/logstash.service
```
_Change `ExecStart` variable to:_
```
ExecStart=/usr/share/logstash/bin/logstash -f /etc/logstash/logstash.conf
```
_After changing your file will consist of content below"_
```
[Unit]
Description=logstash

[Service]
Type=simple
User=logstash
Group=logstash
# Load env vars from /etc/default/ and /etc/sysconfig/ if they exist.
# Prefixing the path with '-' makes it try to load, but if the file doesn't
# exist, it continues onward.
EnvironmentFile=-/etc/default/logstash
EnvironmentFile=-/etc/sysconfig/logstash
ExecStart=/usr/share/logstash/bin/logstash -f /etc/logstash/logstash.conf
Restart=always
WorkingDirectory=/
Nice=19
LimitNOFILE=16384

# When stopping, how long to wait before giving up and sending SIGKILL?
# Keep in mind that SIGKILL on a process can cause data loss.
TimeoutStopSec=infinity

[Install]
WantedBy=multi-user.target
```

## _**`STEP #4`** | Running Logstash by Using Systemd_
```
sudo systemctl start logstash.service
```
_These commands provide no feedback as to whether Logstash was started successfully or not. Log information can be accessed via `journalctl -u logstash.service`._
