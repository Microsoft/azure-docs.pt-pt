---
title: Sobre a Azure ExpressRoute Direct
description: Conheça as principais funcionalidades da Azure ExpressRoute Direct e as informações necessárias para embarcar no ExpressRoute Direct, como SKUs disponíveis e requisitos técnicos.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: duau
ms.openlocfilehash: 0365fd8d0d04b7d144ca6826d3d5eff976558eb3
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202161"
---
# <a name="about-expressroute-direct"></a>Sobre o ExpressRoute Direct

O ExpressRoute Direct dá-lhe a capacidade de se ligar diretamente à rede global da Microsoft em locais de observação estrategicamente distribuídos em todo o mundo. O ExpressRoute Direct fornece conectividade dual 100 Gbps ou 10 Gbps, que suporta a conectividade Ative/Ative à escala.

As principais funcionalidades que o ExpressRoute Direct fornece incluem, mas não se limitam a:

* Ingestão de Dados em Massa em serviços como o Armazenamento e o Cosmos DB
* Isolamento físico para indústrias reguladas e que requerem conectividade dedicada e isolada como: Banca, Governo e Retalho
* Controlo granular de distribuição do circuito baseado na unidade de negócios

## <a name="onboard-to-expressroute-direct"></a>A bordo do ExpressRoute Direct

Antes de utilizar o ExpressRoute Direct, tem primeiro de inscrever a sua subscrição. Para se inscrever, envie um Email para <ExpressRouteDirect@microsoft.com> o seu ID de subscrição, incluindo os seguintes detalhes:

* Cenários que pretende realizar com o **ExpressRoute Direct**
* Preferências de localização - consulte [parceiros e locais de observação](expressroute-locations-providers.md) para obter uma lista completa de todos os locais
* Linha cronológica da implementação
* Quaisquer outras perguntas

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute usando um prestador de serviços e ExpressRoute Direct

| **ExpressRoute usando um prestador de serviços** | **ExpressRoute Direct** | 
| --- | --- |
| Utiliza prestadores de serviços para permitir o embarque rápido e a conectividade na infraestrutura existente | Requer 100 Gbps/10 Gbps de infraestrutura e gestão completa de todas as camadas
| Integra-se com centenas de fornecedores, incluindo ethernet e MPLS | Capacidade direta/dedicada para indústrias reguladas e ingestão massiva de dados |
| Circuitos SKUs de 50 Mbps a 10 Gbps | O cliente pode selecionar uma combinação do seguinte circuito SKUs em 100 Gbps ExpressRoute Direct: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> O cliente pode selecionar uma combinação do seguinte circuito SKUs em 10 Gbps ExpressRoute Direct:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Otimizado para inquilino único | Otimizado para inquilino único com múltiplas unidades de negócio e múltiplos ambientes de trabalho

## <a name="expressroute-direct-circuits"></a>Circuitos Diretos ExpressRoute

O Microsoft Azure ExpressRoute permite-lhe expandir as redes no local para a cloud da Microsoft através de uma ligação privada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode estabelecer ligações aos serviços de cloud da Microsoft, como o Microsoft Azure e o Microsoft 365.

Cada local de observação tem acesso à rede global da Microsoft e pode aceder a qualquer região numa zona geopolítica por padrão e pode aceder a todas as regiões globais com um circuito premium.  

A funcionalidade na maioria dos cenários é equivalente a circuitos que utilizam um prestador de serviços ExpressRoute para operar. Para suportar mais granularidade e novas capacidades oferecidas através do ExpressRoute Direct, existem certas capacidades-chave que existem nos Circuitos Diretos ExpressRoute.

## <a name="circuit-skus"></a>Skus circuito

O ExpressRoute Direct suporta cenários maciços de ingestão de dados no armazenamento de Azure e outros grandes serviços de dados. Os circuitos ExpressRoute em 100 Gbps ExpressRoute Direct agora também suportam **40 Gbps** e **100 Gbps** circuito SKUs. Os pares de portas físicas são apenas **100 ou 10 Gbps** e podem ter vários circuitos virtuais. Tamanhos dos circuitos:

| **100 Gbps ExpressRoute Direct** | **10 Gbps ExpressRoute Direct** | 
| --- | --- |
| **Largura de banda subscrita**: 200 Gbps | **Largura de banda subscrita**: 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>Requisitos Técnicos

* Interfaces do Microsoft Enterprise Edge Router (MSEE):
    * Portas Dual 10 ou 100 Gigabit Ethernet apenas em par de router
    * Conectividade de fibra LR de modo único
    * IPv4 e IPv6
    * IP MTU 1500 bytes

* Ligação entre o interruptor/router Camada 2/Camada 3:
    * Deve suportar 1 802.1Q (Dot1Q) tag ou dois tag 802.1Q (QinQ) encapsulamento da etiqueta
    * Etétipo = 0x8100
    * Deve adicionar a etiqueta VLAN exterior (STAG) com base no ID VLAN especificado pela Microsoft - *aplicável apenas no QinQ*
    * Deve suportar várias sessões de BGP (VLANs) por porta e dispositivo
    * Conectividade IPv4 e IPv6. *Para o IPv6 não será criada nenhuma sub-interface adicional. O endereço IPv6 será adicionado à sub-interface existente.* 
    * Opcional: [Suporte de deteção de encaminhamento bidirecional (BFD),](./expressroute-bfd.md) que é configurado por padrão em todos os pares privados nos circuitos ExpressRoute

## <a name="vlan-tagging"></a>Marcação VLAN

O ExpressRoute Direct suporta a marcação VLAN QinQ e Dot1Q.

* **A marcação QinQ VLAN** permite domínios de encaminhamento isolados numa base de circuito ExpressRoute. O Azure atribui dinamicamente uma S-Tag na criação de circuitos e não pode ser alterada. Cada um dos que espreitar o circuito (Private e Microsoft) utilizará um C-Tag único como o VLAN. A C-Tag não é necessária para ser única em circuitos nas portas ExpressRoute Direct.

* **A marcação Dot1Q VLAN** permite um único VLAN marcado numa base de par de portas ExpressRoute Direct. Uma C-Tag utilizada num espreitante deve ser única em todos os circuitos e espreitar o par de portas ExpressRoute Direct.

## <a name="workflow"></a>Fluxo de trabalho

[![fluxo de trabalho](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

O ExpressRoute Direct fornece o mesmo SLA de nível empresarial com ligações redundantes ativas/ativas na Rede Global da Microsoft. A infraestrutura ExpressRoute é redundante e a conectividade na Microsoft Global Network é redundante e diversificada e escala em conformidade com os requisitos do cliente. 

## <a name="next-steps"></a>Passos seguintes

[Configurar o ExpressRoute Direct](expressroute-howto-erdirect.md)