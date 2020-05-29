---
title: Especificar a versão do modelo em Text Analytics v3
titleSuffix: Azure Cognitive Services
description: Saiba como especificar o modelo API API text Analytics utilizado nos seus dados.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 04/21/2020
ms.author: aahi
ms.openlocfilehash: 9431ff862dd987a1a806087053014e7c880bf801
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84143306"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Versão de modelo na API de Análise de Texto

A versão 3 da API text Analytics permite-lhe escolher a versão modelo que é usada nos seus dados. Utilize o parâmetro opcional `model-version` para selecionar a versão do modelo nos seus pedidos de API. Por exemplo: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Se este parâmetro não for especificado, a API irá predefinir na versão mais recente do estável. 

## <a name="available-versions"></a>Versões disponíveis

Utilize a tabela abaixo para saber quais as versões do modelo suportadas por cada ponto final.


| Ponto Final                        | Versões Suportadas                       | versão mais recente |
|---------------------------------|------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`               | `2020-04-01`   |
| `/languages`                    | `2019-10-01`                             | `2019-10-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`               | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/keyphrases`                   | `2019-10-01`                             | `2019-10-01`   |


Pode encontrar detalhes sobre as atualizações para estes modelos no [Que há de novo.](../whats-new.md)

## <a name="next-steps"></a>Próximos passos

* [Descrição Geral da Análise de Texto](../overview.md)
* [Análise de sentimento](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconhecimento de entidades](../how-tos/text-analytics-how-to-entity-linking.md)