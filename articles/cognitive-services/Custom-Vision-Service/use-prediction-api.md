---
title: 'Exemplo: Utilizar o ponto final de predição através de programação testar imagens com classificador - visão personalizada'
titlesuffix: Azure Cognitive Services
description: Saiba como utilizar a API para testar imagens de forma programática com o nosso classificador Serviço de Visão Personalizada.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 03/26/2019
ms.author: anroth
ms.openlocfilehash: 715fa526c83608c9922315e3a0d89b67b31e0d16
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472732"
---
#  <a name="use-your-model-with-the-prediction-api"></a>Utilizar o seu modelo com a API de predição

Após preparar o seu modelo, pode testar imagens de forma programática ao enviá-las para a API de Predição.

> [!NOTE]
> Este documento demonstra como utilizar a linguagem de programação C# para enviar uma imagem para a API de Predição. Para obter mais informações e exemplos sobre como utilizar a API, veja a [referência da API de Predição](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publicar a sua iteração preparada

Na [página Web do Serviço de Visão Personalizada](https://customvision.ai), selecione o seu projeto e, em seguida, selecione o separador __Performance__ (Desempenho).

Para enviar imagens para a API de predição, primeiro terá de publicar a sua iteração para predição, que pode ser feita selecionando __publicar__ e especificando um nome para a iteração publicado. Isso permitirá que seu modelo seja acessível para a API de predição do seu recurso do Azure de visão personalizada. 

![A guia de desempenho é mostrada, com um retângulo vermelho que envolvem o botão Publish.](./media/use-prediction-api/unpublished-iteration.png)

Assim que o seu modelo foi publicado com êxito, verá uma etiqueta de "Publicado" aparecem junto à sua iteração na barra lateral do lado esquerdo, bem como o nome da iteração publicado na descrição da iteração.

![A guia de desempenho é mostrada, com um retângulo vermelho que envolvem a etiqueta de publicada e o nome da iteração publicado.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Obter o URL e a chave de predição

Assim que o seu modelo for publicado, pode obter informações sobre como utilizar a API de predição, selecionando __URL de predição__. Esta ação irá abrir uma caixa de diálogo, como o mostrado abaixo, com informações sobre como usar a API de predição, incluindo o __URL de predição__ e __chave de predição__.

![A guia de desempenho é mostrada com um retângulo vermelho que envolvem o botão de URL de predição.](./media/use-prediction-api/published-iteration-prediction-url.png)

![A guia de desempenho é mostrada com um retângulo vermelho que envolvem o valor de URL de predição para utilizar um ficheiro de imagem e o valor de chave de predição.](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> Sua __chave de predição__ também podem ser encontrados no [Portal do Azure](https://portal.azure.com) página para o recurso de Azure de visão personalizada associada ao seu projeto, em __chaves__. 

Na caixa de diálogo, copie as seguintes informações para uso no aplicativo:

* __URL de predição__ para a utilização de um __ficheiro de imagem__.
* __Chave de predição__ valor.

## <a name="create-the-application"></a>Criar a aplicação

1. No Visual Studio, crie uma nova aplicação de consola C#.

1. Utilize o seguinte código como o corpo do ficheiro __Program.cs__.

    > [!IMPORTANT]
    > Altere as seguintes informações:
    >
    > * Mude o __namespace__ para o nome do seu projeto.
    > * Definir o __chave de predição__ valor que obteve anteriormente na linha que começa com `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Definir o __URL de predição__ valor que obteve anteriormente na linha que começa com `string url =`.

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
                client.DefaultRequestHeaders.Add("Prediction-Key", "3b9dde6d1ae1453a86bfeb1d945300f2");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "https://southcentralus.api.cognitive.microsoft.com/customvision/v3.0/Prediction/8622c779-471c-4b6e-842c-67a11deffd7b/classify/iterations/Cats%20vs.%20Dogs%20-%20Published%20Iteration%203/image";

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

## <a name="use-the-application"></a>Utilizar a aplicação

Quando executar a aplicação, irá introduzir o caminho para um ficheiro de imagem na consola do. A imagem foi submetida para a API de predição e os resultados da predição são retornados como um documento JSON. O JSON seguinte é um exemplo da resposta.

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

[Export the model for mobile use](export-your-model.md) (Exportar o modelo para utilização em dispositivos móveis)

[Introdução ao .NET SDKs](csharp-tutorial.md)

[Introdução ao Python SDKs](python-tutorial.md)

[Introdução ao Java SDKs](java-tutorial.md)

[Comece com SDKs de nó](node-tutorial.md)

[Introdução ao Go SDKs](go-tutorial.md)
