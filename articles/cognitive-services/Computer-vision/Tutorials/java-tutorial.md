---
title: Efetuar operações de imagem - Java
titlesuffix: Azure Cognitive Services
description: Explore uma aplicação Java Swing básica que utiliza a API de Imagem Digitalizada nos Serviços Cognitivos do Azure. Efetue o OCR, crie miniaturas e trabalhe com funcionalidades visuais numa imagem.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.author: kefre
ms.custom: seodec18
ms.date: 04/30/2019
ms.openlocfilehash: a22308e0c7ff924205f715692d011a4572b2bdb8
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232625"
---
# <a name="use-computer-vision-features-with-the-rest-api-and-java"></a>Utilizar as funcionalidades de imagem digitalizada com a REST API e o Java

Este tutorial mostra as funcionalidades da API REST de Imagem Digitalizada dos Serviços Cognitivos do Azure.

Explore uma aplicação do Java Swing que utilize a API REST de Imagem Digitalizada para realizar o reconhecimento ótico de carateres (OCR), criar miniaturas com recorte inteligente, bem como detetar, categorizar, etiquetar e descrever caraterísticas visuais, incluindo rostos, numa imagem. Este exemplo permite enviar um URL de imagem para análise ou processamento. Pode utilizar este exemplo de open source como um modelo para criar a sua própria aplicação em Java para utilizar a API REST de Imagem Digitalizada.

Este tutorial descreve como utilizar a Imagem Digitalizada para:

> [!div class="checklist"]
> * Analisar uma imagem
> * Identificar um marco natural ou artificial numa imagem
> * Identificar uma celebridade numa imagem
> * Criar uma miniatura de qualidade a partir de uma imagem
> * Ler texto impresso numa imagem
> * Ler texto manuscrito numa imagem

A aplicação de formulário rotativa de Java já foi escrita, mas não tem nenhuma funcionalidade. Neste tutorial, vai adicionar o código específico à API REST de Imagem Digitalizada para concluir a funcionalidade da aplicação.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

Este tutorial foi elaborado recorrendo ao IDE NetBeans. Mais especificamente, a versão **Java SE** do NetBeans, que pode [transferir aqui](https://netbeans.org/downloads/index.html).

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Subscrever a API de Imagem Digitalizada e obter uma chave de subscrição

Antes de criar o exemplo, têm de subscrever API de imagem digitalizada que faz parte dos serviços cognitivos do Azure. Para obter os detalhes da subscrição e da gestão de chaves, veja [Subscrições](https://azure.microsoft.com/try/cognitive-services/). Ambas as chaves primárias e secundárias são válidas para utilizar neste tutorial.

## <a name="acquire-incomplete-tutorial-project"></a>Adquirir o projeto de tutorial incompleto

### <a name="download-the-project"></a>Transfira o projeto

1. Aceda ao repositório do [Cognitive Services Java Computer Vision Tutorial](https://github.com/Azure-Samples/cognitive-services-java-computer-vision-tutorial) (Tutorial da Imagem Digitalizada de Java dos Serviços Cognitivos).
1. Clique no botão para **Clone or download** (Clonar ou transferir).
1. Clique em **Download ZIP** (Transferir ZIP) para transferir um ficheiro .zip do projeto de tutorial.

Não é necessário extrair o conteúdo do ficheiro .zip porque o NetBeans importa o projeto a partir do ficheiro .zip.

### <a name="import-the-tutorial-project"></a>Importar o projeto de tutorial

Importe o ficheiro **cognitive-services-java-computer-vision-tutorial-master.zip** para o NetBeans.

1. No NetBeans, clique em **File** (Ficheiro)  > **Import Project** (Importar Projeto) > **From ZIP...** (Do ZIP). A caixa de diálogo **Import Project(s) from ZIP** (Importar Projetos do ZIP) é apresentada.
1. No campo **ZIP File:** (Ficheiro ZIP:), clique no botão **Browse** (Procurar) para localizar o ficheiro **cognitive-services-java-computer-vision-tutorial-master.zip** e, em seguida, clique em **Open** (Abrir).
1. Clique em **Import** (Importar) na caixa de diálogo **Import Project(s) from ZIP** (Importar Projetos do ZIP).
1. No painel **Projects** (Projetos), expanda **ComputerVision** (Imagem Digitalizada)  > **Source Packages** (Pacotes de Origem)  > **&lt;pacote predefinido&gt;**. 
   Algumas versões do NetBeans utilizam **src** em vez de **Source Packages** (Pacotes de Origem)  > **&lt;pacote predefinido&gt;**. Nesse caso, expanda **src**.
1. Faça duplo clique em **MainFrame.java** para carregar o ficheiro para o editor do NetBeans. O separador **Design** (Estrutura) do ficheiro **MainFrame.java** é apresentado.
1. Clique no separador **Source** (Fonte) para ver o código fonte Java.

### <a name="build-and-run-the-tutorial-project"></a>Criar e executar o projeto de tutorial

1. Prima **F6** para criar e executar a aplicação de tutorial.

    Na aplicação de tutorial, clique num separador para abrir o painel para essa funcionalidade. Os botões têm métodos vazios e, por isso, não fazem nada.

    Na parte inferior da janela estão os campos **Subscription Key** (Chave de Subscrição) e **Subscription Region** (Região de Subscrição). Estes campos têm de ser preenchidos com uma chave de subscrição válida e a região correta para essa chave de subscrição. Para obter uma chave de subscrição, veja [Subscrições](https://azure.microsoft.com/try/cognitive-services/). Se tiver adquirido a sua chave de subscrição a partir da avaliação gratuita nessa ligação, a região predefinida **westcentralus** é a região correta para as chaves de subscrição.

1. Saia da aplicação de tutorial.

## <a name="add-tutorial-code-to-the-project"></a>Adicione o código do tutorial ao projeto

A aplicação Java Swing é configurada com seis separadores. Cada separador demonstra uma função diferente de Imagem Digitalizada (analisar, OCR, e assim por diante). As seis secções do tutorial não têm interdependências, pelo que pode adicionar uma secção, as seis secções ou qualquer subconjunto. Pode adicionar as secções em qualquer ordem.

### <a name="analyze-an-image"></a>Analisar uma imagem

A funcionalidade Analisar da Imagem Digitalizada analisa uma imagem em mais de 2000 objetos reconhecíveis, seres vivos, paisagens e ações. Quando a análise estiver concluída, a funcionalidade Analyze (Analisar) devolve um objeto JSON que descreve a imagem com etiquetas descritivas, análise de cores, legendas e mais.

Para concluir a funcionalidade Analyze (Analisar) da aplicação de tutorial, realize os seguintes passos:

#### <a name="add-the-event-handler-code-for-the-analyze-button"></a>Adicione o código do manipulador de eventos para o botão de analisar

O método **analyzeImageButtonActionPerformed** do processador de eventos limpa o formulário, apresenta a imagem especificada no URL e, em seguida, chama o método **AnalyzeImage** para analisar a imagem. Quando **AnalyzeImage** é devolvido, o método apresenta a resposta JSON formatada na área de texto **Response** (Resposta), extrai a primeira legenda do **JSONObject** e apresenta a legenda e o nível de confiança de que a legenda está correta.

Copie e cole o código seguinte no método **analyzeImageButtonActionPerformed**.

> [!NOTE]
> O NetBeans não permitirá colar na linha de definição de método (```private void```) nem na chaveta de fecho desse método. Para copiar o código, copie as linhas entre a definição de método e a chaveta de fecho e cole-as acima do conteúdo do método.

```java
    private void analyzeImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL analyzeImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        analyzeImage.setIcon(new ImageIcon());
        analyzeCaptionLabel.setText("");
        analyzeResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            analyzeImageUrl = new URL(analyzeImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(analyzeImageUrl);
            scaleAndShowImage(bImage, analyzeImage);
        } catch(IOException e) {
            analyzeResponseTextArea.setText("Error loading Analyze image: " + e.getMessage());
            return;
        }
        
        // Analyze the image.
        JSONObject jsonObj = AnalyzeImage(analyzeImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        analyzeResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("description") && jsonObj.getJSONObject("description").has("captions")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("description").getJSONArray("captions").getJSONObject(0);
            
            if (jsonCaption.has("text") && jsonCaption.has("confidence")) {
                
                analyzeCaptionLabel.setText("Caption: " + jsonCaption.getString("text") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

O método **AnalyzeImage** encapsula a chamada à API REST para analisar uma imagem. O método devolve um **JSONObject** que descreve a imagem ou **nulo**, se tiver ocorrido um erro.

Copie e cole o método **AnalyzeImage** imediatamente abaixo do método **analyzeImageButtonActionPerformed**.

```java
    /**
     * Encapsulates the Microsoft Cognitive Services REST API call to analyze an image.
     * @param imageUrl: The string URL of the image to analyze.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject AnalyzeImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call for Analyze Image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseAnalyze;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color,Adult");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
 ```

#### <a name="run-the-analyze-function"></a>Executar a função de análise

Prima **F6** para executar a aplicação. Coloque a sua chave de subscrição no campo **Subscription Key** (Chave de Subscrição) e verifique se está a utilizar a região correta na **Subscription Region** (Região de Subscrição). Introduza um URL para uma imagem a analisar e, em seguida, clique no botão **Analyze Image** (Analisar Imagem) para analisar uma imagem e ver o resultado.

### <a name="recognize-a-landmark"></a>Reconhecer um marco

A funcionalidade Landmark (Marco) da Imagem Digitalizada analisa uma imagem relativamente a marcos naturais e artificiais, como montanhas ou edifícios famosos. Quando a análise estiver concluída, a funcionalidade Landmark (Marco) devolve um objeto JSON que identifica os marcos encontrados na imagem.

Para concluir a funcionalidade Landmark (Marco) da aplicação de tutorial, realize os seguintes passos:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Adicionar o código do processador de eventos para o botão de formulário

O método **landmarkImageButtonActionPerformed** do processador de eventos limpa o formulário, apresenta a imagem especificada no URL e, em seguida, chama o método **LandmarkImage** para analisar a imagem. Quando **LandmarkImage** é devolvido, o método apresenta a resposta JSON formatada na área de texto **Response** (Resposta) e, em seguida, extrai o primeiro nome de marco do **JSONObject** e apresenta-o na janela juntamente com o nível de confiança de que o marco foi identificado corretamente.

Copie e cole o código seguinte no método **landmarkImageButtonActionPerformed**.

> [!NOTE]
> O NetBeans não permitirá colar na linha de definição de método (```private void```) nem na chaveta de fecho desse método. Para copiar o código, copie as linhas entre a definição de método e a chaveta de fecho e cole-as acima do conteúdo do método.

```java
    private void landmarkImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL landmarkImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        landmarkImage.setIcon(new ImageIcon());
        landmarkCaptionLabel.setText("");
        landmarkResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            landmarkImageUrl = new URL(landmarkImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(landmarkImageUrl);
            scaleAndShowImage(bImage, landmarkImage);
        } catch(IOException e) {
            landmarkResponseTextArea.setText("Error loading Landmark image: " + e.getMessage());
            return;
        }
        
        // Identify the landmark in the image.
        JSONObject jsonObj = LandmarkImage(landmarkImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        landmarkResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("landmarks")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("landmarks").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                landmarkCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

O método **LandmarkImage** encapsula a chamada à API REST para analisar uma imagem. O método devolve um **JSONObject** que descreve os marcos encontrados na imagem ou **null** (nulo), se tiver ocorrido um erro.

Copie e cole o método **LandmarkImage** imediatamente abaixo do método **landmarkImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify a landmark in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject LandmarkImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify a Landmark in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseLandmark;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-landmark-function"></a>Executar a função de ponto de referência

Prima **F6** para executar a aplicação. Coloque a sua chave de subscrição no campo **Subscription Key** (Chave de Subscrição) e verifique se está a utilizar a região correta na **Subscription Region** (Região de Subscrição). Clique no separador **Landmark** (Marco), introduza um URL para uma imagem de um marco e, em seguida, clique no botão **Analyze Image** (Analisar Imagem) para analisar uma imagem e ver o resultado.

### <a name="recognize-celebrities"></a>Reconhecer celebridades

A funcionalidade Celebrities (Celebridades) da Imagem Digitalizada analisa uma imagem relativamente a pessoas famosas. Quando a análise estiver concluída, a funcionalidade Celebrities (Celebridades) devolve um objeto JSON que identifica as celebridades encontradas na imagem.

Para concluir a funcionalidade Celebrities (Celebridades) da aplicação de tutorial, realize os seguintes passos:

#### <a name="add-the-event-handler-code-for-the-celebrities-button"></a>Adicione o código do manipulador de eventos para o botão de celebridades

O método **celebritiesImageButtonActionPerformed** do processador de eventos limpa o formulário, apresenta a imagem especificada no URL e, em seguida, chama o método **CelebritiesImage** para analisar a imagem. Quando **CelebritiesImage** é devolvido, o método apresenta a resposta JSON formatada na área de texto **Response** (Resposta) e, em seguida, extrai o primeiro nome de celebridade do **JSONObject** e apresenta-o na janela juntamente com o nível de confiança de que a celebridade foi identificada corretamente.

Copie e cole o código seguinte no método **celebritiesImageButtonActionPerformed**.

> [!NOTE]
> O NetBeans não permitirá colar na linha de definição de método (```private void```) nem na chaveta de fecho desse método. Para copiar o código, copie as linhas entre a definição de método e a chaveta de fecho e cole-as acima do conteúdo do método.

```java
    private void celebritiesImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL celebritiesImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        celebritiesImage.setIcon(new ImageIcon());
        celebritiesCaptionLabel.setText("");
        celebritiesResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            celebritiesImageUrl = new URL(celebritiesImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(celebritiesImageUrl);
            scaleAndShowImage(bImage, celebritiesImage);
        } catch(IOException e) {
            celebritiesResponseTextArea.setText("Error loading Celebrity image: " + e.getMessage());
            return;
        }
        
        // Identify the celebrities in the image.
        JSONObject jsonObj = CelebritiesImage(celebritiesImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        celebritiesResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("celebrities")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("celebrities").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                celebritiesCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

O método **CelebritiesImage** encapsula a chamada à API REST para analisar uma imagem. O método devolve um **JSONObject** que descreve as celebridades encontradas na imagem ou **nulo**, se tiver ocorrido um erro.

Copie e cole o método **CelebritiesImage** imediatamente abaixo do método **celebritiesImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify celebrities in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject CelebritiesImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseCelebrities;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-celebrities-function"></a>Executar a função de celebridades

Prima **F6** para executar a aplicação. Coloque a sua chave de subscrição no campo **Subscription Key** (Chave de Subscrição) e verifique se está a utilizar a região correta na **Subscription Region** (Região de Subscrição). Clique no separador **Celebrities** (Celebridades), introduza um URL para uma imagem de uma celebridade e, em seguida, clique no botão **Analyze Image** (Analisar Imagem) para analisar uma imagem e ver o resultado.

### <a name="intelligently-generate-a-thumbnail"></a>Gerar uma miniatura de forma inteligente

A funcionalidade Thumbnail (Miniatura) da Imagem Digitalizada gera uma miniatura a partir de uma imagem. Ao utilizar a funcionalidade **Smart Crop** (Recorte Inteligente), a funcionalidade Thumbnail (Miniatura) identificará a área de interesse numa imagem e centrará a miniatura nessa área, para gerar imagens em miniatura mais agradáveis esteticamente.

Para concluir a funcionalidade Thumbnail (Miniatura) da aplicação de tutorial, realize os seguintes passos:

#### <a name="add-the-event-handler-code-for-the-thumbnail-button"></a>Adicione o código do manipulador de eventos do botão em miniatura

O método **thumbnailImageButtonActionPerformed** do processador de eventos limpa o formulário, apresenta a imagem especificada no URL e, em seguida, chama o método **getThumbnailImage** para criar a miniatura. Quando **getThumbnailImage** é devolvido, o método apresenta a miniatura gerada.

Copie e cole o código seguinte no método **thumbnailImageButtonActionPerformed**.

> [!NOTE]
> O NetBeans não permitirá colar na linha de definição de método (```private void```) nem na chaveta de fecho desse método. Para copiar o código, copie as linhas entre a definição de método e a chaveta de fecho e cole-as acima do conteúdo do método.

```java
    private void thumbnailImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL thumbnailImageUrl;
        JSONObject jsonError[] = new JSONObject[1];
        
        // Clear out the previous image, response, and thumbnail, if any.
        thumbnailSourceImage.setIcon(new ImageIcon());
        thumbnailResponseTextArea.setText("");
        thumbnailImage.setIcon(new ImageIcon());

        // Display the image specified in the text box.
        try {
            thumbnailImageUrl = new URL(thumbnailImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(thumbnailImageUrl);
            scaleAndShowImage(bImage, thumbnailSourceImage);
        } catch(IOException e) {
            thumbnailResponseTextArea.setText("Error loading image to thumbnail: " + e.getMessage());
            return;
        }
        
        // Get the thumbnail for the image.
        BufferedImage thumbnail = getThumbnailImage(thumbnailImageUrl.toString(), jsonError);
        
        // A non-null value indicates error.
        if (jsonError[0] != null) {
            // Format and display the JSON error.
            thumbnailResponseTextArea.setText(jsonError[0].toString(2));
            return;
        }
        
        // Display the thumbnail.
        if (thumbnail != null) {
            scaleAndShowImage(thumbnail, thumbnailImage);
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

O método **getThumbnailImage** encapsula a chamada à API REST para analisar uma imagem. O método devolve uma **BufferedImage** que contém a miniatura ou **null** (nulo), se tiver ocorrido um erro. A mensagem de erro será devolvida no primeiro elemento da matriz de cadeia de carateres **jsonError**.

Copie e cole o seguinte método **getThumbnailImage** imediatamente abaixo do método **thumbnailImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to create a thumbnail for an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A BufferedImage containing the thumbnail, or null if a runtime error occurs. In the case
     * of an error, the error message will be returned in the first element of the jsonError string array.
     */
    private BufferedImage getThumbnailImage(String imageUrl, JSONObject[] jsonError) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseThumbnail;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity requestEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(requestEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200)
            {
                // Return the thumbnail.
                return ImageIO.read(entity.getContent());
            }
            else
            {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                jsonError[0] = new JSONObject(jsonString);
                return null;
            }
        }
        catch (Exception e)
        {
            String errorMessage = e.getMessage();
            System.out.println(errorMessage);
            jsonError[0] = new JSONObject(errorMessage);
            return null;
        }
    }
```

#### <a name="run-the-thumbnail-function"></a>Executar a função em miniatura

Prima **F6** para executar a aplicação. Coloque a sua chave de subscrição no campo **Subscription Key** (Chave de Subscrição) e verifique se está a utilizar a região correta na **Subscription Region** (Região de Subscrição). Clique no separador **Thumbnail** (Miniatura), introduza um URL para uma imagem e, em seguida, clique no botão **Generate Thumbnail** (Gerar Miniatura) para analisar uma imagem e ver o resultado.

### <a name="read-printed-text-ocr"></a>Ler texto impresso (OCR)

A funcionalidade Optical Character Recognition (Reconhecimento Ótico de Carateres – OCR) da Imagem Digitalizada analisa uma imagem de texto impresso. Quando a análise estiver concluída, a funcionalidade OCR devolve um objeto JSON que contém o texto e a localização do texto na imagem.

Para concluir a funcionalidade OCR da aplicação de tutorial, realize os seguintes passos:

#### <a name="add-the-event-handler-code-for-the-ocr-button"></a>Adicione o código do manipulador de eventos para o botão de OCR

O método **ocrImageButtonActionPerformed** do processador de eventos limpa o formulário, apresenta a imagem especificada no URL e, em seguida, chama o método **OcrImage** para analisar a imagem. Quando **OcrImage** é devolvido, o método apresenta o texto detetado como JSON formatado na área de texto **Response** (Resposta).

Copie e cole o código seguinte no método **ocrImageButtonActionPerformed**.

> [!NOTE]
> O NetBeans não permitirá colar na linha de definição de método (```private void```) nem na chaveta de fecho desse método. Para copiar o código, copie as linhas entre a definição de método e a chaveta de fecho e cole-as acima do conteúdo do método.

```java
    private void ocrImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL ocrImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        ocrImage.setIcon(new ImageIcon());
        ocrResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            ocrImageUrl = new URL(ocrImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(ocrImageUrl);
            scaleAndShowImage(bImage, ocrImage);
        } catch(IOException e) {
            ocrResponseTextArea.setText("Error loading OCR image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = OcrImage(ocrImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        ocrResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

O método **OcrImage** encapsula a chamada à API REST para analisar uma imagem. O método devolve um **JSONObject** dos dados JSON devolvidos da chamada ou **null** (nulo), se tiver ocorrido um erro.

Copie e cole o seguinte método **OcrImage** imediatamente abaixo do método **ocrImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject OcrImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseOcr;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("language", "unk");
            uriBuilder.setParameter("detectOrientation ", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-ocr-function"></a>Executar a função de OCR

Prima **F6** para executar a aplicação. Coloque a sua chave de subscrição no campo **Subscription Key** (Chave de Subscrição) e verifique se está a utilizar a região correta na **Subscription Region** (Região de Subscrição). Clique no separador **OCR**, introduza um URL para uma imagem de texto impresso e, em seguida, clique no botão **Read Image** (Ler Imagem) para analisar uma imagem e ver o resultado.

### <a name="read-handwritten-text-handwriting-recognition"></a>Ler texto manuscrito (reconhecimento de escrita manual)

A funcionalidade Handwriting Recognition (Reconhecimento de Escrita Manual) da Imagem Digitalizada analisa uma imagem de texto manuscrito. Quando a análise estiver concluída, a funcionalidade Handwriting Recognition (Reconhecimento de Escrita Manual) devolve um objeto JSON que contém o texto e a localização do texto na imagem.

Para concluir a funcionalidade Handwriting Recognition (Reconhecimento de Escrita Manual) da aplicação de tutorial, realize os seguintes passos:

#### <a name="add-the-event-handler-code-for-the-handwriting-button"></a>Adicione o código do manipulador de eventos para o botão de escrita manual

O método **handwritingImageButtonActionPerformed** do processador de eventos limpa o formulário, apresenta a imagem especificada no URL e, em seguida, chama o método **HandwritingImage** para analisar a imagem. Quando **HandwritingImage** é devolvido, o método apresenta o texto detetado como JSON formatado na área de texto **Response** (Resposta).

Copie e cole o código seguinte no método **handwritingImageButtonActionPerformed**.

> [!NOTE]
> O NetBeans não permitirá colar na linha de definição de método (```private void```) nem na chaveta de fecho desse método. Para copiar o código, copie as linhas entre a definição de método e a chaveta de fecho e cole-as acima do conteúdo do método.

```java
    private void handwritingImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL handwritingImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        handwritingImage.setIcon(new ImageIcon());
        handwritingResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            handwritingImageUrl = new URL(handwritingImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(handwritingImageUrl);
            scaleAndShowImage(bImage, handwritingImage);
        } catch(IOException e) {
            handwritingResponseTextArea.setText("Error loading Handwriting image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = HandwritingImage(handwritingImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        handwritingResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

O método **HandwritingImage** encapsula as duas chamadas à API REST, necessárias para analisar uma imagem. Como o reconhecimento de escrita manual é um processo demorado, é utilizado um processo em duas etapas. A primeira chamada submete a imagem para processamento e a segunda chamada obtém o texto detetado quando o processamento for concluído.

Depois de o texto ser obtido, o método **HandwritingImage** devolve um **JSONObject** que descreve o texto e as localizações do texto ou **null** (nulo), se tiver ocorrido um erro.

Copie e cole o seguinte método **HandwritingImage** imediatamente abaixo do método **handwritingImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read handwritten text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject HandwritingImage(String imageUrl) {
        try (CloseableHttpClient textClient = HttpClientBuilder.create().build();
             CloseableHttpClient resultClient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseHandwriting;
            URIBuilder uriBuilder = new URIBuilder(uriString);
            
            // Request parameters.
            uriBuilder.setParameter("handwriting", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response.
            HttpResponse textResponse = textClient.execute(request);
            
            // Check for success.
            if (textResponse.getStatusLine().getStatusCode() != 202) {
                // An error occurred. Return the JSON error message.
                HttpEntity entity = textResponse.getEntity();
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            }
            
            String operationLocation = null;

            // The 'Operation-Location' in the response contains the URI to retrieve the recognized text.
            Header[] responseHeaders = textResponse.getAllHeaders();
            for(Header header : responseHeaders) {
                if(header.getName().equals("Operation-Location"))
                {
                    // This string is the URI where you can get the text recognition operation result.
                    operationLocation = header.getValue();
                    break;
                }
            }
            
            // NOTE: The response may not be immediately available. Handwriting recognition is an
            // async operation that can take a variable amount of time depending on the length
            // of the text you want to recognize. You may need to wait or retry this operation.
            //
            // This example checks once per second for ten seconds.
            
            JSONObject responseObj = null;
            int i = 0;
            do {
                // Wait one second.
                Thread.sleep(1000);
                
                // Check to see if the operation completed.
                HttpGet resultRequest = new HttpGet(operationLocation);
                resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());
                HttpResponse resultResponse = resultClient.execute(resultRequest);
                HttpEntity responseEntity = resultResponse.getEntity();
                if (responseEntity != null)
                {
                    // Get the JSON response.
                    String jsonString = EntityUtils.toString(responseEntity);
                    responseObj = new JSONObject(jsonString);
                }
            }
            while (i < 10 && responseObj != null &&
                    !responseObj.getString("status").equalsIgnoreCase("Succeeded"));
            
            // If the operation completed, return the JSON object.
            if (responseObj != null) {
                return responseObj;
            } else {
                // Return null for timeout error.
                System.out.println("Timeout error.");
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-handwriting-function"></a>Executar a função de manuscrito

Para executar a aplicação, prima **F6**. Coloque a sua chave de subscrição no campo **Subscription Key** (Chave de Subscrição) e verifique se está a utilizar a região correta na **Subscription Region** (Região de Subscrição). Clique no separador **Read Handwritten Text** (Ler Texto Manuscrito), introduza um URL para uma imagem de texto manuscrito e, em seguida clique no botão **Read Image** (Ler Imagem) para analisar uma imagem e ver o resultado.

## <a name="next-steps"></a>Passos Seguintes

Neste guia, utilizada a API de REST de visão do computador com o Java para testar muitas das funcionalidades de análise de imagem disponíveis. Em seguida, consulte a documentação de referência para saber que mais sobre as APIs envolvidos.

- [API de REST de imagem digitalizada](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)
