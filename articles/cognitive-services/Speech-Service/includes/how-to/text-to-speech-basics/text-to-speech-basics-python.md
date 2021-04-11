---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: 9d6d6ec36d5ab7d29b3050dafda5fd711b01f1f4
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108890"
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

Para ligar para o serviço de discurso usando o SDK de discurso, precisa de criar um [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) . Esta classe inclui informações sobre a sua subscrição, como a sua chave e região associada, ponto final, anfitrião ou token de autorização.

> [!NOTE]
> Independentemente de estar a realizar reconhecimento de voz, síntese de fala, tradução ou reconhecimento de intenções, irá sempre criar uma configuração.

Há algumas maneiras de inicializar [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) um:

* Com uma subscrição: passe numa chave e na região associada.
* Com um ponto final: passe num ponto final de serviço de discurso. Uma chave ou sinal de autorização é opcional.
* Com um anfitrião: passe em um endereço de anfitrião. Uma chave ou sinal de autorização é opcional.
* Com um sinal de autorização: passe em um token de autorização e na região associada.

Neste exemplo, cria-se uma [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) chave de subscrição e uma região. Obtenha estas credenciais seguindo os passos no [serviço Try the Speech gratuitamente](../../../overview.md#try-the-speech-service-for-free).

```python
speech_config = SpeechConfig(subscription="YourSubscriptionKey", region="YourServiceRegion")
```

## <a name="synthesize-speech-to-a-file"></a>Sintetizar o discurso a um ficheiro

Em seguida, [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer) cria-se um objeto que executa conversões de texto para discurso e saídas para altifalantes, ficheiros ou outros fluxos de saída. O [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer) aceita como params o [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) objeto criado no passo anterior, e um [`AudioOutputConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig) objeto que especifica como os resultados de saída devem ser tratados.

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

Desta vez, guarde o resultado para uma [`SpeechSynthesisResult`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult) variável. A `audio_data` propriedade contém um objeto dos `bytes` dados de saída. Pode trabalhar com este objeto manualmente, ou pode utilizar a [`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream) classe para gerir o fluxo de memória. Neste exemplo, utiliza-se o `AudioDataStream` construtor para obter um fluxo do resultado.

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

Para alterar o formato áudio, utilize a `set_speech_synthesis_output_format()` função no `SpeechConfig` objeto. Esta função espera um `enum` tipo , que utiliza para [`SpeechSynthesisOutputFormat`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat) selecionar o formato de saída. Consulte os documentos de referência para obter uma [lista de formatos áudio](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat) disponíveis.

Existem várias opções para diferentes tipos de ficheiros, dependendo dos seus requisitos. Note que, por definição, formatos brutos como `Raw24Khz16BitMonoPcm` não incluem cabeçalhos de áudio. Utilize formatos brutos apenas quando souber que a sua implementação a jusante pode descodificar um bitstream cru, ou se planeia construir manualmente cabeçalhos baseados na profundidade de bit, taxa de amostra, número de canais, etc.

Neste exemplo, especifica-se um formato RIFF de alta `Riff24Khz16BitMonoPcm` fidelidade, definindo `SpeechSynthesisOutputFormat` o no `SpeechConfig` objeto. Semelhante ao exemplo na secção anterior, [`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream) usa-se para obter um fluxo de memória do resultado e, em seguida, escreva-o num ficheiro.


```python
speech_config.set_speech_synthesis_output_format(SpeechSynthesisOutputFormat["Riff24Khz16BitMonoPcm"])
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

result = synthesizer.speak_text_async("Customizing audio output format.").get()
stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

Executar o seu programa novamente escreverá um ficheiro personalizado `.wav` para o caminho especificado.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Utilize sSML para personalizar características da fala

A Linguagem de Marcação de Síntese de Fala (SSML) permite-lhe afinar o tom, a pronúncia, a taxa de fala, o volume e muito mais da saída texto-a-voz, submetendo os seus pedidos a partir de um esquema XML. Esta secção mostra um exemplo de mudança de voz, mas para um guia mais detalhado, consulte o [artigo SSML how-to](../../../speech-synthesis-markup.md).

Para começar a utilizar o SSML para personalização, faça uma simples alteração que comuta a voz.
Em primeiro lugar, crie um novo ficheiro XML para o config SSML no seu diretório de projetos de raiz, neste exemplo `ssml.xml` . O elemento raiz é sempre `<speak>` , e embrulhar o texto num elemento `<voice>` permite-lhe alterar a voz usando o `name` param. Consulte a [lista completa](../../../language-support.md#neural-voices) de vozes **neurais** suportadas.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    When you're on the freeway, it's a good idea to use a GPS.
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

> [!NOTE]
> Para alterar a voz sem utilizar o SSML, pode definir a propriedade no através da `SpeechConfig` utilização `SpeechConfig.speech_synthesis_voice_name = "en-US-AriaNeural"`

## <a name="get-facial-pose-events"></a>Obtenha eventos de pose facial

A fala pode ser uma boa maneira de impulsionar a animação de expressões faciais.
Muitas [vezes os visemes](../../../how-to-speech-synthesis-viseme.md) são usados para representar as poses-chave na fala observada, como a posição dos lábios, mandíbula e língua ao produzir um determinado fonme.
Pode subscrever o evento viseme na Speech SDK.
Em seguida, você pode aplicar eventos visemes para animar o rosto de uma personagem enquanto o áudio da fala reproduz.
Saiba [como obter eventos visemes.](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk)
