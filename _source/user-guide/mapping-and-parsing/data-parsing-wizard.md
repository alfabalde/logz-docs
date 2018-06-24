---
layout: article
title: The data parsing wizard
contributors:
  - aduani
  - tdrios
  - sboroda
---

Parsing is the process of breaking down your log message into smaller chunks of data, placing each chunk into its own specific named field, and enriching data with additional information such as geolocation. Parsed logs can be more easily analyzed than raw data, allowing you to create richer visualizations and more helpful alerts.

Parsing is not necessary for all types of logs. But if you use a custom or uncommon log type, parsing can be an invaluable tool. 

You can analyze a set of sample logs in the data parsing wizard, simplifying the process.

![Data parsing wizard]({{site.baseurl}}/images/parsing-and-mapping/parsing-and-mapping--data-parsing-wizard.png)

You can find the data parsing wizard by selecting [**Log Shipping > Data Parsing**](https://app.logz.io/#/dashboard/data-parsing/step1) from the top menu.

<div class="info-box note">The data parsing wizard is a beta feature.</div>

## Using the data parsing wizard

###### Choose a data source

1. Choose the log type you want to parse from the **Select log type** list.

    <div class="info-box note">If the log type you want to parse is disabled, then Logz.io automatically parses it. If you want to override the default parsing, change the log type in your log shipper.</div>

2. Click **Next** to continue.

###### Configure your parse settings

![Step 2: Parse]({{site.baseurl}}/images/parsing-and-mapping/parsing-and-mapping--step-2-parse.png)

1. If you want to change the sample log lines, click **Select**. You can choose up to 5 sample lines.

2. Type your grok pattern in the **Parse method** text box. 

    <div class="info-box note notes"><ul>
    <li>As you type, your parsed log lines are shown in the <strong>Parse results</strong> table. Use the colors to help you match the fields in the log lines with your parsing results.</li>
    <li>To omit data, do not name the fields.</li>
    <li>To override the log's existing message field, name one or more fields <code>message</code> in the grok pattern. If more than one is used, all message fields are concatenated into a single message field. If you don't use this field name, the log's existing message field will be used.</li>
    <li>After entering your grok pattern, you can define a field type for each field that you parse.</li>
    <li>You can let Logz.io detect each field's data type by leaving the default Automatic settings. Otherwise, you can define other data types, such as boolean, date, IP, and byte. For geo-enrichment, for example, you need to select the <strong>Geo-Enrichment</strong> field type.</li>
    </ul></div>

    <div class="info-box tip">To help make the best grok pattern for your logs, read the <a href="https://github.com/elastic/logstash/blob/v1.4.2/patterns/grok-patterns">Elasticsearch grok patterns</a> and use the <a href="https://grokdebug.herokuapp.com/">Grok Debugger</a>.</div>

3. Click **Next** to continue.

###### Enrich

![Step 3: Enrich]({{site.baseurl}}/images/parsing-and-mapping/parsing-and-mapping--step-3-enrich.png)

1. If any fields are parsed as geo IP, choose which geo enrichment fields to include in your logs, such as continent_code or country_name.

2. Configure any timestamp fields. If there are more than one timestamp field, choose a **Leading timestamp**.

3. Click **Next** to continue.

###### Validate

![Step 4: Validate]({{site.baseurl}}/images/parsing-and-mapping/parsing-and-mapping--step-4-validate.png)

1. In both of the tabs, review **Unparsed logs** and **All logs** to troubleshoot any problems with your grok pattern.

2. If everything looks good, click **Apply** to parse future logs using these settings. Otherwise, click **Back** to make changes to your settings.