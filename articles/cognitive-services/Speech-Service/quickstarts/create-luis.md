---
title: 'Início rápido: criar uma chave LUIS'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você aprenderá a criar um aplicativo LUIS e a obter uma chave.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: wolfma
ms.openlocfilehash: 2ee2b25958feac2387779d9e60734a6600b60a2e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682074"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Início rápido: obtendo uma chave de ponto de extremidade LUIS

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você tem os seguintes itens antes de iniciar este tutorial:

* Uma conta do LUIS. Pode obtê-la gratuitamente através do [portal do LUIS](https://www.luis.ai/home).

## <a name="luis-and-speech"></a>O LUIS e a voz

O LUIS integra-se com os serviços de fala para reconhecer tentativas de fala. Você não precisa de uma assinatura de serviços de fala, apenas LUIS.

O LUIS usa três tipos de chaves:

|Tipo de chave|Objetivo|
|--------|-------|
|Criação|Permite criar e modificar aplicativos LUIS de forma programática|
|Inicial|Permite testar seu aplicativo LUIS usando somente texto|
|Ponto Final |Autoriza o acesso a um determinado aplicativo LUIS|

Para este tutorial, você precisa do tipo de chave do ponto de extremidade. O tutorial usa o aplicativo de exemplo Home Automation LUIS, que você pode criar seguindo o guia de início rápido [usar aplicativo de automação inicial predefinido](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) . Se você criou um aplicativo LUIS por conta própria, poderá usá-lo em vez disso.

Quando você cria um aplicativo LUIS, o LUIS gera automaticamente uma chave inicial para que você possa testar o aplicativo usando consultas de texto. Esta chave não habilita a integração dos serviços de fala e não funcionará com este tutorial. Crie um recurso LUIS no painel do Azure e atribua-o ao aplicativo LUIS. Para este tutorial, pode utilizar o escalão de subscrição.

Depois de criar o recurso LUIS no painel do Azure, faça logon no [portal do Luis](https://www.luis.ai/home), escolha seu aplicativo na página **meus aplicativos** e, em seguida, alterne para a página **gerenciar** do aplicativo. Por fim, selecione **chaves e pontos de extremidade** na barra lateral.

![Definições de chaves e ponto final do portal do LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

Na página **configurações de ponto de extremidade e chaves** :

1. Role para baixo até a seção **recursos e chaves** e selecione **atribuir recurso**.
1. Na caixa de diálogo **atribuir uma chave ao seu aplicativo** , faça as seguintes alterações:

   * Em **locatário**, escolha **Microsoft**.
   * Em **nome da assinatura**, escolha a assinatura do Azure que contém o recurso Luis que você deseja usar.
   * Em **chave**, escolha o recurso Luis que você deseja usar com o aplicativo.

   Passados alguns momentos, a subscrição nova aparece na tabela, na parte inferior da página.

1. Selecione o ícone ao lado de uma chave para copiá-lo para a área de transferência. (Pode utilizar uma chave qualquer.)

![Chaves de subscrição da aplicação LUIS](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Reconhecer tentativas](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)
