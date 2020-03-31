---
title: Delegar um subdomínio - Azure PowerShell - Azure DNS
description: Com este caminho de aprendizagem, começar a delegar um subdomínio Azure DNS usando o Azure PowerShell.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: 7e019afaae98422b8d5a3c8fa7a5f79e26c6a149
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937699"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Delege um subdomínio Azure DNS usando o Azure PowerShell

Pode utilizar o Azure PowerShell para delegar um subdomínio DNS. Por exemplo, se possuir o domínio contoso.com, pode delegar um subdomínio chamado *engenharia* para outra zona separada que pode administrar separadamente da zona contoso.com.

Se preferir, pode delegar um subdomínio utilizando o [Portal Azure](delegate-subdomain.md).

> [!NOTE]
> Contoso.com é usado como exemplo ao longo deste artigo. Substitua o seu nome de domínio por contoso.com.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para delegar um subdomínio Azure DNS, deve primeiro delegar o seu domínio público no Azure DNS. Consulte [o Delegado de Um domínio para o DNS Azure](./dns-delegate-domain-azure-dns.md) para obter instruções sobre como configurar os seus servidores de nome para delegação. Assim que o seu domínio for delegado à sua zona DeNS Azure, pode delegar o seu subdomínio.

## <a name="create-a-zone-for-your-subdomain"></a>Crie uma zona para o seu subdomínio

Primeiro, crie a zona para o subdomínio de **engenharia.**

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Note os servidores de nomes

Em seguida, note os quatro servidores de nome para o subdomínio de engenharia.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Criar um registo de teste

Crie um registo **A** na zona de engenharia para utilizar para testes.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>Criar um registo ns

Em seguida, crie um registo de servidor de nome (NS) para a zona **de engenharia** na zona contoso.com.

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
2. No pedido de comando, tipo`nslookup www.engineering.contoso.com.`
3. Deve receber uma resposta não autorizada que mostre o endereço **10.10.10.10 .**

## <a name="next-steps"></a>Passos seguintes

Saiba como [configurar dNS invertidos para serviços hospedados em Azure](dns-reverse-dns-for-azure-services.md).