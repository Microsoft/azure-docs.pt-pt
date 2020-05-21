---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: roy-har
manager: diberry
ms.service: cognitive-services
ms.date: 05/18/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: roy-har
ms.openlocfilehash: 7ab6aa5f830e335a30502207d3a49a528d03f7fd
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654330"
---
1. Selecione [pizza-app-for-luis-v6.json](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) para apresentar a página GitHub para o `pizza-app-for-luis.json` ficheiro.
1. Clique à direita ou toque longa no botão **Raw** e selecione **Guardar o link** para guardar o `pizza-app-for-luis.json` computador.
1. Assine no [portal LUIS.](https://www.luis.ai)
1. Selecione [As minhas aplicações](https://www.luis.ai/applications).
1. Na página **My Apps,** selecione **+ Nova aplicação para conversação.**
1. Selecione **Import as JSON**.
1. No novo diálogo da **aplicação Import,** selecione o botão **Escolha ficheiro.**
1. Selecione o `pizza-app-for-luis.json` ficheiro que descarregou e, em seguida, selecione **Open**.
1. No campo de **dialog** de **nova aplicação Import,** introduza um nome para a sua aplicação Pizza e, em seguida, selecione o botão **Done.**

A aplicação será importada.

Se vir um diálogo **Como criar uma aplicação EFICAZ DO LUIS,** feche o diálogo.

## <a name="train-and-publish-the-pizza-app"></a>Treine e publique a app Pizza

Você deve ver a página **De Intenções** com uma lista das intenções na app Pizza.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

A sua aplicação Pizza está agora pronta a ser utilizada.

## <a name="record-the-app-id-prediction-key-and-prediction-endpoint-of-your-pizza-app"></a>Grave o ID da aplicação, a chave de previsão e o ponto final de previsão da sua aplicação Pizza

Para utilizar a sua nova app Pizza, vai precisar do ID da aplicação, chave de previsão e ponto final da sua aplicação Pizza.

Para encontrar estes valores:

1. A partir da página **Intenções,** selecione **MANAGE**.
1. A partir da página definições de **aplicação,** grave o ID da **aplicação**.
1. Selecione **Recursos Azure**.
1. Na página dos **Recursos Azure,** grave a **Chave Primária.** Este valor é a sua chave de previsão.
1. Grave o **URL do Ponto Final**. Este valor é o seu ponto final de previsão.
