---
title: Criar palavras-chave personalizadas - Serviço de fala
titleSuffix: Azure Cognitive Services
description: O seu dispositivo está sempre a ouvir uma palavra-chave (ou frase). Quando o utilizador diz a palavra-chave, o dispositivo envia todo o áudio subsequente para a nuvem, até que o utilizador deixe de falar. Personalizar a sua palavra-chave é uma forma eficaz de diferenciar o seu dispositivo e fortalecer a sua marca.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: trbye
ms.openlocfilehash: 8e67d624c77eb838f7646731bbdedd8f97f81b96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400066"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>Crie uma palavra-chave personalizada usando o Speech Studio

O seu dispositivo está sempre a ouvir uma palavra-chave (ou frase). Por exemplo, "Hey Cortana" é uma palavra-chave para o assistente da Cortana. Quando o utilizador diz a palavra-chave, o dispositivo envia todo o áudio subsequente para a nuvem, até que o utilizador deixe de falar. Personalizar a sua palavra-chave é uma forma eficaz de diferenciar o seu dispositivo e fortalecer a sua marca.

Neste artigo, aprende-se a criar uma palavra-chave personalizada para o seu dispositivo.

## <a name="create-your-keyword"></a>Crie a sua palavra-chave

Antes de poder usar uma palavra-chave personalizada, terá de criar uma palavra-chave usando a página [de palavras-chave personalizadas](https://aka.ms/sdsdk-wakewordportal) no [Speech Studio](https://aka.ms/sdsdk-speechportal). Depois de fornecer uma palavra-chave, produz um ficheiro que implementa no seu dispositivo.

1. Vá ao Estúdio de [Discurso](https://aka.ms/sdsdk-speechportal) e **assine** ou, se ainda não tiver uma subscrição de discurso, escolha Criar [**uma subscrição**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Na página [de Palavras-chave personalizadas,](https://aka.ms/sdsdk-wakewordportal) crie um **novo projeto.** 

1. Introduza um **Nome**, uma **Descrição**opcional e selecione o idioma. Você precisará de um projeto por língua e o suporte está atualmente limitado à língua en-EUA.

    ![Descreva o seu projeto de palavra-chave](media/custom-keyword/custom-kws-portal-new-project.png)

1. Selecione o seu projeto na lista. 

    ![Selecione o seu projeto de palavra-chave](media/custom-keyword/custom-kws-portal-project-list.png)

1. Para iniciar um novo modelo de palavra-chave clique no **modelo Train**.

1. Introduza um **Nome** para o modelo de palavra-chave e **descrição** opcional e escreva na **palavra-chave** à sua escolha, e clique em **Seguinte**. Temos algumas [orientações](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) para ajudar a escolher uma palavra-chave eficaz.

    ![Introduza a sua palavra-chave](media/custom-keyword/custom-kws-portal-new-model.png)

1. O portal irá agora criar pronúncias candidatas para a sua palavra-chave. Ouça cada candidato clicando nos botões de reprodução e remova as verificações junto a quaisquer pronúncias que estejam erradas. Uma vez verificadas apenas boas pronúncias, clique em **Train** para começar a gerar a palavra-chave. 

    ![Reveja a sua palavra-chave](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Pode levar até 30 minutos para o modelo ser gerado. A lista de palavras-chave passará de **Processamento** para **Sucesso** quando o modelo estiver completo. Em seguida, pode descarregar o ficheiro.

    ![Reveja a sua palavra-chave](media/custom-keyword/custom-kws-portal-download-model.png)

1. Guarde o ficheiro .zip para o seu computador. Vai precisar deste ficheiro para implementar a sua palavra-chave personalizada para o seu dispositivo.

## <a name="next-steps"></a>Passos seguintes

Teste a sua palavra-chave personalizada com dispositivos de [fala SDK Quickstart](https://aka.ms/sdsdk-quickstart).
