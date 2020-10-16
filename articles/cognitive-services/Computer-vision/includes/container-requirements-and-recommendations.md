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
ms.openlocfilehash: 3ab5462cc2b368bd1518606b03c8ec1027bc31c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397198"
---
> [!NOTE]
> Os requisitos e recomendações baseiam-se em parâmetros de referência com um único pedido por segundo, utilizando uma imagem de 8-MB de uma carta de negócios digitalizada que contém 29 linhas e um total de 803 caracteres.

O quadro seguinte descreve a alocação mínima e recomendada de recursos para cada recipiente De leitura.

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
| Ler 2.0 pré-visualização | 1 núcleo, memória de 8 GB |  8 núcleos, memória de 16 GB |
| Ler 3.0 pré-visualização | 8 núcleos, memória de 16 GB | 8 núcleos, memória de 24 GB |
| Ler 3.1 pré-visualização | 8 núcleos, memória de 16 GB | 8 núcleos, memória de 24 GB |

* Cada núcleo deve ser pelo menos 2,6 gigahertz (GHz) ou mais rápido.

O núcleo e a memória correspondem às `--cpus` `--memory` definições e configurações, que são utilizadas como parte do `docker run` comando.
