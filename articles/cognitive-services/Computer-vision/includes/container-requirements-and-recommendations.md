---
title: Requisitos e recomendações de contentores
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c62044582cb488a5ef2d20b3f407c0865b3994ba
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877984"
---
> [!NOTE]
> Os requisitos e recomendações baseiam-se em referências com um único pedido por segundo, utilizando uma imagem de 8 MB de uma carta de negócio digitalizada que contém 29 linhas e um total de 803 caracteres.

O quadro seguinte descreve a alocação mínima e recomendada de recursos para cada recipiente de leitura.

| Contentor | Mínimo | Recomendado |TPS<br>(Mínimo, Máximo)|
|-----------|---------|-------------|--|
| Leitura | 1 núcleos, 8-GB de memória, 0.24 TPS | 8 núcleos, 16-GB de memória, 1.17 TPS | 0.24, 1.17 |

* Cada núcleo deve ter pelo menos 2,6 gigahertz (GHz) ou mais rápido.
* TPS - transações por segundo.

O núcleo e `--cpus` a `--memory` memória correspondem às definições e definições, que são usadas como parte do `docker run` comando.
