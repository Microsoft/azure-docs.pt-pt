---
title: Criar uma palavra de ativação personalizada-serviço de fala
titleSuffix: Azure Cognitive Services
description: O dispositivo está sempre à escuta de uma palavra de reativação (ou expressão). Quando o usuário disser a palavra de reativação, o dispositivo envia áudio subsequente tudo para a cloud, até que o utilizador para a falar. Personalizar a sua palavra de reativação é uma forma eficaz para diferenciar o seu dispositivo e fortalecer a sua imagem corporativa.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 2bc1a6cbbf1e0d790326849a41b0788e332daa31
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553100"
---
# <a name="create-a-custom-wake-word-by-using-the-speech-service"></a>Criar uma palavra de reativação personalizado com o serviço de voz

O dispositivo está sempre à escuta de uma palavra de reativação (ou expressão). Por exemplo, "EI Cortana" é uma palavra de reativação para o Assistente de Cortana. Quando o usuário disser a palavra de reativação, o dispositivo envia áudio subsequente tudo para a cloud, até que o utilizador para a falar. Personalizar a sua palavra de reativação é uma forma eficaz para diferenciar o seu dispositivo e fortalecer a sua imagem corporativa.

Neste artigo, saiba como criar uma palavra de reativação personalizado para o seu dispositivo.

## <a name="choose-an-effective-wake-word"></a>Escolha uma palavra em vigor a partir de reativação

Considere as seguintes diretrizes ao escolher uma palavra de reativação:

* O word reativação deve ser uma palavra em inglês ou uma expressão. Deve demorar mais do que dois segundos para dizer.

* Palavras de 4 a 7 sílabas funcionam melhor. Por exemplo, "EI, computador" é uma palavra de reativação boa. Apenas "Olá" é ruim.

* Palavras de reativação devem seguir as regras de pronúncia inglesa comuns.

* Um único ou até mesmo uma palavra produzida que segue as regras de pronúncia inglesa comum pode reduzir os falsos positivos. Por exemplo, "computerama" pode ser uma palavra de reativação boa.

* Não escolha uma palavra em comum. Por exemplo, "" ilimitado"e"Ir"são palavras que as pessoas dizem com frequência na conversação comum. Elas podem ser acionadores falso para o seu dispositivo.

* Evite utilizar uma palavra de reativação que pode ter pronunciations alternativos. Os utilizadores teria de saber a pronúncia "right" para que o seu dispositivo para responder. Por exemplo, "509" pode ser pronunciado "cinco zero nine," "cinco AH nove," ou "cinco centenas e nove." "R.E.I." pode ser pronunciado "r-e-i" ou "ray." "Ao vivo" pode ser pronunciado "/līv/" ou "/liv/".

* Não utilize carateres especiais, símbolos ou dígitos. Por exemplo, "Go #" e "20 + gatos" não seria bom reativação palavras. No entanto, "aceda pequenos" ou "vinte plus gatos" poderá funcionar. Ainda pode usar os símbolos em sua imagem corporativa e usar o marketing e documentação para reforçar a pronúncia adequada.

> [!NOTE]
> Se escolher uma palavra de marca registada como o word de reativação, certifique-se de que seu essa marca comercial ou que têm permissão do proprietário de marca registada para utilizar a palavra. A Microsoft não se responsabilizará por quaisquer problemas legais que possam surgir da sua escolha de palavras de reativação.

## <a name="create-your-wake-word"></a>Criar a sua palavra de reativação

Antes de usar uma palavra de ativação personalizada com seu dispositivo, você precisará criar uma palavra de ativação com o serviço de geração de palavra de ativação personalizada da Microsoft. Depois de fornecer uma palavra de ativação, o serviço produz um arquivo que você implanta em seu kit de desenvolvimento para habilitar a palavra de ativação em seu dispositivo.

1. Acesse o [portal do serviço fala personalizada](https://aka.ms/sdsdk-speechportal) e **entre** ou, se você não tiver uma assinatura de fala, escolha [**criar uma assinatura**](https://go.microsoft.com/fwlink/?linkid=2086754)

    ![O portal do serviço de Fala Personalizada](media/speech-devices-sdk/wake-word-4.png)

1. No texto de página de [ativação personalizada](https://aka.ms/sdsdk-wakewordportal) , digite na palavra de ativação de sua escolha e clique em **Adicionar ativar palavra**. Temos algumas [diretrizes](#choose-an-effective-wake-word) para ajudar a escolher uma palavra-chave efetiva. Atualmente, damos suporte apenas ao idioma en-US.

    ![Introduza a palavra de reativação](media/speech-devices-sdk/wake-word-5.png)

1. Serão criadas três pronúncias alternativas de sua palavra de ativação. Você pode escolher todas as pronúncias que desejar. Em seguida, selecione **Enviar** para gerar a palavra de ativação. Se você quiser alterar a palavra de ativação, remova a primeira, quando passar o mouse sobre a linha de pronúncia, o ícone de exclusão será exibido.

    ![Examine sua palavra de ativação](media/speech-devices-sdk/wake-word-6.png)

1. Pode levar até um minuto para que o modelo seja gerado. Você será solicitado a baixar o arquivo.

    ![Baixe sua palavra de ativação](media/speech-devices-sdk/wake-word-7.png)

1. Guarde o ficheiro. zip para o seu computador. Você precisará desse arquivo para implantar sua palavra de ativação personalizada no kit de desenvolvimento.

## <a name="next-steps"></a>Passos Seguintes

Teste a palavra de ativação personalizada com o guia de [início rápido do SDK de dispositivos de fala](https://aka.ms/sdsdk-quickstart).
