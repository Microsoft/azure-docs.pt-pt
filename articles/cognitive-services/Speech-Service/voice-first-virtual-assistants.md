---
title: Personalizado voz em primeiro lugar virtual assistentes (pré-visualização) - serviços de voz
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos recursos, recursos e restrições para os assistentes virtual personalizados do voz em primeiro lugar, usando o canal de voz de linha direta no Bot Framework e o Cognitive Services voz Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: 4055b474938e38f653021b46f18200f8e39dd69d
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604751"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>Sobre a pré-visualização de assistentes de virtual primeiro de voz personalizada

Assistentes de virtual personalizados, usando os serviços de voz do Azure permitem aos programadores criar interfaces conversacionais naturais, semelhante à humana para seus aplicativos e experiências. Canal de voz de linha direta do Bot Framework aprimora esses recursos, fornecendo um ponto de entrada organizada, coordenada para um bot compatível, que permite a voz na voz horizontalmente interação com baixa latência e alta fiabilidade. Estes bots pode utilizar a compreensão de idiomas (LUIS da Microsoft) para interação de linguagem natural. Conversão de voz de linha direta é acessada por dispositivos com a conversão de voz Software Development Kit (SDK).

   ![Diagrama conceptual do fluxo de serviço de linha direta de voz orquestração](media/voice-first-virtual-assistants/overview.png "fluxo o canal de voz")


Conversão de voz de linha direta e sua funcionalidade associada para os assistentes de virtual de voz-first personalizadas são um suplemento ideal para o [solução assistente Virtual e o modelo de Enterprise](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Apesar de voz de linha direta pode trabalhar com qualquer bot compatíveis, estes recursos fornecem uma linha de base reutilizável para experiências de conversação de alta qualidade, bem como as habilidades de suporte comuns e modelos para começar a trabalhar rapidamente.


## <a name="core-features"></a>Principais recursos

| Category | Funcionalidades |
|----------|----------|
|[Palavra de reativação personalizado](speech-devices-sdk-create-kws.md) | Pode dar aos utilizadores a conversas com os bots com uma palavra-chave personalizada, como "EI Contoso." Esta tarefa é conseguida com um motor de word reativação personalizadas no SDK de voz, que pode ser configurado com uma palavra de reativação personalizado [que pode gerar aqui](speech-devices-sdk-create-kws.md). O canal direto de linha de voz inclui a verificação de word de reativação do lado do serviço que aumenta a precisão da ativação de word reativação versus o dispositivo sozinho.
|[Conversão de voz em texto](speech-to-text.md) | O canal direto de linha de voz em tempo real transcrição de áudio em texto reconhecido por meio de inclui [voz em texto](speech-to-text.md) voz dos serviços do Azure. Este texto está disponível para o bot e a aplicação cliente, como é transcrito.
|[Texto em voz](text-to-speech.md) | Respostas textuais do seu bot vão ser sintetizadas usando [voz](text-to-speech.md) voz dos serviços do Azure. Esta síntese, em seguida, estarão disponível para a aplicação de cliente como uma transmissão de áudio. A Microsoft oferece a capacidade de criar a sua voz Neural TTS personalizado de alta qualidade, que permite uma voz à sua marca, para saber mais [contacte-nos](mailto:mstts@microsoft.com).
|[Voz de linha direta](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Como um canal dentro da estrutura de Bot, direta de linha de voz permite uma ligação suave e simples entre a aplicação de cliente, um bot compatível e as capacidades dos serviços de voz do Azure. Para obter mais informações sobre como configurar o seu bot para utilizar o canal direto de linha de voz, consulte [sua página na documentação do Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="sample-code"></a>Código de exemplo

Código de exemplo para a criação de um Assistente de virtual de voz em primeiro lugar está disponível no GitHub. Esses exemplos abrangem a aplicação de cliente para ligar ao seu bot em várias linguagens de programação populares.

* [Exemplos de Assistente de voz em primeiro lugar virtual (SDK)](https://aka.ms/csspeech/samples)
* [Início rápido: voz em primeiro lugar virtual assistentes (C#)](quickstart-virtual-assistant-csharp-uwp.md)
* [Início rápido: voz em primeiro lugar virtual assistentes (Java)](quickstart-virtual-assistant-java-jre.md)

## <a name="customization"></a>Personalização

Assistentes de voz em primeiro lugar virtual criados usando os serviços de voz do Azure podem utilizar o intervalo completo de opções de personalização disponíveis para [voz em texto](speech-to-text.md), [voz](text-to-speech.md), e [palavra-chave personalizada seleção](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Opções de personalização variam consoante o idioma/região (consulte [idiomas suportados](supported-languages.md)).

## <a name="reference-docs"></a>Documentos de referência

* [SDK de Voz](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Passos Seguintes

* [Obtenha gratuitamente uma chave de subscrição de serviços de voz](get-started.md)
* [Obtenha o SDK de voz](speech-sdk.md)
* [Criar e implementar um bot básico](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Obter a solução de assistente Virtual e o modelo de Enterprise](https://github.com/Microsoft/AI)
