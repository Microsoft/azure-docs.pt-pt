---
title: Escolha o nível de preços certos / Microsoft Azure Maps
description: Neste artigo, você vai aprender sobre os níveis de preços oferecidos pelo Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 2950c0e0fdd6839b905386c90665d95563a0a3f2
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190275"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Escolha o nível de preços certos no Azure Maps

O mapas do Azure oferece dois tipos de preço, S0 e S1. O objetivo deste artigo é ajudá-lo a escolher o nível de preços certo para as suas necessidades. Para escolher o nível de preços certo, pergunte a si mesmo as duas perguntas seguintes.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Quantos utilizadores simultâneos pretendo apoiar? 
Os níveis de preços S0 e S1 lidam com diferentes quantidades de dados. O nível de preços S0 lida com até **50 consultas por segundo**. Enquanto o nível S1 lida com mais de **50 consultas por segundo**.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Que capacidades geoespaciais planeio usar?
O nível de preços S0 é adequado para si, se as APIs geoespaciais fundamentais cumprirem os seus requisitos de serviço. Se quiser capacidades mais avançadas para a sua aplicação, considere optar pelo nível de preços S1. As capacidades avançadas incluem: Imagens aéreas mais híbridas, obtenção de alcance de rota e geocodificação de lotes. Reveja a tabela de capacidades de **nível** de preços para selecionar o nível de preços mais adequado para a sua aplicação.

### <a name="pricing-tier-capabilities"></a>Capacidades de nível de preços

| Capacidade                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Renderização de mapa                              | ✓                   | ✓       |
| Imagens satélite                       |                     | ✓        |
| Pesquisa                                  | ✓                    | ✓        |
| Pesquisa de lote                            |                     | ✓        |
| Encaminhar                                   | ✓                    |✓        |
| Encaminhamento de lote                            |                    | ✓        |
| Encaminhamento da Matriz                          |                     | ✓        |
| Faixa de rota (Isochrones)                |                     | ✓        |
| Trânsito                                |✓                    |✓        |
| Fuso Horário                               |✓                    |✓        |
| Localização geográfica (versão prévia)                    |✓                   |✓        |
| Operações Espaciais                        |                    |✓        |
| Geofencing                                |                    |✓        |
| Dados do Azure Maps (versão prévia)                |                     | ✓        |
| Mobilidade (visualização)                       |                     | ✓        |
| Clima (visualização)                        |✓                    |✓        |

Considere estes pontos adicionais:
* Que tipo de empresa você tem?
* Quão crítico é seu aplicativo?

### <a name="pricing-tier-targeted-customers"></a>Preços de nível de preços clientes direcionados

Consulte a tabela de clientes orientada para o **nível** de preços para obter uma melhor noção dos níveis de preços S0 e S1. Para mais informações, consulte [os preços do Azure Maps.](https://azure.microsoft.com/pricing/details/azure-maps/) 

| Escalão de preço  |     Clientes direcionados                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>O tipo de preço S0 funciona para aplicativos em todos os estágios de produção: desde desenvolvimento de prova de conceito e teste antecipado para produção e implantação de aplicativos. No entanto, essa camada foi projetada para desenvolvimento de pequena escala ou para clientes com poucos usuários simultâneos, ou ambos. <p>|
| S1            |    <p>O nível de preços S1 destina-se a clientes com aplicações empresariais em larga escala, aplicações críticas de missão ou elevados volumes de utilizadores simultâneos. É também para os clientes que necessitam de serviços geoespaciais avançados.</p>|

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como visualizar e alterar os níveis de preços:

> [!div class="nextstepaction"] 
> [Gerir um nível de preços](how-to-manage-pricing-tier.md)
