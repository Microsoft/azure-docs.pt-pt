---
title: 'Obter tabelas ARP-solução de problemas – ExpressRoute: Azure | Microsoft Docs'
description: Esta página fornece instruções sobre como obter as tabelas ARP para um circuito do ExpressRoute
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: e35020923405ec072ac9c42093752ec5a9290824
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748157"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Obtendo tabelas ARP no modelo de implantação do Gerenciador de recursos
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Clássica](expressroute-troubleshooting-arp-classic.md)
> 
> 

Este artigo orienta você pelas etapas para aprender as tabelas ARP para o circuito do ExpressRoute.

> [!IMPORTANT]
> Este documento destina-se a ajudá-lo a diagnosticar e corrigir problemas simples. Não pretende ser uma substituição para o suporte da Microsoft. Você deve abrir um tíquete de suporte com o [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se não for possível resolver o problema usando as diretrizes descritas abaixo.
> 
> 

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>ARP (protocolo de resolução de endereço) e tabelas ARP
O ARP (protocolo de resolução de endereço) é um protocolo de camada 2 definido no [RFC 826](https://tools.ietf.org/html/rfc826). O ARP é usado para mapear o endereço Ethernet (endereço MAC) com um endereço IP.

A tabela ARP fornece um mapeamento do endereço IPv4 e do endereço MAC para um emparelhamento específico. A tabela ARP para um emparelhamento de circuito do ExpressRoute fornece as seguintes informações para cada interface (primária e secundária)

1. Mapeamento do endereço IP da interface do roteador local para o endereço MAC
2. Mapeamento do endereço IP da interface do roteador do ExpressRoute para o endereço MAC
3. Idade do mapeamento

As tabelas ARP podem ajudar a validar a configuração da camada 2 e solucionar problemas de conectividade básicos da camada 2. 

Exemplo de tabela ARP: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


A seção a seguir fornece informações sobre como você pode exibir as tabelas ARP vistas pelos roteadores de borda do ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Pré-requisitos para aprender tabelas ARP
Verifique se você tem o seguinte antes de progredir ainda mais

* Um circuito de ExpressRoute válido configurado com pelo menos um emparelhamento. O circuito deve ser totalmente configurado pelo provedor de conectividade. Você (ou seu provedor de conectividade) deve ter configurado pelo menos um dos emparelhamentos (privado do Azure, público do Azure e Microsoft) nesse circuito.
* Intervalos de endereços IP usados para configurar os emparelhamentos (privado do Azure, público do Azure e Microsoft). Examine os exemplos de atribuição de endereço IP na [página de requisitos de roteamento do expressroute](expressroute-routing.md) para entender como os endereços IP são mapeados para interfaces no seu lado e no lado do ExpressRoute. Você pode obter informações sobre a configuração de emparelhamento revisando a [página de configuração de emparelhamento do ExpressRoute](expressroute-howto-routing-arm.md).
* Informações de sua equipe de rede/provedor de conectividade nos endereços MAC das interfaces usadas com esses endereços IP.
* Você deve ter o módulo mais recente do PowerShell para o Azure (versão 1,50 ou mais recente).

> [!NOTE]
> Se a camada 3 for fornecida pelo provedor de serviços e as tabelas ARP estiverem em branco no portal/saída abaixo, atualize a configuração de circuito usando o botão Atualizar no Portal. Esta operação aplicará a configuração de roteamento correta em seu circuito. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Obtendo as tabelas ARP para o circuito do ExpressRoute
Esta seção fornece instruções sobre como você pode exibir as tabelas ARP por emparelhamento usando o PowerShell. Você ou seu provedor de conectividade deve ter configurado o emparelhamento antes de avançar. Cada circuito tem dois caminhos (primário e secundário). Você pode verificar a tabela ARP para cada caminho de forma independente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelas ARP para emparelhamento privado do Azure
O cmdlet a seguir fornece as tabelas ARP para o emparelhamento privado do Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

A saída de exemplo é mostrada abaixo para um dos caminhos

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabelas ARP para emparelhamento público do Azure
O cmdlet a seguir fornece as tabelas ARP para o emparelhamento público do Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


A saída de exemplo é mostrada abaixo para um dos caminhos

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabelas ARP para emparelhamento da Microsoft
O cmdlet a seguir fornece as tabelas ARP para o emparelhamento da Microsoft

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


A saída de exemplo é mostrada abaixo para um dos caminhos

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Como usar essas informações
A tabela ARP de um emparelhamento pode ser usada para determinar a configuração e a conectividade da camada 2. Esta seção fornece uma visão geral de como as tabelas ARP ficarão em cenários diferentes.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabela ARP quando um circuito está no estado operacional (estado esperado)
* A tabela ARP terá uma entrada para o lado local com um endereço IP válido e um endereço MAC e uma entrada semelhante para o lado da Microsoft. 
* O último octeto do endereço IP local será sempre um número ímpar.
* O último octeto do endereço IP da Microsoft sempre será um número par.
* O mesmo endereço MAC será exibido no lado da Microsoft para todos os três emparelhamentos (primário/secundário). 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabela ARP quando o lado do provedor de conectividade/local tiver problemas
Se houver problemas com o provedor local ou de conectividade, você poderá ver que apenas uma entrada aparecerá na tabela ARP ou o endereço MAC local será mostrado incompleto. Isso mostrará o mapeamento entre o endereço MAC e o endereço IP usado no lado da Microsoft. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

ou
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Abra uma solicitação de suporte com seu provedor de conectividade para depurar esses problemas. Se a tabela ARP não tiver endereços IP das interfaces mapeadas para endereços MAC, examine as seguintes informações:
> 
> 1. Se o primeiro endereço IP da sub-rede/30 atribuído para o link entre MSEE-PR e MSEE for usado na interface de MSEE-PR. O Azure sempre usa o segundo endereço IP para MSEEs.
> 2. Verifique se as marcas de VLAN Customer (C-tag) e Service (S-tag) correspondem ambas no par MSEE-PR e MSEE.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabela ARP quando o lado da Microsoft tiver problemas
* Você não verá uma tabela ARP mostrada para um emparelhamento se houver problemas no lado da Microsoft. 
* Abra um tíquete de suporte com [o suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Especifique que você tem um problema com conectividade de camada 2. 

## <a name="next-steps"></a>Próximos Passos
* Validar as configurações de camada 3 para o circuito do ExpressRoute
  * Obter o resumo da rota para determinar o estado das sessões BGP 
  * Obter tabela de rotas para determinar quais prefixos são anunciados no ExpressRoute
* Validar a transferência de dados revisando bytes de entrada/saída
* Abra um tíquete de suporte com [o suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se você ainda estiver enfrentando problemas.

