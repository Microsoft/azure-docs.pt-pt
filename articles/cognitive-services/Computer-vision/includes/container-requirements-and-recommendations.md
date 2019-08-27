---
title: Requisitos e recomendações de contêiner.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/26/2019
ms.author: dapine
ms.openlocfilehash: 5da088bf0356dd54d98ec5393fd2db8068f9c666
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034479"
---
A tabela a seguir descreve os núcleos de CPU mínimos e recomendados e a memória a ser alocada para cada contêiner de Reconhecimento de Texto.

| Contentor | Mínimo | Recomendado |TPS<br>(Mínimo, máximo)|
|-----------|---------|-------------|--|
|Reconhecimento de Texto|1 núcleo, 8 GB de memória, 0,5 TPS|2 núcleos, 8 GB de memória, 1 TPS|0,5, 1|

* Cada núcleo deve ter pelo menos 2,6 gigahertz (GHz) ou mais rápido.
* TPS-transações por segundo

O núcleo e a `--cpus` memória correspondem às configurações e `--memory` , que são `docker run` usadas como parte do comando.