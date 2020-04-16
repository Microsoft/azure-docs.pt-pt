---
title: 'Início Rápido: gerar uma miniatura – REST, Java'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, irá gerar uma miniatura a partir de uma imagem através da API de Imagem Digitalizada com Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a1354bc74d13e02e5e0982a8f5d98b01fab67b4b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404778"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-java"></a>Quickstart: Gere uma miniatura utilizando a API e Java de Visão Computacional

Neste arranque rápido, você gerará uma miniatura a partir de uma imagem usando a API DE DESCANSO DE Visão Computacional. Especifica a altura e a largura, que podem ser diferentes da proporção da imagem de entrada. A Computer Vision utiliza uma cultura inteligente para identificar inteligentemente a área de interesse e gerar coordenadas de cultura baseadas nessa região.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter a plataforma [Java&trade;, Standard Edition Development Kit 7 ou 8](https://aka.ms/azure-jdks) (JDK 7 ou 8) instalada.
- Tem de ter uma chave de subscrição da Imagem Digitalizada. Você pode obter uma chave de teste gratuita da [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou, siga as instruções na [Conta Criar uma Conta de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever a Visão Computacional e obter a sua chave. Em seguida, [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para `COMPUTER_VISION_SUBSCRIPTION_KEY` a `COMPUTER_VISION_ENDPOINT`chave e corda final de serviço, nomeada e, respectivamente.

## <a name="create-and-run-the-sample-application"></a>Criar e executar a aplicação de exemplo

Para criar e executar o exemplo, siga os seguintes passos:

1. Crie um novo projeto Java no seu IDE ou editor favorito. Se a opção estiver disponível, crie o projeto Java a partir de um modelo de aplicação de linha de comandos.
1. Importe as seguintes bibliotecas para o projeto Java. Se estiver a utilizar o Maven, as coordenadas do Maven são fornecidas para cada biblioteca.
   - [Cliente Apache HTTP](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.5)
   - [Núcleo Apache HTTP](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.9)
   - [Biblioteca JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. Adicione as seguintes instruções `import` ao ficheiro que contém a classe pública `Main` do seu projeto.  

   ```java
   import java.awt.*;
   import javax.swing.*;
   import java.net.URI;
   import java.io.InputStream;
   import javax.imageio.ImageIO;
   import java.awt.image.BufferedImage;
   import org.apache.http.HttpEntity;
   import org.apache.http.HttpResponse;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.json.JSONObject;
   ```

1. Substitua `Main` a classe pública pelo seguinte código.
1. Opcionalmente, substitua o valor de `imageToAnalyze` pelo URL de uma imagem diferente para a qual pretende gerar uma miniatura.
1. Guarde e, em seguida, crie o projeto Java.
1. Se estiver a utilizar um IDE, execute `Main`. Caso contrário, abra uma janela da linha de comandos e, em seguida, utilize o comando `java` para executar a classe compilada. Por exemplo, `java Main`.

```java
// This sample uses the following libraries:
//  - Apache HTTP client (org.apache.httpcomponents:httpclient:4.5.5)
//  - Apache HTTP core (org.apache.httpcomponents:httpccore:4.4.9)
//  - JSON library (org.json:json:20180130).


public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Add your Computer Vision subscription key and endpoint to your environment variables.
    // After setting, close and then re-open your command shell or project for the changes to take effect.
    String subscriptionKey = System.getenv("COMPUTER_VISION_SUBSCRIPTION_KEY");
    String endpoint = ("COMPUTER_VISION_ENDPOINT");

    private static final String uriBase = endpoint + 
            "vision/v2.1/generateThumbnail";

    private static final String imageToAnalyze =
        "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder uriBuilder = new URIBuilder(uriBase);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API method.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200) {
                // Display the thumbnail.
                System.out.println("\nDisplaying thumbnail.\n");
                displayImage(entity.getContent());
            } else {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Error:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }

    // Displays the given input stream as an image.
    private static void displayImage(InputStream inputStream) {
        try {
            BufferedImage bufferedImage = ImageIO.read(inputStream);

            ImageIcon imageIcon = new ImageIcon(bufferedImage);

            JLabel jLabel = new JLabel();
            jLabel.setIcon(imageIcon);

            JFrame jFrame = new JFrame();
            jFrame.setLayout(new FlowLayout());
            jFrame.setSize(100, 150);

            jFrame.add(jLabel);
            jFrame.setVisible(true);
            jFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="examine-the-response"></a>Examinar a resposta

É devolvida uma resposta com êxito como dados binários, que representa os dados da imagem da miniatura. Se o pedido for bem-sucedido, a miniatura é gerada a partir dos dados binários na resposta e apresentada numa janela separada, criada pela aplicação de exemplo. Se o pedido falhar, a resposta é apresentada na janela da consola. A resposta do pedido falhado contém um código de erro e uma mensagem para ajudar a determinar o que correu mal.

## <a name="next-steps"></a>Passos seguintes

Explore uma aplicação Java Swing que utiliza a Visão Computacional para realizar o reconhecimento ótico de caracteres (OCR); criar miniaturas de corte inteligente; e detetar, categorizar, etiquetar e descrever características visuais em imagens.

> [!div class="nextstepaction"]
> [Tutorial de Java de API de Imagem Digitalizada](../Tutorials/java-tutorial.md)

* Para experimentar rapidamente a API de Imagem Digitalizada, experimente a [Consola de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).