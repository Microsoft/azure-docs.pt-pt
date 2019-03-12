---
title: DNS inverso para os serviços do Azure | Documentos da Microsoft
description: Saiba como configurar as pesquisas de DNS inversas para serviços alojados no Azure
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: victorh
ms.openlocfilehash: e162d838cb4895841428a827b56bec28e3e16b8a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533151"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Configurar DNS inverso para os serviços alojados no Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este artigo explica como configurar as pesquisas de DNS inversas para serviços alojados no Azure.

Serviços do Azure utilizam endereços IP atribuído pelo Azure e que pertence à Microsoft. Estes registos DNS inversos (registos PTR) tem de ser criados as correspondentes pertencentes à Microsoft zonas DNS inversas pesquisa. Este artigo explica como fazer isso.

Este cenário não deve ser confundido com a capacidade de [alojar zonas de pesquisa DNS inversas para os intervalos IP atribuídos no DNS do Azure](dns-reverse-dns-hosting.md). Neste caso, os intervalos IP representados por zona de pesquisa inversa devem ser atribuídos à sua organização, normalmente pelo seu ISP.

Antes de ler este artigo, deve estar familiarizado com isso [descrição geral do DNS inverso e de suporte no Azure](dns-reverse-dns-overview.md).

No DNS do Azure, os recursos de computação (por exemplo, máquinas virtuais, os conjuntos de dimensionamento de máquinas virtuais ou clusters do Service Fabric) são expostos por meio de um recurso de PublicIpAddress. Pesquisas de DNS inversas são configuradas usando a propriedade 'ReverseFqdn' do PublicIpAddress.


DNS inversa não é atualmente suportada para o serviço de aplicações do Azure.

## <a name="validation-of-reverse-dns-records"></a>Validação de registos DNS inversos

Uma aplicação de terceiros não deve ser capaz de criar registos DNS inversos para seu mapeamento de serviço do Azure para os domínios DNS. Para evitar esta situação, Azure apenas permite a criação de um registo DNS inversa em que o nome de domínio especificado no registo DNS inverso é o mesmo ou é resolvido para o nome DNS ou endereço IP de um PublicIpAddress ou serviço em nuvem na mesma subscrição do Azure.

Esta validação só é executada quando o registo DNS reverso é definir ou modificado. Não é efetuada a validação de reavaliação periódica.

Por exemplo: suponha que o recurso de PublicIpAddress tem o contosoapp1.northus.cloudapp.azure.com de nome DNS e o endereço IP 23.96.52.53. O ReverseFqdn para PublicIpAddress pode ser especificado como:
* O nome DNS para o PublicIpAddress, contosoapp1.northus.cloudapp.azure.com
* O nome DNS para um PublicIpAddress diferente na mesma subscrição, como contosoapp2.westus.cloudapp.azure.com
* Um personalizado de nomes DNS, como app1.contoso.com, desde que este nome é *primeiro* configurado como um CNAME para contosoapp1.northus.cloudapp.azure.com ou para um PublicIpAddress diferente na mesma subscrição.
* Um personalizado de nomes DNS, como app1.contoso.com, desde que este nome é *primeiro* configurado como um registo para o endereço IP 23.96.52.53 ou para o endereço IP de um PublicIpAddress diferente na mesma subscrição.

As mesmas restrições quanto se aplicam ao DNS inverso para os serviços Cloud.


## <a name="reverse-dns-for-publicipaddress-resources"></a>DNS inverso para obter recursos PublicIpAddress

Esta secção fornece instruções detalhadas sobre como configurar o DNS inverso para obter recursos PublicIpAddress no modelo de implementação do Resource Manager, utilizando o Azure PowerShell, CLI clássica do Azure ou da CLI do Azure. Configurar DNS inverso para obter recursos PublicIpAddress não é atualmente suportada através do portal do Azure.

Azure atualmente suporta DNS inverso apenas pelos recursos PublicIpAddress de IPv4. Não é suportada para IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Adicionar DNS inverso para um PublicIpAddresses existente

#### <a name="powershell"></a>PowerShell

Para adicionar DNS inverso para um PublicIpAddress existente:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Para adicionar DNS inverso para um PublicIpAddress existente que já não tem um nome DNS, também tem de especificar um nome DNS:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

Para adicionar DNS inverso para um PublicIpAddress existente:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Para adicionar DNS inverso para um PublicIpAddress existente que já não tem um nome DNS, também tem de especificar um nome DNS:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>CLI do Azure

Para adicionar DNS inverso para um PublicIpAddress existente:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Para adicionar DNS inverso para um PublicIpAddress existente que já não tem um nome DNS, também tem de especificar um nome DNS:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Criar um endereço IP público com o DNS inverso

Para criar um novo PublicIpAddress com a propriedade DNS reversa já especificada:

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

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>DNS inverso do modo de exibição para um PublicIpAddress existente

Para ver o valor configurado para um PublicIpAddress existente:

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

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Remover inversa de DNS de endereços IP públicos existentes

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


## <a name="configure-reverse-dns-for-cloud-services"></a>Configurar DNS inverso para serviços Cloud

Esta secção fornece instruções detalhadas sobre como configurar o DNS inversos para serviços em nuvem no modelo de implementação clássico, com o Azure PowerShell. Configurar DNS inverso para os serviços Cloud não é suportada através do portal do Azure, CLI clássica do Azure ou da CLI do Azure.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Adicionar DNS inversos para serviços Cloud existentes

Para adicionar um registo DNS inverso para um serviço Cloud existente:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Criar um serviço Cloud com o DNS inverso

Para criar um novo serviço Cloud com a propriedade DNS reversa já especificada:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Vista DNS inversos para serviços Cloud existentes

Para ver a propriedade DNS inversa para um serviço Cloud existente:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Remover inversa de DNS de serviços Cloud existentes

Para remover uma propriedade DNS inversa de um serviço Cloud existente:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>FAQ

### <a name="how-much-do-reverse-dns-records-cost"></a>Quanto inverter o custo de registos DNS?

Eles são gratuitos!  Não existe nenhum custo adicional para registos DNS inversos ou consultas.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Meu registos DNS inversos resolverá da internet?

Sim. Depois de definir a propriedade DNS inversa para o seu serviço do Azure, o Azure gere todas as delegações DNS e zonas DNS necessárias para garantir que o registo DNS reverso é resolvido para todos os utilizadores de Internet.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Os registos DNS inversos do padrão são criados para meus serviços do Azure?

Não. DNS inverso é um recurso opcional. Não existem registos DNS inversos padrão são criados se optar por não configurá-las.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>O que é o formato para o nome de domínio completamente qualificado (FQDN)?

FQDNs são especificados na ordem de frente e tem de ser terminadas por um ponto (por exemplo, "app1.contoso.com.").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>O que acontece se a verificação de validação do DNS inverso que especifiquei falha?

Quando a verificação de validação do DNS inversa falhar, a operação para configurar o registo DNS reverso falhará. Corrija o valor DNS reverso conforme necessário e tente novamente.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Pode configurar DNS inverso para o serviço de aplicações do Azure?

Não. DNS inversa não é suportada para o serviço de aplicações do Azure.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Pode configurar vários registos DNS inversos para o meu serviço do Azure?

Não. O Azure suporta um único registo DNS inverso para cada serviço Cloud do Azure ou um PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Pode configurar DNS inverso para obter recursos IPv6 PublicIpAddress?

Não. Azure atualmente suporta DNS inverso apenas para recursos PublicIpAddress de IPv4 e serviços em nuvem.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Posso enviar e-mails para domínios externos de meus serviços de computação do Azure?

A habilidade técnica de enviar o e-mail diretamente a partir de uma implementação do Azure depende do tipo de subscrição. Independentemente do tipo de subscrição, a Microsoft recomenda a utilização de serviços de reencaminhamento de correio fidedigna para enviar a saída de emails. Para obter mais detalhes, consulte [segurança avançada do Azure para o envio de E-mails – Novembro de 2017 atualização](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre DNS inverso, consulte [pesquisa reversa de DNS na Wikipédia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Saiba como [alojam a zona de pesquisa inversa para o seu intervalo IP atribuído por ISP no DNS do Azure](dns-reverse-dns-for-azure-services.md).

