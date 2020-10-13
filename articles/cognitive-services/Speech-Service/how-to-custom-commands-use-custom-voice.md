---
title: 'Como: Usar comandos personalizados com voz personalizada - serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, irá especificar a voz de saída de uma aplicação de Comandos Personalizados.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: sausin
ms.openlocfilehash: b1e488653f210089ee5ed6757fb2f3d1e4fb0fe4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87294547"
---
# <a name="use-custom-commands-with-custom-voice"></a>Utilizar Comandos Personalizados com Voz Personalizada

Neste artigo, você vai aprender a selecionar uma voz de saída personalizada para uma aplicação de comandos personalizados.

## <a name="select-a-custom-voice"></a>Selecione uma voz personalizada

1. Na sua aplicação de comandos personalizados, selecione **Definições** a partir do painel esquerdo.
1. Selecione **Voz Personalizada** a partir do painel do meio.
1. Selecione o costume ou a voz pública desejada da mesa.
1. Selecione **Guardar**.

> [!div class="mx-imgBorder"]
> ![Frases de amostra com parâmetros](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Para **vozes públicas,** **os tipos neurais** só estão disponíveis para regiões específicas. Para verificar a disponibilidade, consulte [vozes padrão e neurais por região/ponto final.](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
> - Para **vozes personalizadas,** podem ser criadas a partir da página do projeto Custom Voice. Ver [Começar com Voz Personalizada.](./how-to-custom-voice.md)

Agora a aplicação responderá na voz selecionada, em vez da voz predefinido.
