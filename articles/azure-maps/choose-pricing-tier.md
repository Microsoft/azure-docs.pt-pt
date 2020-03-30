---
title: Escolha o nível de preços certos / Microsoft Azure Maps
description: Neste artigo, você vai aprender sobre os níveis de preços oferecidos pelo Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: a8bf7ff9bacd4fe84ee5b64d0aed5cb271ce06f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335667"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Escolha o nível de preços certos no Azure Maps

O Azure Maps oferece dois níveis de preços, S0 e S1. O objetivo deste artigo é ajudá-lo a escolher o nível de preços certo para as suas necessidades. Para escolher o nível de preços certo, pergunte a si mesmo as duas perguntas seguintes.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Quantos utilizadores simultâneos pretendo apoiar? 
Os níveis de preços S0 e S1 lidam com diferentes quantidades de dados. O nível de preços S0 lida com até **50 consultas por segundo**. Enquanto o nível S1 lida com mais de **50 consultas por segundo**.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Que capacidades geoespaciais planeio usar?
O nível de preços S0 é adequado para si, se as APIs geoespaciais fundamentais cumprirem os seus requisitos de serviço. Se quiser capacidades mais avançadas para a sua aplicação, considere optar pelo nível de preços S1. As capacidades avançadas incluem: Imagens aéreas mais híbridas, obtenção de alcance de rota e geocodificação de lotes. Reveja a tabela de capacidades de **nível** de preços para selecionar o nível de preços mais adequado para a sua aplicação.

### <a name="pricing-tier-capabilities"></a>Capacidades de nível de preços

| Capacidade                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Render do mapa                              | ✓                   | ✓       |
| Imagem de satélite                       |                     | ✓        |
| Pesquisa                                  | ✓                    | ✓        |
| Pesquisa de lote                            |                     | ✓        |
| Encaminhar                                   | ✓                    |✓        |
| Encaminhamento de lote                            |                    | ✓        |
| Encaminhamento da Matriz                          |                     | ✓        |
| Faixa de rota (Isochrones)                |                     | ✓        |
| Trânsito                                |✓                    |✓        |
| Fuso Horário                               |✓                    |✓        |
| Geolocalização (Pré-visualização)                    |✓                   |✓        |
| Operações Espaciais                        |                    |✓        |
| Geofencing                                |                    |✓        |
| Dados do Azure Maps (Pré-visualização)                |                     | ✓        |
| Mobilidade (Pré-visualização)                       |                     | ✓        |
| Tempo (Pré-visualização)                        |✓                    |✓        |

Considere estes pontos adicionais:
* Que tipo de empresa tem?
* Quão crítico é a sua candidatura?

### <a name="pricing-tier-targeted-customers"></a>Preços de nível de preços clientes direcionados

Consulte a tabela de clientes orientada para o **nível** de preços para obter uma melhor noção dos níveis de preços S0 e S1. Para mais informações, consulte [os preços do Azure Maps.](https://azure.microsoft.com/pricing/details/azure-maps/) 

| Escalão de preço  |     Clientes direcionados                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>O nível de preços S0 trabalha para aplicações em todas as fases de produção: desde o desenvolvimento da prova de conceito e testes em fase inicial até à produção e implantação de aplicações. No entanto, este nível é projetado para desenvolvimento em pequena escala, ou clientes com utilizadores simultâneos baixos, ou ambos. <p>|
| S1            |    <p>O nível de preços S1 destina-se a clientes com aplicações empresariais em larga escala, aplicações críticas de missão ou elevados volumes de utilizadores simultâneos. É também para os clientes que necessitam de serviços geoespaciais avançados.</p>|

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como visualizar e alterar os níveis de preços:

> [!div class="nextstepaction"] 
> [Gerir um nível de preços](how-to-manage-pricing-tier.md)
