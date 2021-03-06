---
title: Callout policy - Azure Data Explorer | Microsoft Docs
description: This article describes Callout policy in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
---
# Callout policy

## Overview

Azure Data Explorer clusters can communicate with external services in many different scenarios.
Cluster admins can manage the allowed domains for external calls by updating cluster's callout policy.

Callout policies are being managed in cluster-level and are classified into the following types:
* `kusto` - controls Azure Data Explorer cross-cluster queries.
* `sql` - controls the [SQL plugin](../query/sqlrequestplugin.md).


* `webapi` - controls other external Web calls.
* `sandbox_artifacts` - controls sandboxed plugins ([python](../query/pythonplugin.md) | [R](../query/rplugin.md)).

Callout policy is comprised of the following components:
* **CalloutType** defines the type of the callout, and can be one of the following: kusto, sql, or webapi
* **CalloutUriRegex** specifies the allowed Regex of the callout's domain
* **CanCall** indicates whether the callout is allows external calls.

## Predefined Callout policies
There is a set of predefined callout policies immutably preconfigured on all Azure Data Explorer clusters to facilitate callouts to select services.

|Service      |Cloud        |Designation  |Allowed domains |
|-------------|-------------|-------------|-------------|
|Kusto |Public Azure |Cross cluster queries |`^[^.]*\.kusto\.windows\.net$` <br> `^[^.]*\.kustomfa\.windows\.net$` |
|Kusto |Black Forest |Cross cluster queries |`^[^.]*\.kusto\.cloudapi\.de$` <br> `^[^.]*\.kustomfa\.cloudapi\.de$` |
|Kusto |Fairfax |Cross cluster queries |`^[^.]*\.kusto\.usgovcloudapi\.net$` <br> `^[^.]*\.kustomfa\.usgovcloudapi\.net$` |
|Kusto |Mooncake |Cross cluster queries |`^[^.]*\.kusto\.chinacloudapi\.cn$` <br> `^[^.]*\.kustomfa\.chinacloudapi\.cn$` |
|Azure DB |Public Azure |SQL requests |`^[^.]*\.database\.windows\.net$` <br> `^[^.]*\.databasemfa\.windows\.net$` |
|Azure DB |Black Forest |SQL requests |`^[^.]*\.database\.cloudapi\.de$` <br> `^[^.]*\.databasemfa\.cloudapi\.de$` |
|Azure DB |Fairfax |SQL requests |`^[^.]*\.database\.usgovcloudapi\.net$` <br> `^[^.]*\.databasemfa\.usgovcloudapi\.net$` |
|Azure DB |Mooncake |SQL requests |`^[^.]*\.database\.chinacloudapi\.cn$` <br> `^[^.]*\.databasemfa\.chinacloudapi\.cn$` |
|Baselining service |Public Azure |Baselining requests |`baseliningsvc-int.azurewebsites.net` <br> `baseliningsvc-ppe.azurewebsites.net` <br> `baseliningsvc-prod.azurewebsites.net` |


## Control commands

The commands require [AllDatabasesAdmin](access-control/role-based-authorization.md) permissions.

**Show all configured callout policies**
```kusto
.show cluster policy callout
```

**Alter exiting callout policies**
```kusto
.alter cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}]'
```

**Add a set of allowed callouts**
```kusto
.alter-merge cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}, {"CalloutType": "webapi","CalloutUriRegex": "bing\\.com","CanCall": true}]'
```

**Delete all non-immutable callout policies**
```kusto
.delete cluster policy callout
```