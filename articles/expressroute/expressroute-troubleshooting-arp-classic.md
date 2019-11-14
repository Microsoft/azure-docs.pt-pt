---
title: 'Azure ExpressRoute: tabelas ARP – solução de problemas: clássico'
description: Esta página fornece instruções para obter as tabelas ARP para um modelo de implantação clássico de circuito do ExpressRoute.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.openlocfilehash: 7c223e3802d499e002b12580b17cb9ee3f1bea97
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076618"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>Obtendo tabelas ARP no modelo de implantação clássico
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Clássica](expressroute-troubleshooting-arp-classic.md)
> 
> 

Este artigo orienta você pelas etapas para obter as tabelas ARP (Address Resolution Protocol) para o circuito do Azure ExpressRoute.

> [!IMPORTANT]
> Este documento destina-se a ajudá-lo a diagnosticar e corrigir problemas simples. Não pretende ser uma substituição para o suporte da Microsoft. Se você não conseguir resolver o problema usando as diretrizes a seguir, abra uma solicitação de suporte com [Microsoft Azure ajuda + suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>ARP (protocolo de resolução de endereço) e tabelas ARP
ARP é um protocolo de camada 2 que é definido no [RFC 826](https://tools.ietf.org/html/rfc826). O ARP é usado para mapear um endereço Ethernet (endereço MAC) para um endereço IP.

Uma tabela ARP fornece um mapeamento do endereço IPv4 e do endereço MAC para um emparelhamento específico. A tabela ARP para um emparelhamento de circuito do ExpressRoute fornece as seguintes informações para cada interface (primária e secundária):

1. Mapeamento de um endereço IP de interface de roteador local para um endereço MAC
2. Mapeamento de um endereço IP de interface de roteador do ExpressRoute para um endereço MAC
3. A idade do mapeamento

As tabelas ARP podem ajudar na validação da configuração de camada 2 e na solução de problemas de conectividade básica da camada 2.

Veja a seguir um exemplo de uma tabela ARP:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


A seção a seguir fornece informações sobre como exibir as tabelas ARP que são vistas pelos roteadores de borda do ExpressRoute.

## <a name="prerequisites-for-using-arp-tables"></a>Pré-requisitos para usar tabelas ARP
Verifique se você tem o seguinte antes de continuar:

* Um circuito de ExpressRoute válido configurado com pelo menos um emparelhamento. O circuito deve ser totalmente configurado pelo provedor de conectividade. Você (ou seu provedor de conectividade) deve configurar pelo menos um dos emparelhamentos (privado do Azure, público do Azure ou Microsoft) nesse circuito.
* Os intervalos de endereços IP que são usados para configurar os emparelhamentos (privado do Azure, público do Azure e Microsoft). Examine os exemplos de atribuição de endereço IP na [página de requisitos de roteamento do expressroute](expressroute-routing.md) para entender como os endereços IP são mapeados para interfaces no seu lado e no lado do ExpressRoute. Você pode obter informações sobre a configuração de emparelhamento revisando a [página de configuração de emparelhamento do ExpressRoute](expressroute-howto-routing-classic.md).
* Informações de sua equipe de rede ou provedor de conectividade sobre os endereços MAC das interfaces que são usadas com esses endereços IP.
* O módulo mais recente do Windows PowerShell para Azure (versão 1,50 ou posterior).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Tabelas ARP para o circuito do ExpressRoute
Esta seção fornece instruções sobre como exibir as tabelas ARP para cada tipo de emparelhamento usando o PowerShell. Antes de continuar, você ou seu provedor de conectividade precisa configurar o emparelhamento. Cada circuito tem dois caminhos (primário e secundário). Você pode verificar a tabela ARP para cada caminho de forma independente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelas ARP para emparelhamento privado do Azure
O cmdlet a seguir fornece as tabelas ARP para o emparelhamento privado do Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Veja a seguir um exemplo de saída para um dos caminhos:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tabelas ARP para o emparelhamento público do Azure:
O cmdlet a seguir fornece as tabelas ARP para o emparelhamento público do Azure:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Veja a seguir um exemplo de saída para um dos caminhos:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Veja a seguir um exemplo de saída para um dos caminhos:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tabelas ARP para emparelhamento da Microsoft
O cmdlet a seguir fornece as tabelas ARP para o emparelhamento da Microsoft:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


A saída de exemplo é mostrada abaixo para um dos caminhos:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Como usar essas informações
A tabela ARP de um emparelhamento pode ser usada para validar a configuração e a conectividade da camada 2. Esta seção fornece uma visão geral de como as tabelas ARP são examinadas em cenários diferentes.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>Tabela ARP quando um circuito está em estado operacional (esperado)
* A tabela ARP tem uma entrada para o lado local com um endereço IP e MAC válido e uma entrada semelhante para o lado da Microsoft.
* O último octeto do endereço IP local é sempre um número ímpar.
* O último octeto do endereço IP da Microsoft é sempre um número par.
* O mesmo endereço MAC aparece no lado da Microsoft para todos os três emparelhamentos (primário/secundário).

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Tabela ARP quando ela estiver no local ou quando o lado do provedor de conectividade tiver problemas
 Apenas uma entrada aparece na tabela ARP. Ele mostra o mapeamento entre o endereço MAC e o endereço IP que é usado no lado da Microsoft.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> Se você tiver um problema como esse, abra uma solicitação de suporte com seu provedor de conectividade para resolvê-lo.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Tabela ARP quando o lado da Microsoft apresenta problemas
* Você não verá uma tabela ARP mostrada para um emparelhamento se houver problemas no lado da Microsoft.
* Abra uma solicitação de suporte com [Microsoft Azure ajuda + suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Especifique que você tem um problema com conectividade de camada 2.

## <a name="next-steps"></a>Passos seguintes
* Valide as configurações de camada 3 para o circuito do ExpressRoute:
  * Obtenha um resumo de rota para determinar o estado das sessões BGP.
  * Obtenha uma tabela de rotas para determinar quais prefixos serão anunciados no ExpressRoute.
* Valide a transferência de dados examinando os bytes de entrada e saída.
* Abra uma solicitação de suporte com [Microsoft Azure ajuda + suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se você ainda estiver enfrentando problemas.

