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

Para poder usar uma palavra-chave personalizada, você precisará criar uma palavra-chave usando a página de [palavra-chave personalizada](https://aka.ms/sdsdk-wakewordportal) no [Speech Studio](https://aka.ms/sdsdk-speechportal). Depois de fornecer uma palavra-chave, ela produz um arquivo que você implanta em seu dispositivo.

1. Vá para o [Speech Studio](https://aka.ms/sdsdk-speechportal) e **entre** ou, se você ainda não tiver uma assinatura de fala, escolha [**criar uma assinatura**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Na página de [palavra-chave personalizada](https://aka.ms/sdsdk-wakewordportal) , crie um **novo projeto**. 

1. Insira um **nome**, uma **Descrição**opcional, e selecione o idioma. Você precisará de um projeto por idioma e o suporte no momento é limitado ao idioma en-US.

    ![Descrever seu projeto de palavra-chave](media/custom-keyword/custom-kws-portal-new-project.png)

1. Selecione o projeto na lista. 

    ![Selecione seu projeto de palavra-chave](media/custom-keyword/custom-kws-portal-project-list.png)

1. Para iniciar um novo modelo de palavra-chave, clique em **treinar modelo**.

1. Insira um **nome** para o modelo de palavra-chave e a **Descrição** opcional e digite na **palavra-chave** de sua escolha e clique em **Avançar**. Temos algumas [diretrizes](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) para ajudar a escolher uma palavra-chave efetiva.

    ![Insira sua palavra-chave](media/custom-keyword/custom-kws-portal-new-model.png)

1. Agora, o portal criará pronúncias candidatas para sua palavra-chave. Ouça cada candidato clicando nos botões de reprodução e remove as verificações ao lado de quaisquer pronúncias incorretas. Quando apenas as boas pronúncias estiverem marcadas, clique em **treinar** para começar a gerar a palavra-chave. 

    ![Examine sua palavra-chave](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Pode levar até 30 minutos para o modelo ser gerado. A lista de palavras-chave mudará de **processamento** para com **êxito** quando o modelo for concluído. Em seguida, você pode baixar o arquivo.

    ![Examine sua palavra-chave](media/custom-keyword/custom-kws-portal-download-model.png)

1. Guarde o ficheiro. zip para o seu computador. Você precisará desse arquivo para implantar sua palavra-chave personalizada em seu dispositivo.

## <a name="next-steps"></a>Passos seguintes

Teste sua palavra-chave personalizada com o guia de [início rápido SDK de dispositivos de fala](https://aka.ms/sdsdk-quickstart).
