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
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 8b596a5e54c0b59c4c0b49aa5cdc4fd6477d46dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599334"
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

***

## <a name="next-steps"></a>Passos seguintes

* [Como utilizar o reconhecimento de entidade nomeada em análise de texto](how-tos/text-analytics-how-to-entity-linking.md)
