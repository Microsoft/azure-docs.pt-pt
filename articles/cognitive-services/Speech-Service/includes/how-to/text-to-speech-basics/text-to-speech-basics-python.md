---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: a306cca2d10052d36ae04950deb87fa56d0fd5e1
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97731941"
---
Neste arranque rápido, você aprende padrões de design comuns para fazer síntese de texto-a-fala usando o SDK do discurso. Começa por fazer configuração e síntese básicas e passa a exemplos mais avançados para o desenvolvimento de aplicações personalizadas, incluindo:

* Obtenção de respostas como fluxos de memória
* Personalização da taxa de amostra de saída e da taxa de bits
* Submeter pedidos de síntese utilizando SSML (linguagem de marcação da síntese da fala)
* Usando vozes neurais

## <a name="skip-to-samples-on-github"></a>Salte para as amostras no GitHub

Se quiser saltar diretamente para o código de amostra, consulte as [amostras de arranque rápido](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python/text-to-speech) da Python no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem uma conta Azure e subscrição do serviço de fala. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Instale o SDK de discurso

Antes de poder fazer qualquer coisa, terá de instalar o SDK do Discurso.

```Python
pip install azure-cognitiveservices-speech
```

Se estiver no macOS e tiver problemas de instalação, poderá ter de executar este comando primeiro.

```Python
python3 -m pip install --upgrade pip
```

Após a instalação do Speech SDK, inclua as seguintes declarações de importação no topo do seu script.

```Python
from azure.cognitiveservices.speech import AudioDataStream, SpeechConfig, SpeechSynthesizer, SpeechSynthesisOutputFormat
from azure.cognitiveservices.speech.audio import AudioOutputConfig
```

## <a name="create-a-speech-configuration"></a>Criar uma configuração de discurso

Para ligar para o serviço de discurso usando o SDK de discurso, precisa de criar um [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python) . Esta classe inclui informações sobre a sua subscrição, como a sua chave e região associada, ponto final, anfitrião ou token de autorização.

> [!NOTE]
> Independentemente de estar a realizar reconhecimento de voz, síntese de fala, tradução ou reconhecimento de intenções, irá sempre criar uma configuração.

Há algumas maneiras de inicializar [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python) um:

* Com uma subscrição: passe numa chave e na região associada.
* Com um ponto final: passe num ponto final de serviço de discurso. Uma chave ou sinal de autorização é opcional.
* Com um anfitrião: passe em um endereço de anfitrião. Uma chave ou sinal de autorização é opcional.
* Com um sinal de autorização: passe em um token de autorização e na região associada.

Neste exemplo, cria-se uma [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python) chave de subscrição e uma região. Obtenha estas credenciais seguindo os passos no [serviço Try the Speech gratuitamente](../../../overview.md#try-the-speech-service-for-free).

```python
speech_config = SpeechConfig(subscription="YourSubscriptionKey", region="YourServiceRegion")
```

## <a name="synthesize-speech-to-a-file"></a>Sintetizar o discurso a um ficheiro

Em seguida, [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?preserve-view=true&view=azure-python) cria-se um objeto que executa conversões de texto para discurso e saídas para altifalantes, ficheiros ou outros fluxos de saída. O [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?preserve-view=true&view=azure-python) aceita como params o [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python) objeto criado no passo anterior, e um [`AudioOutputConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig?preserve-view=true&view=azure-python) objeto que especifica como os resultados de saída devem ser tratados.

Para iniciar, crie uma `AudioOutputConfig` para escrever automaticamente a saída num `.wav` ficheiro, utilizando o `filename` param do construtor.

```python
audio_config = AudioOutputConfig(filename="path/to/write/file.wav")
```

Em seguida, instantânea a `SpeechSynthesizer` passando o seu `speech_config` objeto e o objeto como `audio_config` params. Em seguida, executar a síntese da fala e escrever para um ficheiro é tão simples como correr `speak_text_async()` com uma série de textos.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
synthesizer.speak_text_async("A simple test to write to a file.")
```

Execute o programa e um ficheiro sintetizado `.wav` é escrito para o local especificado. Este é um bom exemplo do uso mais básico, mas em seguida você olha para personalizar a saída e lidar com a resposta de saída como um fluxo de memória para trabalhar com cenários personalizados.

## <a name="synthesize-to-speaker-output"></a>Sintetizar para a saída do altifalante

Em alguns casos, pode querer fazer a produção direta do discurso sintetizado diretamente para um orador. Para isso, utilize o exemplo na secção anterior, mas altere o `AudioOutputConfig` removendo o `filename` param, e definido `use_default_speaker=True` . Isto permite obter o dispositivo de saída ativo atual.

```python
audio_config = AudioOutputConfig(use_default_speaker=True)
```

## <a name="get-result-as-an-in-memory-stream"></a>Obtenha o resultado como um fluxo de memória

Para muitos cenários no desenvolvimento de aplicações de fala, é provável que necessite dos dados áudio resultantes como um fluxo de memória em vez de escrever diretamente para um ficheiro. Isto permitir-lhe-á construir comportamentos personalizados, incluindo:

* Resumo da matriz byte resultante como um fluxo procurado para serviços personalizados a jusante.
* Integre o resultado com outros serviços ou serviços da API.
* Modifique os dados áudio, escreva `.wav` cabeçalhos personalizados, etc.

É simples fazer esta mudança do exemplo anterior. Em primeiro lugar, remova o `AudioConfig` , pois irá gerir manualmente o comportamento da saída a partir deste ponto para um maior controlo. Em seguida, passe `None` para o `AudioConfig` no `SpeechSynthesizer` construtor. 

> [!NOTE]
> Passar `None` para o , em vez de `AudioConfig` omiti-lo como no exemplo de saída do altifalante acima, não reproduzirá o áudio por padrão no dispositivo de saída ativo atual.

Desta vez, guarde o resultado para uma [`SpeechSynthesisResult`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult?preserve-view=true&view=azure-python) variável. A `audio_data` propriedade contém um objeto dos `bytes` dados de saída. Pode trabalhar com este objeto manualmente, ou pode utilizar a [`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?preserve-view=true&view=azure-python) classe para gerir o fluxo de memória. Neste exemplo, utiliza-se o `AudioDataStream` construtor para obter um fluxo do resultado.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = synthesizer.speak_text_async("Getting the response as an in-memory stream.").get()
stream = AudioDataStream(result)
```

A partir daqui pode implementar qualquer comportamento personalizado usando o `stream` objeto resultante.

## <a name="customize-audio-format"></a>Personalizar formato de áudio

A seguinte secção mostra como personalizar atributos de saída de áudio, incluindo:

* Tipo de ficheiro de áudio
* Taxa de amostra
* Profundidade da bit

Para alterar o formato áudio, utilize a `set_speech_synthesis_output_format()` função no `SpeechConfig` objeto. Esta função espera um `enum` tipo , que utiliza para [`SpeechSynthesisOutputFormat`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?preserve-view=true&view=azure-python) selecionar o formato de saída. Consulte os documentos de referência para obter uma [lista de formatos áudio](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?preserve-view=true&view=azure-python) disponíveis.

Existem várias opções para diferentes tipos de ficheiros, dependendo dos seus requisitos. Note que, por definição, formatos brutos como `Raw24Khz16BitMonoPcm` não incluem cabeçalhos de áudio. Utilize formatos brutos apenas quando souber que a sua implementação a jusante pode descodificar um bitstream cru, ou se planeia construir manualmente cabeçalhos baseados na profundidade de bit, taxa de amostra, número de canais, etc.

> [!NOTE]
> As vozes **en-US-AriaRUS** e **en-US-GuyRUS** são criadas a partir de amostras codificadas na `Riff24Khz16BitMonoPcm` taxa de amostra.

Neste exemplo, especifica-se um formato RIFF de alta `Riff24Khz16BitMonoPcm` fidelidade, definindo `SpeechSynthesisOutputFormat` o no `SpeechConfig` objeto. Semelhante ao exemplo na secção anterior, [`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?preserve-view=true&view=azure-python) usa-se para obter um fluxo de memória do resultado e, em seguida, escreva-o num ficheiro.


```python
speech_config.set_speech_synthesis_output_format(SpeechSynthesisOutputFormat["Riff24Khz16BitMonoPcm"])
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

result = synthesizer.speak_text_async("Customizing audio output format.").get()
stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

Executar o seu programa novamente escreverá um ficheiro personalizado `.wav` para o caminho especificado.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Utilize sSML para personalizar características da fala

A Linguagem de Marcação de Síntese de Fala (SSML) permite-lhe afinar o tom, a pronúncia, a taxa de fala, o volume e muito mais da saída texto-a-voz, submetendo os seus pedidos a partir de um esquema XML. Esta secção mostra alguns exemplos práticos de utilização, mas para um guia mais detalhado, consulte o [artigo SSML how-to.](../../../speech-synthesis-markup.md)

Para começar a utilizar o SSML para personalização, faça uma simples alteração que comuta a voz.
Em primeiro lugar, crie um novo ficheiro XML para o config SSML no seu diretório de projetos de raiz, neste exemplo `ssml.xml` . O elemento raiz é sempre `<speak>` , e embrulhar o texto num elemento `<voice>` permite-lhe alterar a voz usando o `name` param. Este exemplo muda a voz para uma voz masculina inglesa (Reino Unido). Note que esta voz é uma voz **padrão,** que tem preços e disponibilidade diferentes do que as vozes **neurais.** Consulte a [lista completa](../../../language-support.md#standard-voices) de vozes **padrão** suportadas.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Em seguida, tem de alterar o pedido de síntese de fala para fazer referência ao seu ficheiro XML. O pedido é maioritariamente o mesmo, mas em vez de utilizar a `speak_text_async()` função, `speak_ssml_async()` usa- se. Esta função espera uma cadeia XML, por isso, primeiro leia o seu config SSML como uma corda. A partir daqui, o objeto de resultado é exatamente o mesmo que exemplos anteriores.

> [!NOTE]
> Se o seu `ssml_string` contém no início da `ï»¿` cadeia, tem de retirar o formato BOM ou o serviço retornará um erro. Fá-lo definindo o `encoding` parâmetro da seguinte forma: `open("ssml.xml", "r", encoding="utf-8-sig")` .

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

ssml_string = open("ssml.xml", "r").read()
result = synthesizer.speak_ssml_async(ssml_string).get()

stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
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

Para mudar para uma voz neural, mude `name` a uma das [opções de voz neural](../../../language-support.md#neural-voices). Em seguida, adicione um espaço de nome XML para `mstts` , e embrulhe o seu texto na `<mstts:express-as>` etiqueta. Use o `style` param para personalizar o estilo de fala. Este exemplo usa `cheerful` , mas tente defini-lo `customerservice` ou para ver a diferença no estilo de `chat` fala.

> [!IMPORTANT]
> As vozes neurais **são apenas** apoiadas para os recursos da Fala criados nas regiões *do Leste dos EUA,* *Sudeste Asiático* e Europa *Ocidental.*

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```