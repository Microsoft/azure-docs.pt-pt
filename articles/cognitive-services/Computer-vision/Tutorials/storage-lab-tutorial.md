---
title: 'Tutorial: Gerar metadados para imagens do armazenamento do Azure'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, irá aprender como integrar o serviço de visão do computador do Azure numa aplicação web para gerar metadados de imagens.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: pafarley
ms.openlocfilehash: e5124b07a5aff67d53213149565ddae4ea6dda33
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448004"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Tutorial: Utilizar imagem digitalizada para gerar metadados de imagem no armazenamento do Azure

Neste tutorial, irá aprender como integrar o serviço de visão do computador do Azure numa aplicação web para gerar metadados de imagens carregadas. Um guia completo de aplicação pode ser encontrado na [armazenamento do Azure e o laboratório de serviços cognitivos](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) no GitHub e este tutorial abrange essencialmente exercer 5 do laboratório. Pode deseja criar o aplicativo ponto a ponto ao seguir cada passo, mas se desejar apenas ver como a visão do computador pode ser integrado numa aplicação web existente, de leitura ao longo de aqui.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Criar um recurso de imagem digitalizada no Azure
> * Executar análise de imagem nas imagens do armazenamento do Azure
> * Anexar metadados para imagens do armazenamento do Azure
> * Verifique os metadados de imagem com o Explorador de armazenamento do Azure

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2017 Community edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) ou superior, com o "desenvolvimento do ASP.NET e web" e cargas de trabalho de "Desenvolvimento do Azure" instaladas.
- Uma conta de armazenamento do Azure com um contentor de BLOBs alocado para imagens (siga [1 de exercícios de laboratório de armazenamento do Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) se precisar de ajuda com este passo).
- A ferramenta do Explorador de armazenamento do Azure (siga [Exercício 2 do laboratório de armazenamento do Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) se precisar de ajuda com este passo).
- Uma aplicação web do ASP.NET, com acesso ao armazenamento do Azure (siga [Exercício 3 o laboratório de armazenamento do Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) para criar rapidamente um aplicativo assim).

## <a name="create-a-computer-vision-resource"></a>Criar um recurso de imagem digitalizada

Terá de criar um recurso de imagem digitalizada para a sua conta do Azure; Este recurso gere o seu acesso ao serviço de visão do computador do Azure.

1. Inicie sessão para o [portal do Azure](https://ms.portal.azure.com) e clique em **criar um recurso**, seguido **IA + Machine Learning** e **de imagem digitalizada**.

    ![Criar uma nova subscrição de API de imagem digitalizada](../Images/new-vision-api.png)

1. Na janela da caixa de diálogo, introduza "chave de api de imagem digitalizada" no **Name** campo e selecione **F0** como o **escalão de preço**. Selecione o mesmo **localização** que selecionou quando configurou a sua conta de armazenamento do Azure. Sob **grupo de recursos**, selecione **utilizar existente** e selecione o mesmo grupo de recursos também. Verifique os **confirmo** caixa e, em seguida, clique em **criar**.

    ![Subscrever a API de imagem digitalizada](../Images/create-vision-api.png)

1. Regresse ao menu de grupo de recursos e clique na subscrição de API de imagem digitalizada que acabou de criar. Copie o URL sob **ponto final** para algum lugar pode facilmente recuperá-lo em breve. Em seguida, clique em **Mostrar chaves de acesso**.

    ![Ver as teclas de acesso](../Images/copy-vision-endpoint.png)

1. Na janela seguinte, copie o valor da **chave 1** na área de transferência.

    ![Copiar a chave de acesso](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Adicionar credenciais de imagem digitalizada

Em seguida, irá adicionar credenciais necessárias para a sua aplicação para que possa aceder a recursos de imagem digitalizada

Abra a sua aplicação web ASP.NET no Visual Studio e navegue para o **Web. config** ficheiro na raiz do projeto. Adicione as seguintes instruções para o `<appSettings>` secção do ficheiro, substituindo `VISION_KEY` com a chave que copiou no passo anterior, e `VISION_ENDPOINT` com o URL que guardou no passo antes disso.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Em seguida, no Solution Explorer, clique com botão direito no projeto e utilizar o **gerir pacotes NuGet** comando para instalar o pacote **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**. Este pacote contém os tipos necessários para chamar a API de imagem digitalizada.

## <a name="add-metadata-generation-code"></a>Adicione o código de geração de metadados

Em seguida, irá adicionar o código que realmente tira partido do serviço de visão do computador para criar os metadados para imagens. Estes passos aplicam-se para a sua aplicação ASP.NET no laboratório, mas pode adaptá-los para sua própria aplicação. O importante é que, neste momento tem uma aplicação web do ASP.NET que pode carregar imagens para um contentor de armazenamento do Azure, lê-lo imagens e exibi-los na vista. Se tiver a certeza sobre isso, é melhor seguir [Exercício 3 o laboratório de armazenamento do Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3). 

1. Abra o *HomeController.cs* arquivo do projeto **controladores** pasta e adicione o seguinte `using` declarações na parte superior do ficheiro:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Em seguida, vá para o **carregar** método; este método converte e imagens de carregamentos para o armazenamento de Blobs. Adicione o seguinte código imediatamente depois do bloco que começa com `// Generate a thumbnail` (ou no final do seu processo de criação de blob de imagem). Esse código usa o blob que contém a imagem (`photo`) e utiliza a imagem digitalizada para gerar uma descrição para essa imagem. A API de imagem digitalizada também gera uma lista de palavras-chave que aplicar à imagem. A descrição gerada e palavras-chave são armazenadas nos metadados do blob, para que eles podem ser obtidos mais tarde.

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

1. Em seguida, vá para o **índice** método no mesmo ficheiro; este método enumera os blobs de imagem armazenada no contentor do blob de destino (como **IListBlobItem** instâncias) e os passa para a vista da aplicação. Substitua o `foreach` bloquear nesse método com o código a seguir. Esse código chama **CloudBlockBlob.FetchAttributes** obter cada blob do anexado metadados. Extrai a descrição geradas por computador (`caption`) dos metadados e adiciona-o para o **BlobInfo** objeto, que é passado para o modo de exibição.
    
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

Guardar as alterações no Visual Studio e prima **Ctrl + F5** para iniciar o aplicativo no seu browser. Utilize a aplicação para carregar algumas imagens, a partir da pasta "photos" nos recursos do laboratório ou de sua própria pasta. Quando focaliza o cursor sobre uma das imagens na vista, uma janela de descrição deve aparecer e apresentar a legenda gerado pelo computador para a imagem.

![A legenda gerado pelo computador](../Images/thumbnail-with-tooltip.png)

Para ver todos os metadados de anexado, utilize o Explorador de armazenamento do Azure para ver o contentor de armazenamento que está a utilizar para imagens. Com qualquer um dos blobs no contentor e selecione o botão direito **propriedades**. Na caixa de diálogo, verá uma lista de pares chave-valor. A descrição da imagem gerada pelo computador é armazenada no item de "Legenda", e as palavras-chave de pesquisa são armazenadas em "Tag0", "Tag1" e assim por diante. Quando tiver terminado, clique em **Cancelar** para fechar a caixa de diálogo.

![Metadados do blob](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser continuar a trabalhar na sua aplicação web, consulte a [próximos passos](#next-steps) secção. Se não planear continuar a utilizar esta aplicação, deve eliminar todos os recursos específicos da aplicação. Para tal, basta eliminar o grupo de recursos que contém a sua subscrição de armazenamento do Azure e o recurso de imagem digitalizada. Isto irá remover a conta de armazenamento, blobs carregados ao mesmo e o recurso de serviço de aplicações necessárias para estabelecer ligação com a sua aplicação web ASP.NET. 

Para eliminar o grupo de recursos, abra a **grupos de recursos** painel no portal, navegue para o grupo de recursos que utilizou para este projeto e clique em **eliminar grupo de recursos** na parte superior da vista. Será solicitado a digitar o nome do grupo de recursos para confirmar que pretende eliminá-lo, uma vez que uma vez eliminado, não é possível recuperar um grupo de recursos.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, integrado o serviço de visão do computador do Azure numa aplicação web existente para gerar automaticamente legendas de áudio e palavras-chave para imagens de BLOBs que eles são carregados. Em seguida, veja o Azure Storage laboratório, Exercício 6, para saber como adicionar a funcionalidade de pesquisa à sua aplicação web. Isso aproveita as palavras-chave de pesquisa que gera o serviço de visão do computador.

> [!div class="nextstepaction"]
> [Adicionar pesquisa à sua aplicação](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
