---
title: 'Azure ExpressRoute: Tabelas ARP - Resolução de Problemas'
description: Esta página fornece instruções para obter as tabelas ARP para um circuito ExpressRoute
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 4f1bd064dbc0909be3deba9180be1d8b3c066fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076570"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Obter tabelas ARP no modelo de implementação do Gestor de Recursos
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell - Clássico](expressroute-troubleshooting-arp-classic.md)
> 
> 

Este artigo percorre-o através dos degraus para aprender as tabelas ARP para o seu circuito ExpressRoute.

> [!IMPORTANT]
> Este documento destina-se a ajudá-lo a diagnosticar e corrigir problemas simples. Não se destina a ser um substituto para o suporte da Microsoft. Tem de abrir um bilhete de suporte com suporte à [Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se não conseguir resolver o problema utilizando as orientações descritas abaixo.
> 
> 

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Protocolo de Resolução de Endereços (ARP) e tabelas ARP
O Protocolo de Resolução de Endereços (ARP) é um protocolo de camada 2 definido no [RFC 826](https://tools.ietf.org/html/rfc826). O ARP é utilizado para mapear o endereço Ethernet (endereço MAC) com um endereço IP.

A tabela ARP fornece um mapeamento do endereço ipv4 e endereço MAC para um determinado olhar. A tabela ARP para um circuito ExpressRoute peering fornece as seguintes informações para cada interface (primária e secundária)

1. Mapeamento do endereço IP de interface de router no local para o endereço MAC
2. Mapeamento do endereço IP da interface do router ExpressRoute para o endereço MAC
3. Idade do mapeamento

As tabelas ARP podem ajudar a validar a configuração da camada 2 e resolver problemas problemas de conectividade de camada 2. 

Exemplo Tabela ARP: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


A secção seguinte fornece informações sobre como pode ver as tabelas ARP vistas pelos routers de borda ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Pré-requisitos para a aprendizagem das tabelas ARP
Certifique-se de que tem o seguinte antes de progredir ainda mais

* Um circuito ExpressRoute válido configurado com pelo menos um olhar. O circuito deve ser totalmente configurado pelo fornecedor de conectividade. Você (ou o seu fornecedor de conectividade) deve ter configurado pelo menos um dos pares (Azure private, Azure público e Microsoft) neste circuito.
* Gamas de endereços IP utilizadas para configurar os pares (Azure private, Azure public e Microsoft). Reveja os exemplos de atribuição de endereços ip na página de requisitos de [encaminhamento expressRoute](expressroute-routing.md) para obter uma compreensão de como os endereços IP são mapeados para interfaces do seu lado e do lado expressRoute. Pode obter informações sobre a configuração de peering revendo a página de configuração de [peering ExpressRoute](expressroute-howto-routing-arm.md).
* Informações da sua equipa de networking/fornecedor de conectividade nos endereços MAC das interfaces utilizadas com estes endereços IP.
* Deve ter o mais recente módulo PowerShell para o Azure (versão 1.50 ou mais recente).

> [!NOTE]
> Se a camada 3 for fornecida pelo prestador de serviços e as tabelas ARP estiverem em branco no portal/saída abaixo, refresque a configuração do Circuito utilizando o botão de atualização no portal. Esta operação aplicará a configuração de encaminhamento certa no seu circuito. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Obter as tabelas ARP para o seu circuito ExpressRoute
Esta secção fornece instruções sobre como pode ver as tabelas ARP por observação utilizando o PowerShell. Você ou o seu fornecedor de conectividade devem ter configurado o epeering antes de progredir mais. Cada circuito tem dois caminhos (primário e secundário). Pode consultar a tabela ARP para cada caminho de forma independente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelas ARP para o peering privado Azure
O seguinte cmdlet fornece as tabelas ARP para o peering privado Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

A saída da amostra é mostrada abaixo para um dos caminhos

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Mesas ARP para o público azure espreitando
O seguinte cmdlet fornece as tabelas ARP para o público Azure peering

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


A saída da amostra é mostrada abaixo para um dos caminhos

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabelas ARP para o peering da Microsoft
O seguinte cmdlet fornece as tabelas ARP para o peering da Microsoft

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


A saída da amostra é mostrada abaixo para um dos caminhos

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Como usar esta informação
A tabela ARP de um epeering pode ser usada para determinar a configuração e conectividade validadas da camada 2. Esta secção fornece uma visão geral de como as tabelas ARP serão vistas em diferentes cenários.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabela ARP quando um circuito está em estado operacional (estado esperado)
* A tabela ARP terá uma entrada para o lado no local com um endereço IP válido e endereço MAC e uma entrada semelhante para o lado da Microsoft. 
* O último octeto do endereço IP no local será sempre um número ímpar.
* O último octeto do endereço ip da Microsoft será sempre um número par.
* O mesmo endereço MAC aparecerá no lado da Microsoft para todos os 3 pares (primário/secundário). 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabela ARP quando no local / lado do fornecedor de conectividade tem problemas
Se houver problemas com o fornecedor de conectividade no local ou com o fornecedor de conectividade, poderá ver que apenas uma entrada aparecerá na tabela ARP ou no endereço MAC no local no local mostrar-se-á incompleta. Isto mostrará o mapeamento entre o endereço MAC e o endereço IP utilizado no lado da Microsoft. 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

ou
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> Abra um pedido de apoio com o seu fornecedor de conectividade para desinventar tais problemas. Se a tabela ARP não tiver endereços IP das interfaces mapeadas para endereços MAC, reveja as seguintes informações:
> 
> 1. Se o primeiro endereço IP da sub-rede /30 atribuído para a ligação entre o MSEE-PR e o MSEE for utilizado na interface do MSEE-PR. O Azure utiliza sempre o segundo endereço IP para MSEEs.
> 2. Verifique se as etiquetas VLAN do cliente (C-Tag) e do serviço (S-Tag) correspondem tanto ao par MSEE-PR como ao MSEE.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabela ARP quando lado da Microsoft tem problemas
* Não verá uma tabela ARP mostrada para espreitar se houver problemas do lado da Microsoft. 
* Abra um bilhete de suporte com suporte da [Microsoft.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Especifique que tem um problema com a conectividade da camada 2. 

## <a name="next-steps"></a>Passos Seguintes
* Valide as configurações da Camada 3 para o seu circuito ExpressRoute
  * Obtenha resumo da rota para determinar o estado das sessões de BGP 
  * Obtenha tabela de rotas para determinar quais prefixos são anunciados através do ExpressRoute
* Validar a transferência de dados através da revisão de bytes dentro /fora
* Abra um bilhete de suporte com suporte da [Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se ainda estiver com problemas.

