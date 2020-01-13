---
title: Escolher o tipo de preço certo | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre os tipos de preço oferecidos pelo Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/02/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5ff774f9848db948058075a98504e6c13db5467a
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911756"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Escolha o tipo de preço certo no Azure Maps

O Azure Maps tem dois escalões de preço. A finalidade deste artigo é ajudá-lo a escolher o tipo de preço certo para suas necessidades. Para ajudar a escolher o tipo de preço certo, faça as duas perguntas a seguir.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Quais recursos geoespaciais eu planejo usar?
O tipo de preço S0 é ideal para você se as APIs geoespaciais principais atenderem aos seus requisitos de serviço. Se você quiser recursos mais avançados para seu aplicativo, considere a possibilidade de optar pelo tipo de preço S1. Os recursos de exemplo são área, além de imagens híbridas, obtenção de intervalo de rota e geocodificação em lote. A tabela de **recursos de tipo de preço** a seguir oferece uma ideia melhor das necessidades do seu aplicativo. Ele também ajuda a escolher um tipo de preço mais adequado para seu aplicativo.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>A quantos usuários simultâneos eu planejo oferecer suporte? 
Os tipos de preço S0 e S1 lidam com diferentes quantidades de taxa de transferência de dados. Antes de escolher um tipo de preço do Azure Maps, faça algumas perguntas. Um exemplo é "a quantos usuários simultâneos eu quero dar suporte?" O tipo de preço S0 lida com até **50 consultas por segundo**. O tipo de preço S1 lida com **mais de 50 consultas por segundo**.

### <a name="pricing-tier-capabilities"></a>Recursos de tipo de preço

| Capacidade                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Pesquisa (multicodificação de FWD/REV, pontos de interesse)  |        ✓           |     ✓    |
| Geocodificação em lote (versão prévia)              |                   |     ✓    |
| Polígonos da pesquisa          |                   |     ✓    |
| Encaminhamento                                 |        ✓           |     ✓    |
| Intervalo de rotas                    |                   |     ✓    |
| Roteamento do lote (versão prévia)                |                   |     ✓    |
| Roteamento de matriz (visualização)               |                   |     ✓    |
| Composição                                  |        ✓           |     ✓    |
| Imagens e imagens híbridas    |            |     ✓    |
| Trânsito                                 |        ✓           |     ✓    |
| Fusos horários                              |        ✓           |     ✓    |
| Localização geográfica (versão prévia)                |        ✓           |     ✓    |
| Dados (visualização)               |                   |     ✓    |
| Espacial (versão prévia)               |                   |     ✓    |
| Isolamento geográfico (versão prévia)               |                   |     ✓    |



Vale a pena considerar esses pontos de dados adicionais:
* Que tipo de empresa você tem?
* Quão crítico o aplicativo está sendo compilado?

Consulte a tabela **clientes do tipo de preço direcionados** para obter uma noção melhor dos tipos de preço S0 e S1. Para obter mais informações, consulte [preços do Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>Clientes com direcionamento de tipo de preço

| Escalão de preço  |     Clientes de destino                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>O tipo de preço S0 é para clientes que são empresas de pequeno ou médio porte. É o tipo de preço certo para você se você não espera grandes volumes de usuários simultâneos. Também é certo se as APIs geoespaciais principais mostradas na tabela anterior atendem aos seus requisitos de serviço. Essa camada está disponível para o público geral. Ele funciona para aplicativos em todos os estágios de produção: de desenvolvimento de prova de conceito e teste antecipado para produção e implantação de aplicativos.<p>|
| S1            |    <p>O tipo de preço S1 é para os clientes que precisam de suporte para empresas de grande escala, aplicativos críticos de missão ou grandes volumes de usuários simultâneos. Ele também é para os clientes que precisam de serviços geoespaciais avançados.</p>|

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como exibir e alterar os tipos de preço:

> [!div class="nextstepaction"] 
> [Gerenciar um tipo de preço](how-to-manage-pricing-tier.md)
