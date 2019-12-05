---
title: 'Início rápido: reconhecer a fala armazenada no armazenamento de BLOBs-serviço de fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: ef61139056ad194d89e2a67cb4ac1b1414a3380b
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828966"
---
Neste guia de início rápido, você usará a [API REST de transcrição do lote](../../../batch-transcription.md) para reconhecer a fala armazenada em um [blob SAS](https://aka.ms/ignite2019/speech/placeholder). Depois de satisfazer alguns pré-requisitos, reconhecer a fala usando uma API REST só leva algumas etapas:
> [!div class="checklist"]
> * Envie a solicitação JSON para o serviço de fala para começar a transcrever a fala.
> * Verifique o status da transcrição.
> * Baixe os resultados da transcrição quando terminar.