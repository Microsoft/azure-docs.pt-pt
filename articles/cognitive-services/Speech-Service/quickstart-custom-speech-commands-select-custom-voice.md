---
title: 'Quickstart: Use comandos personalizados com voz personalizada (pré-visualização) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, irá especificar a voz de saída de uma aplicação de Comandos Personalizados.
services: cognitive-services
author: anhoang
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: anhoang
ms.openlocfilehash: c2b9b4d51e89975d988ed94bf85695bd8a1cc770
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872452"
---
# <a name="quickstart-use-custom-commands-with-custom-voice-preview"></a>Quickstart: Use comandos personalizados com voz personalizada (pré-visualização)

No [artigo anterior,](./quickstart-custom-speech-commands-create-parameters.md)criámos um novo projeto de Comandos Personalizados para responder a comandos com parâmetros.

Neste artigo, vamos selecionar uma voz de saída personalizada para a aplicação que criamos.

## <a name="select-a-custom-voice"></a>Selecione uma voz personalizada

1. Abra o projeto [que criamos anteriormente](./quickstart-custom-speech-commands-create-parameters.md)
1. Selecione **Definições** a partir do painel esquerdo
1. Selecione **Voz Personalizada** a partir do painel do meio
1. Selecione o costume ou voz pública desejada da mesa
1. Selecione **Guardar**

> [!div class="mx-imgBorder"]
> ![Frases de amostra com parâmetros](media/custom-speech-commands/select-custom-voice.png)

> [!NOTE]
> - Para **vozes públicas,** os **tipos neurais** só estão disponíveis para regiões específicas. Para verificar a disponibilidade, consulte [vozes standard e neurais por região/ponto final](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
> - Para **vozes personalizadas,** podem ser criadas a partir da página do projeto Custom Voice. Ver [Começar com voz personalizada](./how-to-custom-voice.md).

Agora a aplicação responderá na voz selecionada, em vez da voz predefinida.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Quickstart: Ligue-se a uma aplicação de comando personalizado com o SDK de Fala (Pré-visualização)](./quickstart-custom-speech-commands-speech-sdk.md)

