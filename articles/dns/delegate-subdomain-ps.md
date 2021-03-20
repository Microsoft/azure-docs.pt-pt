---
title: Delegado de um subdomínio - Azure PowerShell - Azure DNS
description: Com este caminho de aprendizagem, começa a delegar um subdomínio Azure DNS usando a Azure PowerShell.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: 9b37d313aa5d8c2255b4e3be69831dfcb50238ea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84712552"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Delege um subdomínio Azure DNS usando Azure PowerShell

Pode utilizar a Azure PowerShell para delegar um subdomínio DNS. Por exemplo, se possuir o domínio contoso.com, pode delegar um subdomínio chamado *engenharia* para outra zona separada que pode administrar separadamente da zona de contoso.com.

Se preferir, pode delegar um subdomínio utilizando o [Portal Azure.](delegate-subdomain.md)

> [!NOTE]
> Contoso.com é usado como exemplo ao longo deste artigo. Substitua o seu nome de domínio por contoso.com.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para delegar um subdomínio Azure DNS, deve primeiro delegar o seu domínio público no Azure DNS. Consulte [um domínio para Azure DNS](./dns-delegate-domain-azure-dns.md) para obter instruções sobre como configurar os servidores do seu nome para a delegação. Uma vez que o seu domínio é delegado à sua zona Azure DNS, pode delegar o seu subdomínio.

## <a name="create-a-zone-for-your-subdomain"></a>Crie uma zona para o seu subdomínio

Primeiro, crie a zona para o subdomínio **de engenharia.**

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Note os servidores de nomes

Em seguida, note os quatro servidores de nome para o subdomínio de engenharia.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Criar um registo de teste

Crie um registo **A** na zona de engenharia para ser utilizado para testes.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>Criar um registo NS

Em seguida, crie um registo de servidor de nome (NS) para a zona **de engenharia** na zona contoso.com.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>Teste a delegação

Use nslookup para testar a delegação.

1. Abra uma janela do PowerShell.
2. No comando rápido, tipo `nslookup www.engineering.contoso.com.`
3. Deverá receber uma resposta não autorizada que mostre o endereço **10.10.10.10.10**.

## <a name="next-steps"></a>Passos seguintes

Saiba como [configurar DNS invertidos para serviços hospedados no Azure](dns-reverse-dns-for-azure-services.md).