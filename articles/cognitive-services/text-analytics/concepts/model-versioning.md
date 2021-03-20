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
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: ee3a56fed87f493d79796c0d91e5324c94e87eca
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599362"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Versão de modelo na API de Análise de Texto

A versão 3 da API text Analytics permite-lhe escolher a versão modelo que é usada nos seus dados. Utilize o parâmetro opcional `model-version` para selecionar a versão do modelo nos seus pedidos de API. Por exemplo: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Se este parâmetro não for especificado, a API irá predefinir na versão mais recente do estável. 

## <a name="available-versions"></a>Versões disponíveis

Utilize a tabela abaixo para saber quais as versões do modelo suportadas por cada ponto final hospedado.


| Ponto final                        | Versões Suportadas                                     | versão mais recente |
|---------------------------------|--------------------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`                             | `2020-04-01`   |
| `/languages`                    | `2019-10-01`, `2020-07-01`, `2020-09-01`, `2021-01-05` | `2021-01-05`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`                             | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2021-01-15`  | `2021-01-15`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`, `2021-01-15`  | `2021-01-15`   |
| `/entities/health`              | `2021-03-01`                           | `2021-03-01`   |
| `/keyphrases`                   | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |


Pode encontrar detalhes sobre as atualizações para estes modelos no [Que há de novo.](../whats-new.md)

## <a name="text-analytics-for-health"></a>Análise de Texto para a saúde

O [recipiente Text Analytics for Health](../how-tos/text-analytics-for-health.md) utiliza a versão de modelo separada do que os pontos finais da API acima.  Por favor, note que apenas uma versão modelo está disponível por imagem de recipiente.

| Ponto final                        | Etiqueta de imagem de recipiente                     | Versão modelo |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`              | `3.0.015370001-onprem-amd64` ou mais recente          | `2021-03-01`  |
| `/entities/health`              | `1.1.013530001-amd64-preview`           | `2020-09-03`  |
| `/entities/health`              | `1.1.013150001-amd64-preview`           | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |


## <a name="next-steps"></a>Passos seguintes

* [Descrição geral da Análise de Texto](../overview.md)
* [Análise de sentimentos](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconhecimento de entidades](../how-tos/text-analytics-how-to-entity-linking.md)
