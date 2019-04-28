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
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b5ace2e741f900dd4ab7ba6518d0956284af35f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461617"
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

Antes de poder utilizar uma palavra de reativação personalizado com o seu dispositivo, tem de criar a palavra de reativação com o serviço de geração de reativação de Word personalizado Microsoft. Depois de fornecer uma palavra de reativação, o serviço de produz um ficheiro que pode implementar em seu kit de desenvolvimento para permitir que a palavra de reativação no seu dispositivo.

1. Vá para o [Portal do serviço de voz personalizada](https://cris.ai/).

    ![O Portal de serviço de voz personalizada](media/speech-devices-sdk/wake-word-4.png)

1. Inicie sessão com o endereço de e-mail que recebeu o convite para o Azure Active Directory.

1. O **Word de reativação personalizado** página não está disponível ao público, portanto, não há nenhuma ligação direta que leva-o aí. A funcionalidade de voz personalizada requer uma subscrição do Azure, mas não a funcionalidade do Word de reativação personalizado. Se tem o **subscrições não encontrado.** página de erro, apenas substitua a **"subscrições? errorMessage = não 20found de % de 20Subscriptions %"** com "**customkws**" no URL e pressionar ENTER. O URL deve ser um dos seguintes: https://westus.cris.ai/customkws, https://eastasia.cris.ai/customkws ou https://northeurope.cris.ai/customkws, consoante o local em que é a sua região.

1. Escreva a palavra de reativação à sua escolha e, em seguida, selecione **submeter palavra**.

    ![Introduza a palavra de reativação](media/speech-devices-sdk/wake-word-5.png)

1. Poderá demorar alguns minutos para que os ficheiros sejam gerados. Deverá ver um círculo a girar na janela do browser. Após alguns momentos, uma barra de informações for apresentada, que lhe pede para transferir um ficheiro. zip.

1. Guarde o ficheiro. zip para o seu computador. Precisa de ter este ficheiro para implementar a palavra de reativação personalizado para o kit de desenvolvimento. Para implementar a palavra de reativação personalizado, siga as instruções em [começar com o SDK de dispositivos de voz](speech-devices-sdk-qsg.md).

1. Selecione **terminar sessão.**

## <a name="next-steps"></a>Passos Seguintes

Para começar, obtenha uma [conta gratuita do Azure](https://azure.microsoft.com/free/) e inscreva-se para o SDK de dispositivos de voz.

> [!div class="nextstepaction"]
> [Inscreva-se o SDK de dispositivos de voz](get-speech-devices-sdk.md)
