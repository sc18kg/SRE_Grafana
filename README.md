# SRE AWS instance with CloudWatch linked to Grafana

## What is Grafana
Grafana is an open source solution for running data analytics, pulling up metrics that make sense of the massive amount of data & to monitor our apps with the help of cool customizable dashboards.

Grafana connects with every possible data source, commonly referred to as databases such as Graphite, Prometheus, Influx DB, ElasticSearch, MySQL, PostgreSQL etc.

Grafana being an open source solution also enables us to write plugins from scratch for integration with several different data sources.

The tool helps us study, analyse & monitor data over a period of time, technically called time series analytics.

It helps us track the user behaviour, application behaviour, frequency of errors popping up in production or a pre-prod environment, type of errors popping up & the contextual scenarios by providing relative data.

## Set-up
1. Create a new `Ubuntu 18.04` EC2 instance in `eu-west-1`
2. In order to connect grafana to AWS the aws access and secret keys are required
3. Grafana runs on `port 3000` so we need a security group that allows access along with `80` and `22`
4. Secure shell into your created EC2 instance

## Installation of Grafana
1. Run `sudo apt-get update -y` followed by `sudo apt-get upgrade -y` incase any missing packages
2. `sudo apt-get install -y adduser libfontconfig1`
3. `wget https://dl.grafana.com/enterprise/release/grafana-enterprise_8.1.5_amd64.deb`
4. `sudo dpkg -i grafana-enterprise_8.1.5_amd64.deb`
5. `sudo nano /etc/apt/sources.list`
Here you want to add the following code at EOF:
```
# Adding a new resource for grafana package from package cloud
deb https://packagecloud.io/grafana/stable/debian/ stretch main
```
6. Press `Ctrl + x `then `y` and `Enter` to save
7. run the commands:
```
curl https://packagecloud.io/gpg.key | sudo apt-key add -
sudo wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_5.2.4_amd64.deb
sudo dpkg -i grafana-enterprise_8.1.5_amd64.deb
sudo apt-get install grafana
sudo systemctl daemon-reload
sudo systemctl start grafana-server
sudo systemctl status grafana-server
sudo systemctl enable grafana-server.service
```
14. Head to the public ip of the AWS instance you created with the `:3000` attached

## To ensure grafana starts when the instance is loaded
```
sudo update-rc.d grafana-server defaults
sudo systemctl enable grafana-server.service
```
## Creating the .credentials file
1. sudo nano /usr/share/grafana/.credentials
```
[default]
aws_access_key_id=
aws_secret_access_key=
region = eu-west-1

```
2. `sudo chmod 0644 /usr/share/grafana/.credentials`

## Now we are loaded onto the grafana
1. Click on `Add data source`
2. Navigate to `CloudWatch`
### Connection Details
You should be met with the following selections:
```
Authentication Provider : Credentials file
Credentials Profile Name :
Assume Role ARN: arn:aws:iam::enter your user id here
External ID:
Endpoint: https://{service}.{region}.amazonaws.com
Default Region: eu-west-1
Namespaces of Custom Metrics:
```
