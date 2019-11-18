---
title: 'Início rápido: reconhecer fala, intenções e entidades – serviço de fala'
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
ms.openlocfilehash: df4604560e05899461b11ec0788f72b27241f1b9
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125575"
---
Neste guia de início rápido, você usará o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer de forma interativa a fala de dados de áudio capturados de um microfone. Depois de satisfazer alguns pré-requisitos, reconhecer a fala de um microfone usa apenas quatro etapas:
> [!div class="checklist"]
>
> * Crie um objeto de ````SpeechConfig```` de sua chave de assinatura e região.
> * Crie um objeto ````IntentRecognizer```` usando o objeto ````SpeechConfig```` acima.
> * Usando o objeto ````IntentRecognizer````, inicie o processo de reconhecimento para um único expressão.
> * Inspecione o ````IntentRecognitionResult```` retornado.
