---
title: 'Tutorial: Gerar metadados para imagens do Azure'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você vai aprender a integrar o serviço Azure Computer Vision numa aplicação web para gerar metadados para imagens.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 7c83350dbecaf20e9b35f159b2c01824777bc665
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973718"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Tutorial: Use a Visão computacional para gerar metadados de imagem no Armazenamento Azure

Neste tutorial, você vai aprender a integrar o serviço Azure Computer Vision numa aplicação web para gerar metadados para imagens carregadas. Isto é útil para cenários de gestão de [ativos digitais (DAM),](../Home.md#computer-vision-for-digital-asset-management) como se uma empresa queira gerar rapidamente legendas descritivas ou palavras-chave pesquisáveis para todas as suas imagens.

Um guia completo de aplicativos pode ser encontrado no [Azure Storage and Cognitive Services Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) no GitHub, e este tutorial cobre essencialmente o Exercício 5 do laboratório. Pode querer criar a aplicação completa seguindo cada passo, mas se quiser aprender a integrar a Computer Vision numa aplicação web existente, leia aqui.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Criar um recurso de Visão Computacional em Azure
> * Realizar análise de imagem em imagens de Armazenamento Azure
> * Anexar metadados às imagens de Armazenamento Azure
> * Verifique os metadados de imagem usando o Azure Storage Explorer

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- [Edição comunitária visual 2017](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) ou superior, com as cargas de trabalho "ASP.NET e desenvolvimento web" e "Desenvolvimento Azure" instaladas.
- Uma conta de Armazenamento Azure com um recipiente de blob configurado para armazenamento de imagem (siga [os exercícios 1 do Laboratório](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) de Armazenamento Azure se precisar de ajuda com este passo).
- A ferramenta Azure Storage Explorer (siga o Exercício 2 do Laboratório de [Armazenamento Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) se precisar de ajuda com este passo).
- Uma ASP.NET aplicação web com acesso ao Armazenamento Azure (siga [o Exercício 3 do Azure Storage Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) para criar uma aplicação deste tipo rapidamente).

## <a name="create-a-computer-vision-resource"></a>Criar um recurso de Visão Computacional

Terá de criar um recurso Computer Vision para a sua conta Azure; este recurso gere o seu acesso ao serviço de Visão Computacional do Azure. 

1. Siga as instruções em [Criar um recurso azure Cognitive Services](../../cognitive-services-apis-create-account.md) para criar um recurso de Visão Computacional.

1. Em seguida, vá ao menu para o seu grupo de recursos e clique na subscrição da Computer Vision API que acabou de criar. Copie o URL em **Endpoint** para um lugar onde possa recuperá-lo facilmente num momento. Em seguida, clique em **mostrar teclas**de acesso .

    ![Página do portal Azure com o URL final e link de chaves de acesso delineadas](../Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. Na janela seguinte, copie o valor da **CHAVE 1** para a pasta.

    ![Gerir o diálogo das teclas, com o botão de cópia delineado](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Adicionar credenciais de Visão Computacional

Em seguida, você adicionará as credenciais necessárias à sua app para que possa aceder aos recursos da Visão Computacional

Abra a sua ASP.NET aplicação web no Visual Studio e navegue para o ficheiro **Web.config** na raiz do projeto. Adicione as seguintes `<appSettings>` declarações à secção `VISION_KEY` do ficheiro, substituindo a chave `VISION_ENDPOINT` que copiou no passo anterior e com o URL que guardou no passo anterior.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Em seguida, no Solution Explorer, clique no projeto e use o comando **Manage NuGet Packages** para instalar o pacote **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**. Este pacote contém os tipos necessários para chamar a API da Visão Computacional.

## <a name="add-metadata-generation-code"></a>Adicionar código de geração de metadados

Em seguida, você irá adicionar o código que realmente aproveita o serviço Computer Vision para criar metadados para imagens. Estes passos aplicar-se-ão à aplicação ASP.NET em laboratório, mas pode adaptá-las à sua própria app. O importante é que neste momento tenha uma aplicação web ASP.NET que possa enviar imagens para um contentor de Armazenamento Azure, ler imagens a partir dele e exibi-las na vista. Se não tem a certeza sobre este passo, o melhor é seguir o Exercício 3 do Laboratório de [Armazenamento Azure.](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) 

1. Abra o ficheiro *HomeController.cs* na pasta **controladora** do `using` projeto e adicione as seguintes declarações na parte superior do ficheiro:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Em seguida, vá ao método **upload;** este método converte e envia imagens para armazenamento de bolhas. Adicione o seguinte código imediatamente após `// Generate a thumbnail` o bloco que começa com (ou no final do seu processo de criação de imagens blob). Este código pega na bolha`photo`que contém a imagem e utiliza a Visão computacional para gerar uma descrição para essa imagem. A API computer Vision também gera uma lista de palavras-chave que se aplicam à imagem. A descrição gerada e as palavras-chave são armazenadas nos metadados da bolha para que possam ser recuperadas mais tarde.

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

1. Em seguida, vá ao método **Index** no mesmo ficheiro. Este método enumera as bolhas de imagem armazenadas no recipiente de blob direcionado (como o **IListBlobItem** instâncias) e passa-as para a vista de aplicação. Substitua `foreach` o bloco neste método pelo seguinte código. Este código chama **CloudBlockBlob.FetchAttributes** para obter os metadados anexados de cada blob. Extrai a descrição gerada`caption`por computador () dos metadados e adiciona-a ao objeto **BlobInfo,** que é passado para a vista.
    
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

Guarde as suas alterações no Visual Studio e prima **Ctrl+F5** para lançar a aplicação no seu navegador. Utilize a aplicação para fazer upload de algumas imagens, quer a partir da pasta "fotos" nos recursos do laboratório, quer da sua própria pasta. Quando pairar sobre uma das imagens na vista, deve aparecer uma janela de ponta de ferramenta e exibir a legenda gerada pelo computador para a imagem.

![A legenda gerada por computador](../Images/thumbnail-with-tooltip.png)

Para ver todos os metadados anexados, utilize o Azure Storage Explorer para visualizar o recipiente de armazenamento que está a utilizar para imagens. Clique à direita em qualquer uma das bolhas no recipiente e selecione **Propriedades**. No diálogo, verá uma lista de pares de valor-chave. A descrição da imagem gerada por computador é armazenada no item "Legenda", e as palavras-chave de pesquisa são armazenadas em "Tag0", "Tag1", e assim por diante. Quando terminar, clique **em Cancelar** para fechar o diálogo.

![Propriedades de imagem marcam janela, com etiquetas de metadados listadas](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser continuar a trabalhar na sua aplicação web, consulte a secção [Next steps.](#next-steps) Se não pretende continuar a utilizar esta aplicação, deverá eliminar todos os recursos específicos da aplicação. Para eliminar recursos, pode eliminar o grupo de recursos que contém a subscrição de Armazenamento Azure e o recurso Computer Vision. Isto removerá a conta de armazenamento, as bolhas que lhe foram enviadas e o recurso do Serviço de Aplicações necessário para se ligar à ASP.NET aplicação web. 

Para eliminar o grupo de recursos, abra o separador de **grupos de recursos** no portal, navegue para o grupo de recursos utilizado para este projeto e clique em Eliminar o grupo de **recursos** no topo da vista. Será-lhe pedido que digite o nome do grupo de recursos para confirmar que quer apagá-lo, porque uma vez eliminado, um grupo de recursos não pode ser recuperado.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configura o serviço De Visão computacional do Azure numa aplicação web existente para gerar automaticamente legendas e palavras-chave para imagens blob à medida que são carregadas. Em seguida, consulte o Azure Storage Lab, Exercise 6, para aprender a adicionar funcionalidade de pesquisa à sua aplicação web. Isto tira partido das palavras-chave de pesquisa que o serviço Computer Vision gera.

> [!div class="nextstepaction"]
> [Adicione pesquisa à sua aplicação](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
