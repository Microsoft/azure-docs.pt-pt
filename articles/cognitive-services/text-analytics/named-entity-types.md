---
title: Categorias apoiadas para reconhecimento de entidades nomeadas
titleSuffix: Azure Cognitive Services
description: Conheça as categorias de entidades suportadas na API text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 01/22/2021
ms.author: aahi
ms.openlocfilehash: 883c5a20612f4dab44c0d06776ee287b27174ab9
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097308"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Categorias de entidades apoiadas no Text Analytics API v3

Utilize este artigo para encontrar as categorias de entidades que podem ser devolvidas pelo [Reconhecimento de Entidade Nomeada](how-tos/text-analytics-how-to-entity-linking.md) (NER). O NER executa um modelo preditivo para identificar e categorizar entidades nomeadas a partir de um documento de entrada.

Está também disponível uma pré-visualização do NER v3.1, que inclui a capacidade de detetar informações pessoais `PII` e de `PHI` saúde. Além disso, clique no separador **Saúde** para ver uma lista de categorias apoiadas em Text Analytics para a saúde. 

Pode encontrar uma lista de tipos devolvidos pela versão 2.1 no guia de [migração](migration-guide.md?tabs=named-entity-recognition)

## <a name="entity-categories"></a>Categorias de entidades

#### <a name="general"></a>[Geral](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[RIO PII](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Saúde](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

**_

## <a name="next-steps"></a>Passos seguintes

_ [Como utilizar o Reconhecimento de Entidade Nomeada em Análise de Texto](how-tos/text-analytics-how-to-entity-linking.md)
