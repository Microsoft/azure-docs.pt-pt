---
title: 'Início rápido: biblioteca de cliente do Análise de Texto V3 para Java | Microsoft Docs'
description: Introdução à biblioteca de cliente do Análise de Texto V3 para Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2020
ms.author: aahi
ms.reviewer: tasharm, assafi
ms.openlocfilehash: a7882f1337e990f828bea15cdc2b9ad72de44f18
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281191"
---
<a name="HOLTop"></a>

[Documentação de referência](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [exemplos](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics) do Maven ( [código-fonte](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [do pacote) | de](https://oss.sonatype.org/#nexus-search;quick~com.azure) biblioteca

> [!NOTE]
> * Este guia de início rápido usa a versão `3.0-preview` da biblioteca de cliente do Análise de Texto, que inclui uma visualização pública para [análise de sentimento](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) aprimorado e [reconhecimento de entidade nomeada (Ner)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * O código neste artigo usa métodos síncronos e o armazenamento de credenciais não protegidas por motivos de simplicidade. Para cenários de produção, é recomendável usar os métodos assíncronos em lote para desempenho e escalabilidade. Por exemplo, chamar `SentimentBatchAsync()` em vez de `Sentiment()`.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) com a versão 8 ou superior

## <a name="setting-up"></a>Configurando

### <a name="create-a-text-analytics-azure-resource"></a>Criar um Análise de Texto recurso do Azure

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-maven-project"></a>Criar um novo projeto Maven

Adicione a seguinte dependência de análise de texto ao seu projeto. Esta versão da dependência usa a versão `3.0-preview` do API de Análise de Texto. 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.1</version>
    </dependency>
</dependencies>
```

Crie um novo arquivo Java no seguinte diretório: `\src\main\java`.

Abra o arquivo Java e adicione as seguintes instruções de `import`:

```java
import com.azure.ai.textanalytics.models.AnalyzeSentimentResult;
import com.azure.ai.textanalytics.models.DetectLanguageResult;
import com.azure.ai.textanalytics.models.DetectedLanguage;
import com.azure.ai.textanalytics.models.ExtractKeyPhraseResult;
import com.azure.ai.textanalytics.models.LinkedEntity;
import com.azure.ai.textanalytics.models.LinkedEntityMatch;
import com.azure.ai.textanalytics.models.NamedEntity;
import com.azure.ai.textanalytics.models.RecognizeEntitiesResult;
import com.azure.ai.textanalytics.models.RecognizeLinkedEntitiesResult;
import com.azure.ai.textanalytics.models.RecognizePiiEntitiesResult;
import com.azure.ai.textanalytics.models.TextSentiment;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
import java.util.List;
```

No arquivo Java, adicione uma nova classe e adicione a chave e o ponto de extremidade do recurso do Azure, conforme mostrado abaixo.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```java
public class TextAnalyticsSample {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Adicione o método Main a seguir à classe. Você irá definir os métodos chamados aqui mais tarde.

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

O cliente Análise de Texto é um objeto `TextAnalyticsClient` que se autentica no Azure usando sua chave e fornece funções para aceitar texto como cadeias de caracteres únicas ou como um lote. Você pode enviar texto para a API de forma síncrona ou assíncrona. O objeto de resposta conterá as informações de análise para cada documento que você enviar. 

## <a name="code-examples"></a>Exemplos de código

* [Autenticar o cliente](#authenticate-the-client)
* [Análise de sentimento](#sentiment-analysis) (visualização pública)
* [Detecção de idioma](#language-detection)
* [Reconhecimento de entidade nomeada](#named-entity-recognition-public-preview) (visualização pública)
* [Reconhecimento de entidade nomeada – informações pessoais](#named-entity-recognition---personal-information-public-preview) (visualização pública)
* [Vinculação de entidade](#entity-linking)
* [Extração de frases-chave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um método para instanciar o objeto de `TextAnalyticsClient` com o `KEY` e `ENDPOINT` criados acima.

```java
static TextAnalyticsClient authenticateClient(String subscriptionKey, String endpoint) {
    return new TextAnalyticsClientBuilder()
    .subscriptionKey(subscriptionKey)
    .endpoint(endpoint)
    .buildClient();
}
```

No método de `main()` do seu programa, chame o método de autenticação para instanciar o cliente.

## <a name="sentiment-analysis"></a>Análise de sentimentos

> [!NOTE]
> O código abaixo é para a análise de sentimentos v3, que está em visualização pública.

Crie uma nova função chamada `sentimentAnalysisExample()` que usa o cliente que você criou anteriormente e chame sua função `analyzeSentiment()`. O objeto `AnalyzeSentimentResult` retornado conterá `documentSentiment` e `sentenceSentiments` se for bem-sucedido ou um `errorMessage` se não for. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    AnalyzeSentimentResult sentimentResult = client.analyzeSentiment(text);
    TextSentiment documentSentiment = sentimentResult.getDocumentSentiment();
    System.out.printf(
        "Recognized TextSentiment: %s, Positive Score: %.2f, Neutral Score: %.2f, Negative Score: %.2f.%n",
        documentSentiment.getTextSentimentClass(),
        documentSentiment.getPositiveScore(),
        documentSentiment.getNeutralScore(),
        documentSentiment.getNegativeScore());

    List<TextSentiment> sentiments = sentimentResult.getSentenceSentiments();
    for (TextSentiment textSentiment : sentiments) {
        System.out.printf(
            "Recognized Sentence TextSentiment: %s, Positive Score: %.2f, Neutral Score: %.2f, Negative Score: %.2f.%n",
            textSentiment.getTextSentimentClass(),
            textSentiment.getPositiveScore(),
            textSentiment.getNeutralScore(),
            textSentiment.getNegativeScore());
    }
}
```

### <a name="output"></a>Saída

```console
Recognized TextSentiment: positive, Positive Score: 1.00, Neutral Score: 0.00, Negative Score: 0.00.
Recognized Sentence TextSentiment: positive, Positive Score: 1.00, Neutral Score: 0.00, Negative Score: 0.00.
Recognized Sentence TextSentiment: neutral, Positive Score: 0.21, Neutral Score: 0.77, Negative Score: 0.02.
```
## <a name="language-detection"></a>Deteção de idioma

Crie uma nova função chamada `detectLanguageExample()` que usa o cliente que você criou anteriormente e chame sua função `detectLanguage()`. O objeto `DetectLanguageResult` retornado conterá um idioma primário detectado, uma lista de outros idiomas detectados se for bem-sucedida ou um `errorMessage` se não for.

> [!Tip]
> Em alguns casos, pode ser difícil eliminar a ambiguidade de idiomas com base na entrada. Você pode usar o parâmetro `countryHint` para especificar um código de país de duas letras. Por padrão, a API está usando o "US" como o countryHint padrão, para remover esse comportamento, você pode redefinir esse parâmetro definindo esse valor como cadeia de caracteres vazia `countryHint = ""`. Para definir um padrão diferente, defina a propriedade `TextAnalyticsClientOptions.DefaultCountryHint` e passe-a durante a inicialização do cliente.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectLanguageResult detectLanguageResult = client.detectLanguage(text, "US");
    DetectedLanguage detectedDocumentLanguage = detectLanguageResult.getPrimaryLanguage();
    System.out.printf("Language: %s, ISO 6391 Name: %s, Score: %s.%n",
        detectedDocumentLanguage.getName(),
        detectedDocumentLanguage.getIso6391Name(),
        detectedDocumentLanguage.getScore());
}
```

### <a name="output"></a>Saída

```console
Language: French, ISO 6391 Name: fr, Score: 1.0.
```
## <a name="named-entity-recognition-public-preview"></a>Reconhecimento de entidade nomeada (visualização pública)

> [!NOTE]
> O código abaixo é para o reconhecimento de entidade nomeada v3, que está em visualização pública.

Crie uma nova função chamada `recognizeEntitiesExample()` que usa o cliente que você criou anteriormente e chame sua função `recognizeEntities()`. O objeto `RecognizeEntitiesResult` retornado conterá uma lista de `NamedEntity` se for bem-sucedido ou um `errorMessage` se não for.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analysed.
    String text = "I had a wonderful trip to Seattle last week.";
    
    RecognizeEntitiesResult recognizeEntitiesResult = client.recognizeEntities(text);

    for (NamedEntity entity : recognizeEntitiesResult.getNamedEntities()) {
        System.out.printf(
            "Recognized NamedEntity Text: %s, Type: %s, Subtype: %s, Offset: %s, Length: %s, Score: %.3f.%n",
            entity.getText(),
            entity.getType(),
            entity.getSubtype() == null || entity.getSubtype().isEmpty() ? "N/A" : entity.getSubtype(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>Saída

```console
Recognized NamedEntity Text: Seattle, Type: Location, Subtype: N/A, Offset: 26, Length: 7, Score: 0.806.
Recognized NamedEntity Text: last week, Type: DateTime, Subtype: DateRange, Offset: 34, Length: 9, Score: 0.800.
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>Reconhecimento de entidade nomeada – informações pessoais (visualização pública)

> [!NOTE]
> O código abaixo é para detectar informações pessoais usando o reconhecimento de entidade nomeada v3, que está em visualização pública.

Crie uma nova função chamada `recognizePIIEntitiesExample()` que usa o cliente que você criou anteriormente e chame sua função `recognizePiiEntities()`. O objeto `RecognizePiiEntitiesResult` retornado conterá uma lista de `NamedEntity` se for bem-sucedido ou um `errorMessage` se não for. 

```java
static void recognizePIIEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analysed.
    String text = "Insurance policy for SSN on file 123-12-1234 is here by approved.";
    
    RecognizePiiEntitiesResult recognizePIIEntitiesResult = client.recognizePiiEntities(text);

    for (NamedEntity entity : recognizePIIEntitiesResult.getNamedEntities()) {
        System.out.printf(
            "Personally Identifiable Information Entities Text: %s, Type: %s, Subtype: %s, Offset: %s, Length: %s, Score: %s.%n",
            entity.getText(),
            entity.getType(),
            entity.getSubtype() == null || entity.getSubtype().isEmpty() ? "N/A" : entity.getSubtype(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>Saída

```console
Personally Identifiable Information Entities
Text: 123-12-1234, Type: U.S. Social Security Number (SSN), Subtype: N/A, Offset: 33, Length: 11, Score: 0.85.
```

## <a name="entity-linking"></a>Vinculação de entidade

Crie uma nova função chamada `recognizeLinkedEntitiesExample()` que usa o cliente que você criou anteriormente e chame sua função `recognizeLinkedEntities()`. O objeto `RecognizeLinkedEntitiesResult` retornado conterá uma lista de `LinkedEntity` se for bem-sucedido ou um `errorMessage` se não for. Como as entidades vinculadas são identificadas exclusivamente, as ocorrências da mesma entidade são agrupadas em um objeto `LinkedEntity` como uma lista de objetos `LinkedEntityMatch`.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analysed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
            "to develop and sell BASIC interpreters for the Altair 8800. " +
            "During his career at Microsoft, Gates held the positions of chairman, " +
            "chief executive officer, president and chief software architect, " +
            "while also being the largest individual shareholder until May 2014.";
    
    RecognizeLinkedEntitiesResult recognizeLinkedEntitiesResult = client.recognizeLinkedEntities(text);

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : recognizeLinkedEntitiesResult.getLinkedEntities()) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("tMatches:%n");
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
tMatches:
Text: Altair 8800, Offset: 11, Length: 116, Score: 0.65.
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
tMatches:
Text: Bill Gates, Offset: 10, Length: 25, Score: 0.24.
Text: Gates, Offset: 5, Length: 161, Score: 0.24.
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
tMatches:
Text: Paul Allen, Offset: 10, Length: 40, Score: 0.17.
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
tMatches:
Text: Microsoft, Offset: 9, Length: 0, Score: 0.20.
Text: Microsoft, Offset: 9, Length: 150, Score: 0.20.
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
tMatches:
Text: April 4, Offset: 7, Length: 54, Score: 0.14.
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
tMatches:
Text: BASIC, Offset: 5, Length: 89, Score: 0.05.
```
## <a name="key-phrase-extraction"></a>Extração de expressões chave

Crie uma nova função chamada `extractKeyPhrasesExample()` que usa o cliente que você criou anteriormente e chame sua função `extractKeyPhrases()`. O objeto `ExtractKeyPhraseResult` retornado conterá uma lista de frases-chave, se for bem-sucedido, ou um `errorMessage` se não for.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";
    
    ExtractKeyPhraseResult keyPhraseResult = client.extractKeyPhrases(text);

    for (String keyPhrase : keyPhraseResult.getKeyPhrases()) {
        System.out.printf("Recognized Phrases: %s.%n", keyPhrase);
    }
}
```

### <a name="output"></a>Saída

```console
Recognized Phrases: cat.
Recognized Phrases: veterinarian.
```
