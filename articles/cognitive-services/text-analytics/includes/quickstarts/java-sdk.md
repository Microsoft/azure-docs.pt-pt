---
title: 'Quickstart: Text Analytics v3 biblioteca de clientes para Java / Microsoft Docs'
description: Começa com a biblioteca de clientes v3 Text Analytics para a Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: bd070300427716634d786e685cfe1cf8e45a246c
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140751"
---
<a name="HOLTop"></a>

[Documentação de referência](https://aka.ms/azsdk-java-textanalytics-ref-docs)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics)  |  [Pacote](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.4)  |  [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/)
* [Kit de Desenvolvimento java](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) com versão 8 ou superior
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" Crie um recurso Text Analytics crie um recurso Text Analytics no portal "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final.  Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API de Análise de Texto. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="add-the-client-library"></a>Adicione a biblioteca do cliente

Crie um projeto Maven no seu IDE preferido ou ambiente de desenvolvimento. Em seguida, adicione a seguinte dependência ao ficheiro *pom.xml* do seu projeto. Pode encontrar a sintaxe de implementação [para outras ferramentas de construção](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.4) online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.5</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Quer ver todo o ficheiro de código de arranque rápido de uma vez? Pode encontrá-lo [no GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java)que contém os exemplos de código neste arranque rápido. 

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

## <a name="object-model"></a>Modelo de objeto

O cliente Text Analytics é um `TextAnalyticsClient` objeto que autentica a Azure usando a sua chave, e fornece funções para aceitar texto como cordas simples ou como um lote. Pode enviar sms para a API de forma sincronizada ou assíncronea. O objeto de resposta conterá as informações de análise de cada documento que enviar. 

## <a name="code-examples"></a>Exemplos de código

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de Sentimentos](#sentiment-analysis) 
* [Deteção linguística](#language-detection)
* [Reconhecimento de entidade nomeada](#named-entity-recognition-ner) 
* [Ligação de entidades](#entity-linking)
* [Extração de frase-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um método para instantaneaizar o `TextAnalyticsClient` objeto com a chave e ponto final para o seu recurso Text Analytics.

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
```

### <a name="output"></a>Saída

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

## <a name="language-detection"></a>Deteção de idioma

Crie uma nova função chamada `detectLanguageExample()` que leva o cliente que criou anteriormente, e chama a sua `detectLanguage()` função. O objeto devolvido `DetectLanguageResult` conterá uma língua primária detetada, uma lista de outras línguas detetadas se for bem sucedida, ou `errorMessage` se não.

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

> [!NOTE]
> Na `3.0` versão:
> * O NER inclui métodos separados para a deteção de informações pessoais. 
> * A ligação da entidade é um pedido separado do que o NER.

Crie uma nova função chamada `recognizeEntitiesExample()` que leva o cliente que criou anteriormente, e chama a sua `recognizeEntities()` função. O objeto devolvido `RecognizeEntitiesResult` conterá uma lista de `NamedEntity` se for bem sucedido, ou se `errorMessage` não.

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

## <a name="entity-linking"></a>Ligação de entidades

Crie uma nova função chamada `recognizeLinkedEntitiesExample()` que leva o cliente que criou anteriormente, e chama a sua `recognizeLinkedEntities()` função. O objeto devolvido `RecognizeLinkedEntitiesResult` conterá uma lista de `LinkedEntity` se for bem sucedido, ou se `errorMessage` não. Uma vez que as entidades ligadas são identificadas de forma única, as ocorrências da mesma entidade são agrupadas sob um `LinkedEntity` objeto como uma lista de `LinkedEntityMatch` objetos.

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
## <a name="key-phrase-extraction"></a>Extração de expressões-chave

Crie uma nova função chamada `extractKeyPhrasesExample()` que leva o cliente que criou anteriormente, e chama a sua `extractKeyPhrases()` função. O objeto devolvido `ExtractKeyPhraseResult` conterá uma lista de frases-chave se for bem sucedido, ou `errorMessage` se não for.

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
