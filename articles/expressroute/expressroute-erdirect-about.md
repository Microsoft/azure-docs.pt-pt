---
title: Sobre o Azure ExpressRoute Direct
description: Esta página fornece uma visão geral do ExpressRoute Direct
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.openlocfilehash: f26fdd75e0a6c6228d329a8b9be4743072ae54c1
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083593"
---
# <a name="about-expressroute-direct"></a>Sobre o ExpressRoute Direct

ExpressRoute Direct dá-lhe a capacidade de ligar diretamente para a rede global da Microsoft em localizações de peering estrategicamente distribuídas em todo o mundo. O ExpressRoute Direct fornece conectividade dual de 100 Gbps ou 10 Gbps, que dá suporte à conectividade ativa/ativa em escala.

Recursos-chave que ExpressRoute Direct fornece incluem, mas não estão limitados a:

* Ingestão de Dados em Massa em serviços como o Armazenamento e o Cosmos DB
* Isolamento físico para setores que estejam reguladas e exigir dedicado e isolado a conectividade, como: bancários, governamentais e varejo
* Controlo granular de distribuição do circuito baseado na unidade de negócios

## <a name="onboard-to-expressroute-direct"></a>Integração ao ExpressRoute Direct

Antes de usar o ExpressRoute Direct, você deve primeiro registrar sua assinatura. Para se inscrever, enviar um E-Mail para <ExpressRouteDirect@microsoft.com> com o seu ID de subscrição, incluindo os seguintes detalhes:

* Cenários que pretende para realizar com **direta do ExpressRoute**
* Preferências de localização - consulte [parceiros e localizações de peering](expressroute-locations-providers.md) para obter uma lista completa de todas as localizações
* Linha cronológica da implementação
* Outras perguntas

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute através de um fornecedor de serviços e direta do ExpressRoute

| **ExpressRoute com um fornecedor de serviços** | **Direct do ExpressRoute** | 
| --- | --- |
| Utiliza fornecedores de serviços para ativar a integração rápida e conectividade na infraestrutura existente | Requer infraestrutura de 100 Gbps/10 Gbps e gerenciamento completo de todas as camadas
| Integra-se com centenas de fornecedores, incluindo Ethernet e MPLS | Capacidade de Direct/dedicado para indústrias reguladas e ingestão de dados em massa |
| SKUs de circuitos de 50 Mbps para 10 Gbps | O cliente pode selecionar uma combinação das seguintes SKUs de circuito no ExpressRoute direto de 100 Gbps: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> O cliente pode selecionar uma combinação dos seguintes SKUs de circuito no ExpressRoute direto de 10 Gbps:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Otimizado para o inquilino único | Otimizado para locatário único com várias unidades de negócios e vários ambientes de trabalho

## <a name="expressroute-direct-circuits"></a>Circuitos do ExpressRoute Direct

O Microsoft Azure ExpressRoute permite-lhe expandir as redes no local para a cloud da Microsoft através de uma ligação privada facilitada por um fornecedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como Microsoft Azure e Office 365.

Cada localização de peering tem acesso à rede global da Microsoft e pode aceder a qualquer região numa zona geopolítica por padrão e pode aceder a todas as regiões globais com um circuito de premium.  

A funcionalidade na maioria dos cenários é equivalente a circuitos que utilizam um fornecedor de serviços de ExpressRoute para operar. Para suportar mais granularidade e novos recursos oferecidos através do ExpressRoute Direct, há determinadas capacidades principais que existe em direto de circuitos do ExpressRoute.

## <a name="circuit-skus"></a>SKUs do circuito

ExpressRoute Direct suporta cenários de ingestão de grandes quantidades de dados no armazenamento do Azure e outros serviços de grandes volumes de dados. Os circuitos do ExpressRoute em 100 Gbps ExpressRoute Direct agora também dão suporte a SKUs de circuito de **40 Gbps** e **100 Gbps** . Os pares de porta física são de **100 ou 10 Gbps** apenas e podem ter vários circuitos virtuais. Tamanhos de circuito:

| **100 Gbps ExpressRoute direto** | **ExpressRoute direto de 10 Gbps** | 
| --- | --- |
| **Largura de banda assinada**: 200 Gbps | **Largura de banda assinada**: 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>Requisitos Técnicos

* Interfaces de roteador do Microsoft Enterprise Edge (MSEE):
    * Duas portas Ethernet de 10 ou 100 Gigabit somente entre o par de roteador
    * Conectividade de fibra de modo único LR
    * IPv4 e IPv6
    * IP MTU 1500 bytes

* Conectividade de camada de comutador/roteador 2/camada 3:
    * Deve oferecer suporte à marca 1 802.1 Q (Dot1Q) ou a um encapsulamento de marca 802.1 Q (QinQ) de duas marcas
    * EtherType = 0x8100
    * É necessário adicionar a tag VLAN externa (STAG) com base na ID de VLAN especificada pela Microsoft- *aplicável somente em QinQ*
    * Deve oferecer suporte a várias sessões BGP (VLANs) por porta e dispositivo
    * Conectividade IPv4 e IPv6. *Para IPv6, nenhuma subinterface adicional será criada. O endereço IPv6 será adicionado à subinterface existente*. 
    * Opcional: suporte a [BFD (detecção de encaminhamento bidirecional)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) , que é configurado por padrão em todos os emparelhamentos privados em circuitos do ExpressRoute

## <a name="vlan-tagging"></a>Marcação de VLAN

ExpressRoute Direct suporta QinQ e Dot1Q marcação de VLAN.

* **A marcação de VLAN QinQ** permite para domínios de encaminhamento isolados num por base de circuito do ExpressRoute. Azure aloca uma marca de S durante a criação do circuito e não pode ser alterado dinamicamente. Cada peering no circuito (privado e Microsoft), utilizará uma etiqueta de C exclusiva como a VLAN. A etiqueta de C não é necessário para ser exclusivo em circuitos nas portas Direct do ExpressRoute.

* **A marcação de VLAN Dot1Q** permite para um único etiquetados VLAN num por base de par de porta Direct do ExpressRoute. Uma etiqueta de C, usada num modo de peering tem de ser exclusiva em todos os circuitos e peerings no par de porta Direct do ExpressRoute.

## <a name="workflow"></a>Fluxo de trabalho

[fluxo de trabalho ![](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct fornece o mesmo SLA de nível empresarial com ligações redundantes do ativo/ativo na rede Global da Microsoft. Infraestrutura de ExpressRoute é redundante e conectividade à rede Global da Microsoft é redundante e diversificado e dimensiona-se em conformidade com os requisitos dos clientes. 

## <a name="next-steps"></a>Passos seguintes

[Configurar o ExpressRoute Direct](expressroute-howto-erdirect.md)
