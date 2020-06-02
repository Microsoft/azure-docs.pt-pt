---
title: Comandos personalizados (Pré-visualização) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral das funcionalidades, capacidades e restrições para Comandos Personalizados (Preview), uma solução para criar aplicações de voz.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 3c02e823202aa848a4de94885276835899562e4b
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266020"
---
# <a name="what-is-custom-commands-preview"></a>O que é Comandos Personalizados (Pré-visualização)?

Aplicações de voz como [assistentes de voz](voice-assistants.md) ouvem os utilizadores e tomam medidas em resposta, muitas vezes respondendo. Usam [a palavra-a-texto para](speech-to-text.md) transcrever o discurso do utilizador e, em seguida, tomam medidas sobre a compreensão da linguagem natural do texto. Esta ação inclui frequentemente a saída falada do assistente gerado com [texto-a-voz](text-to-speech.md). Os dispositivos ligam-se a assistentes com o objeto do SDK do `DialogServiceConnector` discurso.

**Comandos Personalizados (Preview)** é uma solução simplificada para criar aplicações de voz. Proporciona uma experiência de autoria unificada, um modelo de hospedagem automática e uma complexidade relativamente menor em relação a outras opções como [Direct Line Speech](direct-line-speech.md). No entanto, esta simplificação vem com uma redução da flexibilidade. Assim, os Comandos Personalizados (Pré-visualização) são os mais adequados para a conclusão de tarefas ou cenários de comando e controlo. É particularmente bem combinado para Internet of Things (IoT) e dispositivos sem cabeça.

Para uma interação e integração de conversação complexa com outras soluções como a [Solução assistente virtual e o modelo de empresa,](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) você é encorajado a usar o Discurso de Linha Direta.

Os bons candidatos a Comandos Personalizados (Preview) têm um vocabulário fixo com conjuntos bem definidos de variáveis. Por exemplo, as tarefas de domótica, como controlar um termóstato, são ideais.

   ![Exemplos de cenários de conclusão de tarefas](media/voice-assistants/task-completion-examples.png "exemplos de conclusão de tarefas")

## <a name="getting-started-with-custom-commands-preview"></a>Começar com Comandos Personalizados (Pré-visualização)

O primeiro passo para utilizar comandos personalizados (Pré-visualização) para fazer uma aplicação de voz é [obter uma chave de subscrição de voz](get-started.md) e aceder ao Construtor de Comandos Personalizados (Preview) no Estúdio da [Fala](https://speech.microsoft.com). A partir daí, pode ser autor de uma nova Aplicação de Comandos Personalizados (Pré-visualização) e publicá-la, após a qual uma aplicação no dispositivo pode comunicar com ela utilizando o SDK de fala.

   ![Fluxo de autoria de comandos personalizados (pré-visualização)](media/voice-assistants/custom-commands-flow.png "O fluxo de autoria dos Comandos Personalizados (Pré-visualização)")

Oferecemos quickstarts projetados para que você execute código em menos de 10 minutos.

* [Criar uma aplicação de Comandos Personalizados (Pré-visualização)](quickstart-custom-speech-commands-create-new.md)
* [Criar uma aplicação de Comandos Personalizados (Pré-visualização) com parâmetros](quickstart-custom-speech-commands-create-parameters.md)
* [Conecte-se a uma aplicação de Comandos Personalizados (Pré-visualização) com o SDK de fala, C #](quickstart-custom-speech-commands-speech-sdk.md)

Assim que terminar com os quickstarts, explore os nossos "how-tos".

- [Adicionar validações aos parâmetros de Comando Personalizado](./how-to-custom-speech-commands-validations.md)
- [Cumprir comandos no cliente com o Discurso SDK](./how-to-custom-speech-commands-fulfill-sdk.md)
- [Adicionar uma confirmação a um Comando Personalizado](./how-to-custom-speech-commands-confirmations.md)
- [Adicionar uma correção de um passo a um Comando Personalizado](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>Passos seguintes

* [Obtenha gratuitamente uma chave de subscrição de serviço de discurso](get-started.md)
* [Vá ao Estúdio de Fala para experimentar comandos personalizados](https://speech.microsoft.com)
* [Obtenha o Discurso SDK](speech-sdk.md)
