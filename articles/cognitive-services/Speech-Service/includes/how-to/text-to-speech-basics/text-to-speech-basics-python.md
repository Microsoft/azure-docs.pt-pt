---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: be60a2f371148fabf73fc7fcdce114295775d71c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986255"
---
## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que tem uma subscrição de serviço de conta Azure e Speech. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço De Fala.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Instale o SDK do Discurso

Antes de poder fazer qualquer coisa, terá de instalar o SDK do Discurso.

```Python
pip install azure-cognitiveservices-speech
```

Se estiver no macOS e se encontrar em problemas de instalação, poderá ter de executar este comando primeiro.

```Python
python3 -m pip install --upgrade pip
```

Após a instalação do SDK do Discurso, inclua as seguintes declarações de importação no topo do seu script.

```Python
from azure.cognitiveservices.speech import AudioDataStream, SpeechConfig, SpeechSynthesizer, SpeechSynthesisOutputFormat
from azure.cognitiveservices.speech.audio import AudioOutputConfig
```

## <a name="create-a-speech-configuration"></a>Criar uma configuração de fala

Para chamar o serviço de Fala usando o SDK do Discurso, é necessário criar um [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python). Esta aula inclui informações sobre a sua subscrição, como a sua chave e região associada, ponto final, anfitrião ou sinal de autorização.

> [!NOTE]
> Independentemente de estar a realizar reconhecimento de discurso, síntese de fala, tradução ou reconhecimento de intenções, irá sempre criar uma configuração.

Há algumas maneiras de inicializar um: [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)

* Com uma subscrição: passe numa chave e na região associada.
* Com um ponto final: passe num ponto final do serviço da Fala. Uma chave ou ficha de autorização é opcional.
* Com um anfitrião: passe num endereço de anfitrião. Uma chave ou ficha de autorização é opcional.
* Com um símbolo de autorização: passe num símbolo de autorização e na região associada.

Neste exemplo, cria-se uma [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) chave de subscrição e região. Consulte a página de apoio da [região](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) para encontrar o seu identificador de região.

```python
speech_config = SpeechConfig(subscription="YourSubscriptionKey", region="YourServiceRegion")
```

## <a name="synthesize-speech-to-a-file"></a>Sintetizar o discurso a um arquivo

Em seguida, [`SpeechSynthesizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python) cria-se um objeto, que executa conversões e saídas de texto-a-fala para altifalantes, ficheiros ou outros fluxos de saída. Os [`SpeechSynthesizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python) aceitacomo paramas [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) o objeto criado no passo [`AudioOutputConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig?view=azure-python) anterior, e um objeto que especifica como os resultados de saída devem ser tratados.

Para começar, `AudioOutputConfig` crie um para escrever `.wav` automaticamente a `filename` saída para um ficheiro, utilizando o param do construtor.

```python
audio_config = AudioOutputConfig(filename="path/to/write/file.wav")
```

Em seguida, `SpeechSynthesizer` instantaneamente `speech_config` um passando o seu objeto e o `audio_config` objeto como params. Então, executar a síntese da fala e `speak_text_async()` escrever para um arquivo é tão simples como correr com uma série de texto.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
synthesizer.speak_text_async("A simple test to write to a file.")
```

Executar o programa e um ficheiro `.wav` sintetizado está escrito para o local que especificou. Este é um bom exemplo do uso mais básico, mas em seguida você olha para personalizar a saída e lidar com a resposta de saída como um fluxo de memória para trabalhar com cenários personalizados.

## <a name="synthesize-to-speaker-output"></a>Sintetizar para a saída do altifalante

Em alguns casos, é melhor que se produza diretamente um discurso sintetizado diretamente para um orador. Para tal, utilize o exemplo na secção `AudioOutputConfig` anterior, `filename` mas altere `use_default_speaker=True`o, removendo o param, e coloque. Isto é saída para o dispositivo de saída ativo atual.

```python
audio_config = AudioOutputConfig(use_default_speaker=True)
```

## <a name="get-result-as-an-in-memory-stream"></a>Obtenha resultado como um fluxo de memória

Para muitos cenários no desenvolvimento de aplicações da fala, você provavelmente precisa dos dados áudio resultantes como um fluxo de memória em vez de escrever diretamente para um arquivo. Isto permitir-lhe-á construir comportamentos personalizados, incluindo:

* Abstrata a matriz de byte resultante como um fluxo de procura para serviços personalizados a jusante.
* Integrar o resultado com outros Serviços aPi's ou.
* Modifique os dados `.wav` de áudio, escreva cabeçalhos personalizados, etc.

É simples fazer esta mudança do exemplo anterior. Em primeiro `AudioConfig`lugar, remova o , pois irá gerir o comportamento de saída manualmente a partir deste ponto para um maior controlo. Em `None` seguida, `AudioConfig` passe `SpeechSynthesizer` para o construtor. 

> [!NOTE]
> Passar `None` para `AudioConfig`o , em vez de omitir como no exemplo de saída do altifalante acima, não reproduzirá o áudio por padrão no dispositivo de saída ativo atual.

Desta vez, poupa-se [`SpeechSynthesisResult`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult?view=azure-python) o resultado a uma variável. A `audio_data` propriedade `bytes` contém um objeto dos dados de saída. Pode trabalhar com este objeto manualmente, [`AudioDataStream`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?view=azure-python) ou pode utilizar a classe para gerir o fluxo de memória. Neste exemplo, utiliza-se o `AudioDataStream` construtor para obter um fluxo do resultado.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = synthesizer.speak_text_async("Getting the response as an in-memory stream.").get()
stream = AudioDataStream(result)
```

A partir daqui pode implementar qualquer `stream` comportamento personalizado usando o objeto resultante.

## <a name="customize-audio-format"></a>Personalizar formato de áudio

A seguinte secção mostra como personalizar atributos de saída de áudio, incluindo:

* Tipo de ficheiro de áudio
* Taxa de amostra
* Profundidade bit

Para alterar o formato `set_speech_synthesis_output_format()` áudio, `SpeechConfig` utiliza a função no objeto. Esta função `enum` espera [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python)um tipo de tipo , que utiliza para selecionar o formato de saída. Consulte os médicos de referência para obter uma [lista de formatos áudio](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python) disponíveis.

Existem várias opções para diferentes tipos de ficheiros, dependendo dos seus requisitos. Note que, por definição, formatos brutos como `Raw24Khz16BitMonoPcm` não incluem cabeçalhos de áudio. Utilize formatos crus apenas quando souber que a sua implementação a jusante pode descodificar um bitstream bruto, ou se planeia construir cabeçalhos manualmente baseados em profundidade bitada, taxa de amostra, número de canais, etc.

Neste exemplo, especifice um formato `Riff24Khz16BitMonoPcm` RIFF `SpeechSynthesisOutputFormat` de `SpeechConfig` alta fidelidade, definindo o objeto no objeto. Semelhante ao exemplo na secção anterior, utiliza-se [`AudioDataStream`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?view=azure-python) para obter um fluxo de memória do resultado e, em seguida, escrevê-lo num ficheiro.


```python
speech_config.set_speech_synthesis_output_format(SpeechSynthesisOutputFormat["Riff24Khz16BitMonoPcm"])
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

result = synthesizer.speak_text_async("Customizing audio output format.").get()
stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

Executar o seu programa novamente escreverá um ficheiro personalizado `.wav` para o caminho especificado.

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

Em seguida, você precisa alterar o pedido de síntese da fala para fazer referência ao seu ficheiro XML. O pedido é maioritariamente o `speak_text_async()` mesmo, mas `speak_ssml_async()`em vez de utilizar a função, utiliza-se . Esta função espera uma corda XML, por isso lê-se pela primeira vez o seu config SSML como uma corda. A partir daqui, o objeto de resultados é exatamente o mesmo que exemplos anteriores.

> [!NOTE]
> Se `ssml_string` o `ï»¿` seu contiver no início da corda, terá de despir o formato BOM ou o serviço devolverá um erro. Faça-o definindo `encoding` o parâmetro `open("ssml.xml", "r", encoding="utf-8-sig")`da seguinte forma: .

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

ssml_string = open("ssml.xml", "r").read()
result = synthesizer.speak_ssml_async(ssml_string).get()

stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
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
