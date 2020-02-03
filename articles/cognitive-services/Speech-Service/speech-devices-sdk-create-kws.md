---
title: Criar palavras-chave personalizadas-serviço de fala
titleSuffix: Azure Cognitive Services
description: Seu dispositivo está sempre ouvindo uma palavra-chave (ou frase). Quando o usuário diz a palavra-chave, o dispositivo envia todo o áudio subsequente para a nuvem até que o usuário pare de falar. Personalizar sua palavra-chave é uma maneira eficaz de diferenciar seu dispositivo e fortalecer sua identidade visual.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 46e9f8e660c3fd62807d630481e6b3057d2351a5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717018"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>Criar uma palavra-chave personalizada usando o Speech Studio

Seu dispositivo está sempre ouvindo uma palavra-chave (ou frase). Por exemplo, "Ei Cortana" é uma palavra-chave para o assistente da Cortana. Quando o usuário diz a palavra-chave, o dispositivo envia todo o áudio subsequente para a nuvem até que o usuário pare de falar. Personalizar sua palavra-chave é uma maneira eficaz de diferenciar seu dispositivo e fortalecer sua identidade visual.

Neste artigo, você aprenderá a criar uma palavra-chave personalizada para seu dispositivo.

## <a name="create-your-keyword"></a>Criar sua palavra-chave

Antes de poder usar uma palavra-chave personalizada, terá de criar uma palavra-chave usando a página [de palavras-chave personalizadas](https://aka.ms/sdsdk-wakewordportal) no [Speech Studio](https://aka.ms/sdsdk-speechportal). Depois de fornecer uma palavra-chave, ela produz um arquivo que você implanta em seu dispositivo.

1. Vá ao Estúdio de [Discurso](https://aka.ms/sdsdk-speechportal) e **assine** ou, se ainda não tiver uma subscrição de discurso, escolha Criar [**uma subscrição**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Na página [de Palavras-chave personalizadas,](https://aka.ms/sdsdk-wakewordportal) crie um **novo projeto.** 

1. Introduza um **Nome**, uma **Descrição**opcional e selecione o idioma. Você precisará de um projeto por idioma e o suporte no momento é limitado ao idioma en-US.

    ![Descrever seu projeto de palavra-chave](media/custom-keyword/custom-kws-portal-new-project.png)

1. Selecione o projeto na lista. 

    ![Selecione seu projeto de palavra-chave](media/custom-keyword/custom-kws-portal-project-list.png)

1. Para iniciar um novo modelo de palavra-chave clique no **modelo Train**.

1. Introduza um **Nome** para o modelo de palavra-chave e **descrição** opcional e escreva na **palavra-chave** à sua escolha, e clique em **Seguinte**. Temos algumas [orientações](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) para ajudar a escolher uma palavra-chave eficaz.

    ![Insira sua palavra-chave](media/custom-keyword/custom-kws-portal-new-model.png)

1. Agora, o portal criará pronúncias candidatas para sua palavra-chave. Ouça cada candidato clicando nos botões de reprodução e remove as verificações ao lado de quaisquer pronúncias incorretas. Uma vez verificadas apenas boas pronúncias, clique em **Train** para começar a gerar a palavra-chave. 

    ![Examine sua palavra-chave](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Pode levar até 30 minutos para o modelo ser gerado. A lista de palavras-chave passará de **Processamento** para **Sucesso** quando o modelo estiver completo. Em seguida, você pode baixar o arquivo.

    ![Examine sua palavra-chave](media/custom-keyword/custom-kws-portal-download-model.png)

1. Guarde o ficheiro. zip para o seu computador. Você precisará desse arquivo para implantar sua palavra-chave personalizada em seu dispositivo.

## <a name="next-steps"></a>Passos Seguintes

Teste a sua palavra-chave personalizada com dispositivos de [fala SDK Quickstart](https://aka.ms/sdsdk-quickstart).
