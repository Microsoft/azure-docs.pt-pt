---
title: Troubleshoot the Speech Devices SDK - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Este artigo fornece informações para ajudá-lo a resolver problemas que pode encontrar quando utiliza o SDK de Dispositivos de Fala.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: c590e0972de289a36890a75b220eddbded701ea8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74815575"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Resolver problemas do SDK de Dispositivos de Voz

Este artigo fornece informações para ajudá-lo a resolver problemas que pode encontrar quando utiliza o SDK de Dispositivos de Fala.

## <a name="certificate-failures"></a>Falhas no certificado

Se tiver falhas no certificado ao utilizar o serviço Defala, certifique-se de que o seu dispositivo tem a data e a hora corretas:

1. Ir para **Definições**. No **sistema,** selecione **Data & tempo**.

    ![Em Definições, selecione Data & tempo](media/speech-devices-sdk/qsg-12.png)

1. Mantenha a **data Automática &** opção de tempo selecionada. Em **fuso horário, selecione**o fuso horário atual.

    ![Selecione opções de data e fuso horário](media/speech-devices-sdk/qsg-13.png)

    Quando você vê que o tempo do dev kit corresponde ao tempo no seu PC, o dev kit está ligado à internet.

## <a name="next-steps"></a>Passos seguintes

* [Reveja as notas de lançamento](devices-sdk-release-notes.md)
