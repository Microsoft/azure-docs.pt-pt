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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083593"
---
# <a name="about-expressroute-direct"></a>Sobre o ExpressRoute Direct

O ExpressRoute Direct dá-lhe a capacidade de se ligar diretamente à rede global da Microsoft em locais de observação estrategicamente distribuídos pelo mundo. O ExpressRoute Direct fornece conectividade dupla de 100 Gbps ou 10 Gbps, que suporta a conectividade Ative/Ative em escala.

As principais funcionalidades que o ExpressRoute Direct fornece incluem, mas não se limitam a:

* Ingestão de Dados em Massa em serviços como o Armazenamento e o Cosmos DB
* Isolamento físico para indústrias reguladas e que exigem conectividade dedicada e isolada como: Banca, Governo e Retalho
* Controlo granular de distribuição do circuito baseado na unidade de negócios

## <a name="onboard-to-expressroute-direct"></a>A bordo do ExpressRoute Direct

Antes de utilizar o ExpressRoute Direct, tem de inscrever primeiro a sua subscrição. Para se inscrever, <ExpressRouteDirect@microsoft.com> envie um Email com o seu ID de subscrição, incluindo os seguintes detalhes:

* Cenários que procura realizar com o **ExpressRoute Direct**
* Preferências de localização - consulte [Parceiros e locais de observação](expressroute-locations-providers.md) para obter uma lista completa de todos os locais
* Linha cronológica da implementação
* Quaisquer outras perguntas

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute usando um prestador de serviços e ExpressRoute Direct

| **ExpressRoute usando um prestador de serviços** | **ExpressRoute Direct** | 
| --- | --- |
| Utiliza prestadores de serviços para permitir o rápido embarque e conectividade na infraestrutura existente | Requer 100 Gbps/10 Gbps infraestrutura e gestão completa de todas as camadas
| Integra-se com centenas de fornecedores, incluindo ethernet e MPLS | Capacidade direta/dedicada para indústrias regulamentadas e ingestão massiva de dados |
| Circuitos SKUs de 50 Mbps a 10 Gbps | O cliente pode selecionar uma combinação do seguinte circuito SKUs em 100 Gbps ExpressRoute Direct: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> O cliente pode selecionar uma combinação do seguinte circuito SKUs em 10 Gbps ExpressRoute Direct:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Otimizado para inquilino único | Otimizado para inquilino único com múltiplas unidades de negócio e múltiplos ambientes de trabalho

## <a name="expressroute-direct-circuits"></a>Circuitos Direct ExpressRoute

O Microsoft Azure ExpressRoute permite-lhe expandir as redes no local para a cloud da Microsoft através de uma ligação privada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode estabelecer ligações aos serviços de cloud da Microsoft, como o Microsoft Azure e o Office 365.

Cada local de observação tem acesso à rede global da Microsoft e pode aceder a qualquer região de uma zona geopolítica por padrão e pode aceder a todas as regiões globais com um circuito premium.  

A funcionalidade na maioria dos cenários é equivalente a circuitos que utilizam um prestador de serviços ExpressRoute para operar. Para suportar mais granularidade e novas capacidades oferecidas através do ExpressRoute Direct, existem certas capacidades-chave que existem nos Circuitos Direct ExpressRoute.

## <a name="circuit-skus"></a>Circuito SKUs

O ExpressRoute Direct suporta cenários maciços de ingestão de dados no armazenamento do Azure e noutros serviços de big data. Os circuitos ExpressRoute em 100 Gbps ExpressRoute Direct suportam agora **40 Gbps** e **100 Gbps** circuito SKUs. Os pares de portas físicas são apenas **100 ou 10 Gbps** e podem ter vários circuitos virtuais. Tamanhos do circuito:

| **100 Gbps ExpressRoute Direct** | **10 Gbps ExpressRoute Direct** | 
| --- | --- |
| **Largura de banda subscrita**: 200 Gbps | **Largura de banda subscrita**: 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>Requisitos Técnicos

* Interfaces do Microsoft Enterprise Edge Router (MSEE):
    * Portas Ethernet Dual 10 ou 100 Gigabit apenas através do par de router
    * Conectividade de fibra LR de modo único
    * IPv4 e IPv6
    * BYTes IP MTU 1500

* Switch/Router Camada 2/Camada 3 Conectividade:
    * Deve suportar 1 802.1Q (Dot1Q) ou duas etiquetas Tag 802.1Q (QinQ) encapsulação
    * Etertipo = 0x8100
    * Deve adicionar a etiqueta VLAN exterior (STAG) com base no ID VLAN especificado pela Microsoft - *aplicável apenas no QinQ*
    * Deve suportar várias sessões de BGP (VLANs) por porta e dispositivo
    * Conectividade IPv4 e IPv6. *Para o IPv6 não será criada nenhuma subinterface adicional. O endereço IPv6 será adicionado à subinterface existente*. 
    * Opcional: Suporte [bidirecional de deteção de reencaminhamento (BFD),](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) que é configurado por padrão em todos os Pares Privados nos circuitos ExpressRoute

## <a name="vlan-tagging"></a>Marcação VLAN

ExpressRoute Direct suporta a marcação QinQ e Dot1Q VLAN.

* **A marcação QinQ VLAN** permite domínios de encaminhamento isolados numa base de circuito ExpressRoute. Azure aloca dinamicamente um S-Tag na criação de circuitos e não pode ser alterado. Cada olhar no circuito (Privado e Microsoft) utilizará um C-Tag único como VLAN. O C-Tag não é necessário para ser único entre circuitos nas portas ExpressRoute Direct.

* **A marcação Dot1Q VLAN** permite um VLAN com uma única marcação numa base de pares de portas ExpressRoute Direct. Um C-Tag utilizado num epeering deve ser único em todos os circuitos e observações no par de portas ExpressRoute Direct.

## <a name="workflow"></a>Fluxo de trabalho

[![fluxo de trabalho](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

O ExpressRoute Direct fornece o mesmo SLA de nível empresarial com ligações redundantes Ative/Ative na Microsoft Global Network. A infraestrutura ExpressRoute é redundante e a conectividade na Microsoft Global Network é redundante e diversificada e escala em conformidade com os requisitos dos clientes. 

## <a name="next-steps"></a>Passos seguintes

[Configurar o ExpressRoute Direct](expressroute-howto-erdirect.md)
