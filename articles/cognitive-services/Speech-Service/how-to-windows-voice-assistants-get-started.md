---
title: Assistentes de voz no Windows - Começar
titleSuffix: Azure Cognitive Services
description: Os passos para começar a desenvolver um agente de voz windows, incluindo uma referência ao código de amostra arranque rapidamente.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 4ea2f88b02738645af3f8fc32d5fdb99168a1122
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997383"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>Começando com assistentes de voz no Windows

Este guia irá levá-lo através dos passos para começar a desenvolver um assistente de voz no Windows.

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Para começar a desenvolver um assistente de voz para windows, terá de se certificar de que tem o ambiente de desenvolvimento adequado.

- **Visual Studio:** Terá de instalar o [Microsoft Visual Studio 2017](https://visualstudio.microsoft.com/), Community Edition ou superior
- **Versão windows**: Um PC com uma estrutura rápida do Windows Insider do Windows e a versão Do Windows Insider do Windows SDK.Este código de amostra é verificado como funcionando no Windows Insider Release Build 19025.vb_release_analog.191112-1600 usando o Windows SDK 19018.Qualquer Build ou SDK acima das versões especificadas deve ser compatível.
- **Ferramentas de desenvolvimento da UWP**: A carga de trabalho de desenvolvimento da Plataforma Universal Windows em Estúdio Visual.Consulte a página Do UWP [Get configurada](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) para preparar a sua máquina para desenvolver aplicações UWP.
- **Um microfone de trabalho e saída de áudio**

## <a name="obtain-resources-from-microsoft"></a>Obtenha recursos da Microsoft

Alguns recursos necessários para um agente de voz completamente personalizado no Windows exigirão recursos da Microsoft. A Amostra de Assistente de [Voz do UWP](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) fornece versões de amostra destes recursos para desenvolvimento e teste iniciais, pelo que esta secção é desnecessária para o desenvolvimento inicial.

- **Modelo de palavra-chave:** A ativação por voz requer um modelo de palavra-chave da Microsoft sob a forma de um ficheiro .bin. O ficheiro .bin fornecido na Amostra de Assistente de Voz da UWP é treinado na palavra-chave "Contoso".
- Recurso de **acesso limitado Token:** Uma vez que as APIs do ConversationalAgent fornecem acesso ao áudio do microfone, estão protegidas sob restrições de funcionalidadede acesso limitado.Para utilizar uma Funcionalidade de Acesso Limitado, terá de obter um token de Funcionalidade de Acesso Limitado ligado à identidade de pacote da sua aplicação a partir da Microsoft.

## <a name="establish-a-dialog-service"></a>Estabelecer um serviço de diálogo

Para uma experiência completa de assistente de voz, a aplicação precisará de um serviço de diálogo que

- Detete uma palavra-chave num determinado ficheiro áudio
- Ouça a entrada do utilizador e converta-a em texto
- Forneça o texto a um bot
- Traduza a resposta de texto do bot para uma saída de áudio

Estes são os requisitos para criar um serviço de diálogo básico usando o Discurso da Linha Direta.

- **Assinatura dos Serviços de Fala:** Uma subscrição dos Serviços cognitivos de fala para conversões de discurso a texto e texto-a-fala. Experimente serviços de fala gratuitamente [aqui.](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started)
- **Bot Framework bot:**  Um bot criado usando a versão Bot Framework 4.2 ou superior que é subscrito ao [Direct Line Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/direct-line-speech) para permitir a entrada e saída de voz. [Este guia](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk) contém instruções passo a passo para fazer um "eco bot" e subscrevê-lo ao Direct Line Speech. Você também pode ir [aqui](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/) para dar passos sobre como criar um bot personalizado, em seguida, seguir os mesmos passos [aqui](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk) para subscrevê-lo ao Direct Line Speech, mas com o seu novo bot em vez do "eco bot".

## <a name="try-out-the-sample-app"></a>Experimente a aplicação de amostras

Com a sua chave de subscrição do Serviço de Fala e o bot do echo bot ID, está pronto para experimentar a [amostra do UWP Voice Assistant](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample). Siga as instruções no readme para executar a aplicação e insira as suas credenciais.

## <a name="create-your-own-voice-assistant-for-windows"></a>Crie o seu próprio assistente de voz para windows

Assim que receber o seu ficheiro de funcionalidade de acesso limitado e de bin da Microsoft, pode começar no seu próprio assistente de voz no Windows.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Leia o guia de implementação do assistente de voz](windows-voice-assistants-implementation-guide.md)
