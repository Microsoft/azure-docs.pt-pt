---
title: Delegar um subdomínio-Azure PowerShell-DNS do Azure
description: Com este roteiro de aprendizagem, comece a delegar um subdomínio de DNS do Azure usando Azure PowerShell.
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
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Delegar um subdomínio DNS do Azure usando Azure PowerShell

Você pode usar Azure PowerShell para delegar um subdomínio DNS. Por exemplo, se você possui o domínio contoso.com, você pode delegar um subdomínio chamado *Engineering* para outro, zona separada que você pode administrar separadamente da zona contoso.com.

Se preferir, você pode delegar um subdomínio usando o [portal do Azure](delegate-subdomain.md).

> [!NOTE]
> Contoso.com é usado como um exemplo neste artigo. Substitua o seu nome de domínio por contoso.com.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para delegar um subdomínio DNS do Azure, primeiro você deve delegar seu domínio público ao DNS do Azure. Consulte [delegar um domínio ao DNS do Azure](./dns-delegate-domain-azure-dns.md) para obter instruções sobre como configurar seus servidores de nomes para delegação. Depois que o domínio for delegado à zona DNS do Azure, você poderá delegar seu subdomínio.

## <a name="create-a-zone-for-your-subdomain"></a>Criar uma zona para seu subdomínio

Primeiro, crie a zona para o subdomínio de **engenharia** .

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Observe os servidores de nomes

Em seguida, observe os quatro servidores de nomes para o subdomínio de engenharia.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Criar um registro de teste

Crie um registro **a** na zona de engenharia a ser usado para teste.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>Criar um registro NS

Em seguida, crie um registro de servidor de nomes (NS) para a zona de **engenharia** na zona contoso.com.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>Testar a delegação

Use nslookup para testar a delegação.

1. Abra uma janela do PowerShell.
2. No prompt de comando, digite `nslookup www.engineering.contoso.com.`
3. Você deve receber uma resposta não autoritativa mostrando o endereço **10.10.10.10**.

## <a name="next-steps"></a>Passos seguintes

Saiba como [Configurar o DNS reverso para serviços hospedados no Azure](dns-reverse-dns-for-azure-services.md).