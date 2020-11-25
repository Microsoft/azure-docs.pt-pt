---
title: 'Tutorial: Gerar metadados para imagens do Azure'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você vai aprender como integrar o serviço Azure Computer Vision numa aplicação web para gerar metadados para imagens.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: b51234ff121071de27ec8c91425f4a769c8d833e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008791"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Tutorial: Use a Visão de Computador para gerar metadados de imagem no Armazenamento Azure

Neste tutorial, você vai aprender como integrar o serviço Azure Computer Vision numa aplicação web para gerar metadados para imagens carregadas. Isto é útil para cenários [de gestão de ativos digitais (DAM),](../overview.md#computer-vision-for-digital-asset-management) como se uma empresa quiser gerar rapidamente legendas descritivas ou palavras-chave pescáveis para todas as suas imagens.

Um guia completo de aplicativos pode ser encontrado no [Azure Storage and Cognitive Services Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) no GitHub, e este tutorial cobre essencialmente o Exercício 5 do laboratório. Pode querer criar a aplicação completa seguindo cada passo, mas se apenas quiser aprender a integrar a Visão de Computador numa aplicação web existente, leia aqui.

Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Criar um recurso de Visão De Computador em Azure
> * Realizar análise de imagem em imagens de Armazenamento Azure
> * Anexar metadados a imagens de armazenamento Azure
> * Verifique metadados de imagem usando O Explorador de Armazenamento Azure

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- [Edição comunitária do Visual Studio 2017](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) ou superior, com as cargas de trabalho "ASP.NET e web development" e "Azure development" instaladas.
- Uma conta de Armazenamento Azure com um recipiente de bolhas configurado para armazenamento de imagem (siga [os exercícios 1 do Laboratório de Armazenamento Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) se precisar de ajuda com este passo).
- A ferramenta Azure Storage Explorer (siga [o Exercício 2 do Laboratório de Armazenamento Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) se precisar de ajuda com este passo).
- Um ASP.NET aplicação web com acesso ao Azure Storage (siga [o Exercício 3 do Azure Storage Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) para criar uma aplicação assim rapidamente).

## <a name="create-a-computer-vision-resource"></a>Criar um recurso de Visão De Computador

Terá de criar um recurso de Visão Computacional para a sua conta Azure; este recurso gere o seu acesso ao serviço de Visão Computacional da Azure. 

1. Siga as instruções na [Criar um recurso Azure Cognitive Services](../../cognitive-services-apis-create-account.md) para criar um recurso de Visão Computacional.

1. Em seguida, vá ao menu para o seu grupo de recursos e clique na subscrição da API de Visão De Computador que acabou de criar. Copie o URL em **Endpoint** para um lugar onde possa facilmente recuperá-lo em um momento. Em seguida, clique em **Mostrar as teclas de acesso**.

    ![Página do portal Azure com o URL de ponta e link de teclas de acesso delineado](../Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. Na janela seguinte, copie o valor da **CHAVE 1** para a área de transferência.

    ![Gerir o diálogo das teclas, com o botão de cópia delineado](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Adicionar credenciais de Visão de Computador

Em seguida, irá adicionar as credenciais necessárias à sua app para que possa aceder aos recursos da Visão De Computador.

Abra a sua aplicação web ASP.NET no Visual Studio e navegue para o ficheiro **Web.config** na raiz do projeto. Adicione as seguintes declarações à `<appSettings>` secção do ficheiro, substituindo-a `VISION_KEY` pela chave copiada no passo anterior e `VISION_ENDPOINT` com o URL que guardou no passo anterior.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Em seguida, no Solution Explorer, clique com o botão direito do projeto e utilize o comando **'Gerir pacotes NuGet'** para instalar o pacote **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**. Este pacote contém os tipos necessários para chamar a API de Visão De Computador.

## <a name="add-metadata-generation-code"></a>Adicionar código de geração de metadados

Em seguida, irá adicionar o código que realmente aproveita o serviço de Visão de Computador para criar metadados para imagens. Estes passos aplicar-se-ão à aplicação ASP.NET em laboratório, mas pode adaptá-las à sua própria app. O que é importante é que neste momento você tem uma aplicação web ASP.NET que pode fazer upload de imagens para um recipiente de Armazenamento Azure, ler imagens do mesmo e exibi-las na vista. Se não tem a certeza sobre este passo, o melhor é seguir [o Exercício 3 do Laboratório de Armazenamento Azure.](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) 

1. Abra o ficheiro *HomeController.cs* na pasta **controladores** do projeto e adicione as seguintes `using` declarações no topo do ficheiro:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Em seguida, vá ao método **upload;** este método converte e envia imagens para o armazenamento de bolhas. Adicione o seguinte código imediatamente após o bloco que começa com `// Generate a thumbnail` (ou no final do seu processo de criação de bolhas de imagem). Este código pega na bolha que contém a imagem ( `photo` ) e utiliza a Visão de Computador para gerar uma descrição para essa imagem. A API de Visão Computacional também gera uma lista de palavras-chave que se aplicam à imagem. A descrição gerada e as palavras-chave são armazenadas nos metadados da bolha para que possam ser recuperadas mais tarde.

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

1. Em seguida, vá para o método **Índice** no mesmo ficheiro. Este método enumera as bolhas de imagem armazenadas no recipiente de bolhas direcionados (como instâncias **IListBlobItem)** e transmite-as para a vista da aplicação. Substitua o `foreach` bloco neste método pelo seguinte código. Este código chama **CloudBlockBlob.FetchAttributes** para obter os metadados anexados de cada blob. Extrai a descrição gerada por computador ( `caption` ) dos metadados e adiciona-a ao objeto **BlobInfo,** que é passado para a vista.
    
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

Guarde as suas alterações no Visual Studio e pressione **Ctrl+F5** para lançar a aplicação no seu navegador. Utilize a aplicação para fazer o upload de algumas imagens, quer a partir da pasta "fotos" nos recursos do laboratório, quer a partir da sua própria pasta. Quando pairar sobre um dos imagens na vista, deve aparecer uma janela da ponta da ferramenta e exibir a legenda gerada pelo computador para a imagem.

![A legenda gerada por computador](../Images/thumbnail-with-tooltip.png)

Para visualizar todos os metadados anexados, utilize o Azure Storage Explorer para ver o recipiente de armazenamento que está a utilizar para imagens. Clique com o botão direito em qualquer uma das bolhas do recipiente e selecione **Propriedades**. No diálogo, verá uma lista de pares de valores-chave. A descrição da imagem gerada por computador é armazenada no item "Legenda", e as palavras-chave de pesquisa são armazenadas em "Tag0", "Tag1" e assim por diante. Quando terminar, clique em **Cancelar** para fechar o diálogo.

![Janela de diálogo de propriedades de imagem, com etiquetas de metadados listadas](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser continuar a trabalhar na sua aplicação web, consulte a secção [etapas seguintes.](#next-steps) Se não pretender continuar a usar esta aplicação, deverá eliminar todos os recursos específicos da aplicação. Para eliminar recursos, pode eliminar o grupo de recursos que contém a subscrição de Armazenamento Azure e o recurso De Visão De Computador. Isto removerá a conta de armazenamento, as bolhas carregadas na sua e o recurso do Serviço de Aplicações necessário para se conectar com a aplicação web ASP.NET. 

Para eliminar o grupo de recursos, abra o separador **Grupos de Recursos** no portal, navegue para o grupo de recursos utilizado para este projeto e clique em Eliminar grupo de **recursos** na parte superior da vista. Ser-lhe-á pedido que escreva o nome do grupo de recursos para confirmar que pretende eliminá-lo, porque uma vez eliminado, um grupo de recursos não pode ser recuperado.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou o serviço de Visão de Computador do Azure numa aplicação web existente para gerar automaticamente legendas e palavras-chave para imagens blob à medida que são carregadas. Em seguida, consulte o Azure Storage Lab, Exercício 6, para aprender a adicionar funcionalidade de pesquisa à sua aplicação web. Isto tira partido das palavras-chave de pesquisa que o serviço de Visão de Computador gera.

> [!div class="nextstepaction"]
> [Adicione pesquisa à sua aplicação](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
