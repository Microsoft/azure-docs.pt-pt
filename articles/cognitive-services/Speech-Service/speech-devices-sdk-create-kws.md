---
title: Criar uma palavra-chave personalizada-serviço de fala
titleSuffix: Azure Cognitive Services
description: Seu dispositivo está sempre ouvindo uma palavra-chave (ou frase). Quando o usuário diz a palavra-chave, o dispositivo envia todo o áudio subsequente para a nuvem até que o usuário pare de falar. Personalizar sua palavra-chave é uma maneira eficaz de diferenciar seu dispositivo e fortalecer sua identidade visual.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: bf9afb66163532b4095e0d30b1167010320abbf8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490939"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>Criar uma palavra-chave personalizada usando o serviço de fala

Seu dispositivo está sempre ouvindo uma palavra-chave (ou frase). Por exemplo, "Ei Cortana" é uma palavra-chave para o assistente da Cortana. Quando o usuário diz a palavra-chave, o dispositivo envia todo o áudio subsequente para a nuvem até que o usuário pare de falar. Personalizar sua palavra-chave é uma maneira eficaz de diferenciar seu dispositivo e fortalecer sua identidade visual.

Neste artigo, você aprenderá a criar uma palavra-chave personalizada para seu dispositivo.

## <a name="choose-an-effective-keyword"></a>Escolher uma palavra-chave efetiva

Considere as seguintes diretrizes ao escolher uma palavra-chave:

* Sua palavra-chave deve ser uma palavra em inglês ou uma frase. Não deve levar mais de dois segundos para dizer.

* As palavras de 4 a 7 sílabas funcionam melhor. Por exemplo, "Ei, computador" é uma boa palavra-chave. Apenas "Ei" é um mau.

* As palavras-chave devem seguir as regras de pronúncia em inglês comuns.

* Uma única ou até mesmo uma palavra feita que segue regras de pronúncia em inglês comuns pode reduzir falsos positivos. Por exemplo, "computerama" pode ser uma boa palavra-chave.

* Não escolha uma palavra comum. Por exemplo, "comer" e "Go" são palavras que as pessoas dizem frequentemente em conversações comuns. Eles podem ser gatilhos falsos para seu dispositivo.

* Evite usar uma palavra-chave que possa ter pronúncias alternativas. Os usuários precisariam saber a pronúncia "correta" para que seu dispositivo responda. Por exemplo, "509" pode ser pronunciado "5 0 9", "5 0 9" ou "509". "R.E.I." pode ser pronunciado "r-e-i" ou "Ray". "Live" pode ser pronunciado "/līv/" ou "/Liv/".

* Não use caracteres especiais, símbolos ou dígitos. Por exemplo, "Go #" e "20 + gatos" não seriam boas palavras-chave. No entanto, "fique nítido" ou "vinte mais gatos" pode funcionar. Você ainda pode usar os símbolos em sua identidade visual e usar o marketing e a documentação para reforçar a pronúncia correta.

> [!NOTE]
> Se você escolher uma palavra com marca registrada como sua palavra-chave, certifique-se de que você possui essa marca registrada ou que você tem permissão do proprietário da marca comercial para usar a palavra. A Microsoft não é responsável por problemas legais que possam surgir da sua escolha de palavra-chave.

## <a name="create-your-keyword"></a>Criar sua palavra-chave

Antes de usar uma palavra-chave personalizada com seu dispositivo, você precisará criar uma palavra-chave com o serviço de geração de palavras-chave personalizada da Microsoft. Depois de fornecer uma palavra-chave, o serviço produz um arquivo que você implanta em seu kit de desenvolvimento para habilitar a palavra-chave em seu dispositivo.

1. Vá para o [Speech Studio](https://aka.ms/sdsdk-speechportal) e **entre** ou, se você ainda não tiver uma assinatura de fala, escolha [**criar uma assinatura**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Na página [palavra-chave personalizada](https://aka.ms/sdsdk-wakewordportal) , digite a palavra-chave de sua escolha e clique em **Adicionar palavra-chave**. Temos algumas [diretrizes](#choose-an-effective-keyword) para ajudar a escolher uma palavra-chave efetiva. No momento, o suporte é limitado ao idioma en-US.

    ![Insira sua palavra-chave](media/speech-devices-sdk/custom-kws-portal-enter-keyword.png)

1. Agora, o portal criará pronúncias candidatas para sua palavra-chave. Ouça cada candidato clicando nos botões de reprodução e remove as verificações ao lado de quaisquer pronúncias incorretas. Quando apenas as boas pronúncias estiverem marcadas, selecione **Enviar** para começar a gerar a palavra-chave. Se você quiser alterar a palavra-chave, primeiro remova a existente clicando no botão excluir que aparece no lado direito da linha ao passar o mouse sobre ela.

    ![Examine sua palavra-chave](media/speech-devices-sdk/custom-kws-portal-review-keyword.png)

1. Pode levar até um minuto para que o modelo seja gerado. Em seguida, você será solicitado a baixar o arquivo.

    ![Baixe sua palavra-chave](media/speech-devices-sdk/custom-kws-portal-download-keyword.png)

1. Salve o arquivo. zip em seu computador. Você precisará desse arquivo para implantar sua palavra-chave personalizada no kit de desenvolvimento.

## <a name="next-steps"></a>Passos seguintes

Teste sua palavra-chave personalizada com o guia de [início rápido SDK de dispositivos de fala](https://aka.ms/sdsdk-quickstart).
