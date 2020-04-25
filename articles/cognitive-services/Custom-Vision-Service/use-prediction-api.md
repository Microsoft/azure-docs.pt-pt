---
title: Utilize o ponto final da previsão para testar programáticamente imagens com classificador - Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar a API para testar imagens de forma programática com o nosso classificador Serviço de Visão Personalizada.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: 88aaa3d54ea44a15f7900aba093bf28c70c19695
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82129863"
---
# <a name="use-your-model-with-the-prediction-api"></a>Use o seu modelo com a previsão API

Depois de treinar o seu modelo, pode testar as imagens programáticamente, submetendo-as ao ponto final da Previsão API.

> [!NOTE]
> Este documento demonstra como utilizar a linguagem de programação C# para enviar uma imagem para a API de Predição. Para mais informações e exemplos, consulte a referência da [Previsão API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Publique a sua iteração treinada

Na [página Web do Serviço de Visão Personalizada](https://customvision.ai), selecione o seu projeto e, em seguida, selecione o separador __Performance__ (Desempenho).

Para submeter imagens à API de Previsão, primeiro terá de publicar a sua iteração para previsão, o que pode ser feito selecionando __publicar__ e especificando um nome para a iteração publicada. Isto tornará o seu modelo acessível à API de Previsão do seu recurso Custom Vision Azure.

![O separador de desempenho é mostrado, com um retângulo vermelho em torno do botão Publicar.](./media/use-prediction-api/unpublished-iteration.png)

Uma vez publicado com sucesso o seu modelo, verá aparecer uma etiqueta "Publicada" ao lado da sua iteração na barra lateral esquerda, e o seu nome aparecerá na descrição da iteração.

![O separador de desempenho é mostrado, com um retângulo vermelho em torno da etiqueta publicada e o nome da iteração publicada.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>Obter o URL e a chave de predição

Uma vez publicado o seu modelo, pode recuperar as informações necessárias selecionando __o URL__de Previsão . Isto abrirá um diálogo com informações para a utilização da API de previsão, incluindo o __URL__ de Previsão e __a Chave de Previsão__.

![O separador de desempenho é mostrado com um retângulo vermelho em torno do botão URL de previsão.](./media/use-prediction-api/published-iteration-prediction-url.png)

![O separador de desempenho é mostrado com um retângulo vermelho em torno do valor URL de Previsão para a utilização de um ficheiro de imagem e do valor de Previsão-Chave.](./media/use-prediction-api/prediction-api-info.png)


Neste guia, utilizará uma imagem local, por isso copie o URL em **se tiver um ficheiro** de imagem para um local temporário. Copie também o valor correspondente da __Chave de Previsão.__

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
   * Desloque o `namespace` campo ao nome do seu projeto.
   * Substitua o `<Your prediction key>` espaço reservado pelo valor-chave que recuperou anteriormente.
   * Substitua o `<Your prediction URL>` espaço reservado pelo URL que recuperou anteriormente.

## <a name="run-the-application"></a>Executar a aplicação

Ao executar a aplicação, é-lhe solicitado que entre num caminho para um ficheiro de imagem na consola. A imagem é então submetida à API de previsão, e os resultados da previsão são devolvidos como uma cadeia formada pela JSON. Segue-se uma resposta exemplo.

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

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu a submeter imagens ao seu classificador/detetor de imagem personalizado e a receber uma resposta programática com o C# SDK. Em seguida, aprenda a completar cenários de ponta a ponta com C#, ou começar a usar um SDK de linguagem diferente.

* [Quickstart: Custom Vision SDK](quickstarts/image-classification.md)
