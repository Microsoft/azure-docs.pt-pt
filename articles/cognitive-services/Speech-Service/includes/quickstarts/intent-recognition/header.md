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
ms.date: 1/02/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a833d39ab91cd803f066d707306a6ff648d37e8f
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660556"
---
Neste guia de início rápido, você usará o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) e o serviço de reconhecimento vocal (Luis) para reconhecer tentativas de dados de áudio capturados de um microfone. Especificamente, você usará o SDK de fala para capturar fala e um domínio predefinido do LUIS para identificar tentativas de automação doméstica, como ligar e desligar uma luz. 

Depois de atender a alguns pré-requisitos, reconhecer a fala e identificar tentativas de um microfone leva apenas algumas etapas:

> [!div class="checklist"]
>
> * Crie um objeto de ````SpeechConfig```` de sua chave de assinatura e região.
> * Crie um objeto ````IntentRecognizer```` usando o objeto ````SpeechConfig```` acima.
> * Usando o objeto ````IntentRecognizer````, inicie o processo de reconhecimento para um único expressão.
> * Inspecione o ````IntentRecognitionResult```` retornado.
