---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: d59a55a9b6d57d90ac6ae893b05e1064c11eb58b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900585"
---
Neste guia de início rápido, você usará o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) e o serviço de reconhecimento vocal (Luis) para reconhecer tentativas de dados de áudio capturados de um microfone. Especificamente, você usará o SDK de fala para capturar fala e um domínio predefinido do LUIS para identificar tentativas de automação doméstica, como ligar e desligar uma luz. 

Depois de atender a alguns pré-requisitos, reconhecer a fala e identificar tentativas de um microfone leva apenas algumas etapas:

> [!div class="checklist"]
>
> * Crie um objeto de `SpeechConfig` de sua chave de assinatura e região.
> * Crie um objeto `IntentRecognizer` utilizando o objeto `SpeechConfig` de cima.
> * Utilizando o objeto `IntentRecognizer`, inicie o processo de reconhecimento para uma única expressão.
> * Inspecione o `IntentRecognitionResult` devolvido.
