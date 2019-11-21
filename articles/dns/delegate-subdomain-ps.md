---
title: Delegate a subdomain - Azure PowerShell - Azure DNS
description: With this learning path, get started delegating an Azure DNS subdomain using Azure PowerShell.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: allensu
ms.openlocfilehash: b6023adb65469dedeeba664a51917306e634146f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212509"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Delegate an Azure DNS subdomain using Azure PowerShell

You can use Azure PowerShell to delegate a DNS subdomain. For example, if you own the contoso.com domain, you can delegate a subdomain called *engineering* to another, separate zone that you can administer separately from the contoso.com zone.

If you prefer, you can delegate a subdomain using the [Azure Portal](delegate-subdomain.md).

> [!NOTE]
> Contoso.com is used as an example throughout this article. Substitua o seu nome de domínio por contoso.com.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

To delegate an Azure DNS subdomain, you must first delegate your public domain to Azure DNS. See [Delegate a domain to Azure DNS](./dns-delegate-domain-azure-dns.md) for instructions on how to configure your name servers for delegation. Once your domain is delegated to your Azure DNS zone, you can delegate your subdomain.

## <a name="create-a-zone-for-your-subdomain"></a>Create a zone for your subdomain

First, create the zone for the **engineering** subdomain.

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Note the name servers

Next, note the four name servers for the engineering subdomain.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Create a test record

Create an **A** record in the engineering zone to use for testing.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>Create an NS record

Next, create a name server (NS) record  for the **engineering** zone in the contoso.com zone.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>Test the delegation

Use nslookup to test the delegation.

1. Open a PowerShell window.
2. At command prompt, type `nslookup www.engineering.contoso.com.`
3. You should receive a non-authoritative answer showing the address **10.10.10.10**.

## <a name="next-steps"></a>Passos seguintes

Learn how to [configure reverse DNS for services hosted in Azure](dns-reverse-dns-for-azure-services.md).