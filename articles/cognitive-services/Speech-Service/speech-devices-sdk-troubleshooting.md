---
title: Resolução de problemas dos dispositivos de fala SDK - serviço de fala
titleSuffix: Azure Cognitive Services
description: Este artigo fornece informações para ajudá-lo a resolver problemas que pode encontrar quando utilizar o SDK dos Dispositivos de Fala.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: c590e0972de289a36890a75b220eddbded701ea8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "74815575"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Resolver problemas do SDK de Dispositivos de Voz

Este artigo fornece informações para ajudá-lo a resolver problemas que pode encontrar quando utilizar o SDK dos Dispositivos de Fala.

## <a name="certificate-failures"></a>Falhas de certificados

Se tiver falhas de certificado ao utilizar o serviço Speech, certifique-se de que o seu dispositivo tem a data e a hora corretas:

1. Ir para **Definições**. No **Sistema**, selecione **Data & hora**.

    ![Em Definições, selecione data & hora](media/speech-devices-sdk/qsg-12.png)

1. Selecione a **opção de hora & de data automática.** Em **Fuso horário Select,** selecione o seu fuso horário atual.

    ![Selecione opções de data e fuso horário](media/speech-devices-sdk/qsg-13.png)

    Quando vir que o tempo do dev kit corresponde à hora do seu PC, o kit dev está ligado à internet.

## <a name="next-steps"></a>Passos seguintes

* [Rever as notas de lançamento](devices-sdk-release-notes.md)
