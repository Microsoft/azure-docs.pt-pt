---
title: 'Quickstart: Text Analytics v3 biblioteca de clientes para Java / Microsoft Docs'
description: Começa com a biblioteca de clientes v3 Text Analytics para a Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 10/07/2020
ms.custom: devx-track-java
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: b7e5ebb9ac4c71d71b19b10763ebbdf57d752d49
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980947"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

[Documentação de referência](/java/api/overview/azure/ai-textanalytics-readme-pre?view=azure-java-preview)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)  |  [Pacote](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.1)  |  [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples)

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

[Documentação de referência](/java/api/overview/azure/ai-textanalytics-readme?view=azure-java-stable)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics)  |  [Pacote](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0)  |  [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Este artigo descreve apenas a versão 3.x da API.

---

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [Kit de Desenvolvimento java](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) com versão 8 ou superior
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" Crie um recurso Text Analytics crie um recurso Text Analytics no portal "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final.  Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API de Análise de Texto. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="add-the-client-library"></a>Adicione a biblioteca do cliente

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

Crie um projeto Maven no seu IDE preferido ou ambiente de desenvolvimento. Em seguida, adicione a seguinte dependência ao ficheiro *pom.xml* do seu projeto. Pode encontrar a sintaxe de implementação [para outras ferramentas de construção](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.1) online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0-beta.1</version>
    </dependency>
</dependencies>
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Crie um projeto Maven no seu IDE preferido ou ambiente de desenvolvimento. Em seguida, adicione a seguinte dependência ao ficheiro *pom.xml* do seu projeto. Pode encontrar a sintaxe de implementação [para outras ferramentas de construção](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.0.0</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo [no GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java)que contém os exemplos de código neste arranque rápido. 

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Este artigo descreve apenas a versão 3.x da API.

---

Crie um ficheiro Java chamado `TextAnalyticsSamples.java` . Abra o ficheiro e adicione as seguintes `import` declarações:

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

No ficheiro java, adicione uma nova classe e adicione a chave e o ponto final do seu recurso Azure, conforme mostrado abaixo.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Adicione o seguinte método principal à classe. Definirá os métodos aqui chamados mais tarde.

# <a name="version-31-preview"></a>[Versão 3.1 (Pré-visualização)](#tab/version-3-1)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisWithOpinionMiningExample(client)
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    recognizePiiEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Este artigo descreve apenas a versão 3.x da API.

---


## <a name="object-model"></a>Modelo de objeto

O cliente Text Analytics é um `TextAnalyticsClient` objeto que autentica a Azure usando a sua chave, e fornece funções para aceitar texto como cordas simples ou como um lote. Pode enviar sms para a API de forma sincronizada ou assíncronea. O objeto de resposta conterá as informações de análise de cada documento que enviar. 

## <a name="code-examples"></a>Exemplos de código

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de Sentimentos](#sentiment-analysis) 
* [Mineração de opinião](#opinion-mining)
* [Deteção linguística](#language-detection)
* [Reconhecimento de entidade nomeada](#named-entity-recognition-ner)
* [Ligação de entidades](#entity-linking)
* [Extração de frase-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um método para instantaneaizar o `TextAnalyticsClient` objeto com a chave e ponto final para o seu recurso Text Analytics. Este exemplo é o mesmo para as versões 3.0 e 3.1 da API.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```


No método do seu `main()` programa, ligue para o método de autenticação para instantaneaizar o cliente.

## <a name="sentiment-analysis"></a>Análise de sentimentos

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

> [!NOTE]
> Na `3.1` versão:
> * A Análise de Sentimento inclui a análise de Mineração de Opinião que é uma bandeira opcional. 
> * A mineração de opinião contém aspeto e sentimento de nível de opinião. 

Crie uma nova função chamada `sentimentAnalysisExample()` que leva o cliente que criou anteriormente, e chama a sua `analyzeSentiment()` função. O objeto devolvido `AnalyzeSentimentResult` conterá `documentSentiment` `sentenceSentiments` e, se for bem sucedido, ou `errorMessage` se não. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>Saída

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

### <a name="opinion-mining"></a>Mineração de opinião

Para realizar a análise de sentimento com a mineração de opinião, crie uma nova função chamada `sentimentAnalysisWithOpinionMiningExample()` que leva o cliente que criou anteriormente, e ligue para a sua `analyzeSentiment()` função com o objeto de opção de definição `AnalyzeSentimentOptions` . O objeto devolvido `AnalyzeSentimentResult` conterá `documentSentiment` `sentenceSentiments` e, se for bem sucedido, ou `errorMessage` se não. 


```java
static void sentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    // The document that needs be analyzed.
    String document = "Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.";

    System.out.printf("Document = %s%n", document);

    AnalyzeSentimentOptions options = new AnalyzeSentimentOptions().setIncludeOpinionMining(true);
    final DocumentSentiment documentSentiment = client.analyzeSentiment(document, "en", options);
    SentimentConfidenceScores scores = documentSentiment.getConfidenceScores();
    System.out.printf(
            "Recognized document sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
            documentSentiment.getSentiment(), scores.getPositive(), scores.getNeutral(), scores.getNegative());

    documentSentiment.getSentences().forEach(sentenceSentiment -> {
        SentimentConfidenceScores sentenceScores = sentenceSentiment.getConfidenceScores();
        System.out.printf("\tSentence sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
                sentenceSentiment.getSentiment(), sentenceScores.getPositive(), sentenceScores.getNeutral(), sentenceScores.getNegative());
        sentenceSentiment.getMinedOpinions().forEach(minedOpinions -> {
            AspectSentiment aspectSentiment = minedOpinions.getAspect();
            System.out.printf("\t\tAspect sentiment: %s, aspect text: %s%n", aspectSentiment.getSentiment(),
                    aspectSentiment.getText());
            SentimentConfidenceScores aspectScores = aspectSentiment.getConfidenceScores();
            System.out.printf("\t\tAspect positive score: %f, negative score: %f.%n",
                    aspectScores.getPositive(), aspectScores.getNegative());
            for (OpinionSentiment opinionSentiment : minedOpinions.getOpinions()) {
                System.out.printf("\t\t\t'%s' opinion sentiment because of \"%s\". Is the opinion negated: %s.%n",
                        opinionSentiment.getSentiment(), opinionSentiment.getText(), opinionSentiment.isNegated());
                SentimentConfidenceScores opinionScores = opinionSentiment.getConfidenceScores();
                System.out.printf("\t\t\tOpinion positive score: %f, negative score: %f.%n",
                        opinionScores.getPositive(), opinionScores.getNegative());
            }
        });
    });
}
```

### <a name="output"></a>Saída

```console
Document = Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.
Recognized document sentiment: negative, positive score: 0.010000, neutral score: 0.140000, negative score: 0.850000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Aspect sentiment: negative, aspect text: atmosphere
        Aspect positive score: 0.010000, negative score: 0.990000.
            'negative' opinion sentiment because of "bad". Is the opinion negated: false.
            Opinion positive score: 0.010000, negative score: 0.990000.
    Sentence sentiment: negative, positive score: 0.020000, neutral score: 0.440000, negative score: 0.540000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Aspect sentiment: negative, aspect text: Staff
        Aspect positive score: 0.000000, negative score: 1.000000.
            'negative' opinion sentiment because of "friendly". Is the opinion negated: true.
            Opinion positive score: 0.000000, negative score: 1.000000.
            'negative' opinion sentiment because of "helpful". Is the opinion negated: true.
            Opinion positive score: 0.000000, negative score: 1.000000.

Process finished with exit code 0
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Crie uma nova função chamada `sentimentAnalysisExample()` que leva o cliente que criou anteriormente, e chama a sua `analyzeSentiment()` função. O objeto devolvido `AnalyzeSentimentResult` conterá `documentSentiment` `sentenceSentiments` e, se for bem sucedido, ou `errorMessage` se não. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>Saída

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Este artigo descreve apenas a versão 3.x da API.

---

## <a name="language-detection"></a>Deteção de idioma

Crie uma nova função chamada `detectLanguageExample()` que leva o cliente que criou anteriormente, e chama a sua `detectLanguage()` função. O objeto devolvido `DetectLanguageResult` conterá uma língua primária detetada, uma lista de outras línguas detetadas se for bem sucedida, ou `errorMessage` se não. Este exemplo é o mesmo para as versões 3.0 e 3.1 da API.

> [!Tip]
> Em alguns casos, pode ser difícil desambiguar línguas com base na entrada. Pode utilizar o `countryHint` parâmetro para especificar um código de 2 letras do país. Por predefinição, a API está a usar os "EUA" como país padrãoHint, para remover este comportamento pode redefinir este parâmetro definindo este valor para cadeia vazia `countryHint = ""` . Para definir um padrão diferente, desaperte a `TextAnalyticsClientOptions.DefaultCountryHint` propriedade e passe-a durante a inicialização do cliente.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getConfidenceScore());
}
```

### <a name="output"></a>Saída

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```

## <a name="named-entity-recognition-ner"></a>Reconhecimento de entidade nomeada (NER)

# <a name="version-31-preview"></a>[Visualização da versão 3.1](#tab/version-3-1)

> [!NOTE]
> Na `3.1` versão:
> * O NER inclui métodos separados para a deteção de informações pessoais. 
> * A ligação da entidade é um pedido separado do que o NER.

Crie uma nova função chamada `recognizeEntitiesExample()` que leva o cliente que criou anteriormente, e chama a sua `recognizeEntities()` função. O objeto devolvido `CategorizedEntityCollection` conterá uma lista de `CategorizedEntity` se for bem sucedido, ou se `errorMessage` não.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s, offset: %s, length: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore(),
            entity.getOffset(),
            entity.getLength());
    }
}
```

### <a name="output"></a>Saída

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61, offset: 8, length: 4.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82, offset: 16, length: 7.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8, offset: 24, length: 9.
```

### <a name="entity-linking"></a>Ligação de entidades

Crie uma nova função chamada `recognizeLinkedEntitiesExample()` que leva o cliente que criou anteriormente, e chama a sua `recognizeLinkedEntities()` função. O objeto devolvido `LinkedEntityCollection` conterá uma lista de `LinkedEntity` se for bem sucedido, ou se `errorMessage` não. Uma vez que as entidades ligadas são identificadas de forma única, as ocorrências da mesma entidade são agrupadas sob um `LinkedEntity` objeto como uma lista de `LinkedEntityMatch` objetos.


```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f, Offset: %s, Length: %s%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore(),
            linkedEntityMatch.getOffset(),
            linkedEntityMatch.getLength());
        }
    }
}
```

### <a name="output"></a>Saída

```console
Linked Entities:
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55, Offset: 9, Length: 0
Text: Microsoft, Score: 0.55, Offset: 9, Length: 150
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63, Offset: 10, Length: 25
Text: Gates, Score: 0.63, Offset: 5, Length: 161
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60, Offset: 10, Length: 40
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32, Offset: 7, Length: 54
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33, Offset: 5, Length: 89
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88, Offset: 11, Length: 116
```


### <a name="personally-identifiable-information-recognition"></a>Reconhecimento de Informação Pessoalmente Identificável

Crie uma nova função chamada `recognizePiiEntitiesExample()` que leva o cliente que criou anteriormente, e chama a sua `recognizePiiEntities()` função. O objeto devolvido `PiiEntityCollection` conterá uma lista de `PiiEntity` se for bem sucedido, ou se `errorMessage` não. Conterá também o texto redigido, que consiste no texto de entrada com todas as entidades identificáveis substituídas por `*****` .

```java
static void recognizePiiEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String document = "My SSN is 859-98-0987";
    PiiEntityCollection piiEntityCollection = client.recognizePiiEntities(document);
    System.out.printf("Redacted Text: %s%n", piiEntityCollection.getRedactedText());
    piiEntityCollection.forEach(entity -> System.out.printf(
        "Recognized Personally Identifiable Information entity: %s, entity category: %s, entity subcategory: %s,"
            + " confidence score: %f.%n",
        entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
}
```

### <a name="output"></a>Saída

```console
Redacted Text: My SSN is ***********
Recognized Personally Identifiable Information entity: 859-98-0987, entity category: U.S. Social Security Number (SSN), entity subcategory: null, confidence score: 0.650000.
```

# <a name="version-30"></a>[Versão 3.0](#tab/version-3)

> [!NOTE]
> Na `3.0` versão:
> * O NER inclui métodos separados para a deteção de informações pessoais. 
> * A ligação da entidade é um pedido separado do que o NER.

Crie uma nova função chamada `recognizeEntitiesExample()` que leva o cliente que criou anteriormente, e chama a sua `recognizeEntities()` função. O objeto devolvido `CategorizedEntityCollection` conterá uma lista de `CategorizedEntity` se for bem sucedido, ou se `errorMessage` não.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Saída

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

### <a name="entity-linking"></a>Ligação de entidades

Crie uma nova função chamada `recognizeLinkedEntitiesExample()` que leva o cliente que criou anteriormente, e chama a sua `recognizeLinkedEntities()` função. O objeto devolvido `LinkedEntityCollection` conterá uma lista de `LinkedEntity` se for bem sucedido, ou se `errorMessage` não. Uma vez que as entidades ligadas são identificadas de forma única, as ocorrências da mesma entidade são agrupadas sob um `LinkedEntity` objeto como uma lista de `LinkedEntityMatch` objetos.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore());
        }
    }
}
```

### <a name="output"></a>Saída

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63
Text: Gates, Score: 0.63
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55
Text: Microsoft, Score: 0.55
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33
```

# <a name="version-21"></a>[Versão 2.1](#tab/version-2)

Este artigo descreve apenas a versão 3.x da API.

---

## <a name="key-phrase-extraction"></a>Extração de expressões-chave

Crie uma nova função chamada `extractKeyPhrasesExample()` que leva o cliente que criou anteriormente, e chama a sua `extractKeyPhrases()` função. O objeto devolvido `ExtractKeyPhraseResult` conterá uma lista de frases-chave se for bem sucedido, ou `errorMessage` se não for. Este exemplo é o mesmo para as versões 3.0 e 3.1 da API.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>Saída

```console
Recognized phrases: 
cat
veterinarian
```
