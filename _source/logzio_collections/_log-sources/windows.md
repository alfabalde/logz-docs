---
title: Ship Windows logs
logo:
  logofile: windows.svg
  orientation: vertical
data-source: Windows
contributors:
  - imnotashrimp
shipping-tags:
  - os
---

<!-- tabContainer:start -->
<div class="branching-container">

* [Winlogbeat <span class="sm ital">(recommended)</span>](#winlogbeat-config)
* [NXLog](#nxlog-config)
{:.branching-tabs}

<!-- tab:start -->
<div id="winlogbeat-config">

#### Configure Winlogbeat

**Before you begin, you'll need**:
[Winlogbeat 7](https://www.elastic.co/downloads/beats/winlogbeat) or
[Winlogbeat 6](https://www.elastic.co/guide/en/beats/winlogbeat/6.8/winlogbeat-installation.html)

<div class="tasklist">

##### Download the Logz.io certificate

Download the [Logz.io public certificate](https://raw.githubusercontent.com/logzio/public-certificates/master/COMODORSADomainValidationSecureServerCA.crt) to your machine.

We'll place the certificate in `C:\ProgramData\Filebeat\COMODORSADomainValidationSecureServerCA.crt` for this example.

##### Configure Windows input

If you're working with the default configuration file,
(`C:\Program Files\Winlogbeat\winlogbeat.yml`)
clear the contents and start with a fresh file.

Paste this code block.

{% include log-shipping/replace-vars.html token=true %}

```yaml
winlogbeat.event_logs:
  - name: Application
    ignore_older: 72h
  - name: Security
  - name: System

fields:
  logzio_codec: json
  token: <<SHIPPING-TOKEN>>
  type: wineventlog
fields_under_root: true
```

If you're running Winlogbeat 7, paste this code block.
Otherwise, you can leave it out.

```yaml
# ... For Winlogbeat 7 only ...
processors:
  - rename:
      fields:
      - from: "agent"
        to: "beat_agent"
      ignore_missing: true
  - rename:
      fields:
      - from: "log.file.path"
        to: "source"
      ignore_missing: true
  - rename:
      fields:
      - from: "log"
        to: "log_information"
      ignore_missing: true
```


##### Add Logz.io as an output

If Logz.io isn't the output, set it now.

Winlogbeat can have one output only, so remove any other `output` entries.

{% include log-shipping/replace-vars.html listener=true %}

```yaml
output.logstash:
  hosts: ["<<LISTENER-HOST>>:5015"]
  ssl:
    certificate_authorities: ['C:\ProgramData\Filebeat\COMODORSADomainValidationSecureServerCA.crt']
```

##### Restart Winlogbeat

Open PowerShell as an admin and run this command:

```powershell
Restart-Service winlogbeat
```

##### Check Logz.io for your logs

Give your logs some time to get from your system to ours, and then open [Kibana](https://app.logz.io/#/dashboard/kibana).

If you still don't see your logs, see [log shipping troubleshooting]({{site.baseurl}}/user-guide/log-shipping/log-shipping-troubleshooting.html).

</div>

</div>
<!-- tab:end -->

<!-- tab:start -->
<div id="nxlog-config">

#### Configure NXLog

**Before you begin, you'll need**:
[NXLog](https://nxlog.co/products/nxlog-community-edition/download)

<div class="tasklist">

##### Configure NXLog basics

Copy this code into your configuration file (`C:\Program Files (x86)\nxlog\conf\nxlog.conf` by default).

```conf
define ROOT C:\\Program Files (x86)\\nxlog
define ROOT_STRING C:\\Program Files (x86)\\nxlog
define CERTDIR %ROOT%\\cert
Moduledir %ROOT%\\modules
CacheDir %ROOT%\\data
Pidfile %ROOT%\\data\\nxlog.pid
SpoolDir %ROOT%\\data
LogFile %ROOT%\\data\\nxlog.log
<Extension charconv>
    Module xm_charconv
    AutodetectCharsets utf-8, euc-jp, utf-16, utf-32, iso8859-2
</Extension>
```

##### Add Windows as an input

Add an `Input` block to append your account token to log records.

{% include log-shipping/replace-vars.html token=true %}

```conf
<Input eventlog>

# For Windows Vista/2008 and later, set Module to `im_msvistalog`. For
#  Windows XP/2000/2003, set to `im_mseventlog`.
    Module im_msvistalog

    Exec if $raw_event =~ /^#/ drop();
    Exec convert_fields("AUTO", "utf-8");
    Exec    $raw_event = '[<<SHIPPING-TOKEN>>][type=wineventlog]' + $raw_event;
</Input>
```

##### Add Logz.io as an output

Add the Logz.io listener in the `Output` block.

{% include log-shipping/replace-vars.html listener=true %}

```conf
<Output out>
    Module  om_tcp
    Host    <<LISTENER-HOST>>
    Port    8010
</Output>
<Route 1>
    Path eventlog => out
</Route>
```

##### Restart NXLog

Open PowerShell as an admin and run this command:

```powershell
Restart-Service nxlog
```

##### Check Logz.io for your logs

Give your logs some time to get from your system to ours, and then open [Kibana](https://app.logz.io/#/dashboard/kibana).

</div>

</div>
<!-- tab:end -->

</div>
<!-- tabContainer:end -->