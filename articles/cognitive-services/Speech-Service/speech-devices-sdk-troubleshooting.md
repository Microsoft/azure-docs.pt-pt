---
title: Solucionar problemas do SDK de dispositivos de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: Este artigo fornece informações para ajudá-lo a resolver problemas que você pode encontrar ao usar o SDK de dispositivos de fala.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: c590e0972de289a36890a75b220eddbded701ea8
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815575"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Resolver problemas do SDK de Dispositivos de Voz

Este artigo fornece informações para ajudá-lo a resolver problemas que você pode encontrar ao usar o SDK de dispositivos de fala.

## <a name="certificate-failures"></a>Falhas de certificado

Se você obtiver falhas de certificado ao usar o serviço de fala, verifique se o dispositivo tem a data e a hora corretas:

1. Aceda a **definições**. Sob **System**, selecione **data e hora**.

    ![Em definições, selecione a data e hora](media/speech-devices-sdk/qsg-12.png)

1. Manter o **automática data e hora** opção selecionada. Sob **fuso de horário selecione**, selecione o seu fuso horário atual.

    ![Selecione as opções de data e fuso horário](media/speech-devices-sdk/qsg-13.png)

    Quando vir que o tempo o kit de desenvolvimento corresponde a hora no seu PC, o kit de desenvolvimento está ligado à internet.

## <a name="next-steps"></a>Passos seguintes

* [Reveja as notas de versão](devices-sdk-release-notes.md)
