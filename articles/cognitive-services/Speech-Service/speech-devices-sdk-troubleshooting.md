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
ms.date: 05/02/2019
ms.author: wellsi
ms.openlocfilehash: 87fb35f06dcb1d1e3fb8c3ae3be64c7448162f14
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026159"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Resolver problemas relacionados com os SDK de dispositivos de voz

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
