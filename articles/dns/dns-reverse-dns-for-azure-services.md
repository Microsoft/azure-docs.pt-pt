---
title: DNS invertidos para serviços Azure - Azure DNS
description: Com este caminho de aprendizagem, começar a configurar as procuras de DNS inversas para serviços hospedados em Azure.
services: dns
documentationcenter: na
author: rohinkoul
manager: KumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 073e84ece11f6817bfe2c5a94735ec6e16dac4fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76932374"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Configure DNS invertido para serviços hospedados em Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este artigo explica como configurar as procuras inversas de DNS para os serviços hospedados em Azure.

Os serviços em Azure utilizam endereços IP atribuídos pelo Azure e propriedade da Microsoft. Estes registos DNS invertidos (registos PTR) devem ser criados nas correspondentes zonas de procura de DNS inversas da Microsoft. Este artigo explica como fazer isto.

Este cenário não deve ser confundido com a capacidade de hospedar as zonas de [procura ção DNS inversas para as suas gamas IP atribuídas em Azure DNS](dns-reverse-dns-hosting.md). Neste caso, as gamas IP representadas pela zona de procura inversa devem ser atribuídas à sua organização, tipicamente pelo seu ISP.

Antes de ler este artigo, deve estar familiarizado com esta [visão geral do DNS invertido e do suporte em Azure](dns-reverse-dns-overview.md).

No Azure DNS, os recursos computacionais (como máquinas virtuais, conjuntos de escala de máquinas virtuais ou clusters de tecido de serviço) são expostos através de um recurso PublicIpAddress. As upups DNS inversas são configuradas utilizando a propriedade 'ReverseFqdn' do PublicIpAddress.


O DNS inverso não é atualmente suportado para o Serviço de Aplicações Azure.

## <a name="validation-of-reverse-dns-records"></a>Validação de registos dNS invertidos

Um terceiro não deve ser capaz de criar registos DNS invertidos para o seu mapeamento de serviço Azure para os seus domínios DNS. Para evitar isto, o Azure só permite a criação de um registo DNS inverso onde o nome de domínio especificado no registo dNS invertido é o mesmo que, ou resolve, o nome DNS ou endereço IP de um Serviço De Nuvem PublicIpAddress ou Cloud na mesma subscrição Azure.

Esta validação só é efetuada quando o registo dNS invertido é definido ou modificado. A revalidação periódica não é realizada.

Por exemplo: suponha que o recurso PublicIpAddress tem o nome DNS contosoapp1.northus.cloudapp.azure.com e endereço IP 23.96.52.53. O ReverseFqdn para o PublicIpAddress pode ser especificado como:
* O nome DNS para o PublicIpAddress, contosoapp1.northus.cloudapp.azure.com
* O nome DNS para um PublicIpAddress diferente na mesma subscrição, como contosoapp2.westus.cloudapp.azure.com
* Um nome DNS de vaidade, como app1.contoso.com, desde que este nome seja configurado *pela primeira vez* como Um CNAME para contosoapp1.northus.cloudapp.azure.com, ou para um PublicIpAddress diferente na mesma subscrição.
* Um nome DNS de vaidade, como app1.contoso.com, desde que este nome seja configurado *pela primeira vez* como um registo a do endereço IP 23.96.52.53, ou para o endereço IP de um diferente PublicIpAddress na mesma subscrição.

Os mesmos constrangimentos aplicam-se ao DNS invertido para serviços em nuvem.


## <a name="reverse-dns-for-publicipaddress-resources"></a>DNS invertidos para recursos PublicIpAddress

Esta secção fornece instruções detalhadas para configurar dNS invertidos para recursos PublicIpAddress no modelo de implementação do Gestor de Recursos, utilizando o Azure PowerShell, o Azure classic CLI ou o Azure CLI. Configurar dNS invertidos para recursos PublicIpAddress não é atualmente suportado através do portal Azure.

Atualmente, o Azure suporta o DNS invertido apenas para os recursos do IPv4 PublicIpAddress. Não é suportado para o IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Adicione DNS invertido a um PublicIpAddresss existente

#### <a name="powershell"></a>PowerShell

Para atualizar o DNS revertês para um Endereço Público existente:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Para adicionar DNS invertido a um PublicIpAddress existente que ainda não tem um nome DNS, também deve especificar um nome DNS:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

Para adicionar DNS invertido a um Endereço Público existente:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Para adicionar DNS invertido a um PublicIpAddress existente que ainda não tem um nome DNS, também deve especificar um nome DNS:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>CLI do Azure

Para adicionar DNS invertido a um Endereço Público existente:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Para adicionar DNS invertido a um PublicIpAddress existente que ainda não tem um nome DNS, também deve especificar um nome DNS:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Criar um endereço IP público com DNS invertidos

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

Para ver o valor configurado para um Endereço Público existente:

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

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Remover DNS invertidos dos endereços IP públicos existentes

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


## <a name="configure-reverse-dns-for-cloud-services"></a>Configure dNS invertidos para serviços em nuvem

Esta secção fornece instruções detalhadas para configurar dNS invertidos para serviços de nuvem no modelo de implementação Classic, utilizando o Azure PowerShell. Configurar DNS invertidos para Serviços em Nuvem não é suportado através do portal Azure, do clássico CLI do Azure ou do Azure CLI.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Adicione DNS invertido sairá aos serviços de nuvem existentes

Para adicionar um registo dNS invertido a um serviço de nuvem existente:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Criar um Serviço de Nuvem com DNS invertidos

Para criar um novo Serviço cloud com a propriedade DNS inversa já especificada:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Ver DNS invertidos para os serviços de nuvem existentes

Para visualizar a propriedade DNS inversa para um serviço de nuvem existente:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Remova o DNS invertido dos serviços de nuvem existentes

Para remover uma propriedade DNS inversa de um serviço de nuvem existente:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>FAQ

### <a name="how-much-do-reverse-dns-records-cost"></a>Quanto custam os registos inversos do DNS?

São de graça!  Não há custos adicionais para registos ou consultas de DNS invertidos.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Os meus registos dNS invertidos vão resolver-se a partir da internet?

Sim. Uma vez que você estabelece a propriedade DNS inversa para o seu serviço Azure, o Azure gere todas as delegações dNS e zonas DNS necessárias para garantir que o registo inverso de DNS resolve para todos os utilizadores da Internet.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Os registos dNS invertidos são criados para os meus serviços Azure?

Não. O DNS invertido é uma funcionalidade de opt-in. Não são criados registos DNS invertidos predefinidos se optar por não configurá-los.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Qual é o formato para o nome de domínio totalmente qualificado (FQDN)?

As FQDNs são especificadas por ordem avançada e devem ser terminadas por um ponto (por exemplo, "app1.contoso.com.").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>O que acontece se a verificação de validação do DNS invertido que especifiquei falhar?

Quando a verificação de validação dNS inversa falhar, a operação para configurar o registo dNS invertido falha. Corrija o valor dNS invertido conforme necessário, e tente novamente.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Posso configurar o DNS invertido para o Serviço de Aplicações Azure?

Não. O DNS inverso não é suportado para o Serviço de Aplicações Azure.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Posso configurar vários registos dNS invertidos para o meu serviço Azure?

Não. O Azure suporta um único registo dNS invertido para cada Serviço de Nuvem Azure ou PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Posso configurar dNS invertidos para recursos IPv6 PublicIpAddress?

Não. Atualmente, o Azure suporta dNS invertidos apenas para recursos IPv4 PublicIpAddress e Serviços cloud.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Posso enviar e-mails para domínios externos dos meus serviços da Azure Compute?

A capacidade técnica de enviar e-mail diretamente de uma implementação do Azure depende do tipo de subscrição. Independentemente do tipo de subscrição, a Microsoft recomenda a utilização de serviços de retransmissão de correio fidedigno para enviar correio de saída. Para mais detalhes, consulte [A Segurança Azure Avançada para o envio de Emails – Atualização de novembro de 2017.](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre dNS invertidos, consulte a [procura inversa de DNS na Wikipédia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Saiba como hospedar a zona de procura inversa para a sua gama IP atribuída ao [ISP em Azure DNS](dns-reverse-dns-for-azure-services.md).

