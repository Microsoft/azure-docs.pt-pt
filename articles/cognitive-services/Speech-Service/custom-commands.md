---
title: Comandos personalizados (versão prévia) – serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos recursos, funcionalidades e restrições para comandos personalizados (versão prévia), uma solução para criar assistentes de voz.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: dd3e27aefe03ee30e721fcbb915d9ad4b3821618
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806308"
---
# <a name="custom-commands-preview"></a>Comandos Personalizados (Pré-visualização)

Os [assistentes de voz](voice-assistants.md) ouvem os usuários e tomam uma ação em resposta, geralmente falando de volta. Eles usam a [fala para o texto](speech-to-text.md) para transcrever a fala do usuário e, em seguida, tomar medidas sobre a compreensão do idioma natural do texto. Essa ação geralmente inclui a saída falada do Assistente gerado com [conversão de texto em fala](text-to-speech.md). Os dispositivos se conectam aos assistentes com o objeto `DialogServiceConnector` do SDK de fala.

Os **comandos personalizados (versão prévia)** são uma solução simplificada para a criação de um assistente de voz. Ele fornece uma experiência de criação unificada, um modelo de hospedagem automática e complexidade relativamente menor em relação a outras opções de criação de assistente, como a [Direct line Speech](direct-line-speech.md). No entanto, essa simplificação vem com uma redução na flexibilidade. Assim, os comandos personalizados (versão prévia) são mais adequados para os cenários de conclusão de tarefas ou de comando e controle. Ele é particularmente bem correspondido para dispositivos de Internet das Coisas (IoT) e sem periféricos.

Para interação e integração de conversas complexas com outras soluções, como a [solução de assistente virtual e modelo empresarial](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) , você é incentivado a usar a fala de linha direta.

Bons candidatos para comandos personalizados (versão prévia) têm um vocabulário fixo com conjuntos bem definidos de variáveis. Por exemplo, as tarefas de automação doméstica, como controlar um termostato, são ideais.

   ![Exemplos de cenários de conclusão de tarefas](media/voice-assistants/task-completion-examples.png "exemplos de conclusão de tarefas")

## <a name="getting-started-with-custom-commands-preview"></a>Introdução aos comandos personalizados (visualização)

A primeira etapa para usar comandos personalizados (versão prévia) para criar um assistente de voz é [obter uma chave de assinatura de fala](get-started.md) e acessar o construtor de comandos personalizados (versão prévia) no [Speech Studio](https://speech.microsoft.com). A partir daí, você pode criar um novo aplicativo de comandos personalizados (versão prévia) e publicá-lo, após o qual um aplicativo no dispositivo pode se comunicar com ele usando o SDK de fala.

   ![Fluxo de criação para comandos personalizados (versão prévia)](media/voice-assistants/custom-commands-flow.png "O fluxo de criação de comandos personalizados (visualização)")

Oferecemos guias de início rápido projetados para que você execute códigos em menos de 10 minutos.

* [Criar um aplicativo de comandos personalizados (versão prévia)](quickstart-custom-speech-commands-create-new.md)
* [Criar um aplicativo de comandos personalizados (versão prévia) com parâmetros](quickstart-custom-speech-commands-create-parameters.md)
* [Conectar-se a um aplicativo de comandos personalizados (versão prévia) com o SDK de fala,C#](quickstart-custom-speech-commands-speech-sdk.md)

## <a name="sample-code"></a>Código de exemplo

O código de exemplo para a criação de um assistente de voz com comandos personalizados (versão prévia) está disponível no GitHub.

* [Exemplos do assistente de voz (SDK)](https://aka.ms/csspeech/samples)

## <a name="customization"></a>Personalização

Os assistentes de voz criados com o uso do serviço de fala podem usar a gama completa de opções de personalização disponíveis para [a](speech-to-text.md)seleção de [texto para fala](text-to-speech.md)e de [palavra-chave personalizada](speech-devices-sdk-create-kws.md).

> [!NOTE]
> As opções de personalização variam por idioma/localidade (consulte [idiomas com suporte](supported-languages.md)).

## <a name="reference-docs"></a>Documentos de referência

* [SDK de Voz](speech-sdk-reference.md)

## <a name="next-steps"></a>Passos seguintes

* [Obtenha uma chave de assinatura do serviço de fala gratuitamente](get-started.md)
* [Obtenha o SDK de fala](speech-sdk.md)
