---
title: 'Obterem tabelas ARP – resolução de problemas - ExpressRoute: Azure| Microsoft Docs'
description: Esta página fornece instruções sobre como obter o ARP tabelas para um circuito do ExpressRoute
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 01eac27b63f9eaaf62e863cd023201c3eab4b74e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432146"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Obter tabelas ARP no modelo de implementação do Resource Manager
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Clássica](expressroute-troubleshooting-arp-classic.md)
> 
> 

Este artigo orienta-o pelos passos para obter tabelas ARP para o seu circuito do ExpressRoute.

> [!IMPORTANT]
> Este documento destina-se para o ajudar a diagnosticar e resolver problemas de simples. Não se destina a ser uma substituição para o suporte da Microsoft. Tem de abrir um pedido de suporte [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se não conseguir resolver o problema através da orientação descrita abaixo.
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Protocolo ARP (resolução) e ARP tabelas de endereços
Protocolo de resolução de endereço (ARP) é um protocolo de camada 2 definido no [RFC 826](https://tools.ietf.org/html/rfc826). ARP é utilizado para mapear o endereço Ethernet (endereço MAC) com um endereço ip.

A tabela de ARP fornece um mapeamento do endereço ipv4 e o endereço MAC para um determinado peering. A tabela de ARP para um peering de circuito de ExpressRoute fornece as seguintes informações para cada interface de rede (primário e secundário)

1. Mapeamento de endereço ip de interface de router no local para o endereço MAC
2. Mapeamento de endereço ip de interface de router do ExpressRoute para o endereço MAC
3. Idade do mapeamento

Tabelas ARP podem ajudar a validar a configuração de camada 2 e resolução de problemas básicos de camada 2 problemas de conectividade. 

Tabela de ARP de exemplo: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


A secção seguinte fornece informações sobre como pode ver as tabelas de ARP vistas pelos routers de limite do ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Pré-requisitos para obter tabelas ARP
Certifique-se de que tem o seguinte antes de avançar ainda mais

* Um circuito de ExpressRoute válido configurado com, pelo menos, um peering. O circuito deve ser totalmente configurado pelo fornecedor de conectividade. Utilizador (ou o fornecedor de conectividade) tem de ter configurado, pelo menos, um dos peerings (Azure privado, Azure público e Microsoft) neste circuito.
* Intervalos de endereços IP utilizados para configurar os peerings (Azure privado, Azure público e Microsoft). Reveja os exemplos de atribuição de endereço ip no [página de requisitos de encaminhamento ExpressRoute](expressroute-routing.md) para obter uma compreensão de como os endereços ip são mapeados para interfaces do seu lado e no lado do ExpressRoute. Pode obter informações sobre a configuração do peering ao rever o [página de configuração de peering de ExpressRoute](expressroute-howto-routing-arm.md).
* Informações da sua equipa de rede / fornecedor de conectividade no endereço MAC da interfaces utilizada com estes endereços IP.
* Tem de ter o módulo do PowerShell mais recente para o Azure (versão 1.50 ou mais recente).

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Obter o ARP tabelas para o seu circuito do ExpressRoute
Esta secção fornece instruções sobre como pode ver as tabelas ARP por peering com o PowerShell. Utilizador ou o fornecedor de conectividade tem de ter configurado o peering antes de evoluir ainda mais. Cada circuito tem dois caminhos (primários e secundários). Pode verificar a tabela de ARP para cada caminho de forma independente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelas de ARP para peering privado do Azure
O seguinte cmdlet fornece o ARP tabelas para peering privado do Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Saída de exemplo é mostrada abaixo, para um dos caminhos

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabelas de ARP para peering público do Azure
O seguinte cmdlet fornece o ARP tabelas para peering público do Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Saída de exemplo é mostrada abaixo, para um dos caminhos

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabelas ARP para peering da Microsoft
O seguinte cmdlet fornece o ARP tabelas para peering da Microsoft

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Saída de exemplo é mostrada abaixo, para um dos caminhos

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Como utilizar estas informações
A tabela de ARP de um peering pode ser utilizada para determinar validar configuração de camada 2 e a conectividade. Esta seção fornece uma visão geral de como tabelas ARP ficarão em diferentes cenários.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabela de ARP quando um circuito está no estado operacional (estado esperado)
* A tabela de ARP terão uma entrada para o lado no local com um endereço IP válido e o endereço MAC e uma entrada semelhante para o lado da Microsoft. 
* O último octeto do endereço ip no local será sempre um número ímpar.
* O último octeto do endereço de ip Microsoft será sempre um número par.
* O mesmo endereço MAC será apresentado no lado do Microsoft para todos os 3 peerings (primários / secundários). 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabela de ARP quando no local / lado de fornecedor de conectividade tem problemas
Se existirem problemas com no local ou o fornecedor de conectividade, que poderá ver que seja apenas uma entrada irá aparecer na tabela de ARP ou o endereço de MAC no local irá mostrar incompleta. Isto irá mostrar o mapeamento entre o endereço MAC e o endereço IP utilizado no lado do Microsoft. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

ou
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Abra um pedido de suporte junto do seu fornecedor de conectividade para depurar esses problemas. Se a tabela de ARP não tem endereços IP das interfaces mapeados para os endereços MAC, reveja as seguintes informações:
> 
> 1. Se o primeiro endereço IP de/30 sub-rede atribuída para a ligação entre o MSEE-PR e MSEE é utilizada na interface do MSEE-Pr. O Azure utiliza sempre o segundo endereço IP para MSEEs.
> 2. Certifique-se de que se o cliente (C-etiqueta) e etiquetas de VLAN de serviço (S-etiqueta) coincidir ambos num par de MSEE-PR e MSEE.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabela de ARP ao lado da Microsoft tem problemas
* Não verá uma tabela de ARP mostrada para um peering se existirem problemas no lado do Microsoft. 
* Abra um pedido de suporte [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Especifique a que tem um problema com a conectividade de camada 2. 

## <a name="next-steps"></a>Próximos Passos
* Validar configurações de camada 3 para o seu circuito do ExpressRoute
  * Obter a rota resumo para determinar o estado de sessões BGP 
  * Obter a tabela de rotas para determinar os prefixos são anunciados através de ExpressRoute
* Validar a transferência de dados, revendo os bytes de entrada / saída
* Abra um pedido de suporte [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se ainda ocorrerem problemas.

