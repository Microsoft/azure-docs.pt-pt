---
title: Utilizar o ponto final de predição através de programação testar imagens com classificador - visão personalizada
titlesuffix: Azure Cognitive Services
description: Saiba como utilizar a API para testar imagens de forma programática com o nosso classificador Serviço de Visão Personalizada.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: 1ee6edbf49bbcd2014afcf29ed3b737168a3b5bc
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046075"
---
# <a name="use-your-model-with-the-prediction-api"></a>Utilizar o seu modelo com a API de predição

Depois de preparar o seu modelo, pode testar imagens por meio de programação submetê-los para o ponto final de API de predição.

> [!NOTE]
> Este documento demonstra como utilizar a linguagem de programação C# para enviar uma imagem para a API de Predição. Para obter mais informações e exemplos, consulte a [referência da API de predição](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publicar a sua iteração preparada

Na [página Web do Serviço de Visão Personalizada](https://customvision.ai), selecione o seu projeto e, em seguida, selecione o separador __Performance__ (Desempenho).

Para enviar imagens para a API de predição, primeiro terá de publicar a sua iteração para predição, que pode ser feita selecionando __publicar__ e especificando um nome para a iteração publicado. Isso fará com que seu modelo acessível para a API de predição do seu recurso do Azure de visão personalizada.

![A guia de desempenho é mostrada, com um retângulo vermelho que envolvem o botão Publish.](./media/use-prediction-api/unpublished-iteration.png)

Assim que o seu modelo foi publicado com êxito, verá uma etiqueta de "Publicado" aparecem junto à sua iteração na barra lateral do lado esquerdo, e seu nome aparecerá na descrição da iteração.

![A guia de desempenho é mostrada, com um retângulo vermelho que envolvem a etiqueta de publicada e o nome da iteração publicado.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Obter o URL e a chave de predição

Assim que o seu modelo for publicado, pode obter as informações necessárias, selecionando __URL de predição__. Esta ação irá abrir uma caixa de diálogo com informações sobre como usar a API de predição, incluindo o __URL de predição__ e __chave de predição__.

![A guia de desempenho é mostrada com um retângulo vermelho que envolvem o botão de URL de predição.](./media/use-prediction-api/published-iteration-prediction-url.png)

![A guia de desempenho é mostrada com um retângulo vermelho que envolvem o valor de URL de predição para utilizar um ficheiro de imagem e o valor de chave de predição.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> Sua __chave de predição__ também podem ser encontrados no [portal do Azure](https://portal.azure.com) página para o recurso de Azure de visão personalizada associados a seu projeto, no __chaves__ painel.

Neste guia, irá utilizar uma imagem local, por isso, copie o URL sob **se tiver um ficheiro de imagem** para uma localização temporária. Copie o correspondente __chave de predição__ valor também.

## <a name="create-the-application"></a>Criar a aplicação

1. No Visual Studio, crie um novo C# aplicação de consola.

1. Utilize o seguinte código como o corpo do ficheiro __Program.cs__.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. Altere as seguintes informações:
   * Definir o `namespace` campo para o nome do seu projeto.
   * Substitua o marcador de posição `<Your prediction key>` com o valor da chave que obteve anteriormente.
   * Substitua o marcador de posição `<Your prediction URL>` com o URL obtido anteriormente.

## <a name="run-the-application"></a>Executar a aplicação

Quando o aplicativo é executado, lhe for pedido para introduzir um caminho para um ficheiro de imagem na consola do. A imagem, em seguida, foi submetida para a API de predição e os resultados da predição são retornados como uma cadeia de caracteres formatada em JSON. Segue-se uma resposta de exemplo.

```json
{
    "Id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "Project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "Iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "Created":"2019-03-20T16:47:31.322Z",
    "Predictions":[
        {"TagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","TagName":"cat", "Probability":1.0},
        {"TagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","TagName":"dog", "Probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Passos Seguintes

Neste guia, aprendeu a enviar imagens para sua personalizado/detetor de classificador de imagem e recebem uma resposta por meio de programação com o C# SDK. Em seguida, aprenda como concluir cenários ponto a ponto com o C#, ou começar a utilizar um idioma diferente do SDK.

* [Início rápido: .NET SDK](csharp-tutorial.md)
* [Início rápido: SDK Python](python-tutorial.md)
* [Início rápido: SDK Java](java-tutorial.md)
* [Início rápido: SDK de Node](node-tutorial.md)
* [Início rápido: SDK Go](go-tutorial.md)
