---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: cognitive-services
author: roy-har
manager: diberry
ms.service: cognitive-services
ms.date: 06/03/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: roy-har
ms.openlocfilehash: 8e67a6d0c98a3839922a79e9b452465087da1b69
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418067"
---
1. Selecione [pizza-app-for-luis-v6.json](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) para trazer a página do GitHub para o `pizza-app-for-luis.json` ficheiro.
1. Clique com o botão à direita ou durante um longo toque no botão **Raw** e selecione **Guarde** o link para guardar `pizza-app-for-luis.json` o computador.
1. Inscreva-se no [portal LUIS.](https://www.luis.ai)
1. Selecione [As Minhas Apps](https://www.luis.ai/applications).
1. Na página **My Apps,** selecione **+ nova aplicação para conversação.**
1. Selecione **Import as JSON**.
1. No **diálogo de nova aplicação Import,** selecione o botão **Escolher Ficheiro.**
1. Selecione o `pizza-app-for-luis.json` ficheiro que descarregou e, em seguida, selecione **Abrir**.
1. No **campo** de diálogo de **nova aplicação Import,** insira um nome para a sua aplicação Pizza e, em seguida, selecione o botão **'Fazer'.**

A aplicação será importada.

Se vir o diálogo **Como criar uma aplicação LUIS eficaz,** feche o diálogo.

## <a name="train-and-publish-the-pizza-app"></a>Treine e publique a app Pizza

Você deve ver a página **Intenções** com uma lista das intenções na aplicação Pizza.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

A sua aplicação Pizza está agora pronta a ser utilizada.

## <a name="record-the-app-id-prediction-key-and-prediction-endpoint-of-your-pizza-app"></a>Grave o ID da aplicação, a chave de previsão e o ponto final de previsão da sua app Pizza

Para utilizar a sua nova aplicação Pizza, vai precisar do ID da aplicação, da chave de previsão e do ponto final de previsão da sua aplicação Pizza.

Para encontrar estes valores:

1. Na página **Intenções,** selecione **MANAGE**.
1. A partir da página Configurações de **Aplicações,** grave o **ID da aplicação.**
1. Selecione **Recursos Azure**.
1. Na página **Azure Resources,** grave a **Chave Primária.** Este valor é a sua chave de previsão.
1. Grave o **URL endpoint**. Este valor é o seu ponto final de previsão.
