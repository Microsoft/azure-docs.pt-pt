---
title: 'Quickstart: Text Analytics v3 biblioteca de clientes para Java Microsoft Docs'
description: Começa com a biblioteca de clientes v3 Text Analytics para java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/26/2020
ms.author: aahi
ms.reviewer: tasharm, assafi
ms.openlocfilehash: 79b4063d6b65d6861dd7864c4225e91f4ea5bc6d
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78155476"
---
<a name="HOLTop"></a>

[Documentação de referência](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [Biblioteca código de | ](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) Pacote [(Maven)](https://oss.sonatype.org/#nexus-search;quick~com.azure) | [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* [Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) de Desenvolvimento Java (JDK) com versão 8 ou superior
* Assim que tiver a sua assinatura Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Criar <span class="docon docon-navigate-external x-hidden-focus"></span> um recurso De Análise de Texto"  target="_blank">criar um recurso De SMAnalytics</a> no portal Azure para obter a sua chave e ponto final. 
    * Necessitará da chave e do ponto final do recurso que cria para ligar a sua aplicação à API textanalytics. Vais fazer isto mais tarde, no início.
    * Você pode usar o nível de preços gratuitos para experimentar o serviço, e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-maven-project"></a>Criar um novo projeto do Maven

Adicione a seguinte dependência de análise de texto ao seu projeto. Esta versão da dependência utiliza versão `3.0-preview` da API text analytics. 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>
</dependencies>
```

Crie um novo ficheiro java no seguinte diretório: `\src\main\java`.

Abra o ficheiro java e adicione as seguintes `import` declarações:

```java
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

No ficheiro java, adicione uma nova classe e adicione a chave e o ponto final do seu recurso azul, como mostrado abaixo.

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

O cliente Text Analytics é um objeto `TextAnalyticsClient` que autentica o Azure usando a sua chave, e fornece funções para aceitar texto como cordas únicas ou como um lote. Pode enviar sms para a API sincronicamente, ou assincronicamente. O objeto de resposta conterá as informações de análise de cada documento que enviar. 

## <a name="code-examples"></a>Exemplos de código

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de Sentimentos](#sentiment-analysis) 
* [Deteção de linguagem](#language-detection)
* [Reconhecimento de Entidade Nomeada](#named-entity-recognition-ner) 
* [Ligação de entidades](#entity-linking)
* [Extração de frase-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um método para instantaneamente o objeto `TextAnalyticsClient` com o seu `KEY` e `ENDPOINT` criado acima.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .apiKey(new TextAnalyticsApiKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

No método `main()` do seu programa, ligue para o método de autenticação para instantanear o cliente.

## <a name="sentiment-analysis"></a>Análise de sentimentos

Crie uma nova função chamada `sentimentAnalysisExample()` que leve o cliente que criou anteriormente, e chame a sua função `analyzeSentiment()`. O objeto `AnalyzeSentimentResult` devolvido conterá `documentSentiment` e `sentenceSentiments` se for bem sucedido, ou um `errorMessage` se não for. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
        System.out.printf(
            "Recognized document sentiment: %s, positive score: %.2f, neutral score: %.2f, negative score: %.2f.%n",
            documentSentiment.getSentiment(),
            documentSentiment.getSentimentScores().getPositive(),
            documentSentiment.getSentimentScores().getNeutral(),
            documentSentiment.getSentimentScores().getNegative());

        for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
            System.out.printf(
                "Recognized sentence sentiment: %s, positive score: %.2f, neutral score: %.2f, negative score: %.2f.%n",
                sentenceSentiment.getSentiment(),
                sentenceSentiment.getSentimentScores().getPositive(),
                sentenceSentiment.getSentimentScores().getNeutral(),
                sentenceSentiment.getSentimentScores().getNegative());
        }
}
```

### <a name="output"></a>Saída

```console
Recognized document sentiment: positive, Positive Score: 1.00, Neutral Score: 0.00, Negative Score: 0.00.
Recognized sentence sentiment: positive, positive score: 1.00, neutral score: 0.00, negative score: 0.00.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```
## <a name="language-detection"></a>Deteção de idioma

Crie uma nova função chamada `detectLanguageExample()` que leve o cliente que criou anteriormente, e chame a sua função `detectLanguage()`. O objeto `DetectLanguageResult` devolvido conterá uma língua primária detetada, uma lista de outras línguas detetadas se for bem sucedida, ou uma `errorMessage` se não for.

> [!Tip]
> Em alguns casos, pode ser difícil desambiguar línguas com base na entrada. Pode utilizar o parâmetro `countryHint` para especificar um código de país de 2 letras. Por padrão, a API está a usar os "EUA" como país padrãoHint, para remover este comportamento pode redefinir este parâmetro definindo este valor para `countryHint = ""`de cadeias vazias . Para definir um padrão diferente, detete a propriedade `TextAnalyticsClientOptions.DefaultCountryHint` e passe-a durante a inicialização do cliente.

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
## <a name="named-entity-recognition-ner"></a>Reconhecimento de Entidade Seleção (NER)

> [!NOTE]
> Na versão `3.0-preview`:
> * O NER inclui métodos separados para detetar informações pessoais. 
> * A ligação de entidades é um pedido separado do NER.

Crie uma nova função chamada `recognizeEntitiesExample()` que leve o cliente que criou anteriormente, e chame a sua função `recognizeEntities()`. O objeto `RecognizeEntitiesResult` devolvido conterá uma lista de `NamedEntity` se for bem sucedido, ou um `errorMessage` se não for.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, offset: %s, length: %s, score: %.2f.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory() == null || entity.getSubCategory().isEmpty() ? "N/A" : entity.getSubCategory(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>Saída

```console
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, offset: 26, length: 7, score: 0.92.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, offset: 34, length: 9, score: 0.80.
```

## <a name="using-ner-to-recognize-personal-information"></a>Usar ner para reconhecer informações pessoais

Crie uma nova função chamada `recognizePIIEntitiesExample()` que leve o cliente que criou anteriormente, e chame a sua função `recognizePiiEntities()`. O objeto `RecognizePiiEntitiesResult` devolvido conterá uma lista de `NamedEntity` se for bem sucedido, ou um `errorMessage` se não for. 

```java
static void recognizePIIEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Insurance policy for SSN on file 123-12-1234 is here by approved.";

    for (PiiEntity entity : client.recognizePiiEntities(text)) {
        System.out.printf(
            "Recognized personal identifiable information entity: %s, entity category: %s, entity sub-category: %s, offset: %s, length: %s, score: %.2f.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory() == null || entity.getSubCategory().isEmpty() ? "N/A" : entity.getSubCategory(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>Saída

```console
Recognized personal identifiable information entity: 123-12-1234, entity category: U.S. Social Security Number (SSN), entity sub-category: N/A, offset: 33, length: 11, score: 0.85.
```

## <a name="entity-linking"></a>Ligação de entidades

Crie uma nova função chamada `recognizeLinkedEntitiesExample()` que leve o cliente que criou anteriormente, e chame a sua função `recognizeLinkedEntities()`. O objeto `RecognizeLinkedEntitiesResult` devolvido conterá uma lista de `LinkedEntity` se for bem sucedido, ou um `errorMessage` se não for. Uma vez que as entidades ligadas são identificadas de forma única, as ocorrências da mesma entidade são agrunadas sob um objeto `LinkedEntity` como uma lista de objetos `LinkedEntityMatch`.

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
                linkedEntity.getId(),
                linkedEntity.getUrl(),
                linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getLinkedEntityMatches()) {
            System.out.printf("Text: %s, Offset: %s, Length: %s, Score: %.2f.%n",
                    linkedEntityMatch.getText(),
                    linkedEntityMatch.getOffset(),
                    linkedEntityMatch.getLength(),
                    linkedEntityMatch.getScore());
        }
    }
}
```

### <a name="output"></a>Saída

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Offset: 11, Length: 116, Score: 0.78.
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Offset: 10, Length: 25, Score: 0.55.
Text: Gates, Offset: 5, Length: 161, Score: 0.55.
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Offset: 10, Length: 40, Score: 0.53.
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Offset: 9, Length: 0, Score: 0.47.
Text: Microsoft, Offset: 9, Length: 150, Score: 0.47.
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Offset: 7, Length: 54, Score: 0.25.
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Offset: 5, Length: 89, Score: 0.28.
```
## <a name="key-phrase-extraction"></a>Extração de expressões-chave

Crie uma nova função chamada `extractKeyPhrasesExample()` que leve o cliente que criou anteriormente, e chame a sua função `extractKeyPhrases()`. O `ExtractKeyPhraseResult` objeto devolvido conterá uma lista de frases-chave se for bem sucedida, ou uma `errorMessage` se não for.

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
