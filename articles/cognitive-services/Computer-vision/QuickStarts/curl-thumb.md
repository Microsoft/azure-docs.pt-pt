---
title: 'Início rápido: Gerar um cURL miniatura - REST,'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, irá gerar uma miniatura de uma imagem através da API de Imagem Digitalizada com o cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 0b6430a31f31f85f01f67994f6b6fa0f30af9575
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57879927"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-curl-in-computer-vision"></a>Início rápido: Gerar uma miniatura com a API REST e cURL na visão do computador

Neste início rápido, vai gerar uma miniatura de uma imagem usando a API de REST da visão do computador. Especifica a altura desejada e a largura, o que pode ser diferente no aspecto ration da imagem de entrada. Imagem digitalizada utiliza o corte inteligente para inteligentemente identificar a área de interesse e gerar o recorte de coordenadas em torno dessa região.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter o [cURL](https://curl.haxx.se/windows).
- Tem de ter uma chave de subscrição da Imagem Digitalizada. Para obter uma chave de subscrição, veja [Obter Chaves de Subscrição](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="get-thumbnail-request"></a>Pedido Obter Miniatura

Com o [método Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) (método Obter miniatura) pode gerar uma miniatura de uma imagem.

Para executar o exemplo, siga os seguintes passos:

1. Copie o código seguinte para um editor.
1. Substitua `<Subscription Key>` pela sua chave de subscrição válida.
1. Substitua `<File>` pelo caminho e nome de ficheiro para guardar a miniatura.
1. Altere o URL de Pedido (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) para utilizar a localização onde obteve as suas chaves de subscrição, se necessário.
1. Opcionalmente, altere a imagem (`{\"url\":\"...`) a analisar.
1. Abra uma janela de comando num computador com o cURL instalado.
1. Cole o código na janela e execute o comando.

>[!NOTE]
>Tem de utilizar na sua chamada REST a mesma localização que utilizou para obter as chaves de subscrição. Por exemplo, se tiver obtido as chaves de subscrição a partir de westus, substitua "westcentralus" no URL abaixo por "westus".

## <a name="create-and-run-the-sample-command"></a>Criar e executar o comando de exemplo

Para criar e executar o exemplo, siga os seguintes passos:

1. Copie o comando seguinte para um editor de texto.
1. Faça as alterações seguintes ao comando, se for necessário:
    1. Substitua o valor de `<subscriptionKey>` pela chave de subscrição.
    1. Substitua o valor de `<thumbnailFile>` pelo nome do ficheiro e o caminho no qual pretende guardar a miniatura.
    1. Substitua o URL do pedido (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail`) pelo URL de ponto final do método [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) (Obter Miniatura) da região do Azure onde obteve as chaves de subscrição, se necessário.
    1. Opcionalmente, altere o URL da imagem no corpo do pedido (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) pelo URL de uma imagem diferente a partir da qual pretende gerar uma miniatura.
1. Abra uma janela da linha de comandos.
1. Cole o comando a partir do editor de texto na janela da linha de comandos e, em seguida, execute o comando.

    ```console
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Examinar a resposta

Uma resposta de êxito escreve a imagem em miniatura para o ficheiro especificado em `<thumbnailFile>`. Se o pedido falhar, a resposta contém um código de erro e uma mensagem para ajudar a determinar o que correu mal.

## <a name="next-steps"></a>Passos Seguintes

Explore a API de imagem digitalizada como analisar uma imagem, detetar celebridades e marcos, criar uma miniatura e extrair texto manuscrito e impresso. Para experimentar rapidamente a API de Imagem Digitalizada, experimente a [Consola de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorar a API de Imagem Digitalizada](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
