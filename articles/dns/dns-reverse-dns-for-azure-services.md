---
title: DNS reverso para serviços do Azure-DNS do Azure
description: Com este roteiro de aprendizagem, comece Configurando pesquisas reversas de DNS para serviços hospedados no Azure.
services: dns
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: allensu
ms.openlocfilehash: 550ba617dec0359fd7d4e0bc309e411095de0d1e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211235"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Configurar o DNS reverso para serviços hospedados no Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este artigo explica como configurar pesquisas inversas de DNS para serviços hospedados no Azure.

Os serviços no Azure usam endereços IP atribuídos pelo Azure e de propriedade da Microsoft. Esses registros DNS reversos (registros PTR) devem ser criados nas zonas de pesquisa inversa de DNS de propriedade da Microsoft correspondentes. Este artigo explica como fazer isso.

Esse cenário não deve ser confundido com a capacidade de [hospedar as zonas de pesquisa inversa de DNS para seus intervalos de IP atribuídos no DNS do Azure](dns-reverse-dns-hosting.md). Nesse caso, os intervalos de IP representados pela zona de pesquisa inversa devem ser atribuídos à sua organização, normalmente pelo seu ISP.

Antes de ler este artigo, você deve estar familiarizado com esta [visão geral do DNS reverso e do suporte no Azure](dns-reverse-dns-overview.md).

No DNS do Azure, os recursos de computação (como máquinas virtuais, conjuntos de dimensionamento de máquinas virtuais ou clusters de Service Fabric) são expostos por meio de um recurso PublicIpAddress. As pesquisas de DNS reverso são configuradas usando a propriedade ' ReverseFqdn ' de PublicIpAddress.


O DNS reverso não tem suporte no momento para o serviço de Azure App.

## <a name="validation-of-reverse-dns-records"></a>Validação de registros DNS reversos

Terceiros não devem ser capazes de criar registros DNS reversos para seu mapeamento de serviço do Azure para seus domínios DNS. Para evitar isso, o Azure permite a criação de um registro DNS reverso em que o nome de domínio especificado no registro DNS reverso é o mesmo que ou resolve para o nome DNS ou o endereço IP de um PublicIpAddress ou serviço de nuvem na mesma assinatura do Azure.

Essa validação é executada somente quando o registro DNS reverso é definido ou modificado. A revalidação periódica não é executada.

Por exemplo: Suponha que o recurso PublicIpAddress tenha o nome DNS contosoapp1.northus.cloudapp.azure.com e o endereço IP 23.96.52.53. O ReverseFqdn para PublicIpAddress pode ser especificado como:
* O nome DNS para o PublicIpAddress, contosoapp1.northus.cloudapp.azure.com
* O nome DNS para um PublicIpAddress diferente na mesma assinatura, como contosoapp2.westus.cloudapp.azure.com
* Um nome DNS intuitivo, como app1.contoso.com, desde que esse nome seja configurado *pela primeira vez* como um CNAME para contosoapp1.NORTHUS.cloudapp.Azure.com ou para um PublicIpAddress diferente na mesma assinatura.
* Um nome DNS intuitivo, como app1.contoso.com, desde que esse nome seja configurado *pela primeira vez* como um registro a para o endereço IP 23.96.52.53 ou para o endereço IP de um PublicIpAddress diferente na mesma assinatura.

As mesmas restrições se aplicam ao DNS reverso para serviços de nuvem.


## <a name="reverse-dns-for-publicipaddress-resources"></a>DNS reverso para recursos PublicIpAddress

Esta seção fornece instruções detalhadas sobre como configurar o DNS reverso para recursos PublicIpAddress no modelo de implantação do Gerenciador de recursos, usando Azure PowerShell, CLI clássica do Azure ou CLI do Azure. A configuração do DNS reverso para recursos PublicIpAddress não tem suporte no momento por meio do portal do Azure.

Atualmente, o Azure dá suporte ao DNS reverso somente para recursos de PublicIpAddress IPv4. Não há suporte para IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Adicionar o DNS reverso a um PublicIpAddresses existente

#### <a name="powershell"></a>PowerShell

Para atualizar o DNS reverso para um PublicIpAddress existente:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Para adicionar o DNS reverso a um PublicIpAddress existente que ainda não tem um nome DNS, você também deve especificar um nome DNS:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

Para adicionar o DNS reverso a um PublicIpAddress existente:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Para adicionar o DNS reverso a um PublicIpAddress existente que ainda não tem um nome DNS, você também deve especificar um nome DNS:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>CLI do Azure

Para adicionar o DNS reverso a um PublicIpAddress existente:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Para adicionar o DNS reverso a um PublicIpAddress existente que ainda não tem um nome DNS, você também deve especificar um nome DNS:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Criar um endereço IP público com DNS reverso

Para criar um novo PublicIpAddress com a propriedade de DNS reverso já especificada:

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

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Exibir o DNS reverso para um PublicIpAddress existente

Para exibir o valor configurado para um PublicIpAddress existente:

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

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Remover o DNS reverso de Endereços IP Públicos existentes

Para remover uma propriedade de DNS reverso de um PublicIpAddress existente:

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


## <a name="configure-reverse-dns-for-cloud-services"></a>Configurar o DNS reverso para serviços de nuvem

Esta seção fornece instruções detalhadas sobre como configurar o DNS reverso para serviços de nuvem no modelo de implantação clássico, usando Azure PowerShell. Não há suporte para configurar o DNS reverso para serviços de nuvem por meio do portal do Azure, CLI clássica do Azure ou CLI do Azure.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Adicionar o DNS reverso aos serviços de nuvem existentes

Para adicionar um registro DNS reverso a um serviço de nuvem existente:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Criar um serviço de nuvem com DNS reverso

Para criar um novo serviço de nuvem com a propriedade DNS reverso já especificada:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Exibir o DNS reverso para serviços de nuvem existentes

Para exibir a propriedade de DNS reverso para um serviço de nuvem existente:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Remover o DNS reverso dos serviços de nuvem existentes

Para remover uma propriedade de DNS reverso de um serviço de nuvem existente:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>FAQ

### <a name="how-much-do-reverse-dns-records-cost"></a>Quanto custam os registros DNS reversos?

Eles são gratuitos!  Não há nenhum custo adicional para consultas ou registros DNS reversos.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Meus registros DNS reversos serão resolvidos da Internet?

Sim. Depois de definir a propriedade DNS reverso para seu serviço do Azure, o Azure gerencia todas as delegações de DNS e as zonas DNS necessárias para garantir que o registro DNS reverso seja resolvido para todos os usuários da Internet.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>São registros DNS reversos padrão criados para meus serviços do Azure?

Não. O DNS reverso é um recurso opcional. Nenhum registro DNS reverso padrão será criado se você optar por não configurá-los.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Qual é o formato do FQDN (nome de domínio totalmente qualificado)?

Os FQDNs são especificados em ordem de encaminhamento e devem ser terminados por um ponto (por exemplo, "app1.contoso.com.").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>O que acontece se a verificação de validação para o DNS reverso que especifiquei falhar?

Quando a verificação de validação de DNS reverso falha, a operação para configurar o registro de DNS reverso falha. Corrija o valor de DNS reverso conforme necessário e tente novamente.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Posso configurar o DNS reverso para o serviço Azure App?

Não. Não há suporte para o DNS reverso para o serviço Azure App.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Posso configurar vários registros DNS reversos para meu serviço do Azure?

Não. O Azure dá suporte a um único registro DNS reverso para cada serviço de nuvem do Azure ou PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Posso configurar o DNS reverso para recursos de PublicIpAddress IPv6?

Não. Atualmente, o Azure dá suporte ao DNS reverso somente para recursos de PublicIpAddress e serviços de nuvem IPv4.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Posso enviar emails para domínios externos de meus serviços de computação do Azure?

A capacidade técnica de enviar email diretamente de uma implantação do Azure depende do tipo de assinatura. Independentemente do tipo de assinatura, a Microsoft recomenda usar serviços de retransmissão de email confiáveis para enviar emails de saída. Para obter mais detalhes, consulte [segurança aprimorada do Azure para enviar emails – atualização de novembro de 2017](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o DNS reverso, consulte [pesquisa reversa de DNS na Wikipédia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Saiba como [hospedar a zona de pesquisa inversa para seu intervalo de IP atribuído pelo ISP no DNS do Azure](dns-reverse-dns-for-azure-services.md).

