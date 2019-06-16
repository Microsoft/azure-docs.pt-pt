---
title: O que é de voz personalizada? -Serviços de voz
titlesuffix: Azure Cognitive Services
description: Voz personalizada é um conjunto de ferramentas online que permitem-lhe criar uma voz reconhecível, um do-única para sua marca. Só para começar a utilizar são necessários alguns arquivos de áudio e transcrições de associados. Siga os links abaixo para começar a criar uma experiência de voz em texto personalizada.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 016dcf32f2f846e43362f17bc9f4627113908352
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075154"
---
# <a name="get-started-with-custom-voice"></a>Introdução à Voz Personalizada

Voz personalizada é um conjunto de ferramentas online que permitem-lhe criar uma voz reconhecível, um do-única para sua marca. Só para começar a utilizar são necessários alguns arquivos de áudio e transcrições de associados. Siga os links abaixo para começar a criar uma experiência de texto para discurso personalizada.

## <a name="whats-in-custom-voice"></a>O que inclui voz personalizada?

Antes de iniciar com voz personalizada, terá uma conta do Azure e uma subscrição de serviços de voz. Depois de criar uma conta, pode preparar os dados, preparar e testar seus modelos, avaliar a qualidade de voz e, por fim, implementar o seu modelo de voz personalizada.

O diagrama abaixo destaca os passos para criar um modelo de voz personalizada com o portal de voz personalizada. Utilize as ligações para saber mais.

![Diagrama de arquitetura de voz personalizado](media/custom-voice/custom-voice-diagram.png)

1.  [Inscrever-se e crie um projeto](#set-up-your-azure-account) - criar uma conta do Azure e criar uma subscrição de serviços de voz. Esta subscrição unificada fornece acesso a conversão de voz em texto, voz, tradução de voz e o portal de voz personalizada. Em seguida, utilizar a sua subscrição de serviços de voz, crie seu primeiro projeto de voz personalizada.

2.  [Carregar dados](how-to-custom-voice-create-voice.md#upload-your-datasets) -carregar dados (áudio e texto) com o portal de voz personalizada ou a API de voz personalizada. No portal, pode investigar e avaliar as pontuações de pronúncia e proporções de sinal e ruído. Para obter mais informações, consulte [como preparar dados para voz personalizada](how-to-custom-voice-prepare-data.md).

3.  [Preparar o seu modelo](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – utilizar os seus dados para criar um modelo de texto para discurso de voz personalizada. Pode preparar um modelo em idiomas diferentes. Depois de treinamento, testar o seu modelo e, se estiver satisfeito com o resultado, pode implementar o modelo.

4.  [Implementar o seu modelo](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) - criar um ponto final personalizado para o modelo de texto para discurso de voz e utilizá-lo para síntese de fala em seus produtos, ferramentas e aplicações.

## <a name="set-up-your-azure-account"></a>Configurar a sua conta do Azure

É necessária uma subscrição de serviços de voz antes de poder utilizar o portal de voz personalizada para criar um modelo personalizado. Siga estas instruções para criar uma subscrição de serviços de voz no Azure. Se não tiver uma conta do Azure, pode inscrever-se para um novo.  

Depois de criar uma conta do Azure e uma subscrição de serviços de voz, terá de iniciar sessão portal da voz personalizada e ligue-se a sua subscrição.

1. Obtenha a chave de subscrição de serviços de voz do portal do Azure.
2. Inicie sessão para o [portal de voz personalizada](https://aka.ms/custom-voice).
3. Selecione a sua subscrição e crie um projeto de conversão de voz.
4. Se gostaria de mudar para outra subscrição de voz, utilize o ícone de engrenagem localizado na parte superior da navegação.

> [!NOTE]
> O serviço de voz personalizada não suporta a chave de avaliação gratuita de 30 dias. Tem de ter um F0 ou uma chave de S0 criada no Azure antes de poder utilizar o serviço.

## <a name="how-to-create-a-project"></a>Como criar um projeto

Como os dados, modelos, testes e pontos finais estão organizados de conteúdo **projetos** no portal da voz personalizada. Cada projeto é específico para um idioma/país e o sexo de voz que pretende criar. Por exemplo, pode criar um projeto para uma voz female para bots de bate-papo de sua central de atendimento que utilizam o inglês nos Estados Unidos (em inglês).

Para criar seu primeiro projeto, selecione o **voz Text-to-Speech/personalizado** separador, em seguida, clique em **novo projeto**. Siga as instruções fornecidas pelo Assistente para criar o projeto. Depois de criar um projeto, irá ver quatro separadores: **Dados**, **formação**, **teste**, e **implementação**. Utilize as ligações fornecidas [próximos passos](#next-steps) para saber como utilizar cada guia.

## <a name="next-steps"></a>Passos Seguintes

- [Preparar os dados de voz personalizada](how-to-custom-voice.md)
- [Criar uma voz personalizada](how-to-custom-voice-create-voice.md)
- [Guia de: Gravar seus exemplos de voz](record-custom-voice-samples.md)
