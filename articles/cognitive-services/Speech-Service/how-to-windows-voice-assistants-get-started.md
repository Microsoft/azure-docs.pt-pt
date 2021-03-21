---
title: Assistentes de voz no Windows - Começar
titleSuffix: Azure Cognitive Services
description: Os passos para começar a desenvolver um agente de voz do windows, incluindo uma referência ao arranque rápido do código de amostra.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: b50b98095cdfe0e6ec19c89b57887ebc4a0f6317
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713053"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>Começar com assistentes de voz no Windows

Este guia irá levá-lo através dos passos para começar a desenvolver um assistente de voz no Windows.

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Para começar a desenvolver um assistente de voz para o Windows, terá de se certificar de que tem o ambiente de desenvolvimento adequado.

- **Estúdio Visual:** Terá de instalar o [Microsoft Visual Studio 2017](https://visualstudio.microsoft.com/), Edição Comunitária ou superior
- **Versão windows**: Um PC com um anel rápido Windows Insider construído do Windows e a versão Insider do Windows SDK. Este código de amostra é verificado como trabalhando no Windows Insider Release Build 19025.vb_release_analog.191112-1600 usando o Windows SDK 19018. Qualquer Build ou SDK acima das versões especificadas deve ser compatível.
- **Ferramentas de desenvolvimento UWP**: A carga de trabalho de desenvolvimento da Plataforma Universal Windows em Estúdio Visual. Consulte a página [configurada](/windows/uwp/get-started/get-set-up) do UWP Para preparar a sua máquina para o desenvolvimento de aplicações UWP.
- **Um microfone de trabalho e saída de áudio**

## <a name="obtain-resources-from-microsoft"></a>Obtenha recursos da Microsoft

Alguns recursos necessários para um agente de voz completamente personalizado no Windows exigirão recursos da Microsoft. A [Amostra de Assistente de Voz UWP](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) fornece versões de amostra destes recursos para o desenvolvimento e teste inicial, pelo que esta secção é desnecessária para o desenvolvimento inicial.

- **Modelo de palavra-chave:** A ativação por voz requer um modelo de palavra-chave da Microsoft sob a forma de um ficheiro .bin. O ficheiro .bin fornecido na Amostra de Assistente de Voz do UWP é treinado na palavra-chave *Contoso*.
- **Ficha de recurso de acesso limitado:** Uma vez que as APIs do ConversationalAgent fornecem acesso ao áudio do microfone, estão protegidas sob restrições de Funcionalidade de Acesso Limitada. Para utilizar uma Funcionalidade de Acesso Limitado, terá de obter um token de Funcionalidade de Acesso Limitado ligado à identidade do pacote da sua aplicação a partir da Microsoft.

## <a name="establish-a-dialog-service"></a>Estabelecer um serviço de diálogo

Para uma experiência completa de assistente de voz, a aplicação precisará de um serviço de diálogo que

- Detetar uma palavra-chave num dado ficheiro áudio
- Ouça a entrada do utilizador e converta-a em texto
- Fornecer o texto a um bot
- Traduza a resposta de texto do bot a uma saída de áudio

Estes são os requisitos para criar um serviço básico de diálogo utilizando o Direct Line Speech.

- **Assinatura dos Serviços de Fala:** Uma subscrição de Serviços de Fala Cognitiva para conversões de discurso-a-texto e texto-a-voz. Experimente os Serviços de Fala gratuitamente [aqui.](./overview.md#try-the-speech-service-for-free)
- **Bot Framework bot:**  Um bot criado usando a versão 4.2 ou superior do Bot Framework que é subscrito no [Direct Line Speech](./direct-line-speech.md) para permitir a entrada e saída de voz. [Este guia](./tutorial-voice-enable-your-bot-speech-sdk.md) contém instruções passo a passo para fazer um "echo bot" e subscrevê-lo no Direct Line Speech. Você também pode ir [aqui](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/) para passos sobre como criar um bot personalizado, em seguida, seguir os mesmos passos [aqui](./tutorial-voice-enable-your-bot-speech-sdk.md) para subscrevê-lo em Direct Line Speech, mas com o seu novo bot em vez do "echo bot".

## <a name="try-out-the-sample-app"></a>Experimente a aplicação da amostra

Com a sua chave de subscrição de Serviços de Discurso e o bot ID do Echo Bot, está pronto para experimentar a [amostra do Assistente de Voz do UWP.](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) Siga as instruções na leitura para executar a aplicação e insira as suas credenciais.

## <a name="create-your-own-voice-assistant-for-windows"></a>Crie o seu próprio assistente de voz para o Windows

Assim que receber o seu token de Funcionalidade de Acesso Limitado e ficheiro bin da Microsoft, pode começar no seu próprio assistente de voz no Windows.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Leia o guia de implementação do assistente de voz](windows-voice-assistants-implementation-guide.md)