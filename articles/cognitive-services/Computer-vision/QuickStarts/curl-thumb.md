---
title: 'Início Rápido: Gerar uma miniatura – REST, cURL'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, irá gerar uma miniatura de uma imagem através da API de Imagem Digitalizada com o cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 05/20/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d6b55a7f3df7657e2b6b0b23b797d36788658f32
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745166"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Quickstart: Gere uma miniatura utilizando a API e cURL de Visão Computacional

Neste arranque rápido, gera-se uma miniatura a partir de uma imagem utilizando a API DO REST da Visão Computacional. Especifica a altura e a largura desejadas, que podem diferir na ração de aspeto da imagem de entrada. A Computer Vision usa uma cultura inteligente para identificar inteligentemente a área de interesse e gerar coordenadas de cultura em torno daquela região.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter o [cURL](https://curl.haxx.se/windows).
- Tem de ter uma chave de subscrição da Imagem Digitalizada. Você pode obter uma chave de teste gratuita da [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou, siga as instruções na [Conta Criar uma Conta de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever a Visão Computacional e obter a sua chave.

## <a name="create-and-run-the-sample-command"></a>Criar e executar o comando de exemplo

Para criar e executar o exemplo, siga os seguintes passos:

1. Copie o comando seguinte para um editor de texto.
1. Faça as alterações seguintes ao comando, se for necessário:
    1. Substitua o valor de `<subscriptionKey>` pela chave de subscrição.
    1. Substitua o valor de `<thumbnailFile>` pelo nome do ficheiro e o caminho no qual pretende guardar a miniatura.
    1. Substitua a primeira parte do URL de pedido `westcentralus` com o texto no seu URL final.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Opcionalmente, altere o URL da imagem no corpo do pedido (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) pelo URL de uma imagem diferente a partir da qual pretende gerar uma miniatura.
1. Abra uma janela da linha de comandos.
1. Passe o comando do editor de texto para a janela de solicitação de comando.
1. Pressione a entrada para executar o programa.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Examinar a resposta

Uma resposta de êxito escreve a imagem em miniatura para o ficheiro especificado em `<thumbnailFile>`. Se o pedido falhar, a resposta contém um código de erro e uma mensagem para ajudar a determinar o que correu mal. Se o pedido parece ter sucesso, mas a miniatura criada não é um ficheiro de imagem válido, pode ser que a sua chave de subscrição não seja válida.

## <a name="next-steps"></a>Passos seguintes

Explore a API computer Vision para analisar uma imagem, detetar celebridades e marcos históricos, criar uma miniatura e extrair texto impresso e manuscrito. Para experimentar rapidamente a API de Imagem Digitalizada, experimente a [Consola de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c).

> [!div class="nextstepaction"]
> [Explorar a API de Imagem Digitalizada](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f21b)
