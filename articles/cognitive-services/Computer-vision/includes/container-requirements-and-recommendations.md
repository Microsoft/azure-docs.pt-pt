---
title: Requisitos e recomendações do contentor
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: bffdb7b33fc7da38b6985edc3948b848f417a497
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982075"
---
> [!NOTE]
> Os requisitos e recomendações baseiam-se em parâmetros de referência com um único pedido por segundo, utilizando uma imagem de 8-MB de uma carta de negócios digitalizada que contém 29 linhas e um total de 803 caracteres.

O quadro seguinte descreve a alocação mínima e recomendada de recursos para cada recipiente De leitura.

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
| Ler 3.0 pré-visualização | 8 núcleos, memória de 16 GB | 8 núcleos, memória de 24 GB |
| Ler 3.1 pré-visualização | 8 núcleos, memória de 16 GB | 8 núcleos, memória de 24 GB |

* Cada núcleo deve ser pelo menos 2,6 gigahertz (GHz) ou mais rápido.

O núcleo e a memória correspondem às `--cpus` `--memory` definições e configurações, que são utilizadas como parte do `docker run` comando.
