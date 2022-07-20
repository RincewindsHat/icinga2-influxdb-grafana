## Graphing data backends

### InfluxDB

#### Get packages

```bash
apt-get update
apt-get install grafana influxdb influxdb-client
```

#### Create database and user

```bash
influx
```
```sql
CREATE DATABASE icinga2;
CREATE USER icinga2 WITH PASSWORD 'your-icinga2-pwd';
```

#### Send performance data to influx

```bash
icinga2 feature enable influxdb

vim /etc/icinga2/features-enabled/influxdb.conf
```
```diff
-  //host = "127.0.0.1"
-  //port = 8086
-  //database = "icinga2"
-  //flush_threshold = 1024
-  //flush_interval = 10s
-  //host_template = {
-  //  measurement = "$host.check_command$"
-  //  tags = {
-  //    hostname = "$host.name$"
-  //  }
-  //}
-  //service_template = {
-  //  measurement = "$service.check_command$"
-  //  tags = {
-  //    hostname = "$host.name$"
-  //    service = "$service.name$"
-  //  }
-  //}
+  host = "127.0.0.1"
+  port = 8086
+  database = "icinga2"
+  username = "icinga2"
+  password = "your-icinga2-pwd"
+  enable_send_thresholds = true
+  enable_send_metadata = true
+  flush_threshold = 1024
+  flush_interval = 10s
+  host_template = {
+    measurement = "$host.check_command$"
+    tags = {
+      hostname = "$host.name$"
+    }
+  }
+  service_template = {
+    measurement = "$service.check_command$"
+    tags = {
+      hostname = "$host.name$"
+      service = "$service.name$"
+    }
+  }
```
```bash
systemctl restart icinga2.service
```

### InfluxDB2

```bash
# influxdb.key GPG Fingerprint: 05CE15085FC09D18E99EFB22684A14CF2582E0C5
wget -q https://repos.influxdata.com/influxdb.key
echo '23a1c8836f0afc5ed24e0486339d7cc8f6790b83886c4c96995b88a061c5bb5d influxdb.key' | sha256sum -c && cat influxdb.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/influxdb.gpg > /dev/null
echo 'deb [signed-by=/etc/apt/trusted.gpg.d/influxdb.gpg] https://repos.influxdata.com/debian stable main' | sudo tee /etc/apt/sources.list.d/influxdata.list

sudo apt-get update && sudo apt-get install influxdb2
```

```
influx setup
> Welcome to InfluxDB 2.0!
? Please type your primary username admin
? Please type your password *****
? Please type your password again ********
? Please type your primary organization name example
? Please type your primary bucket name example
? Please type your retention period in hours, or 0 for infinite 2160
? Setup with these parameters?
  Username:          admin
  Organization:      example
  Bucket:            example
  Retention Period:  2160h0m0s
 Yes
User    Organization    Bucket
admin   example         example


influx bucket create --name icinga2
influx auth create --write-bucket abcde
influx auth create --read-bucket abcde
```

```bash
icinga2 feature enable infludb2
```

```diff
-  //host = "127.0.0.1"
-  //port = 8086
-  //organization = "monitoring"
-  //bucket = "icinga2"
-  //auth_token = "ABCDEvwxyz0189-_"
-  //flush_threshold = 1024
-  //flush_interval = 10s
-  //host_template = {
-  //  measurement = "$host.check_command$"
-  //  tags = {
-  //    hostname = "$host.name$"
-  //  }
-  //}
-  //service_template = {
-  //  measurement = "$service.check_command$"
-  //  tags = {
-  //    hostname = "$host.name$"
-  //    service = "$service.name$"
-  //  }
-  //}
+  host = "127.0.0.1"
+  port = 8086
+  organization = "example"
+  bucket = "icinga2"
+  auth_token = "abdef234234"
+  //flush_threshold = 1024
+  //flush_interval = 10s
+  enable_send_thresholds = true
+  host_template = {
+    measurement = "$host.check_command$"
+    tags = {
+      hostname = "$host.name$"
+    }
+  }
+  service_template = {
+    measurement = "$service.check_command$"
+    tags = {
+      hostname = "$host.name$"
+      service = "$service.name$"
+    }
+  }
```
