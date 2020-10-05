---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: cognitive-services
author: roy-har
manager: diberry
ms.service: cognitive-services
ms.date: 06/04/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: roy-har
ms.openlocfilehash: 9965e4c856fdef2af17b116264ad5344ebc97eb2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "84466933"
---
Crie a aplicação de pizza.

1. Selecione [pizza-app-for-luis-v6.js](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) para apresentar a página do GitHub para o `pizza-app-for-luis.json` ficheiro.
1. Clique com o botão à direita ou durante um longo toque no botão **Raw** e selecione **Guarde** o link para guardar `pizza-app-for-luis.json` o computador.
1. Inscreva-se no [portal LUIS.](https://www.luis.ai)
1. Selecione [As Minhas Apps](https://www.luis.ai/applications).
1. Na página **My Apps,** selecione **+ nova aplicação para conversação.**
1. Selecione **Import as JSON**.
1. No **diálogo de nova aplicação Import,** selecione o botão **Escolher Ficheiro.**
1. Selecione o `pizza-app-for-luis.json` ficheiro que descarregou e, em seguida, selecione **Abrir**.
1. No **campo** de diálogo de **nova aplicação Import,** insira um nome para a sua aplicação Pizza e, em seguida, selecione o botão **'Fazer'.**

A aplicação será importada.

Se vir um diálogo **Como criar uma aplicação LUIS eficaz,** feche o diálogo.

## <a name="train-and-publish-the-pizza-app"></a>Treine e publique a app Pizza

Você deve ver a página **Intenções** com uma lista das intenções na aplicação Pizza.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

## <a name="add-an-authoring-resource-to-the-pizza-app"></a>Adicione um recurso de autoria à app Pizza

1. Selecione **MANAGE**.
1. Selecione **Recursos do Azure**.
1. Selecione **o recurso de autoria.**
1. Selecione **Alterar recurso de autoria**.

Se tiver um recurso de autoria, insira o Nome do **Inquilino,** **Nome de Assinatura**e nome de recurso **LUIS** do seu recurso de autoria.

Se não tiver um recurso de autoria:

1. **Selecione Criar novo recurso.**
1. Insira um **Nome de Inquilino,** **Nome de Recurso,** **Nome de Assinatura**e Nome do Grupo de **Recursos Azure**.

A sua aplicação Pizza está agora pronta a ser utilizada.

## <a name="record-the-access-values-for-your-pizza-app"></a>Registem os valores de acesso para a sua app Pizza

Para utilizar a sua nova aplicação Pizza, vai precisar do ID da aplicação, da chave de autoria e da autoria da sua aplicação Pizza.

Para encontrar estes valores:

1. Na página **Intenções,** selecione **MANAGE**.
1. A partir da página Configurações de **Aplicações,** grave o **ID da aplicação.**
1. Selecione **Recursos do Azure**.
1. Selecione **o recurso de autoria.**
1. A partir do **separador Recursos de Autoria,** grave a **Chave Primária**. Este valor é a sua chave de autoria.
1. Grave o **URL endpoint**. Este valor é o seu ponto final de autoria.
