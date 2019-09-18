---
title: 'Tutorial: Gerar metadados para imagens do Azure'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você aprenderá a integrar o serviço de Pesquisa Visual Computacional do Azure a um aplicativo Web para gerar metadados para imagens.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: pafarley
ms.openlocfilehash: 7caf4493db32201a8e83ffb3722c80c5e9b41a8f
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057732"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Tutorial: Usar Pesquisa Visual Computacional para gerar metadados de imagem no armazenamento do Azure

Neste tutorial, você aprenderá a integrar o serviço de Pesquisa Visual Computacional do Azure a um aplicativo Web para gerar metadados para imagens carregadas. Um guia de aplicativo completo pode ser encontrado no [laboratório de armazenamento do Azure e serviços cognitivas](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) no GitHub, e este tutorial aborda, essencialmente, o exercício 5 do laboratório. Você pode desejar criar o aplicativo de ponta a ponta seguindo cada etapa, mas se você quiser ver como Pesquisa Visual Computacional pode ser integrado a um aplicativo Web existente, leia aqui.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Criar um recurso de Pesquisa Visual Computacional no Azure
> * Executar análise de imagem em imagens de armazenamento do Azure
> * Anexar metadados a imagens de armazenamento do Azure
> * Verificar metadados de imagem usando Gerenciador de Armazenamento do Azure

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2017 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) ou superior, com as cargas de trabalho "desenvolvimento do ASP.net e da Web" e "Azure Development" instaladas.
- Uma conta de armazenamento do Azure com um contêiner de blob alocado para imagens (siga [os exercícios 1 do laboratório de armazenamento do Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) se precisar de ajuda com esta etapa).
- A ferramenta de Gerenciador de Armazenamento do Azure (siga [o exercício 2 do laboratório de armazenamento do Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) se precisar de ajuda com esta etapa).
- Um aplicativo Web ASP.NET com acesso ao armazenamento do Azure (siga [o exercício 3 do laboratório de armazenamento do Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) para criar um aplicativo desse tipo rapidamente).

## <a name="create-a-computer-vision-resource"></a>Criar um recurso de Pesquisa Visual Computacional

Você precisará criar um recurso de Pesquisa Visual Computacional para sua conta do Azure; Esse recurso gerencia o acesso ao serviço de Pesquisa Visual Computacional do Azure. 

1. Siga as instruções em [criar um recurso de serviços cognitivas do Azure](../../cognitive-services-apis-create-account.md) para criar um recurso de pesquisa Visual computacional.

1. Em seguida, vá para o menu do seu grupo de recursos e clique na assinatura API da Pesquisa Visual Computacional que você acabou de criar. Copie a URL no **ponto de extremidade** para algum lugar para poder recuperá-la com facilidade em um momento. Em seguida, clique em **Mostrar chaves de acesso**.

    ![portal do Azure página com a URL do ponto de extremidade e o link de chaves de acesso descritos](../Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. Na próxima janela, copie o valor da **chave 1** para a área de transferência.

    ![Caixa de diálogo gerenciar chaves, com o botão de cópia descrito](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Adicionar credenciais de Pesquisa Visual Computacional

Em seguida, você adicionará as credenciais necessárias ao seu aplicativo para que ele possa acessar Pesquisa Visual Computacional recursos

Abra seu aplicativo Web ASP.NET no Visual Studio e navegue até o arquivo **Web. config** na raiz do projeto. Adicione as instruções a seguir à `<appSettings>` seção do arquivo, substituindo `VISION_KEY` pela chave que você copiou na etapa anterior e `VISION_ENDPOINT` pela URL que você salvou na etapa anterior.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Em seguida, na Gerenciador de Soluções, clique com o botão direito do mouse no projeto e use o comando **gerenciar pacotes NuGet** para instalar o pacote **Microsoft. Azure. cognitivaservices. Vision. ComputerVision**. Esse pacote contém os tipos necessários para chamar o API da Pesquisa Visual Computacional.

## <a name="add-metadata-generation-code"></a>Adicionar código de geração de metadados

Em seguida, você adicionará o código que realmente aproveita o serviço de Pesquisa Visual Computacional para criar metadados para imagens. Essas etapas serão aplicadas ao aplicativo ASP.NET no laboratório, mas você poderá adaptá-las ao seu próprio aplicativo. O que é importante é que, neste ponto, você tem um aplicativo Web ASP.NET que pode carregar imagens para um contêiner de armazenamento do Azure, ler imagens dele e exibi-las na exibição. Se você não tiver certeza sobre isso, é melhor seguir [o exercício 3 do laboratório de armazenamento do Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3). 

1. Abra o arquivo *HomeController.cs* na pasta **controladores** do projeto e adicione as seguintes `using` instruções na parte superior do arquivo:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Em seguida, vá para o método **upload** ; Esse método converte e carrega imagens no armazenamento de BLOBs. Adicione o código a seguir imediatamente após o bloco que começa `// Generate a thumbnail` com (ou no final do seu processo de criação de blob de imagem). Esse código usa o blob que contém a imagem`photo`() e usa pesquisa Visual computacional para gerar uma descrição para essa imagem. O API da Pesquisa Visual Computacional também gera uma lista de palavras-chave que se aplicam à imagem. A descrição e as palavras-chave geradas são armazenadas nos metadados do blob para que possam ser recuperadas posteriormente.

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    VisualFeatureTypes[] features = new VisualFeatureTypes[] { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. Em seguida, vá para o método **index** no mesmo arquivo; Esse método enumera os blobs de imagem armazenados no contêiner de blob de destino (como instâncias de **IListBlobItem** ) e os passa para a exibição do aplicativo. Substitua o `foreach` bloco nesse método pelo código a seguir. Esse código chama **CloudBlockBlob. fetchattributes** para obter os metadados anexados de cada blob. Ele extrai a descrição gerada pelo computador (`caption`) dos metadados e a adiciona ao objeto **BlobInfo** , que é passado para a exibição.
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

## <a name="test-the-app"></a>Testar a aplicação

Salve suas alterações no Visual Studio e pressione **Ctrl + F5** para iniciar o aplicativo em seu navegador. Use o aplicativo para carregar algumas imagens, seja da pasta "fotos" nos recursos do laboratório ou de sua própria pasta. Quando você passa o cursor sobre uma das imagens na exibição, uma janela de dica de ferramenta deve aparecer e exibir a legenda gerada pelo computador para a imagem.

![A legenda gerada pelo computador](../Images/thumbnail-with-tooltip.png)

Para exibir todos os metadados anexados, use o Gerenciador de Armazenamento do Azure para exibir o contêiner de armazenamento que você está usando para imagens. Clique com o botão direito do mouse em qualquer um dos BLOBs no contêiner e selecione **Propriedades**. Na caixa de diálogo, você verá uma lista de pares chave-valor. A descrição da imagem gerada pelo computador é armazenada no item "legenda" e as palavras-chave de pesquisa são armazenadas em "Tag0", "da tag1" e assim por diante. Quando tiver terminado, clique em **Cancelar** para fechar a caixa de diálogo.

![Janela de diálogo Propriedades da imagem, com marcações de metadados listadas](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser continuar trabalhando em seu aplicativo Web, consulte a seção [próximas etapas](#next-steps) . Se você não planeja continuar usando este aplicativo, exclua todos os recursos específicos do aplicativo. Para fazer isso, você pode simplesmente excluir o grupo de recursos que contém sua assinatura do armazenamento do Azure e Pesquisa Visual Computacional recurso. Isso removerá a conta de armazenamento, os BLOBs carregados nela e o recurso do serviço de aplicativo necessário para se conectar ao aplicativo Web ASP.NET. 

Para excluir o grupo de recursos, abra a folha **grupos de recursos** no portal, navegue até o grupo de recursos usado para este projeto e clique em **excluir grupo de recursos** na parte superior da exibição. Você será solicitado a digitar o nome do grupo de recursos para confirmar que deseja excluí-lo, pois, uma vez excluído, um grupo de recursos não poderá ser recuperado.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você integrou o serviço de Pesquisa Visual Computacional do Azure a um aplicativo Web existente para gerar automaticamente legendas e palavras-chave para imagens de blob conforme elas são carregadas. Em seguida, consulte o laboratório de armazenamento do Azure, exercício 6, para saber como adicionar a funcionalidade de pesquisa ao seu aplicativo Web. Isso aproveita as palavras-chave de pesquisa que o serviço Pesquisa Visual Computacional gera.

> [!div class="nextstepaction"]
> [Adicionar pesquisa ao seu aplicativo](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
