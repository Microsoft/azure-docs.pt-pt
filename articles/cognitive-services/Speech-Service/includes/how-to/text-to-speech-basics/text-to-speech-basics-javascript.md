---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.openlocfilehash: d91082ae3c0ae5e501675a06b02e2f55f20ce236
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81399574"
---
## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que tem uma subscrição de serviço de conta Azure e Speech. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço De Fala.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Instale o SDK do Discurso

Antes de poder fazer qualquer coisa, terá de instalar o <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">JavaScript Speech <span class="docon docon-navigate-external x-hidden-focus"> </span>SDK </a>. Dependendo da sua plataforma, utilize as seguintes instruções:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Nó.js<span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Navegador Web<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Adicionalmente, dependendo do ambiente-alvo, utilize um dos seguintes:

# <a name="import"></a>[importação](#tab/import)

```javascript
import { readFileSync } from "fs";
import {
    AudioConfig,
    SpeechConfig,
    SpeechSynthesisOutputFormat,
    SpeechSynthesizer 
} from "microsoft-cognitiveservices-speech-sdk";
```

Para obter `import`mais informações sobre , consulte <a href="https://javascript.info/import-export" target="_blank">a exportação e a importação. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>

# <a name="require"></a>[exigir](#tab/require)

```javascript
const readFileSync = require("fs").readFileSync;
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Para mais `require`informações sobre, veja <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">o que é necessário? <span class="docon docon-navigate-external x-hidden-focus"></span></a>.


# <a name="script"></a>[script](#tab/script)

Descarregue e extraia o ficheiro <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">JavaScript Speech <span class="docon docon-navigate-external x-hidden-focus"></span> SDK</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* e coloque-o numa pasta acessível ao seu ficheiro HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Se estiver a direcionar um navegador web `<script>` e a usar a etiqueta; o `sdk` prefixo não é necessário. O `sdk` prefixo é um pseudónimo `require` usado para nomear o módulo.

---

## <a name="create-a-speech-configuration"></a>Criar uma configuração de fala

Para chamar o serviço de Fala usando o SDK do Discurso, é necessário criar um [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest). Esta aula inclui informações sobre a sua subscrição, como a sua chave e região associada, ponto final, anfitrião ou sinal de autorização.

> [!NOTE]
> Independentemente de estar a realizar reconhecimento de discurso, síntese de fala, tradução ou reconhecimento de intenções, irá sempre criar uma configuração.

Há algumas maneiras de inicializar um: [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)

* Com uma subscrição: passe numa chave e na região associada.
* Com um ponto final: passe num ponto final do serviço da Fala. Uma chave ou ficha de autorização é opcional.
* Com um anfitrião: passe num endereço de anfitrião. Uma chave ou ficha de autorização é opcional.
* Com um símbolo de autorização: passe num símbolo de autorização e na região associada.

Neste exemplo, cria-se uma [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) chave de subscrição e região. Consulte a página de apoio da [região](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) para encontrar o seu identificador de região. Também cria um código básico de placa de caldeira para usar para o resto deste artigo, que modifica para diferentes personalizações.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>Sintetizar o discurso a um arquivo

Em seguida, [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) cria-se um objeto, que executa conversões e saídas de texto-a-fala para altifalantes, ficheiros ou outros fluxos de saída. Os [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) aceitacomo paramas [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) o objeto criado no passo [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) anterior, e um objeto que especifica como os resultados de saída devem ser tratados.

Para começar, `AudioConfig` crie um para escrever `.wav` automaticamente `fromAudioFileOutput()` a saída para um ficheiro utilizando a função estática.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

Em seguida, `SpeechSynthesizer` instantaneamente `speechConfig` um `audioConfig` objeto passando o seu objeto e o objeto como params. Então, executar a síntese da fala e `speakTextAsync()` escrever para um arquivo é tão simples como correr com uma série de texto. O recuo do resultado é `synthesizer.close()`um ótimo lugar para chamar , na verdade - esta chamada é necessária para que a síntese funcione corretamente.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        }
    },
    error => {
        console.log(error);
        synthesizer.close();
    });
}
```

Executar o programa e um ficheiro `.wav` sintetizado está escrito para o local que especificou. Este é um bom exemplo do uso mais básico, mas em seguida você olha para personalizar a saída e lidar com a resposta de saída como um fluxo de memória para trabalhar com cenários personalizados.

## <a name="synthesize-to-speaker-output"></a>Sintetizar para a saída do altifalante

Em alguns casos, é melhor que se produza diretamente um discurso sintetizado diretamente para um orador. Para tal, instanteo `AudioConfig` `fromDefaultSpeakerOutput()` a utilização da função estática. Isto é saída para o dispositivo de saída ativo atual.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Obtenha resultado como um fluxo de memória

Para muitos cenários no desenvolvimento de aplicações da fala, você provavelmente precisa dos dados áudio resultantes como um fluxo de memória em vez de escrever diretamente para um arquivo. Isto permitir-lhe-á construir comportamentos personalizados, incluindo:

* Abstrata a matriz de byte resultante como um fluxo de procura para serviços personalizados a jusante.
* Integrar o resultado com outros Serviços aPi's ou.
* Modifique os dados `.wav` de áudio, escreva cabeçalhos personalizados, etc.

É simples fazer esta mudança do exemplo anterior. Primeiro, remova `AudioConfig` o bloco, pois irá gerir o comportamento de saída manualmente a partir deste ponto para um maior controlo. Em `undefined` seguida, `AudioConfig` passe `SpeechSynthesizer` para o construtor. 

> [!NOTE]
> Passar `undefined` para `AudioConfig`o , em vez de omitir como no exemplo de saída do altifalante acima, não reproduzirá o áudio por padrão no dispositivo de saída ativo atual.

Desta vez, poupa-se [`SpeechSynthesisResult`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult?view=azure-node-latest) o resultado a uma variável. A `SpeechSynthesisResult.audioData` propriedade `ArrayBuffer` devolve um dos dados de saída. Pode trabalhar com `ArrayBuffer` isto manualmente.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

A partir daqui pode implementar qualquer `ArrayBuffer` comportamento personalizado usando o objeto resultante.

## <a name="customize-audio-format"></a>Personalizar formato de áudio

A seguinte secção mostra como personalizar atributos de saída de áudio, incluindo:

* Tipo de ficheiro de áudio
* Taxa de amostra
* Profundidade bit

Para alterar o formato `speechSynthesisOutputFormat` áudio, `SpeechConfig` utiliza a propriedade no objeto. Esta propriedade `enum` espera [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest)um tipo de tipo , que você usa para selecionar o formato de saída. Consulte os médicos de referência para obter uma [lista de formatos áudio](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest) disponíveis.

Existem várias opções para diferentes tipos de ficheiros, dependendo dos seus requisitos. Note que, por definição, formatos brutos como `Raw24Khz16BitMonoPcm` não incluem cabeçalhos de áudio. Utilize formatos crus apenas quando souber que a sua implementação a jusante pode descodificar um bitstream bruto, ou se planeia construir cabeçalhos manualmente baseados em profundidade bitada, taxa de amostra, número de canais, etc.

Neste exemplo, especifice um formato `Riff24Khz16BitMonoPcm` RIFF `speechSynthesisOutputFormat` de `SpeechConfig` alta fidelidade, definindo o objeto no objeto. Semelhante ao exemplo na secção anterior, `ArrayBuffer` obtenha os dados áudio e interaja com ele.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
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

Em seguida, você precisa alterar o pedido de síntese da fala para fazer referência ao seu ficheiro XML. O pedido é maioritariamente o `speakTextAsync()` mesmo, mas `speakSsmlAsync()`em vez de utilizar a função, utiliza-se . Esta função espera uma cadeia XML, por isso primeiro cria-se uma função para carregar um ficheiro XML e devolvê-lo como uma corda.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

Para mais `readFileSync`informações sobre , consulte o sistema <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank"><span class="docon docon-navigate-external x-hidden-focus"></span>de ficheiros Node.js</a>. A partir daqui, o objeto de resultados é exatamente o mesmo que exemplos anteriores.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
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
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
    xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
