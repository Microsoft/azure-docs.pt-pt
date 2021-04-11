---
title: 'Quickstart: Criar uma chave LUIS'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, aprende-se a criar uma aplicação LUIS e obtém uma chave.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2020
ms.author: trbye
ms.openlocfilehash: 45c509ea2391c370aa0a399b2f0c621238fabc55
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209685"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Quickstart: Obter uma chave de ponta LUIS

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de ter os seguintes itens antes de iniciar este tutorial:

* Uma conta do LUIS. Pode obtê-la gratuitamente através do [portal do LUIS](https://www.luis.ai/home).

## <a name="luis-and-speech"></a>O LUIS e a voz

O LUIS integra-se no serviço Voz para reconhecer as intenções de voz. Não precisa de uma subscrição do serviço Voz, apenas do LUIS.

LUIS usa três tipos de chaves:

|Tipo de chave|Objetivo|
|--------|-------|
|Criação|Permite criar e modificar aplicações LUIS programáticamente|
|Inicial|Permite testar a sua aplicação LUIS apenas com texto|
|Ponto final |Autoriza o acesso a uma determinada aplicação LUIS|

Para este tutorial, precisa do tipo chave de ponta. O tutorial usa o exemplo da aplicação Home Automation LUIS, que pode criar seguindo o quickstart da [app de domótica Utilização.](../../luis/luis-get-started-create-app.md) Se criou uma aplicação PRÓPRIA LUIS, pode usá-la em vez disso.

Quando cria uma aplicação LUIS, o LUIS gera automaticamente uma chave de arranque para que possa testar a aplicação utilizando consultas de texto. Esta chave não permite a integração do serviço de fala e não funcionará com este tutorial. Crie um recurso LUIS no painel Azure e atribua-o à app LUIS. Para este tutorial, pode utilizar o escalão de subscrição.

Depois de criar o recurso LUIS no painel Azure, inicie sessão no [portal LUIS,](https://www.luis.ai/home)escolha a sua aplicação na página **My Apps** e, em seguida, mude para a página **Manage** da app. Por fim, selecione **Chaves e Pontos finais** na barra lateral.

![Definições de chaves e ponto final do portal do LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

Na página **de definições de Chaves e Ponto final:**

1. Percorra a secção **Recursos e Chaves e** selecione Recurso de **atribuição**.
1. Na chave Atribuir uma chave para a sua caixa de diálogo **de aplicações,** faça as seguintes alterações:

   * Sob **Inquilino,** escolha **a Microsoft.**
   * Em **Nome de Assinatura,** escolha a subscrição Azure que contém o recurso LUIS que pretende utilizar.
   * Em **Tecla,** escolha o recurso LUIS que pretende utilizar com a app.

   Passados alguns momentos, a subscrição nova aparece na tabela, na parte inferior da página.

1. Selecione o ícone ao lado de uma chave para copiá-lo para a área de transferência. (Pode utilizar uma chave qualquer.)

![Chaves de subscrição da aplicação LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Reconhecer Intenções](~/articles/cognitive-services/Speech-Service/get-started-intent-recognition.md)