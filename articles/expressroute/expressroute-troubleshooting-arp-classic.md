---
title: 'Obter ARP ExpressRoute de resolução de problemas do tabelas -: clássico: Azure| Microsoft Docs'
description: Esta página fornece instruções para obter tabelas ARP de um circuito do ExpressRoute - modelo de implementação clássica.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 3e49a1da0e8ea83faf5fc5a10d4c01a41d62fa88
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439643"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>Obter tabelas ARP no modelo de implementação clássica
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Clássica](expressroute-troubleshooting-arp-classic.md)
> 
> 

Este artigo orienta-o através dos passos para obter as tabelas de protocolo de resolução de endereço (ARP) para o seu circuito do ExpressRoute do Azure.

> [!IMPORTANT]
> Este documento destina-se para o ajudar a diagnosticar e resolver problemas de simples. Não se destina a ser uma substituição para o suporte da Microsoft. Se não é possível resolver o problema ao utilizar as seguintes orientações, abra um pedido de suporte com [Microsoft Azure ajuda + suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Protocolo ARP (resolução) e ARP tabelas de endereços
ARP é um protocolo de camada 2 que é definido no [RFC 826](https://tools.ietf.org/html/rfc826). ARP é utilizado para mapear um endereço Ethernet (endereço MAC) para um endereço IP.

Uma tabela de ARP fornece um mapeamento do endereço IPv4 e o endereço MAC para um determinado peering. A tabela de ARP para um peering de circuito de ExpressRoute fornece as seguintes informações para cada interface de rede (primário e secundário):

1. Mapeamento de um endereço IP de interface router no local para um endereço MAC
2. Mapeamento de um endereço IP do interface do router do ExpressRoute para um endereço MAC
3. A idade do mapeamento

Tabelas ARP podem ajudar com a validar a configuração de camada 2 e resolução de problemas de conectividade de camada 2 básicos.

Segue-se um exemplo de uma tabela de ARP:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


A secção seguinte fornece informações sobre como ver as tabelas de ARP são vistas pelos routers de limite do ExpressRoute.

## <a name="prerequisites-for-using-arp-tables"></a>Pré-requisitos para utilizar tabelas ARP
Certifique-se de que tem o seguinte antes de continuar:

* Um circuito do ExpressRoute válido que esteja configurado com, pelo menos, um peering. O circuito deve ser totalmente configurado pelo fornecedor de conectividade. Utilizador (ou o fornecedor de conectividade) tem de configurar, pelo menos, um dos peerings (Azure privado, Azure público ou Microsoft) neste circuito.
* Intervalos de endereços IP que são utilizados para configurar os peerings (Azure privado, Azure público e Microsoft). Reveja os exemplos de atribuição de endereço IP no [página de requisitos de encaminhamento ExpressRoute](expressroute-routing.md) para obter uma compreensão de como os endereços IP são mapeados para interfaces do seu lado e no lado do ExpressRoute. Pode obter informações sobre a configuração do peering ao rever o [página de configuração de peering de ExpressRoute](expressroute-howto-routing-classic.md).
* Informações do fornecedor de equipa ou conectividade de rede sobre os endereços MAC das interfaces que são utilizadas com estes endereços IP.
* O módulo Windows PowerShell mais recente para o Azure (versão 1.50 ou posterior).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Tabelas ARP para o seu circuito do ExpressRoute
Esta secção fornece instruções sobre como ver as tabelas ARP para cada tipo de peering com o PowerShell. Antes de continuar, por si ou pelo seu fornecedor de conectividade são tem de configurar o peering. Cada circuito tem dois caminhos (primários e secundários). Pode verificar a tabela de ARP para cada caminho de forma independente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelas de ARP para peering privado do Azure
O seguinte cmdlet fornece o ARP tabelas para peering privado do Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Segue-se a saída de exemplo para um dos caminhos:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabelas de ARP para peering público do Azure:
O seguinte cmdlet fornece o ARP tabelas para peering público do Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Segue-se a saída de exemplo para um dos caminhos:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Segue-se a saída de exemplo para um dos caminhos:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabelas ARP para peering da Microsoft
O seguinte cmdlet fornece o ARP tabelas para peering da Microsoft:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Saída de exemplo é mostrada abaixo, para um dos caminhos:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Como utilizar estas informações
A tabela de ARP de um peering pode ser utilizada para validar a configuração de camada 2 e a conectividade. Esta seção fornece uma visão geral de como a aparência de tabelas ARP em cenários diferentes.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>Tabela de ARP quando um circuito está num estado operacional (esperado)
* A tabela de ARP tem uma entrada para o lado no local com um endereço IP e MAC válido e uma entrada semelhante para o lado da Microsoft.
* O último octeto do endereço IP no local é sempre um número ímpar.
* O último octeto do endereço IP da Microsoft é sempre um número par.
* O mesmo endereço MAC é apresentado no lado do Microsoft para todos os três peerings (primária/secundária).

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Tabela de ARP quando está no local ou quando o lado de fornecedor de conectividade tem problemas
 É apresentada apenas uma entrada na tabela de ARP. Mostra o mapeamento entre o endereço MAC e o endereço IP que é utilizado no lado do Microsoft.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> Se ocorrer um problema semelhante a esta, abra um pedido de suporte junto do seu fornecedor de conectividade para resolvê-lo.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Tabela de ARP ao lado da Microsoft tem problemas
* Não verá uma tabela de ARP mostrada para um peering se existirem problemas no lado do Microsoft.
* Abra um pedido de suporte com [Microsoft Azure ajuda + suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Especifique a que tem um problema com a conectividade de camada 2.

## <a name="next-steps"></a>Passos Seguintes
* Valide configurações de camada 3 para o seu circuito do ExpressRoute:
  * Obtenha uma rota de resumo para determinar o estado de sessões BGP.
  * Obtenha uma tabela de rotas para determinar os prefixos são anunciados através de ExpressRoute.
* Valide a transferência de dados ao rever bytes de entrada e saída.
* Abra um pedido de suporte com [Microsoft Azure ajuda + suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se ainda ocorrerem problemas.

