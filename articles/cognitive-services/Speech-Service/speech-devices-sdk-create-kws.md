---
title: Criar uma palavra de reativação personalizados - serviços de voz
titleSuffix: Azure Cognitive Services
description: O dispositivo está sempre à escuta de uma palavra de reativação (ou expressão). Quando o usuário disser a palavra de reativação, o dispositivo envia áudio subsequente tudo para a cloud, até que o utilizador para a falar. Personalizar a sua palavra de reativação é uma forma eficaz para diferenciar o seu dispositivo e fortalecer a sua imagem corporativa.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 2280af4bf37fdb3cd12482da855f979a9180f0ec
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020519"
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

Antes de poder utilizar uma palavra de reativação personalizado com o seu dispositivo, terá de criar uma palavra de reativação com o serviço de geração de reativação de Word personalizado Microsoft. Depois de fornecer uma palavra de reativação, o serviço de produz um ficheiro que pode implementar em seu kit de desenvolvimento para permitir que a palavra de reativação no seu dispositivo.

1. Vá para o [Portal de serviço de voz personalizada](https://aka.ms/sdsdk-speechportal) e **iniciar sessão** ou se não tiver uma subscrição de voz, escolha [ **criar uma subscrição**](https://go.microsoft.com/fwlink/?linkid=2086754)

    ![O Portal de serviço de voz personalizada](media/speech-devices-sdk/wake-word-4.png)

1. Na [personalizado palavra de reativação](https://aka.ms/sdsdk-wakewordportal) página Introduza a palavra de reativação de sua escolha e clique em **adicionar reativação word**. Temos algumas [diretrizes](#choose-an-effective-wake-word) para ajudar a escolher uma palavra-chave em vigor. Atualmente suportamos apenas idioma en-US.

    ![Introduza a palavra de reativação](media/speech-devices-sdk/wake-word-5.png)

1. Três pronunciations alternativos da palavra de reativação serão criados. Pode escolher todos os pronunciations que desejar. Em seguida, selecione **submeter** para gerar a palavra de reativação. Se pretender alterar a palavra de reativação, remove a já existente em primeiro lugar, quando pairar o cursor na linha de pronúncia será apresentado o ícone Eliminar.

    ![Reveja a sua palavra de reativação](media/speech-devices-sdk/wake-word-6.png)

1. Poderá demorar até um minuto para que o modelo a ser gerado. Será solicitado para transferir o ficheiro.

    ![Transferir a sua palavra de reativação](media/speech-devices-sdk/wake-word-7.png)

1. Guarde o ficheiro. zip para o seu computador. Precisará esse arquivo para implementar a sua palavra de reativação personalizado para o kit de desenvolvimento.

## <a name="next-steps"></a>Passos Seguintes

Testar a sua palavra de reativação personalizado com [início rápido do SDK de dispositivos de voz](https://aka.ms/sdsdk-quickstart).
