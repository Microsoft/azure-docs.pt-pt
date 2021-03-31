---
title: Use o ponto final de previsão para testar programáticamente imagens com classificador - Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar a API para testar imagens de forma programática com o nosso classificador Serviço de Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 7f1939536e033d2cf964dd2f4ee562e4ee20061b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "88934757"
---
# <a name="use-your-model-with-the-prediction-api"></a>Use o seu modelo com a previsão API

Depois de treinar o seu modelo, pode testar as imagens programáticamente submetendo-as ao ponto final da Previsão API.

> [!NOTE]
> Este documento demonstra como utilizar a linguagem de programação C# para enviar uma imagem para a API de Predição. Para obter mais informações e exemplos, consulte a [referência API de previsão](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publique a sua iteração treinada

Na [página Web do Serviço de Visão Personalizada](https://customvision.ai), selecione o seu projeto e, em seguida, selecione o separador __Performance__ (Desempenho).

Para submeter imagens à API de Previsão, primeiro terá de publicar a sua iteração para previsão, o que pode ser feito selecionando __Publicar__ e especificar um nome para a iteração publicada. Isto tornará o seu modelo acessível à API de Previsão do seu recurso Custom Vision Azure.

![O separador de desempenho é mostrado, com um retângulo vermelho em torno do botão Publicar.](./media/use-prediction-api/unpublished-iteration.png)

Uma vez publicado o seu modelo com sucesso, verá uma etiqueta "Publicada" aparecer ao lado da sua iteração na barra lateral esquerda, e o seu nome aparecerá na descrição da iteração.

![O separador de desempenho é mostrado, com um retângulo vermelho em torno da etiqueta publicada e o nome da iteração publicada.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Obter o URL e a chave de predição

Uma vez publicado o seu modelo, pode recuperar as informações necessárias selecionando __URL de previsão__. Isto abrirá um diálogo com informações para a utilização da API de previsão, incluindo o __URL de previsão__ e a chave de __previsão__.

![O separador de desempenho é mostrado com um retângulo vermelho em torno do botão URL de previsão.](./media/use-prediction-api/published-iteration-prediction-url.png)

![O separador de desempenho é mostrado com um retângulo vermelho em torno do valor URL de previsão para a utilização de um ficheiro de imagem e do valor Prediction-Key.](./media/use-prediction-api/prediction-api-info.png)


Neste guia, utilizará uma imagem local, por isso copie o URL em **se tiver um ficheiro de imagem** para um local temporário. Copie também o valor __correspondente da Chave de Previsão.__

## <a name="create-the-application"></a>Criar a aplicação

1. No Visual Studio, crie uma nova aplicação de consola C#.

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
   * Desaça o `namespace` campo para o nome do seu projeto.
   * Substitua o espaço reservado `<Your prediction key>` pelo valor chave que recuperou anteriormente.
   * Substitua o espaço reservado `<Your prediction URL>` pelo URL que recuperou anteriormente.

## <a name="run-the-application"></a>Executar a aplicação

Quando executar a aplicação, é solicitado que introduza um caminho para um ficheiro de imagem na consola. A imagem é então submetida à API de Previsão, e os resultados da previsão são devolvidos como uma corda formatada por JSON. Segue-se uma resposta de exemplo.

```json
{
    "id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "created":"2019-03-20T16:47:31.322Z",
    "predictions":[
        {"tagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","tagName":"cat", "probability":1.0},
        {"tagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","tagName":"dog", "probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a enviar imagens para o seu classificador/detetor de imagens personalizado e receber uma resposta programática com o C# SDK. Em seguida, aprenda a completar cenários de ponta a ponta com C#, ou começar a usar um SDK de linguagem diferente.

* [Quickstart: Custom Vision SDK](quickstarts/image-classification.md)
