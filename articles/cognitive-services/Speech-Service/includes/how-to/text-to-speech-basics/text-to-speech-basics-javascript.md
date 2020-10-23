---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 3ac9c0f473279866d580795b76c77516fc3ef679
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92470708"
---
Neste arranque rápido, você aprende padrões de design comuns para fazer síntese de texto-a-fala usando o SDK do discurso. Começa por fazer configuração e síntese básicas e passa a exemplos mais avançados para o desenvolvimento de aplicações personalizadas, incluindo:

* Obtenção de respostas como fluxos de memória
* Personalização da taxa de amostra de saída e da taxa de bits
* Submeter pedidos de síntese utilizando SSML (linguagem de marcação da síntese da fala)
* Usando vozes neurais

## <a name="skip-to-samples-on-github"></a>Salte para as amostras no GitHub

Se quiser saltar diretamente para o código de amostra, consulte as [amostras de arranque rápido do JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem uma conta Azure e subscrição do serviço de fala. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

Antes de poder fazer qualquer coisa, terá de instalar o <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a>de voz para JavaScript . Dependendo da sua plataforma, utilize as seguintes instruções:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Navegador Web <span class="docon docon-navigate-external x-hidden-focus"></span></a>

Além disso, dependendo do ambiente-alvo, utilize um dos seguintes:

# <a name="script"></a>[script](#tab/script)

Faça o download e extrai o <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">Ficheiro SDK de discurso para <span class="docon docon-navigate-external x-hidden-focus"></span> JavaScript</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* e coloque-o numa pasta acessível ao seu ficheiro HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Se estiver a direcionar um navegador web e a usar a `<script>` etiqueta; o `sdk` prefixo não é necessário. O `sdk` prefixo é um pseudónimo usado para dar o nome ao `require` módulo.

# <a name="import"></a>[importar](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Para obter mais informações `import` sobre, consulte <a href="https://javascript.info/import-export" target="_blank">a exportação e <span class="docon docon-navigate-external x-hidden-focus"></span> a importação. </a>

# <a name="require"></a>[requerer](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Para mais informações `require` sobre, veja <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">o que é necessário? <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

---


## <a name="create-a-speech-configuration"></a>Criar uma configuração de discurso

Para ligar para o serviço de discurso usando o SDK de discurso, precisa de criar um [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) . Esta classe inclui informações sobre a sua subscrição, como a sua chave e região associada, ponto final, anfitrião ou token de autorização.

> [!NOTE]
> Independentemente de estar a realizar reconhecimento de voz, síntese de fala, tradução ou reconhecimento de intenções, irá sempre criar uma configuração.

Há algumas maneiras de inicializar [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) um:

* Com uma subscrição: passe numa chave e na região associada.
* Com um ponto final: passe num ponto final de serviço de discurso. Uma chave ou sinal de autorização é opcional.
* Com um anfitrião: passe em um endereço de anfitrião. Uma chave ou sinal de autorização é opcional.
* Com um sinal de autorização: passe em um token de autorização e na região associada.

Neste exemplo, cria-se uma [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) chave de subscrição e uma região. Obtenha estas credenciais seguindo os passos no [serviço Try the Speech gratuitamente](../../../overview.md#try-the-speech-service-for-free). Também cria um código básico de placa de caldeira para usar para o resto deste artigo, que modifica para diferentes personalizações.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}

synthesizeSpeech();
```

## <a name="synthesize-speech-to-a-file"></a>Sintetizar o discurso a um ficheiro

Em seguida, [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest&preserve-view=true) cria-se um objeto que executa conversões de texto para discurso e saídas para altifalantes, ficheiros ou outros fluxos de saída. O [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest&preserve-view=true) aceita como params o [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true) objeto criado no passo anterior, e um [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true) objeto que especifica como os resultados de saída devem ser tratados.

Para iniciar, crie uma `AudioConfig` saída para escrever automaticamente a saída para um ficheiro utilizando a `.wav` `fromAudioFileOutput()` função estática.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

Em seguida, instantânea um `SpeechSynthesizer` objeto que passa o seu objeto e o objeto como `speechConfig` `audioConfig` params. Em seguida, executar a síntese da fala e escrever para um ficheiro é tão simples como correr `speakTextAsync()` com uma série de textos. O retorno do resultado é um ótimo lugar para chamar `synthesizer.close()` , na verdade - esta chamada é necessária para que a síntese funcione corretamente.

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
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Execute o programa e um ficheiro sintetizado `.wav` é escrito para o local especificado. Este é um bom exemplo do uso mais básico, mas em seguida você olha para personalizar a saída e lidar com a resposta de saída como um fluxo de memória para trabalhar com cenários personalizados.

## <a name="synthesize-to-speaker-output"></a>Sintetizar para a saída do altifalante

Em alguns casos, pode querer fazer a produção direta do discurso sintetizado diretamente para um orador. Para isso, instantaneamente a `AudioConfig` utilização da `fromDefaultSpeakerOutput()` função estática. Isto permite obter o dispositivo de saída ativo atual.

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

## <a name="get-result-as-an-in-memory-stream"></a>Obtenha o resultado como um fluxo de memória

Para muitos cenários no desenvolvimento de aplicações de fala, é provável que necessite dos dados áudio resultantes como um fluxo de memória em vez de escrever diretamente para um ficheiro. Isto permitir-lhe-á construir comportamentos personalizados, incluindo:

* Resumo da matriz byte resultante como um fluxo procurado para serviços personalizados a jusante.
* Integre o resultado com outros serviços ou serviços da API.
* Modifique os dados áudio, escreva `.wav` cabeçalhos personalizados, etc.

É simples fazer esta mudança do exemplo anterior. Em primeiro lugar, retire o `AudioConfig` bloco, pois irá gerir manualmente o comportamento da saída a partir deste ponto para um maior controlo. Em seguida, passe `undefined` para o `AudioConfig` no `SpeechSynthesizer` construtor. 

> [!NOTE]
> Passar `undefined` para o , em vez de `AudioConfig` omiti-lo como no exemplo de saída do altifalante acima, não reproduzirá o áudio por padrão no dispositivo de saída ativo atual.

Desta vez, guarde o resultado para uma [`SpeechSynthesisResult`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult?view=azure-node-latest&preserve-view=true) variável. A `SpeechSynthesisResult.audioData` propriedade devolve um dos `ArrayBuffer` dados de saída. Pode trabalhar com isto `ArrayBuffer` manualmente.

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

A partir daqui pode implementar qualquer comportamento personalizado usando o `ArrayBuffer` objeto resultante.

## <a name="customize-audio-format"></a>Personalizar formato de áudio

A seguinte secção mostra como personalizar atributos de saída de áudio, incluindo:

* Tipo de ficheiro de áudio
* Taxa de amostra
* Profundidade da bit

Para alterar o formato áudio, utilize a `speechSynthesisOutputFormat` propriedade no `SpeechConfig` objeto. Esta propriedade espera um `enum` [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest&preserve-view=true) tipo, que você usa para selecionar o formato de saída. Consulte os documentos de referência para obter uma [lista de formatos áudio](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest&preserve-view=true) disponíveis.

Existem várias opções para diferentes tipos de ficheiros, dependendo dos seus requisitos. Note que, por definição, formatos brutos como `Raw24Khz16BitMonoPcm` não incluem cabeçalhos de áudio. Utilize formatos brutos apenas quando souber que a sua implementação a jusante pode descodificar um bitstream cru, ou se planeia construir manualmente cabeçalhos baseados na profundidade de bit, taxa de amostra, número de canais, etc.

Neste exemplo, especifica-se um formato RIFF de alta `Riff24Khz16BitMonoPcm` fidelidade, definindo `speechSynthesisOutputFormat` o no `SpeechConfig` objeto. Semelhante ao exemplo na secção anterior, obtenha os dados áudio `ArrayBuffer` e interaja com os mesmos.

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

Executar o seu programa novamente escreverá um `.wav` ficheiro para o caminho especificado.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Utilize sSML para personalizar características da fala

A Linguagem de Marcação de Síntese de Fala (SSML) permite-lhe afinar o tom, a pronúncia, a taxa de fala, o volume e muito mais da saída texto-a-voz, submetendo os seus pedidos a partir de um esquema XML. Esta secção mostra alguns exemplos práticos de utilização, mas para um guia mais detalhado, consulte o [artigo SSML how-to.](../../../speech-synthesis-markup.md)

Para começar a utilizar o SSML para personalização, faça uma simples alteração que comuta a voz.
Em primeiro lugar, crie um novo ficheiro XML para o config SSML no seu diretório de projetos de raiz, neste exemplo `ssml.xml` . O elemento raiz é sempre `<speak>` , e embrulhar o texto num elemento `<voice>` permite-lhe alterar a voz usando o `name` param. Este exemplo muda a voz para uma voz masculina inglesa (Reino Unido). Note que esta voz é uma voz **padrão,** que tem preços e disponibilidade diferentes do que as vozes **neurais.** Consulte a [lista completa](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices) de vozes **padrão** suportadas.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Em seguida, tem de alterar o pedido de síntese de fala para fazer referência ao seu ficheiro XML. O pedido é maioritariamente o mesmo, mas em vez de utilizar a `speakTextAsync()` função, `speakSsmlAsync()` usa- se. Esta função espera uma cadeia XML, por isso primeiro cria uma função para carregar um ficheiro XML e devolvê-lo como uma corda.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

Para obter mais informações `readFileSync` sobre , consulteNode.js sistema de <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">ficheiros <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. A partir daqui, o objeto de resultado é exatamente o mesmo que exemplos anteriores.

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

A saída funciona, mas há algumas mudanças adicionais simples que pode fazer para ajudá-la a soar mais natural. A velocidade geral de falar é um pouco rápida demais, por isso vamos adicionar uma `<prosody>` etiqueta e reduzir a velocidade para **90%** da taxa de incumprimento. Além disso, a pausa após a vírgula na frase é um pouco curta e pouco natural. Para corrigir este problema, adicione uma `<break>` etiqueta para atrasar o discurso e defina o tempo para **200ms**. Re-executar a síntese para ver como estas personalizações afetaram a saída.

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

Vozes neurais são algoritmos de síntese da fala alimentados por redes neuronais profundas. Quando se usa uma voz neural, o discurso sintetizado é quase indistinguível das gravações humanas. Com a prosódia natural humana e a clara articulação das palavras, as vozes neurais reduzem significativamente a fadiga auditiva quando os utilizadores interagem com os sistemas de IA.

Para mudar para uma voz neural, mude `name` a uma das [opções de voz neural](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices). Em seguida, adicione um espaço de nome XML para `mstts` , e embrulhe o seu texto na `<mstts:express-as>` etiqueta. Use o `style` param para personalizar o estilo de fala. Este exemplo usa `cheerful` , mas tente defini-lo `customerservice` ou para ver a diferença no estilo de `chat` fala.

> [!IMPORTANT]
> As vozes neurais **são apenas** apoiadas para os recursos da Fala criados nas regiões *do Leste dos EUA,* *Sudeste Asiático*e Europa *Ocidental.*

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
