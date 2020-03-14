---
title: Comandos Personalizados (Pré-visualização) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral das funcionalidades, capacidades e restrições para Comandos Personalizados (Pré-visualização), uma solução para criar aplicações de voz.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 2e1b6ee0bd6c392804915fac6ff23278a00b6d33
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367844"
---
# <a name="what-are-custom-commands-preview"></a>O que são Comandos Personalizados (Pré-visualização)?

Aplicações de voz como [assistentes](voice-assistants.md) de voz ouvem os utilizadores e tomam uma ação em resposta, muitas vezes falando de volta. Usam [o discurso ao texto](speech-to-text.md) para transcrever o discurso do utilizador e, em seguida, tomam medidas sobre a compreensão da linguagem natural do texto. Esta ação inclui frequentemente a saída falada do assistente gerado com [texto-a-fala](text-to-speech.md). Os dispositivos ligam-se aos assistentes com o objeto `DialogServiceConnector` do SDK do Discurso.

**Comandos Personalizados (Pré-visualização)** é uma solução simplificada para a criação de aplicações de voz. Proporciona uma experiência de autoria unificada, um modelo de hospedagem automático, e uma complexidade relativamente menor em relação a outras opções como [Direct Line Speech](direct-line-speech.md). No entanto, esta simplificação vem com uma redução da flexibilidade. Assim, os Comandos Personalizados (Pré-visualização) são mais adequados para a conclusão da tarefa ou cenários de comando e controlo. É particularmente bem combinado para Internet das Coisas (IoT) e dispositivos sem cabeça.

Para uma interação conversacional complexa e integração com outras soluções, como a [Solução Assistente Virtual e o Modelo de Empresa,](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) é encorajado a usar o Discurso da Linha Direta.

Os bons candidatos a Comandos Personalizados (Pré-visualização) têm um vocabulário fixo com conjuntos bem definidos de variáveis. Por exemplo, tarefas de domótica, como controlar um termóstato, são ideais.

   ![Exemplos de cenários de conclusão de tarefas](media/voice-assistants/task-completion-examples.png "exemplos de conclusão de tarefas")

## <a name="getting-started-with-custom-commands-preview"></a>Começar com Comandos Personalizados (Pré-visualização)

O primeiro passo para usar comandos personalizados (Pré-visualização) para fazer uma aplicação de voz é [obter uma chave de subscrição](get-started.md) de discurso e aceder ao Construtor de Comandos Personalizados (Pré-visualização) no Estúdio de [Discurso](https://speech.microsoft.com). A partir daí, pode ser autor de uma nova aplicação de Comandos Personalizados (Pré-visualização) e publicá-la, após a qual uma aplicação no dispositivo pode comunicar com ela usando o Speech SDK.

   ![Fluxo de autoria para comandos personalizados (Pré-visualização)](media/voice-assistants/custom-commands-flow.png "O fluxo de autor de comandos personalizados (pré-visualização)")

Oferecemos quickstarts projetados para tê-lo em funcionamento código em menos de 10 minutos.

* [Criar uma aplicação de Comandos Personalizados (Pré-visualização)](quickstart-custom-speech-commands-create-new.md)
* [Criar uma aplicação de Comandos Personalizados (Pré-visualização) com parâmetros](quickstart-custom-speech-commands-create-parameters.md)
* [Ligue-se a uma aplicação de Comandos Personalizados (Pré-visualização) com o SDK de Fala,C#](quickstart-custom-speech-commands-speech-sdk.md)

Assim que terminar com os quickstarts, explore os nossos "how-tos".

- [Adicionar validações aos parâmetros de Comando Personalizado](./how-to-custom-speech-commands-validations.md)
- [Cumprir comandos sobre o cliente com o SDK de Fala](./how-to-custom-speech-commands-fulfill-sdk.md)
- [Adicione uma confirmação a um Comando Personalizado](./how-to-custom-speech-commands-confirmations.md)
- [Adicione uma correção de um passo a um Comando Personalizado](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>Passos seguintes

* [Obtenha uma chave de subscrição do serviço Speech gratuitamente](get-started.md)
* [Vá ao Estúdio de Discurso para experimentar comandos personalizados](https://speech.microsoft.com)
* [Obtenha o SDK do Discurso](speech-sdk.md)
