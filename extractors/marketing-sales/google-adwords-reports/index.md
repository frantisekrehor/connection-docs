---
title: Google AdWords Reports
permalink: /extractors/marketing-sales/google-adwords-reports/
---

* TOC
{:toc}

This extractor allows you to import data from Google AdWords reports.
Before you start, [get a Google Adwords API developer token](https://developers.google.com/adwords/api/docs/guides/signup#token_review_team_has_approved_my_developer_token) 
and your Google AdWords [customer ID](https://support.google.com/adwords/answer/1704344?hl=en).

If you do not have a Google AdWords account, create a [test account](https://developers.google.com/adwords/api/docs/guides/first-api-call#create_test_accounts).

## Create New Configuration
Find Google AdWords in the list of extractors and create a new configuration. Name it.

{: .image-popup}
![Screenshot - Create configuration](/extractors/marketing-sales/google-adwords-reports/ui_create_config.png)

Then click **Authorize Account** to be redirected to Google, and authorize the extractor to access your AdWords reports.

{: .image-popup}
![Screenshot - Create configuration](/extractors/marketing-sales/google-adwords-reports/ui_authorize_config.png)

## Configuration
To run the extractor, specify your *developer token* and *customer id*. 

To download a report, specify an [AWQL query](https://developers.google.com/adwords/api/docs/guides/awql),
through which you can customize the output of a [predefined report type](https://developers.google.com/adwords/api/docs/appendix/reports). 
There are some specific instructions for 
[using AWQL with reports](https://developers.google.com/adwords/api/docs/guides/awql#using_awql_with_reports) and
[mapping of AWQL reports to the UI](https://developers.google.com/adwords/api/docs/guides/uireports).

Optionally, you can specify the target *bucket* in Storage and the start (*since*) and end (*until*) dates of downloaded stats.

In each AWQL query, pick columns to download from allowed report values, and the FROM clause from allowed report types.
You also need to specify the name of the query and destination table in Storage (in the specified bucket). 

**Important**: *customers* and *campaigns* are reserved names, thus cannot be used as table names.

Additionally, for each query, specify a list of columns to be used as a primary key. 
Use *Display Name* of the columns as defined in the [reports types documentation](https://developers.google.com/adwords/api/docs/appendix/reports) and replace spaces with underscores 
(for example, for CampaignId use Campaign_ID and for Date use Day).

{: .image-popup}
![Screenshot - Report column names](/extractors/marketing-sales/google-adwords-reports/report_types.png)

## Example
To download a keyword performance report, use the following query configuration:

{: .image-popup}
![Screenshot - Query configuration](/extractors/marketing-sales/google-adwords-reports/ui_queries.png)

This downloads the report into a `keywords` table. The `Id` column is listed as `Keyword_ID` in the primary columns 
because that is [its display name](https://developers.google.com/adwords/api/docs/appendix/reports/keywords-performance-report#id).

When running the above configuration, you get three tables in the output bucket:
`campaigns`, `customers` and `keywords`. The tables are overwritten on each run; incremental downloads are not supported.

### Campaigns
This table is created automatically and contains a list of all campaigns in the used account, for instance:

| customerId | id        | name        | status  | servingStatus | startDate |
|------------|-----------|-------------|---------|---------------|-----------|
| 1111111111 | 610580109 | Campaign #1 | ENABLED | SUSPENDED     | 20160614  |

| endDate  | adServingOptimizationStatus | advertisingChannelType | displaySelect |
|----------|-----------------------------|------------------------|---------------|
| 20371230 | OPTIMIZE                    | SEARCH                 |               |

### Customers 
This table is created automatically and contains a list of all associated customers, for instance:

| customerId | name      | companyName     | canManageClients | currencyCode | dateTimeZone  |
|------------|-----------|-----------------|------------------|--------------|---------------|
| 1111111111 | mycompany | My Company Ltd. |                  | CZK          | Europe/Prague |

### Keywords
This table is created by the AWQL query you specified and contains the result of the defined AWQL query, for example:

| Keyword_ID | Keyword | Ad_group    |
|------------|---------|-------------|
| 12345678   | jumped  | Ad Group #1 |
| 90123456   | fox     | Ad Group #1 |
| 78901234   | quick   | Ad Group #1 |
| 56789012   | brown   | Ad Group #1 |
