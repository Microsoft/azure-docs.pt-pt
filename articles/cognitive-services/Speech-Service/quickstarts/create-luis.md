---
title: 'Quickstart: Criar uma chave LUIS'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, você vai aprender a criar uma aplicação LUIS e obter uma chave.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: trbye
ms.openlocfilehash: df12b51020083489d431d0ebcd7eb506ef97caa2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400838"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Quickstart: Obter uma chave de ponto final LUIS

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que tem os seguintes itens antes de iniciar este tutorial:

* Uma conta do LUIS. Pode obtê-la gratuitamente através do [portal do LUIS](https://www.luis.ai/home).

## <a name="luis-and-speech"></a>O LUIS e a voz

O LUIS integra-se no serviço Voz para reconhecer as intenções de voz. Não precisa de uma subscrição do serviço Voz, apenas do LUIS.

Luis usa três tipos de chaves:

|Tipo de chave|Objetivo|
|--------|-------|
|Criação|Permite criar e modificar as aplicações do LUIS programáticamente|
|Inicial|Permite testar a sua aplicação LUIS usando apenas texto|
|Ponto Final |Autoriza o acesso a uma determinada app LUIS|

Para este tutorial, você precisa do tipo de chave de ponto final. O tutorial utiliza o exemplo da aplicação Home Automation LUIS, que pode criar seguindo a [aplicação de domótica pré-construída Utilização.](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) Se criou uma aplicação LUIS própria, pode usá-la.

Ao criar uma aplicação LUIS, o LUIS gera automaticamente uma chave de arranque para que possa testar a aplicação usando consultas de texto. Esta chave não permite a integração do serviço da Fala e não funcionará com este tutorial. Crie um recurso LUIS no painel azure e atribua-o à aplicação LUIS. Para este tutorial, pode utilizar o escalão de subscrição.

Depois de criar o recurso LUIS no painel de instrumentos Do Azure, inicie sessão no [portal LUIS,](https://www.luis.ai/home)escolha a sua aplicação na página **My Apps** e, em seguida, mude para a página **'Gerir'** da aplicação. Por fim, selecione **Keys e Endpoints** na barra lateral.

![Definições de chaves e ponto final do portal do LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

Na página de **definições de Teclas e ponto final:**

1. Desloque-se até à secção **Recursos e Teclas** e selecione **recurso atribuir**.
1. Na placa de atribuição de uma chave para a sua caixa de diálogo de **aplicações,** epreça as seguintes alterações:

   * Under **Tenant,** escolha **Microsoft**.
   * Em **nome de subscrição,** escolha a subscrição Azure que contém o recurso LUIS que pretende utilizar.
   * Em **Chave,** escolha o recurso LUIS que pretende utilizar com a app.

   Passados alguns momentos, a subscrição nova aparece na tabela, na parte inferior da página.

1. Selecione o ícone ao lado de uma tecla para copiá-lo para a área de recortes. (Pode utilizar uma chave qualquer.)

![Chaves de subscrição da aplicação LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Reconhecer intenções](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)
