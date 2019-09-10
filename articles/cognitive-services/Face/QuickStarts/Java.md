---
title: 'Início rápido: Detectar faces em uma imagem com a API REST do Azure e o Java'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você usará a API REST do Azure face com Java para detectar rostos em uma imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 09/06/2019
ms.author: pafarley
ms.openlocfilehash: 30e4852668fc12c38cd7d1794c461041acd654db
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859193"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Início rápido: Detectar faces em uma imagem usando a API REST e o Java

Neste guia de início rápido, você usará a API REST do Azure face com Java para detectar faces humanas em uma imagem.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura da Face API. Pode obter uma chave de subscrição de avaliação gratuita de [experimentar os serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Em alternativa, siga as instruções em [criar uma conta dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço de API de rostos e obtenha a chave.
- Qualquer Java IDE de sua escolha.

## <a name="create-the-java-project"></a>Criar o projeto Java

1. Crie um novo aplicativo Java de linha de comando em seu IDE e adicione uma classe **principal** com um método **Main** .
1. Importe as seguintes bibliotecas para o projeto Java. Se estiver a utilizar o Maven, as coordenadas do Maven são fornecidas para cada biblioteca.
   - [Cliente http do Apache](https://hc.apache.org/downloads.cgi) (org. Apache. httpcomponents: HttpClient: 4.5.6)
   - [Núcleo http do Apache](https://hc.apache.org/downloads.cgi) (org. Apache. httpcomponents: httpcore: 4.4.10)
   - [Biblioteca JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Log do Apache Commons](https://commons.apache.org/proper/commons-logging/download_logging.cgi) (Commons – log: Commons-Logging: 1.1.2)

## <a name="add-face-detection-code"></a>Adicionar código de detecção facial

Abra a classe principal do seu projeto. Aqui, você adicionará o código necessário para carregar imagens e detectar rostos.

### <a name="import-packages"></a>Importar pacotes

Adicione as instruções `import` a seguir à parte superior do arquivo.

```java
// This sample uses Apache HttpComponents:
// http://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/
// https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/

import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.util.EntityUtils;
import org.json.JSONArray;
import org.json.JSONObject;
```

### <a name="add-essential-fields"></a>Adicionar campos essenciais

Substitua a classe **principal** pelo código a seguir. Esses dados especificam como se conectar ao serviço de face e onde obter os dados de entrada. Você precisará atualizar o `subscriptionKey` campo com o valor de sua chave de assinatura e alterar a cadeia de `uriBase` caracteres para que ela contenha a cadeia de caracteres de ponto de extremidade correta. Talvez você também queira definir o `imageWithFaces` valor para um caminho que aponte para um arquivo de imagem diferente.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

O `faceAttributes` campo é simplesmente uma lista de determinados tipos de atributos. Ele especificará quais informações serão recuperadas sobre as faces detectadas.

```Java
public class Main {
    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    private static final String uriBase =
        "https://<My Endpoint String>.com/face/v1.0/detect";

    private static final String imageWithFaces =
        "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}";

    private static final String faceAttributes =
        "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

### <a name="call-the-face-detection-rest-api"></a>Chamar a API REST de detecção facial

Adicione o método **Main** com o código a seguir. Ele constrói uma chamada REST para o API de detecção facial para detectar informações de face na imagem remota (a cadeia `faceAttributes` de caracteres especifica quais atributos de face recuperar). Em seguida, ele grava os dados de saída em uma cadeia de caracteres JSON.

```Java
    public static void main(String[] args) {
        HttpClient httpclient = HttpClientBuilder.create().build();

        try
        {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("returnFaceId", "true");
            builder.setParameter("returnFaceLandmarks", "false");
            builder.setParameter("returnFaceAttributes", faceAttributes);

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity reqEntity = new StringEntity(imageWithFaces);
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();
```

### <a name="parse-the-json-response"></a>Analisar a resposta JSON

Diretamente abaixo do código anterior, adicione o bloco a seguir, que converte os dados JSON retornados em um formato mais fácil de ler antes de imprimi-lo no console. Finalmente, feche o bloco try-catch, o método **Main** e a classe **Main** .

```Java
            if (entity != null)
            {
                // Format and display the JSON response.
                System.out.println("REST Response:\n");

                String jsonString = EntityUtils.toString(entity).trim();
                if (jsonString.charAt(0) == '[') {
                    JSONArray jsonArray = new JSONArray(jsonString);
                    System.out.println(jsonArray.toString(2));
                }
                else if (jsonString.charAt(0) == '{') {
                    JSONObject jsonObject = new JSONObject(jsonString);
                    System.out.println(jsonObject.toString(2));
                } else {
                    System.out.println(jsonString);
                }
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="run-the-app"></a>Executar a aplicação

Compile o código e execute-o. Uma resposta bem-sucedida exibirá dados de face em formato JSON facilmente legível na janela do console. Por exemplo:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
    "hair": {
      "bald": 0,
      "invisible": false,
      "hairColor": [
        {
          "color": "brown",
          "confidence": 1
        },
        {
          "color": "black",
          "confidence": 0.87
        },
        {
          "color": "other",
          "confidence": 0.51
        },
        {
          "color": "blond",
          "confidence": 0.08
        },
        {
          "color": "red",
          "confidence": 0.08
        },
        {
          "color": "gray",
          "confidence": 0.02
        }
      ]
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você criou um aplicativo de console Java simples que usa chamadas REST com o API de Detecção Facial do Azure para detectar rostos em uma imagem e retornar seus atributos. Em seguida, saiba como fazer mais com essa funcionalidade em um aplicativo Android.

> [!div class="nextstepaction"]
> [Tutorial: Criar um aplicativo Android para detectar e enquadrar rostos](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)
