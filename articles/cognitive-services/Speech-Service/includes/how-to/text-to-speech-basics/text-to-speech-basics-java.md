---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: e0c2554162d54af6c0a483e26f708838c3045a03
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80986248"
---
## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que tem uma subscrição de serviço de conta Azure e Speech. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço De Fala.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Instale o SDK do Discurso

Antes de poder fazer qualquer coisa, terá de instalar o SDK do Discurso. Dependendo da sua plataforma, utilize as seguintes instruções:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Tempo de execução de Java<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Dependências de importação

Para executar os exemplos neste artigo, inclua as seguintes declarações de importação no topo do seu script.

```java
import com.microsoft.cognitiveservices.speech.AudioDataStream;
import com.microsoft.cognitiveservices.speech.SpeechConfig;
import com.microsoft.cognitiveservices.speech.SpeechSynthesizer;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisOutputFormat;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisResult;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;

import java.io.*;
import java.util.Scanner;
```

## <a name="create-a-speech-configuration"></a>Criar uma configuração de fala

Para chamar o serviço de Fala usando o SDK do Discurso, é necessário criar um [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable). Esta aula inclui informações sobre a sua subscrição, como a sua chave e região associada, ponto final, anfitrião ou sinal de autorização.

> [!NOTE]
> Independentemente de estar a realizar reconhecimento de discurso, síntese de fala, tradução ou reconhecimento de intenções, irá sempre criar uma configuração.

Há algumas maneiras de inicializar um: [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)

* Com uma subscrição: passe numa chave e na região associada.
* Com um ponto final: passe num ponto final do serviço da Fala. Uma chave ou ficha de autorização é opcional.
* Com um anfitrião: passe num endereço de anfitrião. Uma chave ou ficha de autorização é opcional.
* Com um símbolo de autorização: passe num símbolo de autorização e na região associada.

Neste exemplo, cria-se uma [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) chave de subscrição e região. Consulte a página de apoio da [região](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) para encontrar o seu identificador de região. Também cria um código básico de placa de caldeira para usar para o resto deste artigo, que modifica para diferentes personalizações.

```java
public class Program 
{
    public static void main(String[] args) {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>Sintetizar o discurso a um arquivo

Em seguida, [`SpeechSynthesizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-java-stable) cria-se um objeto, que executa conversões e saídas de texto-a-fala para altifalantes, ficheiros ou outros fluxos de saída. Os [`SpeechSynthesizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-java-stable) aceitacomo paramas [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) o objeto criado no passo [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable) anterior, e um objeto que especifica como os resultados de saída devem ser tratados.

Para começar, `AudioConfig` crie um para escrever `.wav` automaticamente `fromWavFileOutput()` a saída para um ficheiro utilizando a função estática.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");
}
```

Em seguida, `SpeechSynthesizer` instantaneamente `speechConfig` um `audioConfig` objeto passando o seu objeto e o objeto como params. Então, executar a síntese da fala e `SpeakText()` escrever para um arquivo é tão simples como correr com uma série de texto.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("A simple test to write to a file.");
}
```

Executar o programa e um ficheiro `.wav` sintetizado está escrito para o local que especificou. Este é um bom exemplo do uso mais básico, mas em seguida você olha para personalizar a saída e lidar com a resposta de saída como um fluxo de memória para trabalhar com cenários personalizados.

## <a name="synthesize-to-speaker-output"></a>Sintetizar para a saída do altifalante

Em alguns casos, é melhor que se produza diretamente um discurso sintetizado diretamente para um orador. Para tal, instanteo `AudioConfig` `fromDefaultSpeakerOutput()` a utilização da função estática. Isto é saída para o dispositivo de saída ativo atual.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Obtenha resultado como um fluxo de memória

Para muitos cenários no desenvolvimento de aplicações da fala, você provavelmente precisa dos dados áudio resultantes como um fluxo de memória em vez de escrever diretamente para um arquivo. Isto permitir-lhe-á construir comportamentos personalizados, incluindo:

* Abstrata a matriz de byte resultante como um fluxo de procura para serviços personalizados a jusante.
* Integrar o resultado com outros Serviços aPi's ou.
* Modifique os dados `.wav` de áudio, escreva cabeçalhos personalizados, etc.

É simples fazer esta mudança do exemplo anterior. Primeiro, remova `AudioConfig` o bloco, pois irá gerir o comportamento de saída manualmente a partir deste ponto para um maior controlo. Em `null` seguida, `AudioConfig` passe `SpeechSynthesizer` para o construtor. 

> [!NOTE]
> Passar `null` para `AudioConfig`o , em vez de omitir como no exemplo de saída do altifalante acima, não reproduzirá o áudio por padrão no dispositivo de saída ativo atual.

Desta vez, poupa-se [`SpeechSynthesisResult`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult?view=azure-java-stable) o resultado a uma variável. A `SpeechSynthesisResult.getAudioData()` função `byte []` devolve um dos dados de saída. Pode trabalhar com `byte []` isto manualmente, ou [`AudioDataStream`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audiodatastream?view=azure-java-stable) pode utilizar a classe para gerir o fluxo de memória. Neste exemplo, utiliza-se a `AudioDataStream.fromResult()` função estática para obter um fluxo do resultado.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);
    
    SpeechSynthesisResult result = synthesizer.SpeakText("Getting the response as an in-memory stream.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    System.out.print(stream.getStatus());
}
```

A partir daqui pode implementar qualquer `stream` comportamento personalizado usando o objeto resultante.

## <a name="customize-audio-format"></a>Personalizar formato de áudio

A seguinte secção mostra como personalizar atributos de saída de áudio, incluindo:

* Tipo de ficheiro de áudio
* Taxa de amostra
* Profundidade bit

Para alterar o formato `setSpeechSynthesisOutputFormat()` áudio, `SpeechConfig` utiliza a função no objeto. Esta função `enum` espera [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-java-stable)um tipo de tipo , que utiliza para selecionar o formato de saída. Consulte os médicos de referência para obter uma [lista de formatos áudio](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet) disponíveis.

Existem várias opções para diferentes tipos de ficheiros, dependendo dos seus requisitos. Note que, por definição, formatos brutos como `Raw24Khz16BitMonoPcm` não incluem cabeçalhos de áudio. Utilize formatos crus apenas quando souber que a sua implementação a jusante pode descodificar um bitstream bruto, ou se planeia construir cabeçalhos manualmente baseados em profundidade bitada, taxa de amostra, número de canais, etc.

Neste exemplo, especifice um formato `Riff24Khz16BitMonoPcm` RIFF `SpeechSynthesisOutputFormat` de `SpeechConfig` alta fidelidade, definindo o objeto no objeto. Semelhante ao exemplo na secção anterior, utiliza-se [`AudioDataStream`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audiodatastream?view=azure-java-stable) para obter um fluxo de memória do resultado e, em seguida, escrevê-lo num ficheiro.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // set the output format
    speechConfig.setSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);
    SpeechSynthesisResult result = synthesizer.SpeakText("Customizing audio output format.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
}
```

Executar o seu programa `.wav` novamente escreverá um ficheiro para o caminho especificado.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Use o SSML para personalizar as características da fala

Síntese de Fala A Linguagem de Marcação (SSML) permite afinar o pitch, a pronúncia, a taxa de fala, o volume e mais da saída de texto-a-fala, submetendo os seus pedidos a partir de um esquema XML. Esta secção mostra alguns exemplos práticos de utilização, mas para um guia mais detalhado, consulte o artigo como [fazer](../../../speech-synthesis-markup.md).

Para começar a utilizar o SSML para personalização, faça uma simples alteração que altere a voz.
Em primeiro lugar, crie um novo ficheiro XML para a config `ssml.xml`SSML no seu diretório de projeto raiz, neste exemplo . O elemento raiz `<speak>`é sempre , `<voice>` e embrulhar o texto num `name` elemento permite-lhe alterar a voz utilizando o param. Este exemplo muda a voz para uma voz masculina inglesa (Reino Unido). Note que esta voz é uma voz **padrão,** que tem preços e disponibilidade diferentes do que as vozes **neurais.** Consulte a [lista completa](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices) de vozes **padrão** apoiadas.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Em seguida, você precisa alterar o pedido de síntese da fala para fazer referência ao seu ficheiro XML. O pedido é maioritariamente o `SpeakText()` mesmo, mas `SpeakSsml()`em vez de utilizar a função, utiliza-se . Esta função espera uma cadeia XML, por isso primeiro cria-se uma função para carregar um ficheiro XML e devolvê-lo como uma corda.

```java
private static String xmlToString(String filePath) {
    File file = new File(filePath);
    StringBuilder fileContents = new StringBuilder((int)file.length());

    try (Scanner scanner = new Scanner(file)) {
        while(scanner.hasNextLine()) {
            fileContents.append(scanner.nextLine() + System.lineSeparator());
        }
        return fileContents.toString().trim();
    } catch (FileNotFoundException ex) {
        return "File not found.";
    }
}
```

A partir daqui, o objeto de resultados é exatamente o mesmo que exemplos anteriores.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);

    String ssml = xmlToString("ssml.xml");
    SpeechSynthesisResult result = synthesizer.SpeakSsml(ssml);
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
}
```

A saída funciona, mas há algumas alterações adicionais simples que pode fazer para ajudar a soar mais natural. A velocidade de fala geral é um pouco rápida `<prosody>` demais, por isso vamos adicionar uma etiqueta e reduzir a velocidade para **90%** da taxa padrão. Além disso, a pausa após a vírem na frase é um pouco curta e pouco natural. Para corrigir esta questão, adicione uma `<break>` etiqueta para atrasar o discurso e coloque o param de hora em **200ms**. Reexecutar a síntese para ver como estas personalizações afetaram a saída.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Vozes neurais

As vozes neurais são algoritmos de síntese da fala alimentados por redes neuronais profundas. Quando se usa uma voz neural, a fala sintetizada é quase indistinguível das gravações humanas. Com a prosódia natural humana e a clara articulação das palavras, as vozes neurais reduzem significativamente a fadiga auditiva quando os utilizadores interagem com sistemas de IA.

Para mudar para uma voz `name` neural, mude a para uma das [opções](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices)de voz neural . Em seguida, adicione um `mstts`espaço de nome XML para , e envolva o `<mstts:express-as>` seu texto na etiqueta. Use `style` o param para personalizar o estilo de fala. Este exemplo `cheerful`usa, mas `customerservice` tente `chat` defini-lo para ou para ver a diferença no estilo de falar.

> [!IMPORTANT]
> As vozes neurais **só** são apoiadas pelos recursos da Fala criados nas regiões *leste dos EUA*, *Sudeste Asiático*e Europa *Ocidental.*

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
