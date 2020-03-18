---
title: 'Início Rápido: Biblioteca de cliente de Análise de Texto v3 para Java | Microsoft Docs'
description: Comece a utilizar a biblioteca de cliente de Análise de Texto v3 para Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: 11092b74c0256d298dece0f909d8d7dd241e7b13
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371338"
---
<a name="HOLTop"></a>

[Documentação de referência](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [Pacote](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.3) | [Exemplos](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) com a versão 8 ou superior
* Assim que tiver a sua subscrição do Azure, pode <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Criar um recurso de Análise de Texto"  target="_blank">criar um recurso de Análise de Texto <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto final. 
    * Necessitará da chave e do ponto final do recurso criado para ligar a sua aplicação à API de Análise de Texto. Fará isto mais adiante no início rápido.
    * Pode utilizar o escalão de preço gratuito para experimentar o serviço e atualizar mais tarde para um escalão pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="add-the-client-library"></a>Adicionar a biblioteca de cliente

Crie um projeto do Maven no IDE ou ambiente de desenvolvimento da sua preferência. Em seguida, adicione a seguinte dependência ao ficheiro *pom.xml* do projeto. Pode encontrar a sintaxe de implementação [para outras ferramentas de criação](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.3) online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.3</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Quer ver todo o ficheiro de código do início rápido de uma vez? Pode encontrá-lo [no GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java), que contém os exemplos de código deste início rápido. 

Crie um ficheiro Java com o nome `TextAnalyticsSamples.java`. Abra o ficheiro e adicione as seguintes instruções `import`:

```java
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

No ficheiro Java, adicione uma nova classe e adicione a chave e o ponto final do recurso do Azure, conforme mostrado abaixo.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Adicione o seguinte método principal à classe. Irá definir os métodos aqui chamados mais tarde.

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizePIIEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

## <a name="object-model"></a>Modelo de objeto

O cliente Análise de Texto é um objeto `TextAnalyticsClient` que autentica o Azure com a sua chave e fornece funções para aceitar texto como cadeias individuais ou como um lote. Pode enviar texto para a API sincronamente ou assincronamente. O objeto de resposta irá conter as informações de análise de cada documento que envia. 

## <a name="code-examples"></a>Exemplos de código

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de Sentimentos](#sentiment-analysis) 
* [Deteção de idioma](#language-detection)
* [Reconhecimento de entidades nomeadas](#named-entity-recognition-ner) 
* [Associação de entidades](#entity-linking)
* [Extração de expressões-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um método para instanciar o objeto `TextAnalyticsClient` com a chave e o ponto final do recurso de Análise de Texto.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .apiKey(new TextAnalyticsApiKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

No método `main()` do programa, chame o método de autenticação para instanciar o cliente.

## <a name="sentiment-analysis"></a>Análise de sentimentos

Crie uma nova função chamada `sentimentAnalysisExample()`, que assuma o cliente que criou anteriormente, e chame a respetiva função `analyzeSentiment()`. O objeto `AnalyzeSentimentResult` devolvido conterá `documentSentiment` e `sentenceSentiments` se tiver êxito ou um `errorMessage` se não tiver. 

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

Crie uma nova função chamada `detectLanguageExample()`, que assuma o cliente que criou anteriormente, e chame a respetiva função `detectLanguage()`. O objeto `DetectLanguageResult` devolvido conterá um idioma principal detetado, uma lista de outros idiomas detetados se tiver êxito ou `errorMessage` se não tiver.

> [!Tip]
> Em alguns casos, pode ser difícil desambiguar os idiomas com base na entrada. Pode utilizar o parâmetro `countryHint` para especificar um código de país de 2 letras. Por predefinição, a API utiliza "US" como countryHint predefinida. Para remover este comportamento, pode repor este parâmetro definindo este valor como uma cadeia `countryHint = ""` vazia. Para configurar uma predefinição diferente, defina a propriedade `TextAnalyticsClientOptions.DefaultCountryHint` e transmita-a durante a inicialização do cliente.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getScore());
}
```

### <a name="output"></a>Saída

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```
## <a name="named-entity-recognition-ner"></a>Reconhecimento de Entidades Nomeadas (NER)

> [!NOTE]
> Na versão `3.0-preview`:
> * o NER inclui métodos separados para detetar informações pessoais. 
> * A associação de entidades é um pedido separado do NER.

Crie uma nova função chamada `recognizeEntitiesExample()`, que assuma o cliente que criou anteriormente, e chame a respetiva função `recognizeEntities()`. O objeto `RecognizeEntitiesResult` devolvido conterá uma lista de `NamedEntity` se tiver êxito ou `errorMessage` se não tiver.

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
            entity.getSubCategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Saída

```console
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.92.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

## <a name="using-ner-to-recognize-personal-information"></a>Utilizar o NER para reconhecer informações pessoais

Crie uma nova função chamada `recognizePIIEntitiesExample()`, que assuma o cliente que criou anteriormente, e chame a respetiva função `recognizePiiEntities()`. O objeto `RecognizePiiEntitiesResult` devolvido conterá uma lista de `NamedEntity` se tiver êxito ou `errorMessage` se não tiver. 

```java
static void recognizePIIEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Insurance policy for SSN on file 123-12-1234 is here by approved.";

    for (PiiEntity entity : client.recognizePiiEntities(text)) {
        System.out.printf(
            "Recognized personal identifiable information entity: %s, entity category: %s, %nentity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Saída

```console
Recognized personal identifiable information entity: 123-12-1234, entity category: U.S. Social Security Number (SSN), 
entity sub-category: null, score: 0.85.
```

## <a name="entity-linking"></a>Associação de entidades

Crie uma nova função chamada `recognizeLinkedEntitiesExample()`, que assuma o cliente que criou anteriormente, e chame a respetiva função `recognizeLinkedEntities()`. O objeto `RecognizeLinkedEntitiesResult` devolvido conterá uma lista de `LinkedEntity` se tiver êxito ou `errorMessage` se não tiver. Uma vez que as entidades associadas são identificadas exclusivamente, as ocorrências da mesma entidade são agrupadas num objeto `LinkedEntity` como uma lista de objetos `LinkedEntityMatch`.

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
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getLinkedEntityMatches()) {
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
Text: Altair 8800, Score: 0.78
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.55
Text: Gates, Score: 0.55
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.53
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.47
Text: Microsoft, Score: 0.47
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.25
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.28
```
## <a name="key-phrase-extraction"></a>Extração de expressões-chave

Crie uma nova função chamada `extractKeyPhrasesExample()`, que assuma o cliente que criou anteriormente, e chame a respetiva função `extractKeyPhrases()`. O objeto `ExtractKeyPhraseResult` devolvido conterá uma lista de expressões-chave se tiver êxito ou `errorMessage` se não tiver.

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
