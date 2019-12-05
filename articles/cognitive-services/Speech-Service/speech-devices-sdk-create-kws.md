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
ms.date: 11/18/2019
ms.author: erhopf
ms.openlocfilehash: 15a0e27f3f96eda27182e8437dc95d047f56e260
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815289"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>Criar uma palavra-chave personalizada usando o serviço de fala

Seu dispositivo está sempre ouvindo uma palavra-chave (ou frase). Por exemplo, "Ei Cortana" é uma palavra-chave para o assistente da Cortana. Quando o usuário diz a palavra-chave, o dispositivo envia todo o áudio subsequente para a nuvem até que o usuário pare de falar. Personalizar sua palavra-chave é uma maneira eficaz de diferenciar seu dispositivo e fortalecer sua identidade visual.

Neste artigo, você aprenderá a criar uma palavra-chave personalizada para seu dispositivo.

## <a name="choose-an-effective-keyword"></a>Escolher uma palavra-chave efetiva

Considere as seguintes diretrizes ao escolher uma palavra-chave:

* Sua palavra-chave deve ser uma palavra em inglês ou uma frase. Deve demorar mais do que dois segundos para dizer.

* Palavras de 4 a 7 sílabas funcionam melhor. Por exemplo, "Ei, computador" é uma boa palavra-chave. Apenas "Olá" é ruim.

* As palavras-chave devem seguir as regras de pronúncia em inglês comuns.

* Um único ou até mesmo uma palavra produzida que segue as regras de pronúncia inglesa comum pode reduzir os falsos positivos. Por exemplo, "computerama" pode ser uma boa palavra-chave.

* Não escolha uma palavra em comum. Por exemplo, "" ilimitado"e"Ir"são palavras que as pessoas dizem com frequência na conversação comum. Elas podem ser acionadores falso para o seu dispositivo.

* Evite usar uma palavra-chave que possa ter pronúncias alternativas. Os utilizadores teria de saber a pronúncia "right" para que o seu dispositivo para responder. Por exemplo, "509" pode ser pronunciado "cinco zero nine," "cinco AH nove," ou "cinco centenas e nove." "R.E.I." pode ser pronunciado "r-e-i" ou "ray." "Ao vivo" pode ser pronunciado "/līv/" ou "/liv/".

* Não utilize carateres especiais, símbolos ou dígitos. Por exemplo, "Go #" e "20 + gatos" não seriam boas palavras-chave. No entanto, "aceda pequenos" ou "vinte plus gatos" poderá funcionar. Ainda pode usar os símbolos em sua imagem corporativa e usar o marketing e documentação para reforçar a pronúncia adequada.

> [!NOTE]
> Se você escolher uma palavra com marca registrada como sua palavra-chave, certifique-se de que você possui essa marca registrada ou que você tem permissão do proprietário da marca comercial para usar a palavra. A Microsoft não é responsável por problemas legais que possam surgir da sua escolha de palavra-chave.

## <a name="create-your-keyword"></a>Criar sua palavra-chave

Para poder usar uma palavra-chave personalizada, você precisará criar uma palavra-chave usando a página de [palavra-chave personalizada](https://aka.ms/sdsdk-wakewordportal) no [Speech Studio](https://aka.ms/sdsdk-speechportal). Depois de fornecer uma palavra-chave, ela produz um arquivo que você implanta em seu dispositivo.

1. Vá para o [Speech Studio](https://aka.ms/sdsdk-speechportal) e **entre** ou, se você ainda não tiver uma assinatura de fala, escolha [**criar uma assinatura**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Na página [palavra-chave personalizada](https://aka.ms/sdsdk-wakewordportal) , digite a palavra-chave de sua escolha e clique em **Adicionar palavra-chave**. Temos algumas [diretrizes](#choose-an-effective-keyword) para ajudar a escolher uma palavra-chave efetiva. No momento, o suporte é limitado ao idioma en-US.

    ![Insira sua palavra-chave](media/speech-devices-sdk/custom-kws-portal-enter-keyword.png)

1. Agora, o portal criará pronúncias candidatas para sua palavra-chave. Ouça cada candidato clicando nos botões de reprodução e remove as verificações ao lado de quaisquer pronúncias incorretas. Quando apenas as boas pronúncias estiverem marcadas, selecione **Enviar** para começar a gerar a palavra-chave. Se você quiser alterar a palavra-chave, primeiro remova a existente clicando no botão excluir que aparece no lado direito da linha ao passar o mouse sobre ela.

    ![Examine sua palavra-chave](media/speech-devices-sdk/custom-kws-portal-review-keyword.png)

1. Pode levar até um minuto para que o modelo seja gerado. Em seguida, você será solicitado a baixar o arquivo.

    ![Baixe sua palavra-chave](media/speech-devices-sdk/custom-kws-portal-download-keyword.png)

1. Guarde o ficheiro. zip para o seu computador. Você precisará desse arquivo para implantar sua palavra-chave personalizada em seu dispositivo.

## <a name="next-steps"></a>Passos seguintes

Teste sua palavra-chave personalizada com o guia de [início rápido SDK de dispositivos de fala](https://aka.ms/sdsdk-quickstart).
