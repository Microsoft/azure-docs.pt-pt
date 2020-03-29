---
title: Requisitos e recomendações de contentores
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481773"
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
