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
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: e50933ea0231b4be22c2d0f82d33fd02dd0918f5
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351614"
---
# <a name="use-the-prediction-endpoint-to-test-images-programmatically"></a>Utilizar o ponto de final de predição para testar as imagens por meio de programação

Após preparar o seu modelo, pode testar imagens de forma programática ao enviá-las para a API de Predição.

> [!NOTE]
> Este documento demonstra como utilizar a linguagem de programação C# para enviar uma imagem para a API de Predição. Para obter mais informações e exemplos sobre como utilizar a API, veja a [referência da API de Predição](https://go.microsoft.com/fwlink/?linkid=865445).

## <a name="get-the-url-and-prediction-key"></a>Obter o URL e a chave de predição

Na [página Web do Serviço de Visão Personalizada](https://customvision.ai), selecione o seu projeto e, em seguida, selecione o separador __Performance__ (Desempenho). Para apresentar informações sobre como utilizar a API de Predição, incluindo a __chave de Predição__, selecione __Prediction URL__ (URL de Predição). Para projetos anexados a um recurso do Azure, sua __chave de predição__ também podem ser encontrados no [portal do Azure](https://portal.azure.com) página de recursos do Azure associados sob __chaves__. Copie as seguintes informações para utilizar na aplicação:

* O __URL__ para utilizar um __ficheiro de imagem__.
* O valor da __chave de Predição__.

> [!TIP]
> Se tiver múltiplas iterações, pode controlar a que pretende utilizar ao predefini-la. Selecione a iteração na secção __Iterations__ (Iterações) e, em seguida, selecione __Make default__ (Predefinir) na parte superior da página.

![O separador Performance (Desempenho) com um retângulo vermelho em redor da opção Prediction URL (URL de Predição).](./media/use-prediction-api/prediction-url.png)

## <a name="create-the-application"></a>Criar a aplicação

1. No Visual Studio, crie uma nova aplicação de consola C#.

1. Utilize o seguinte código como o corpo do ficheiro __Program.cs__.

    > [!IMPORTANT]
    > Altere as seguintes informações:
    >
    > * Mude o __namespace__ para o nome do seu projeto.
    > * Defina o valor __Prediction-Key__ (Chave de Predição) que recebeu anteriormente na linha que começa por `client.DefaultRequestHeaders.Add("Prediction-Key",`.
    > * Defina o valor __URL__ que recebeu anteriormente na linha que começa por `string url =`.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CSPredictionSample
    {
        static class Program
        {
            static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }

            static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid subscription key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

                // Prediction URL - replace this example URL with your valid prediction URL.
                string url = "https://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/prediction/d16e136c-5b0b-4b84-9341-6a3fff8fa7fe/image?iterationId=f4e573f6-9843-46db-8018-b01d034fd0f2";

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
        }
    }
    ```

## <a name="use-the-application"></a>Utilizar a aplicação

Ao executar a aplicação, introduza o caminho para um ficheiro de imagem. A imagem será enviada para a API e os resultados serão devolvidos como um documento JSON. O seguinte JSON é um exemplo da resposta

```json
{
    "Id":"3f76364c-b8ae-4818-a2b2-2794cfbe377a",
    "Project":"2277aca4-7aff-4742-8afb-3682e251c913",
    "Iteration":"84105bfe-73b5-4fcc-addb-756c0de17df2",
    "Created":"2018-05-03T14:15:22.5659829Z",
    "Predictions":[
        {"TagId":"35ac2ad0-e3ef-4e60-b81f-052a1057a1ca","Tag":"dog","Probability":0.102716163},
        {"TagId":"28e1a872-3776-434c-8cf0-b612dd1a953c","Tag":"cat","Probability":0.02037274}
    ]
}
```

## <a name="next-steps"></a>Passos Seguintes

[Export the model for mobile use](export-your-model.md) (Exportar o modelo para utilização em dispositivos móveis)
