---
title: 'Guia de Início Rápido: gerar uma miniatura – SDK, C# – Imagem Digitalizada'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, irá gerar uma miniatura a partir de uma imagem através da biblioteca de cliente C# da Imagem Digitalizada do Windows.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 09/14/2018
ms.author: pafarley
ms.openlocfilehash: 8c8a15af39972b79086a3eb2d392fdc4e7635323
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959432"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-sdk-and-c"></a>Guia de Início Rápido: gerar uma miniatura com o SDK de Imagem Digitalizada e C#

Neste início rápido, irá gerar uma miniatura a partir de uma imagem através da biblioteca de cliente da Imagem Digitalizada do Windows.

## <a name="prerequisites"></a>Pré-requisitos

* Para utilizar a Imagem Digitalizada, precisa de uma chave de subscrição; veja [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Obter Chaves de Subscrição).
* Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).
* O pacote NuGet da biblioteca de cliente [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision). Não é necessário transferir o pacote. Abaixo, são fornecidas as instruções de instalação.

## <a name="generatethumbnailasync-method"></a>Método GenerateThumbnailAsync

> [!TIP]
> Obter o código mais recente como uma solução do Visual Studio a partir do [Github](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision).

Os métodos `GenerateThumbnailAsync` e `GenerateThumbnailInStreamAsync` encapsulam a [API de Obter Miniatura](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) para imagens remotas e locais, respetivamente.  Pode utilizar estes métodos para gerar uma miniatura de uma imagem. O utilizador especifica a altura e a largura, que podem ser diferentes da proporção da imagem introduzida. Imagem digitalizada utiliza o corte inteligente para inteligentemente identificar a área de interesse e gerar as coordenadas de recorte com base na região.

Para executar o exemplo, siga os seguintes passos:

1. Crie uma nova Aplicação da Consola Visual C# no Visual Studio.
1. Instale o pacote NuGet da biblioteca de cliente de Imagem Digitalizada.
    1. No menu, clique em **Ferramentas**, selecione **Gestor de Pacotes NuGet** e, em seguida, **Gerir Pacotes NuGet para Solução**.
    1. Clique no separador **Procurar** e na caixa **Pesquisar** escreva "Microsoft.Azure.CognitiveServices.Vision.ComputerVision".
    1. Selecione **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** quando for apresentado e, em seguida, clique na caixa de verificação junto do nome do projeto e em **Instalar**.
1. Substitua `Program.cs` pelo código abaixo.
1. Substitua `<Subscription Key>` pela sua chave de subscrição válida.
1. Altere `computerVision.Endpoint` para a região do Azure associada às chaves de subscrição, se necessário.
1. Opcionalmente, substitua `<LocalImage>` pelo caminho e pelo nome de ficheiro de uma imagem local (se não for definido, é ignorado).
1. Opcionalmente, defina `remoteImageUrl` para uma imagem diferente.
1. Opcionalmente, defina `writeThumbnailToDisk` para `true` para guardar a miniatura no disco.
1. Execute o programa.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;

using System;
using System.IO;
using System.Threading.Tasks;

namespace ImageThumbnail
{
    class Program
    {
        private const bool writeThumbnailToDisk = false;

        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg";

        private const int thumbnailWidth = 100;
        private const int thumbnailHeight = 100;

        static void Main(string[] args)
        {
            ComputerVisionClient computerVision = new ComputerVisionClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });

            // You must use the same region as you used to get your subscription
            // keys. For example, if you got your subscription keys from westus,
            // replace "westcentralus" with "westus".
            //
            // Free trial subscription keys are generated in the "westus"
            // region. If you use a free trial subscription key, you shouldn't
            // need to change the region.

            // Specify the Azure region
            computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

            Console.WriteLine("Images being analyzed ...\n");
            var t1 = GetRemoteThumbnailAsync(computerVision, remoteImageUrl);
            var t2 = GetLocalThumbnailAsnc(computerVision, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press ENTER to exit");
            Console.ReadLine();
        }

        // Create a thumbnail from a remote image
        private static async Task GetRemoteThumbnailAsync(
            ComputerVisionClient computerVision, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine(
                    "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            Stream thumbnail = await computerVision.GenerateThumbnailAsync(
                thumbnailWidth, thumbnailHeight, imageUrl, true);

            string path = Environment.CurrentDirectory;
            string imageName = imageUrl.Substring(imageUrl.LastIndexOf('/') + 1);
            string thumbnailFilePath =
                path + "\\" + imageName.Insert(imageName.Length - 4, "_thumb");

            // Save the thumbnail to the current working directory,
            // using the original name with the suffix "_thumb".
            SaveThumbnail(thumbnail, thumbnailFilePath);
        }

        // Create a thumbnail from a local image
        private static async Task GetLocalThumbnailAsnc(
            ComputerVisionClient computerVision, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            using (Stream imageStream = File.OpenRead(imagePath))
            {
                Stream thumbnail = await computerVision.GenerateThumbnailInStreamAsync(
                    thumbnailWidth, thumbnailHeight, imageStream, true);

                string thumbnailFilePath =
                    localImagePath.Insert(localImagePath.Length - 4, "_thumb");

                // Save the thumbnail to the same folder as the original image,
                // using the original name with the suffix "_thumb".
                SaveThumbnail(thumbnail, thumbnailFilePath);
            }
        }

        // Save the thumbnail locally.
        // NOTE: This will overwrite an existing file of the same name.
        private static void SaveThumbnail(Stream thumbnail, string thumbnailFilePath)
        {
            if (writeThumbnailToDisk)
            {
                using (Stream file = File.Create(thumbnailFilePath))
                {
                    thumbnail.CopyTo(file);
                }
            }
            Console.WriteLine("Thumbnail {0} written to: {1}\n",
                writeThumbnailToDisk ? "" : "NOT", thumbnailFilePath);
        }
    }
}
```

## <a name="generatethumbnailasync-response"></a>Resposta GenerateThumbnailAsync

Uma resposta de êxito guarda a miniatura de cada imagem localmente e apresenta a localização da miniatura, por exemplo:

```cmd
Thumbnail written to: C:\Documents\LocalImage_thumb.jpg

Thumbnail written to: ...\bin\Debug\Bloodhound_Puppy_thumb.jpg
```

## <a name="next-steps"></a>Passos Seguintes

Explore as API de Imagem Digitalizada utilizadas para analisar uma imagem, detetar celebridades e marcos, criar uma miniatura e extrair texto impresso e manuscrito.

> [!div class="nextstepaction"]
> [Explorar API de Imagem Digitalizada](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
