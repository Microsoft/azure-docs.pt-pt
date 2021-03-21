---
title: Escolha o nível de preços certo para o Microsoft Azure Maps
description: Saiba mais sobre os níveis de preços do Azure Maps. Veja quais as características que são oferecidas em que níveis, e veja considerações fundamentais para escolher um nível de preços.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cb57e424642141e73588a61d026d1ff2f1a8b096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905303"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Escolha o nível de preços certo em Azure Maps

A Azure Maps oferece dois níveis de preços: S0 e S1. O objetivo deste artigo é ajudá-lo a escolher o nível de preços certo para as suas necessidades. Para escolher o nível de preços certo, pergunte a si mesmo as duas seguintes perguntas.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Quantos utilizadores simultâneos pretendo apoiar?

Os níveis de preços S0 e S1 lidam com diferentes quantidades de dados. O nível de preços S0 lida com até **50 consultas por segundo.** Enquanto o nível S1 lida com **mais de 50 consultas por segundo**.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Que capacidades geoespaciais pretendo usar?

Se as APIs geoespaciais de base satisfarem os seus requisitos de serviço, escolha o nível de preços S0. Se quiser capacidades mais avançadas para a sua aplicação, considere escolher para o nível de preços S1. As capacidades avançadas incluem: imagens aéreas mais híbridas, alcance de rota e geocoding de lote. Para selecionar o nível de preços mais adequado para a sua aplicação, reveja a tabela **de capacidades de preços** abaixo:

### <a name="pricing-tier-capabilities"></a>Capacidades de níveis de preços

| Funcionalidade                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Renderização do mapa                              | ✓                   | ✓       |
| Imagens de satélite                       |                     | ✓        |
| Pesquisar                                  | ✓                    | ✓        |
| Pesquisa de lotes                            |                     | ✓        |
| Rota                                   | ✓                    |✓        |
| Encaminhamento de lote                            |                    | ✓        |
| Encaminhamento de Matriz                          |                     | ✓        |
| Rota (Isochrones)                |                     | ✓        |
| Trânsito                                |✓                    |✓        |
| Fuso Horário                               |✓                    |✓        |
| Geolocalização (Pré-visualização)                    |✓                   |✓        |
| Operações Espaciais                        |                    |✓        |
| Geofencing                                |                    |✓        |
| Dados do Azure Maps (Pré-visualização)                |                     | ✓        |
| Mobilidade (Pré-visualização)                       |                     | ✓        |
| Tempo (Pré-visualização)                        |✓                    |✓        |
|  Criador (Pré-visualização)                         |                   |✓        |
|  Elevação (Pré-visualização)                        |                   |✓        |

Considere estes pontos adicionais:

* Que tipo de empreendimento tem?
* Quão crítica é a sua candidatura?

### <a name="pricing-tier-targeted-customers"></a>Clientes direcionados para o nível de preços

Consulte a tabela **de clientes direcionados** para o nível de preços para obter uma melhor noção dos níveis de preços S0 e S1. Para mais informações, consulte [os preços do Azure Maps.](https://azure.microsoft.com/pricing/details/azure-maps/) 

| Escalão de preço  |     Clientes direcionados                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    O nível de preços S0 funciona para aplicações em todas as fases de produção: desde o desenvolvimento da prova de conceito e testes em fase inicial até à produção e implantação de aplicações. No entanto, este nível é projetado para desenvolvimento em pequena escala, ou clientes com utilizadores de baixa simultânea, ou ambos. 
| S1            |    O nível de preços S1 destina-se a clientes com aplicações empresariais em larga escala, aplicações críticas de missão ou volumes elevados de utilizadores simultâneos. É também para os clientes que necessitam de serviços geoespaciais avançados.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como ver e alterar os níveis de preços:

> [!div class="nextstepaction"]
> [Gerir um nível de preços](how-to-manage-pricing-tier.md)
