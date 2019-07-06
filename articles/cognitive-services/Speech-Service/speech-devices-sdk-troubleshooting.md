---
title: Resolver problemas relacionados com os dispositivos de voz SDK - serviços de voz
titleSuffix: Azure Cognitive Services
description: Este artigo fornece informações para ajudar a resolver problemas que poderá encontrar ao utilizar o SDK de dispositivos de voz.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: f55171a177dfcbebb9bc6df5ce125a8f29494946
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606303"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Resolver problemas do SDK de Dispositivos de Voz

Este artigo fornece informações para ajudar a resolver problemas que poderá encontrar ao utilizar o SDK de dispositivos de voz.

## <a name="certificate-failures"></a>Falhas de certificado

Se obtiver falhas de certificado ao utilizar os serviços de voz, certifique-se de que o dispositivo tem a correta data e hora:

1. Aceda a **definições**. Sob **System**, selecione **data e hora**.

    ![Em definições, selecione a data e hora](media/speech-devices-sdk/qsg-12.png)

1. Manter o **automática data e hora** opção selecionada. Sob **fuso de horário selecione**, selecione o seu fuso horário atual.

    ![Selecione as opções de data e fuso horário](media/speech-devices-sdk/qsg-13.png)

    Quando vir que o tempo o kit de desenvolvimento corresponde a hora no seu PC, o kit de desenvolvimento está ligado à internet.

## <a name="next-steps"></a>Passos Seguintes

* [Reveja as notas de versão](devices-sdk-release-notes.md)
