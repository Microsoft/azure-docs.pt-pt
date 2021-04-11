---
title: Requisitos e recomendações do contentor
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/23/2020
ms.author: aahi
ms.openlocfilehash: 2a399b683dc9596d3514ce7d3be1fa2444449e2a
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284950"
---
> [!NOTE]
> Os requisitos e recomendações baseiam-se em parâmetros de referência com um único pedido por segundo, utilizando uma imagem de 8-MB de uma carta de negócios digitalizada que contém 29 linhas e um total de 803 caracteres.

O quadro seguinte descreve a alocação mínima e recomendada de recursos para cada recipiente De Leitura OCR.

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
| Ler 2.0 pré-visualização | 1 núcleo, memória de 8 GB |    8 núcleos, memória de 16 GB |
| Ler 3.2 pré-visualização | 8 núcleos, memória de 16 GB | 8 núcleos, memória de 24 GB |

* Cada núcleo deve ser pelo menos 2,6 gigahertz (GHz) ou mais rápido.

O núcleo e a memória correspondem às `--cpus` `--memory` definições e configurações, que são utilizadas como parte do `docker run` comando.
