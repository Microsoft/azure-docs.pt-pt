---
title: 'Azure ExpressRoute: Tabelas ARP - Resolução de problemas: clássico'
description: Esta página fornece instruções para obter as tabelas ARP para um circuito ExpressRoute - modelo de implementação clássico.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 01/30/2017
ms.author: duau
ms.openlocfilehash: 7c83b6d25936e184c70d4bf0a51636a3986bf4b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89394844"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>Obter tabelas ARP no modelo de implementação clássico
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell - Clássico](expressroute-troubleshooting-arp-classic.md)
> 
> 

Este artigo percorre os passos para obter as tabelas do Protocolo de Resolução de Endereços (ARP) para o seu circuito Azure ExpressRoute.

> [!IMPORTANT]
> Este documento destina-se a ajudá-lo a diagnosticar e corrigir problemas simples. Não se pretende que seja um substituto para o suporte da Microsoft. Se não conseguir resolver o problema utilizando as seguintes orientações, abra um pedido de suporte com [o suporte do Microsoft Azure Help+.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Tabelas de Protocolo de Resolução de Endereços (ARP) e ARP
ARP é um protocolo de Camada 2 que é definido no [RFC 826](https://tools.ietf.org/html/rfc826). O ARP é utilizado para mapear um endereço Ethernet (endereço MAC) para um endereço IP.

Uma tabela ARP fornece um mapeamento do endereço IPv4 e endereço MAC para um levantamento específico. A tabela ARP para um perspério do circuito ExpressRoute fornece as seguintes informações para cada interface (primária e secundária):

1. Mapeamento de um endereço IP de interface de router no local para um endereço MAC
2. Mapeamento de um endereço IP interface de router ExpressRoute para um endereço MAC
3. A idade do mapeamento

As tabelas ARP podem ajudar na validação da configuração da Camada 2 e com problemas básicos de conectividade da Camada 2.

Segue-se um exemplo de uma tabela ARP:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


A secção seguinte fornece informações sobre como ver as tabelas ARP que são vistas pelos routers de borda ExpressRoute.

## <a name="prerequisites-for-using-arp-tables"></a>Pré-requisitos para a utilização de tabelas ARP
Certifique-se de que tem o seguinte antes de continuar:

* Um circuito ExpressRoute válido que está configurado com pelo menos um espreitando. O circuito deve ser totalmente configurado pelo fornecedor de conectividade. Você (ou o seu fornecedor de conectividade) deve configurar pelo menos um dos seus pares (Azure private, Azure public, ou Microsoft) neste circuito.
* Intervalos de endereços IP que são usados para configurar os seus pares (Azure private, Azure public, e Microsoft). Reveja os exemplos de atribuição de endereços IP na página de requisitos de [encaminhamento ExpressRoute](expressroute-routing.md) para obter uma compreensão de como os endereços IP são mapeados para interfaces do seu lado e do lado do ExpressRoute. Pode obter informações sobre a configuração de peering, analisando a [página de configuração de peering ExpressRoute](expressroute-howto-routing-classic.md).
* Informações da sua equipa de networking ou fornecedor de conectividade sobre os endereços MAC das interfaces que são utilizadas com estes endereços IP.
* O mais recente módulo Windows PowerShell para Azure (versão 1.50 ou posterior).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Tabelas ARP para o seu circuito ExpressRoute
Esta secção fornece instruções sobre como ver as tabelas ARP para cada tipo de observação utilizando o PowerShell. Antes de continuar, você ou o seu fornecedor de conectividade precisa configurar o espreitamento. Cada circuito tem dois caminhos (primário e secundário). Pode verificar a tabela ARP para cada caminho de forma independente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelas ARP para Azure private peering
O cmdlet seguinte fornece as tabelas ARP para o espreguiçadinhas privadas Azure:

```azurepowershell
# Required variables
$ckt = "<your Service Key here>

# ARP table for Azure private peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

# ARP table for Azure private peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary
```

Segue-se a saída da amostra para um dos caminhos:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>Tabelas ARP para Azure olhar público:
O cmdlet seguinte fornece as tabelas ARP para o azure do estado de observação:

```azurepowershell
# Required variables
$ckt = "<your Service Key here>

# ARP table for Azure public peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

# ARP table for Azure public peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary
```

Segue-se a saída da amostra para um dos caminhos:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


Segue-se a saída da amostra para um dos caminhos:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>Tabelas ARP para olhando a Microsoft
O cmdlet seguinte fornece as tabelas ARP para o espreguiços da Microsoft:

```azurepowershell
# ARP table for Microsoft peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

# ARP table for Microsoft peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary
```


A saída da amostra é mostrada abaixo para um dos caminhos:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>Como usar esta informação
A tabela ARP de um espreite pode ser usada para validar a configuração e conectividade da Camada 2. Esta secção fornece uma visão geral de como as tabelas ARP parecem em diferentes cenários.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>Tabela ARP quando um circuito está em estado operacional (esperado)
* A tabela ARP tem uma entrada para o lado no local com um endereço IP e MAC válido, e uma entrada semelhante para o lado da Microsoft.
* O último octeto do endereço IP no local é sempre um número ímpar.
* O último octeto do endereço IP da Microsoft é sempre um número par.
* O mesmo endereço MAC aparece no lado da Microsoft para os três pares (primário/secundário).

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Tabela ARP quando está no local ou quando o lado do fornecedor de conectividade tem problemas
 Apenas uma entrada aparece na tabela ARP. Mostra o mapeamento entre o endereço MAC e o endereço IP que é usado no lado da Microsoft.

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
   0 Microsoft         65.0.0.2  aaaa.bbbb.cccc
```

> [!NOTE]
> Se tiver um problema como este, abra um pedido de apoio com o seu fornecedor de conectividade para o resolver.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Tabela ARP quando o lado da Microsoft tem problemas
* Não verá uma tabela ARP mostrada para um espreite se houver problemas no lado da Microsoft.
* Abra um pedido de suporte com [o suporte do Microsoft Azure Help+.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Especifique que tem um problema com a conectividade da Camada 2.

## <a name="next-steps"></a>Passos seguintes
* Validar as configurações da Camada 3 para o seu circuito ExpressRoute:
  * Obtenha um resumo de rota para determinar o estado das sessões de BGP.
  * Obtenha uma tabela de rotas para determinar quais prefixos são anunciados através do ExpressRoute.
* Validar a transferência de dados através da revisão de bytes dentro e fora.
* Abra um pedido de suporte com [o suporte do Microsoft Azure Help+se](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ainda estiver com problemas.

