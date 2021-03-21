---
title: 'Azure ExpressRoute: tabelas ARP - Resolução de problemas'
description: Esta página fornece instruções para obter as tabelas do Protocolo de Resolução de Endereços (ARP) para um circuito ExpressRoute
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 12/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 7d8ae2c58979c66ebbbab366d172179bdeee4253
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97561584"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Obtenção de tabelas ARP no modelo de implementação do Gestor de Recursos
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell - Clássico](expressroute-troubleshooting-arp-classic.md)
> 
> 

Este artigo percorre os degraus para aprender as tabelas ARP para o seu circuito ExpressRoute.

> [!IMPORTANT]
> Este documento destina-se a ajudá-lo a diagnosticar e corrigir problemas simples. Não se pretende que seja um substituto para o suporte da Microsoft. Tem de abrir um bilhete de suporte com suporte da [Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se não conseguir resolver o problema utilizando as orientações descritas abaixo.
> 
> 

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Tabelas de Protocolo de Resolução de Endereços (ARP) e ARP
Address Resolution Protocol (ARP) é um protocolo de camada 2 definido no [RFC 826](https://tools.ietf.org/html/rfc826). O ARP é utilizado para mapear o endereço Ethernet (endereço MAC) com um endereço IP.

A tabela ARP fornece as seguintes informações tanto para as interfaces primárias como secundárias para cada tipo de pares:

1. Mapeamento do endereço IP da interface do router no local para o endereço MAC
2. Mapeamento do endereço IP da interface do router ExpressRoute para o endereço MAC
3. Idade do mapeamento

As tabelas ARP podem ajudar a validar a configuração da camada 2 e resolver problemas básicos de conectividade da camada 2. 

Tabela ARP exemplo: 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


A secção seguinte fornece informações sobre como pode ver as tabelas ARP vistas pelos routers de borda ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Pré-requisitos para a aprendizagem das tabelas ARP
Certifique-se de que as informações abaixo são verdadeiras antes de progredir mais:

* Um circuito ExpressRoute válido configurado com pelo menos um espreitando. O circuito deve ser totalmente configurado pelo fornecedor de conectividade. Você ou o seu fornecedor de conectividade devem ter configurado pelo menos Azure privado, Azure público, ou Microsoft espreitando neste circuito.
* Intervalos de endereço IP utilizados para configurar os seus pares. Reveja os exemplos de atribuição de endereço ip na página de [requisitos de encaminhamento ExpressRoute](expressroute-routing.md) para entender como os endereços IP são mapeados para interfaces. Pode obter informações sobre a configuração de peering, revendo a [página de configuração de peering ExpressRoute](expressroute-howto-routing-arm.md).
* Informações da sua equipa de networking/fornecedor de conectividade nos endereços MAC das interfaces utilizadas com estes endereços IP.
* Deve ter o mais recente módulo PowerShell para Azure (versão 1.50 ou mais recente).

> [!NOTE]
> Se a camada 3 for fornecida pelo prestador de serviços e as tabelas ARP estiverem em branco no portal/saída abaixo, refresque a configuração do Circuito utilizando o botão de atualização no portal. Esta operação aplicará a configuração de encaminhamento correta no seu circuito. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Obter as tabelas ARP para o seu circuito ExpressRoute
Esta secção fornece instruções sobre como ver as tabelas ARP por espreitar utilizando o PowerShell. Você ou o seu fornecedor de conectividade devem ter configurado o espreitamento antes de progredir mais. Cada circuito tem dois caminhos (primário e secundário). Pode verificar a tabela ARP para cada caminho de forma independente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelas ARP para Azure private peering
O cmdlet seguinte fornece as tabelas ARP para o azure private peering

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure private peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

# ARP table for Azure private peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 
```

A saída da amostra é mostrada abaixo para um dos caminhos

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>Tabelas ARP para olhando público Azure
O cmdlet seguinte fornece as tabelas ARP para o azure olhar público

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure public peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

# ARP table for Azure public peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 
```


A saída da amostra é mostrada abaixo para um dos caminhos

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>Tabelas ARP para olhando a Microsoft
O cmdlet seguinte fornece as tabelas ARP para o espreguiços da Microsoft

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Microsoft peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

# ARP table for Microsoft peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 
```


A saída da amostra é mostrada abaixo para um dos caminhos

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>Como usar esta informação
A tabela ARP de um espreite pode ser usada para determinar a validação da configuração e conectividade da camada 2. Esta secção fornece uma visão geral de como as tabelas ARP vão olhar em diferentes cenários.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabela ARP quando um circuito está em estado operacional (estado esperado)
* A tabela ARP terá uma entrada para o lado no local com um endereço IP válido e endereço MAC. O mesmo pode ser visto para o lado da Microsoft. 
* O último octeto do endereço IP no local será sempre um número ímpar.
* O último octeto do endereço IP da Microsoft será sempre um número par.
* O mesmo endereço MAC aparecerá no lado da Microsoft para os três pares (primário/secundário). 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabela ARP quando há problemas de conectividade no lado do servidor/no local
Se ocorrer um problema com o operador de conectividade ou com o fornecedor de conectividade, a tabela ARP mostrará uma de duas coisas. Ou verá o endereço MAC no local incompleto ou só verá a entrada da Microsoft na tabela ARP.
  
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------   
  0 On-Prem           65.0.0.1   Incomplete
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```
ou
   
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```  

> [!NOTE]
> Abra um pedido de apoio com o seu fornecedor de conectividade para depurar tais problemas. Se a tabela ARP não tiver endereços IP das interfaces mapeadas para endereços MAC, reveja as seguintes informações:
> 
> 1. Se for utilizado na interface do MSEE-PR o primeiro endereço IP da sub-rede /30 atribuído para a ligação entre o MSEE-PR e o MSEE. O Azure utiliza sempre o segundo endereço IP para MSEEs.
> 2. Verifique se as etiquetas VLAN (C-Tag) e de serviço (S-Tag) correspondem tanto no par MSEE-PR como no MSEE.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabela ARP quando o lado da Microsoft tem problemas
* Não verá uma tabela ARP mostrada para um olhar se houver problemas no lado da Microsoft. 
* Abra um bilhete de apoio com [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Especifique que tem um problema com a conectividade da camada 2. 

## <a name="next-steps"></a>Passos Seguintes
* Validar as configurações da Camada 3 para o seu circuito ExpressRoute.
  * Obtenha o resumo da rota para determinar o estado das sessões de BGP.
  * Obtenha a tabela de rotas para determinar quais prefixos são anunciados através do ExpressRoute.
* Validar a transferência de dados através da revisão de bytes dentro/fora.
* Abra um bilhete de apoio com [o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) da Microsoft se ainda tiver problemas.

