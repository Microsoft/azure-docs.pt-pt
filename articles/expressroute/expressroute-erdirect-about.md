---
title: Sobre o ExpressRoute Direct – Azure | Documentos da Microsoft
description: Esta página fornece uma visão geral do ExpressRoute Direct
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: e598cc03a1b7b4999719152540866c7168130e03
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807486"
---
# <a name="about-expressroute-direct"></a>Sobre o ExpressRoute Direct

ExpressRoute Direct dá-lhe a capacidade de ligar diretamente para a rede global da Microsoft em localizações de peering estrategicamente distribuídas em todo o mundo. ExpressRoute Direct fornece 100 Gbps duplas ou de 10 Gbps conectividade, que suporta a conetividade de ativo/ativo em escala.

Recursos-chave que ExpressRoute Direct fornece incluem, mas não estão limitados a:

* Ingestão de Dados em Massa em serviços como o Armazenamento e o Cosmos DB
* Isolamento físico para setores que estejam reguladas e requerem conectividade dedicada e isolada, como: Banca, governamentais e varejo
* Controlo granular de distribuição do circuito baseado na unidade de negócios

## <a name="onboard-to-expressroute-direct"></a>Carregar para o ExpressRoute Direct

Antes de utilizar o ExpressRoute Direct, primeiro tem de inscrever a sua subscrição. Para se inscrever, enviar um E-Mail para <ExpressRouteDirect@microsoft.com> com o seu ID de subscrição, incluindo os seguintes detalhes:

* Cenários que pretende para realizar com **direta do ExpressRoute**
* Preferências de localização - consulte [parceiros e localizações de peering](expressroute-locations-providers.md) para obter uma lista completa de todas as localizações
* Linha cronológica para implementação
* Outras perguntas

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute através de um fornecedor de serviços e direta do ExpressRoute

| **ExpressRoute com um fornecedor de serviços** | **Direct do ExpressRoute** | 
| --- | --- |
| Utiliza fornecedores de serviços para ativar a integração rápida e conectividade na infraestrutura existente | Requer 100 infraestrutura Gbps/10 Gbps e uma gestão completa de todas as camadas
| Integra-se com centenas de fornecedores, incluindo Ethernet e MPLS | Capacidade de Direct/dedicado para indústrias reguladas e ingestão de dados em massa |
| SKUs de circuitos de 50 Mbps para 10 Gbps | Cliente pode selecionar uma combinação do circuito seguinte SKUs no 100 Gbps ExpressRoute direto: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> Cliente pode selecionar uma combinação do circuito seguinte SKUs no 10 Gbps ExpressRoute direto:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Otimizado para o inquilino único | Otimizado para fornecedores de serviços de inquilino único/Cloud / várias unidades de negócio

## <a name="expressroute-direct-circuits"></a>Circuitos do ExpressRoute Direct

O Microsoft Azure ExpressRoute permite-lhe expandir as redes no local para a cloud da Microsoft através de uma ligação privada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode ligar aos serviços cloud da Microsoft, tais como o Microsoft Azure, o Office 365 e o Dynamics 365.  

Cada localização de peering tem acesso à rede global da Microsoft e pode aceder a qualquer região numa zona geopolítica por padrão e pode aceder a todas as regiões globais com um circuito de premium.  

A funcionalidade na maioria dos cenários é equivalente a circuitos que utilizam um fornecedor de serviços de ExpressRoute para operar. Para suportar mais granularidade e novos recursos oferecidos através do ExpressRoute Direct, há determinadas capacidades principais que existe em direto de circuitos do ExpressRoute.

## <a name="circuit-skus"></a>SKUs do circuito

ExpressRoute Direct suporta cenários de ingestão de grandes quantidades de dados no armazenamento do Azure e outros serviços de grandes volumes de dados. Circuitos do ExpressRoute no 100 Gbps ExpressRoute agora também o suporte direto **40 Gbps** e **100 Gbps** circuito SKUs. Os pares de porta física são **100 ou 10 Gbps** apenas e pode ter vários circuitos virtual. Tamanhos de circuito:

| **Gbps de 100 ExpressRoute Direct** | **Direct do ExpressRoute de 10 Gbps** | 
| --- | --- |
| **Subscreveu a largura de banda**: 200 Gbps | **Subscreveu a largura de banda**: 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>Requisitos Técnicos

* Microsoft Enterprise Edge Router (MSEE) Interfaces:
    * Portas duplas de Gigabit Ethernet de 10 ou 100 apenas em par de router
    * Conectividade de modo LR fibra único
    * IPv4 e IPv6
    * Bytes de 1500 de MTU de IP

* Conectividade de 2/camada 3 da camada de comutador/roteador:
    * Tem de suportar tag de 1 802.1Q (Dot1Q) ou a Tag dois 802.1Q (QinQ) Etiquetar encapsulamento
    * Ethertype = 0x8100
    * Tem de adicionar a etiqueta VLAN externa (STAG) com base no ID da VLAN especificada pelo Microsoft - *aplicável apenas em QinQ*
    * Tem de suportar várias sessões de BGP (VLANs) por porta e o dispositivo
    * Conectividade IPv4 e IPv6. *Nenhuma interface secundárias adicional será criado para IPv6. Endereço IPv6 será adicionado à interface secundárias existente*. 
    * Opcional: [Deteção de reencaminhamento bidirecional (BFD)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) suportar, que é configurado por predefinição em todos os Peerings privados em circuitos do ExpressRoute

## <a name="vlan-tagging"></a>Marcação de VLAN

ExpressRoute Direct suporta QinQ e Dot1Q marcação de VLAN.

* **A marcação de VLAN QinQ** permite para domínios de encaminhamento isolados num por base de circuito do ExpressRoute. Azure aloca uma marca de S durante a criação do circuito e não pode ser alterado dinamicamente. Cada peering no circuito (privado e Microsoft), utilizará uma etiqueta de C exclusiva como a VLAN. A etiqueta de C não é necessário para ser exclusivo em circuitos nas portas Direct do ExpressRoute.

* **A marcação de VLAN Dot1Q** permite para um único etiquetados VLAN num por base de par de porta Direct do ExpressRoute. Uma etiqueta de C, usada num modo de peering tem de ser exclusiva em todos os circuitos e peerings no par de porta Direct do ExpressRoute.

## <a name="workflow"></a>Fluxo de trabalho

[![Fluxo de trabalho](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct fornece o mesmo SLA de nível empresarial com ligações redundantes do ativo/ativo na rede Global da Microsoft. Infraestrutura de ExpressRoute é redundante e conectividade à rede Global da Microsoft é redundante e diversificado e dimensiona-se em conformidade com os requisitos dos clientes. 

## <a name="next-steps"></a>Passos Seguintes

[Configurar o ExpressRoute Direct](expressroute-howto-erdirect.md)
