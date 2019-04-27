---
title: 'Início rápido: Detetar rostos numa imagem com a API REST do Azure e Java'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá utilizar a API de REST de Face do Azure com Java para detetar rostos numa imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: pafarley
ms.openlocfilehash: c0c1b9c1e9afc84e9702f6c1897d372a017be868
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815608"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Início rápido: Detetar rostos numa imagem usando a REST API e o Java

Neste início rápido, irá utilizar a API de REST de Face do Azure com o Java para detetar rostos humanos numa imagem.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura da Face API. Pode obter uma chave de subscrição de avaliação gratuita de [experimentar os serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Em alternativa, siga as instruções em [criar uma conta dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço de API de rostos e obtenha a chave.
- Qualquer Java IDE à sua escolha.

## <a name="create-the-java-project"></a>Criar o projeto de Java

1. Crie uma nova aplicação de Java de linha de comandos no seu IDE e adicione um **Main** classe com um **principal** método.
1. Importe as seguintes bibliotecas para o projeto Java. Se estiver a utilizar o Maven, as coordenadas do Maven são fornecidas para cada biblioteca.
   - [Cliente Apache HTTP](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.6)
   - [Principais de Apache HTTP](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.10)
   - [Biblioteca JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Registo do Apache Commons](https://commons.apache.org/proper/commons-logging/download_logging.cgi) (commons-registo: commons-registo: 1.1.2)

## <a name="add-face-detection-code"></a>Adicione o código de detecção de rostos

Abra a classe principal do seu projeto. Aqui, irá adicionar o código necessário para carregar imagens e detetar rostos.

### <a name="import-packages"></a>Importar pacotes

Adicione o seguinte `import` declarações na parte superior do ficheiro.

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

### <a name="add-essential-fields"></a>Adicione campos essenciais

Substitua a **Main** classe com o código a seguir. Estes dados especifica como se pode ligar para o serviço de rostos e onde obter os dados de entrada. Terá de atualizar o `subscriptionKey` campo com o valor da sua chave de assinatura e poderá ter de alterar o `uriBase` , para que ele contém o identificador de região correto de cadeias de caracteres (consulte a [documentos da API de rostos](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para obter uma lista de todas as regiões pontos de extremidade). Também pode ser útil definir o `imageWithFaces` valor para um caminho que aponta para um ficheiro de imagem diferentes.

O `faceAttributes` campo é simplesmente uma lista de determinados tipos de atributos. Ele irá especificar quais as informações para recuperar sobre os rostos detetados.

```Java
public class Main {
    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // NOTE: You must use the same region in your REST call as you used to
    // obtain your subscription keys. For example, if you obtained your
    // subscription keys from westus, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the "westus" region. If you
    // use a free trial subscription key, you shouldn't need to change this region.
    private static final String uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect";

    private static final String imageWithFaces =
        "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}";

    private static final String faceAttributes =
        "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

### <a name="call-the-face-detection-rest-api"></a>Chamar a REST API de deteção de rostos

Adicionar a **principal** método com o código a seguir. Ele constrói uma chamada REST para a API de rostos para detetar informações de rosto na imagem remota (o `faceAttributes` cadeia Especifica atributos de qual face deve obter). Em seguida, escreve os dados de saída para uma cadeia de caracteres do JSON.

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

Diretamente abaixo do código anterior, adicione o bloco seguinte, que converte os dados JSON retornados num formato mais facilmente legível antes imprimi-la na consola. Finalmente, fechar o bloco try-catch, a **principal** método e o **Main** classe.

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

Compilar o código e executá-lo. Uma resposta com êxito irá apresentar dados de Face no formato de fácil leitura JSON na janela da consola. Por exemplo:

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

Neste início rápido, criou uma aplicação de consola Java simples que utiliza chamadas REST com a API Face do Azure para detetar rostos numa imagem e retornar seus atributos. Em seguida, Aprenda a fazer mais com esta funcionalidade num aplicativo Android.

> [!div class="nextstepaction"]
> [Tutorial: Criar uma aplicação Android para detetar e rostos de fotograma](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)
