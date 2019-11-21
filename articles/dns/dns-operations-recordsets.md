---
title: Manage DNS records in Azure DNS using Azure PowerShell | Microsoft Docs
description: Managing DNS record sets and records on Azure DNS when hosting your domain on Azure DNS. All PowerShell commands for operations on record sets and records.
services: dns
documentationcenter: na
author: asudbring
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: allensu
ms.openlocfilehash: c11a5c4a3cfe18fbc203ad641ab1de866915bcc4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211678"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Manage DNS records and recordsets in Azure DNS using Azure PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-operations-recordsets-portal.md)
> * [CLI clássica do Azure](dns-operations-recordsets-cli-nodejs.md)
> * [CLI do Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

This article shows you how to manage DNS records for your DNS zone by using Azure PowerShell. DNS records can also be managed by using the cross-platform [Azure CLI](dns-operations-recordsets-cli.md) or the [Azure portal](dns-operations-recordsets-portal.md).

The examples in this article assume you have already [installed Azure PowerShell, signed in, and created a DNS zone](dns-operations-dnszones.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Introdução

Antes de criar registos DNS no DNS do Azure, terá primeiro de compreender como o DNS do Azure organiza os registos DNS em conjuntos de registos DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para obter mais informações sobre os registos DNS no DNS do Azure, veja [Zonas e registos DNS](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Create a new DNS record

If your new record has the same name and type as an existing record, you need to [add it to the existing record set](#add-a-record-to-an-existing-record-set). If your new record has a different name and type to all existing records, you need to create a new record set. 

### <a name="create-a-records-in-a-new-record-set"></a>Create 'A' records in a new record set

Pode criar conjuntos de registos com o cmdlet `New-AzDnsRecordSet`. When creating a record set, you need to specify the record set name, the zone, the time to live (TTL), the record type, and the records to be created.

Os parâmetros para adicionar registos a um conjunto de registos variam consoante o tipo do conjunto de registos. For example, when using a record set of type 'A', you need to specify the IP address using the parameter `-IPv4Address`. Other parameters are used for other record types. See Additional record type examples for details.

The following example creates a record set with the relative name 'www' in the DNS Zone 'contoso.com'. The fully-qualified name of the record set is 'www.contoso.com'. The record type is 'A', and the TTL is 3600 seconds. The record set contains a single record, with IP address '1.2.3.4'.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

To create a record set at the 'apex' of a zone (in this case, 'contoso.com'), use the record set name '\@' (excluding quotation marks):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

If you need to create a record set containing more than one record, first create a local array and add the records, then pass the array to `New-AzDnsRecordSet` as follows:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Record set metadata](dns-zones-records.md#tags-and-metadata) can be used to associate application-specific data with each record set, as key-value pairs. The following example shows how to create a record set with two metadata entries, 'dept=finance' and 'environment=production'.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Azure DNS also supports 'empty' record sets, which can act as a placeholder to reserve a DNS name before creating DNS records. Empty record sets are visible in the Azure DNS control plane, but do appear on the Azure DNS name servers. The following example creates an empty record set:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Create records of other types

Having seen in detail how to create 'A' records, the following examples show how to create records of other record types supported by Azure DNS.

In each case, we show how to create a record set containing a single record. The earlier examples for 'A' records can be adapted to create record sets of other types containing multiple records, with metadata, or to create empty record sets.

We do not give an example to create an SOA record set, since SOAs are created and deleted with each DNS zone and cannot be created or deleted separately. However, [the SOA can be modified, as shown in a later example](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Criar um conjunto de registos AAAA com um único registo

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Create a CAA record set with a single record

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Criar um conjunto de registos CNAME com um único registo

> [!NOTE]
> The DNS standards do not permit CNAME records at the apex of a zone (`-Name '@'`), nor do they permit record sets containing more than one record.
> 
> For more information, see [CNAME records](dns-zones-records.md#cname-records).


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Criar um conjunto de registos MX com um único registo

In this example, we use the record set name '\@' to create an MX record at the zone apex (in this case, 'contoso.com').


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Criar um conjunto de registos NS com um único registo

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Criar um conjunto de registos PTR com um único registo

In this case, 'my-arpa-zone.com' represents the ARPA reverse lookup zone representing your IP range. Cada conjunto de registos PTR nesta zona corresponde a um endereço IP neste intervalo de IPs. The record name '10' is the last octet of the IP address within this IP range represented by this record.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Criar um conjunto de registos SRV com um único registo

When creating an [SRV record set](dns-zones-records.md#srv-records), specify the *\_service* and *\_protocol* in the record set name. There is no need to include '\@' in the record set name when creating an SRV record set at the zone apex.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Create a TXT record set with a single record

The following example shows how to create a TXT record. For more information about the maximum string length supported in TXT records, see [TXT records](dns-zones-records.md#txt-records).

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Get a record set

To retrieve an existing record set, use `Get-AzDnsRecordSet`. This cmdlet returns a local object that represents the record set in Azure DNS.

As with `New-AzDnsRecordSet`, the record set name given must be a *relative* name, meaning it must exclude the zone name. You also need to specify the record type, and the zone containing the record set.

The following example shows how to retrieve a record set. In this example, the zone is specified using the `-ZoneName` and `-ResourceGroupName` parameters.

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternatively, you can also specify the zone using a zone object, passed using the `-Zone` parameter.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>List record sets

You can also use `Get-AzDnsZone` to list record sets in a zone, by omitting the `-Name` and/or `-RecordType` parameters.

The following example returns all record sets in the zone:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

The following example shows how all record sets of a given type can be retrieved by specifying the record type while omitting the record set name:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

To retrieve all record sets with a given name, across record types, you need to retrieve all record sets and then filter the results:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

In all the above examples, the zone can be specified either by using the `-ZoneName` and `-ResourceGroupName`parameters (as shown), or by specifying a zone object:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Add a record to an existing record set

To add a record to an existing record set, follow the following three steps:

1. Get the existing record set

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Add the new record to the local record set. This is an off-line operation.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Commit the change back to the Azure DNS service. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

Using `Set-AzDnsRecordSet` *replaces* the existing record set in Azure DNS (and all records it contains) with the record set specified. [Etag checks](dns-zones-records.md#etags) are used to ensure concurrent changes are not overwritten. You can use the optional `-Overwrite` switch to suppress these checks.

This sequence of operations can also be *piped*, meaning you pass the record set object by using the pipe rather than passing it as a parameter:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

The examples above show how to add an 'A' record to an existing record set of type 'A'. A similar sequence of operations is used to add records to record sets of other types, substituting the `-Ipv4Address` parameter of `Add-AzDnsRecordConfig` with other parameters specific to each record type. The parameters for each record type are the same as for the `New-AzDnsRecordConfig` cmdlet, as shown in Additional record type examples above.

Record sets of type 'CNAME' or 'SOA' cannot contain more than one record. This constraint arises from the DNS standards. It is not a limitation of Azure DNS.

## <a name="remove-a-record-from-an-existing-record-set"></a>Remove a record from an existing record set

The process to remove a record from a record set is similar to the process to add a record to an existing record set:

1. Get the existing record set

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Remove the record from the local record set object. This is an off-line operation. The record that's being removed must be an exact match with an existing record across all parameters.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Commit the change back to the Azure DNS service. Use the optional `-Overwrite` switch to suppress [Etag checks](dns-zones-records.md#etags) for concurrent changes.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

Using the above sequence to remove the last record from a record set does not delete the record set, rather it leaves an empty record set. To remove a record set entirely, see [Delete a record set](#delete-a-record-set).

Similarly to adding records to a record set, the sequence of operations to remove a record set can also be piped:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Different record types are supported by passing the appropriate type-specific parameters to `Remove-AzDnsRecordSet`. The parameters for each record type are the same as for the `New-AzDnsRecordConfig` cmdlet, as shown in Additional record type examples above.


## <a name="modify-an-existing-record-set"></a>Modify an existing record set

The steps for modifying an existing record set are similar to the steps you take when adding or removing records from a record set:

1. Retrieve the existing record set by using `Get-AzDnsRecordSet`.
2. Modify the local record set object by:
    * Adding or removing records
    * Changing the parameters of existing records
    * Changing the record set metadata and time to live (TTL)
3. Commit your changes by using the `Set-AzDnsRecordSet` cmdlet. This *replaces* the existing record set in Azure DNS with the record set specified.

When using `Set-AzDnsRecordSet`, [Etag checks](dns-zones-records.md#etags) are used to ensure concurrent changes are not overwritten. You can use the optional `-Overwrite` switch to suppress these checks.

### <a name="to-update-a-record-in-an-existing-record-set"></a>To update a record in an existing record set

In this example, we change the IP address of an existing 'A' record:

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>To modify an SOA record

You cannot add or remove records from the automatically created SOA record set at the zone apex (`-Name "@"`, including quote marks). However, you can modify any of the parameters within the SOA record (except "Host") and the record set TTL.

The following example shows how to change the *Email* property of the SOA record:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>To modify NS records at the zone apex

The NS record set at the zone apex is automatically created with each DNS zone. It contains the names of the Azure DNS name servers assigned to the zone.

You can add additional name servers to this NS record set, to support co-hosting domains with more than one DNS provider. You can also modify the TTL and metadata for this record set. However, you cannot remove or modify the pre-populated Azure DNS name servers.

Note that this applies only to the NS record set at the zone apex. Other NS record sets in your zone (as used to delegate child zones) can be modified without constraint.

The following example shows how to add an additional name server to the NS record set at the zone apex:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>To modify record set metadata

[Record set metadata](dns-zones-records.md#tags-and-metadata) can be used to associate application-specific data with each record set, as key-value pairs.

The following example shows how to modify the metadata of an existing record set:

```powershell
# Get the record set
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Delete a record set

Record sets can be deleted by using the `Remove-AzDnsRecordSet` cmdlet. Deleting a record set also deletes all records within the record set.

> [!NOTE]
> You cannot delete the SOA and NS record sets at the zone apex (`-Name '@'`).  Azure DNS created these automatically when the zone was created, and deletes them automatically when the zone is deleted.

The following example shows how to delete a record set. In this example, the record set name, record set type, zone name, and resource group are each specified explicitly.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternatively, the record set can be specified by name and type, and the zone specified using an object:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

As a third option, the record set itself can be specified using a record set object:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

When you specify the record set to be deleted by using a record set object, [Etag checks](dns-zones-records.md#etags) are used to ensure concurrent changes are not deleted. You can use the optional `-Overwrite` switch to suppress these checks.

The record set object can also be piped instead of being passed as a parameter:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Pedidos de confirmação

Todos os cmdlets `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` e `Remove-AzDnsRecordSet` suportam pedidos de confirmação.

Each cmdlet prompts for confirmation if the `$ConfirmPreference` PowerShell preference variable has a value of `Medium` or lower. Since the default value for `$ConfirmPreference` is `High`, these prompts are not given when using the default PowerShell settings.

Pode substituir a definição `$ConfirmPreference` atual com o parâmetro `-Confirm`. Se especificar `-Confirm` ou `-Confirm:$True` , o cmdlet irá pedir-lhe a confirmação antes de ser executado. Se especificar `-Confirm:$False`, o cmdlet não solicita a confirmação. 

Para obter mais informações sobre `-Confirm` e `$ConfirmPreference`, veja [Sobre as Variáveis de Preferência](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Passos seguintes

Learn more about [zones and records in Azure DNS](dns-zones-records.md).
<br>
Learn how to [protect your zones and records](dns-protect-zones-recordsets.md) when using Azure DNS.
<br>
Review the [Azure DNS PowerShell reference documentation](/powershell/module/az.dns).
