---
title: DNS invertidos para serviços Azure - Azure DNS
description: Com este caminho de aprendizagem, começa a configurar pesquisas de DNS inversas para serviços hospedados em Azure.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 15396467e92b3e035add03d0d29888558571aa2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84711243"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Configurar DNS invertidos para serviços hospedados em Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este artigo explica como configurar pesquisas de DNS inversas para serviços hospedados em Azure.

Os serviços em Azure utilizam endereços IP atribuídos pela Azure e propriedade da Microsoft. Estes registos DNS invertidos (registos PTR) devem ser criados nas zonas de pesquisa reversas de DNS da Microsoft. Este artigo explica como fazer isto.

Este cenário não deve ser confundido com a capacidade de [hospedar as zonas de pesquisa de DNS inversas para as gamas IP atribuídas em DNS Azure](dns-reverse-dns-hosting.md). Neste caso, os intervalos IP representados pela zona de procura inversa devem ser atribuídos à sua organização, normalmente pelo seu ISP.

Antes de ler este artigo, deve estar familiarizado com esta [visão geral do DNS invertido e suporte em Azure](dns-reverse-dns-overview.md).

No Azure DNS, os recursos de computação (tais como máquinas virtuais, conjuntos de escala de máquinas virtuais ou clusters de tecido de serviço) são expostos através de um recurso PublicIpAddress. As pesquisas de DNS inversas são configuradas usando a propriedade 'ReverseFqdn' do PublicIpAddress.


O DNS inverso não é atualmente suportado para o Serviço de Aplicações Azure.

## <a name="validation-of-reverse-dns-records"></a>Validação de registos DNS invertidos

Um terceiro não deve ser capaz de criar registos DNS invertidos para o seu mapeamento de serviço Azure para os seus domínios DNS. Para evitar isto, o Azure apenas permite a criação de um registo DNS invertido onde o nome de domínio especificado no registo DNS invertido seja o mesmo que, ou resolve, o nome DNS ou endereço IP de um PublicIpAddress ou Cloud Service na mesma subscrição Azure.

Esta validação só é efetuada quando o registo de DNS invertido é definido ou modificado. A re-validação periódica não é realizada.

Por exemplo: suponha que o recurso PublicIpAddress tem o nome DNS contosoapp1.northus.cloudapp.azure.com e endereço IP 23.96.52.53. O ReverseFqdn para o PublicIpAddress pode ser especificado como:
* O nome DNS para o PublicIpAddress, contosoapp1.northus.cloudapp.azure.com
* O nome DNS para um PublicIpAddress diferente na mesma subscrição, como contosoapp2.westus.cloudapp.azure.com
* Um nome de DNS de vaidade, como app1.contoso.com, desde que este nome seja configurado *pela primeira vez* como CNAME para contosoapp1.northus.cloudapp.azure.com, ou para um PublicIpAddress diferente na mesma subscrição.
* Um nome DE DNS de vaidade, como app1.contoso.com, desde que este nome seja configurado *pela primeira vez* como um registo A para o endereço IP 23.96.52.53, ou para o endereço IP de um PublicIpAddress diferente na mesma subscrição.

Os mesmos constrangimentos aplicam-se ao DNS invertido para serviços cloud.


## <a name="reverse-dns-for-publicipaddress-resources"></a>DNS invertidos para recursos PublicIpAddress

Esta secção fornece instruções detalhadas sobre como configurar DNS invertidos para recursos PublicIpAddress no modelo de implementação do Gestor de Recursos, utilizando quer a Azure PowerShell, Azure classic CLI, quer Azure CLI. Configurar o DNS invertido para recursos PublicIpAddress não é atualmente suportado através do portal Azure.

A Azure suporta atualmente DNS invertidos apenas para recursos IPv4 PublicIpAddress. Não é apoiado para o IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Adicione DNS invertidos a um PublicIpAddresss existente

#### <a name="powershell"></a>PowerShell

Para atualizar o DNS invertido para um PublicIpAddress existente:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Para adicionar DNS invertido a um PublicIpAddress existente que já não tenha um nome DNS, também deve especificar um nome DNS:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

Para adicionar DNS invertido a um PublicIpAddress existente:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Para adicionar DNS invertido a um PublicIpAddress existente que já não tenha um nome DNS, também deve especificar um nome DNS:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>CLI do Azure

Para adicionar DNS invertido a um PublicIpAddress existente:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Para adicionar DNS invertido a um PublicIpAddress existente que já não tenha um nome DNS, também deve especificar um nome DNS:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Criar um endereço IP público com DNS invertido

Para criar um novo PublicIpAddress com a propriedade DNS inversa já especificada:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Ver DNS invertido para um PublicIpAddress existente

Para visualizar o valor configurado para um PublicIpAddress existente:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Remova DNS invertidos dos endereços IP públicos existentes

Para remover uma propriedade DNS inversa de um PublicIpAddress existente:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Configurar DNS invertidos para serviços em nuvem

Esta secção fornece instruções detalhadas sobre como configurar DNS invertidos para serviços em nuvem no modelo de implementação Clássico, utilizando a Azure PowerShell. Configurar o DNS inverso para serviços em nuvem não é suportado através do portal Azure, Azure classic CLI ou Azure CLI.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Adicione DNS invertidos aos serviços de cloud existentes

Para adicionar um registo DNS invertido a um Serviço cloud existente:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Criar um Serviço de Nuvem com DNS invertido

Para criar um novo Serviço cloud com a propriedade DNS inversa já especificada:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Ver DNS invertidos para serviços cloud existentes

Para ver a propriedade DNS inversa para um Serviço cloud existente:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Remova DNS invertidos dos serviços de cloud existentes

Para remover uma propriedade DNS inversa de um Serviço cloud existente:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>FAQ

### <a name="how-much-do-reverse-dns-records-cost"></a>Quanto custam os registos dns invertidos?

São de graça!  Não há custo adicional para registos ou consultas de DNS invertidos.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Os meus registos DNS invertidos vão resolver-se a partir da internet?

Sim. Uma vez definida a propriedade DNS inversa para o seu serviço Azure, a Azure gere todas as delegações de DNS e zonas DNS necessárias para garantir que o registo de DNS invertido resolve para todos os utilizadores da Internet.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Os registos DNS invertidos padrão são criados para os meus serviços Azure?

N.º O DNS invertido é uma funcionalidade de opt-in. Não são criados registos DNS invertidos padrão se optar por não os configurar.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Qual é o formato para o nome de domínio totalmente qualificado (FQDN)?

As FQDNs são especificadas por ordem a prazo e devem ser encerradas por um ponto (por exemplo, "app1.contoso.com.").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>O que acontece se o cheque de validação do DNS invertido que especifico falhar?

Quando a verificação de validação de DNS inversa falha, a operação para configurar o registo de DNS invertido falha. Corrija o valor DNS invertido conforme necessário e redaça.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Posso configurar DNS invertidos para o Serviço de Aplicações Azure?

N.º O DNS inverso não é suportado para o Serviço de Aplicações Azure.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Posso configurar vários registos dns invertidos para o meu serviço Azure?

N.º O Azure suporta um único registo de DNS invertido para cada Azure Cloud Service ou PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Posso configurar DNS invertidos para recursos IPv6 PublicIpAddress?

N.º Atualmente, o Azure suporta DNS invertidos apenas para recursos IPv4 PublicIpAddress e Serviços Cloud.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Posso enviar e-mails para domínios externos dos meus serviços Azure Compute?

A capacidade técnica de enviar e-mails diretamente de uma implementação Azure depende do tipo de subscrição. Independentemente do tipo de subscrição, a Microsoft recomenda a utilização de serviços de retransmissão de correio fidedignos para enviar correio de saída. Para mais detalhes, consulte [Segurança Azure Reforçada para envio de E-mails – Atualização de novembro de 2017](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre DNS invertidos, consulte [a pesquisa reversa do DNS na Wikipédia.](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)
<br>
Saiba como [hospedar a zona de pesquisa inversa para a gama IP atribuída ao ISP em Azure DNS](dns-reverse-dns-for-azure-services.md).

