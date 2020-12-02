---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 21216625ed99a595b0ceeeea90e0cd00ce447498
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509179"
---
Uma das características centrais do serviço de fala é a capacidade de reconhecer e transcrever a fala humana (muitas vezes referida como discurso-a-texto). Neste arranque rápido, aprende-se a usar o Speech SDK nas suas apps e produtos para realizar conversão de fala a texto de alta qualidade.

## <a name="skip-to-samples-on-github"></a>Salte para as amostras no GitHub

Se quiser saltar diretamente para o código de amostra, consulte as [amostras de arranque rápido](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python) da Python no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume:

* Tem uma conta Azure e subscrição do serviço de voz. Se não tiver, conta e subscrição -- [Experimente o serviço de Voz gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-and-import-the-speech-sdk"></a>Instale e importe o Speech SDK

Antes de poder fazer qualquer coisa, terá de instalar o SDK do Discurso.

```Python
pip install azure-cognitiveservices-speech
```

Se estiver no macOS e tiver problemas de instalação, poderá ter de executar este comando primeiro.

```Python
python3 -m pip install --upgrade pip
```

Depois da instalação do Speech SDK, importe-o para o seu projeto Python.

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>Criar uma configuração de discurso

Para ligar para o serviço de discurso usando o SDK de discurso, precisa de criar um [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) . Esta classe inclui informações sobre a sua subscrição, como a sua chave e região associada, ponto final, anfitrião ou token de autorização. Crie uma [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) utilização da sua chave e região. Consulte as [teclas e a](../../../overview.md#find-keys-and-region) página da região para encontrar o seu par de regiões-chave.

```Python
speech_config = speechsdk.SpeechConfig(subscription="<paste-your-subscription-key>", region="<paste-your-region>")
```

Há algumas outras formas de inicializar [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) uma:

* Com um ponto final: passe num ponto final de serviço de discurso. Uma chave ou sinal de autorização é opcional.
* Com um anfitrião: passe em um endereço de anfitrião. Uma chave ou sinal de autorização é opcional.
* Com um sinal de autorização: passe em um token de autorização e na região associada.

> [!NOTE]
> Independentemente de estar a realizar reconhecimento de voz, síntese de fala, tradução ou reconhecimento de intenções, irá sempre criar uma configuração.

## <a name="recognize-from-microphone"></a>Reconhecer a partir do microfone

Para reconhecer a fala usando o microfone do seu dispositivo, basta criar um `SpeechRecognizer` sem passar um , e passar o seu `AudioConfig` `speech_config` .

```Python
import azure.cognitiveservices.speech as speechsdk

def from_mic():
    speech_config = speechsdk.SpeechConfig(subscription="<paste-your-subscription-key>", region="<paste-your-region>")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
    
    print("Speak into your microphone.")
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

from_mic()
```

Se pretender utilizar um dispositivo de entrada de áudio *específico,* tem de especificar o ID do dispositivo num `AudioConfig` , e passá-lo para o `SpeechRecognizer` param do `audio_config` construtor. Saiba [como obter o ID do dispositivo](../../../how-to-select-audio-input-devices.md) para o seu dispositivo de entrada de áudio.

## <a name="recognize-from-file"></a>Reconhecer a partir de arquivo

Se quiser reconhecer a fala a partir de um ficheiro áudio em vez de utilizar um microfone, crie um [`AudioConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?preserve-view=true&view=azure-python) e utilize o `filename` parâmetro.

```Python
import azure.cognitiveservices.speech as speechsdk

def from_file():
    speech_config = speechsdk.SpeechConfig(subscription="<paste-your-subscription-key>", region="<paste-your-region>")
    audio_input = speechsdk.AudioConfig(filename="your_file_name.wav")
    speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
    
    result = speech_recognizer.recognize_once_async().get()
    print(result.text)

from_file()
```

## <a name="error-handling"></a>Processamento de erros

Os exemplos anteriores simplesmente obtêm o texto reconhecido `result.text` de , mas para lidar com erros e outras respostas, terá de escrever algum código para lidar com o resultado. O seguinte código avalia o [`result.reason`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason?view=azure-python) imóvel e:

* Imprime o resultado do reconhecimento: `speechsdk.ResultReason.RecognizedSpeech`
* Se não houver correspondência de reconhecimento, informe o utilizador: `speechsdk.ResultReason.NoMatch `
* Se for encontrado um erro, imprima a mensagem de erro: `speechsdk.ResultReason.Canceled`

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

## <a name="continuous-recognition"></a>Reconhecimento contínuo

Os exemplos anteriores usam o reconhecimento de um único tiro, que reconhece uma única expressão. O fim de uma única expressão é determinado ouvindo o silêncio no final ou até que um máximo de 15 segundos de áudio seja processado.

Em contrapartida, o reconhecimento contínuo é utilizado quando se pretende **controlar** quando se deve deixar de reconhecer. Requer que se conecte ao `EventSignal` para obter os resultados de reconhecimento, e para parar o reconhecimento, deve chamar [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) ou [stop_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--). Aqui está um exemplo de como o reconhecimento contínuo é realizado num ficheiro de entrada de áudio.

Comecemos por definir a entrada e inicializar [`SpeechRecognizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?preserve-view=true&view=azure-python) um:

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Em seguida, vamos criar uma variável para gerir o estado do reconhecimento da fala. Para começar, vamos definir isto para `False` , uma vez que no início do reconhecimento podemos assumir com segurança que não está terminado.

```Python
done = False
```

Agora, vamos criar uma chamada para parar o reconhecimento contínuo quando um `evt` é recebido. Há algumas coisas a ter em mente.

* Quando um `evt` é recebido, a `evt` mensagem é impressa.
* Depois de `evt` recebido, [stop_continuous_recognition é](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) chamado a parar o reconhecimento.
* O estado de reconhecimento é alterado para `True` .

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

Esta amostra de código mostra como ligar as chamadas a eventos enviados a partir do [`SpeechRecognizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#start-continuous-recognition--) .

* [`recognizing`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognizing): Sinal para eventos que contenham resultados intermédios de reconhecimento.
* [`recognized`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognized): Sinal para eventos que contenham resultados finais de reconhecimento (indicando uma tentativa de reconhecimento bem sucedida).
* [`session_started`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-started): Sinal para eventos que indiquem o início de uma sessão de reconhecimento (operação).
* [`session_stopped`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped): Sinal para eventos que indiquem o fim de uma sessão de reconhecimento (operação).
* [`canceled`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#canceled): Sinal para eventos que contenham resultados de reconhecimento cancelados (indicando uma tentativa de reconhecimento que foi cancelada em resultado ou um pedido de cancelamento direto ou, em alternativa, uma falha de transporte ou protocolo).

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

Com tudo configurado, podemos chamar [start_continuous_recognition()](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped).

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>Modo de ditado

Ao utilizar o reconhecimento contínuo, pode ativar o processamento de ditados utilizando a função correspondente de "ativar o ditado". Este modo fará com que a instância config da fala interprete descrições de palavras de estruturas de frases como a pontuação. Por exemplo, a expressão "Vives no ponto de interrogação da cidade" seria interpretada como o texto "Vives na cidade?".

Para ativar o modo de ditado, utilize o [`enable_dictation()`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--) método no seu [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python) .

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>Alterar linguagem de origem

Uma tarefa comum para o reconhecimento da fala é especificar a linguagem de entrada (ou origem). Vamos ver como mudaria a língua de entrada para alemão. No seu código, encontre o seu SpeechConfig e, em seguida, adicione esta linha diretamente abaixo.

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-recognition-language) é um parâmetro que toma uma corda como argumento. Pode fornecer qualquer valor na lista de [locais/idiomas suportados.](../../../language-support.md)

## <a name="improve-recognition-accuracy"></a>Melhorar a precisão do reconhecimento

Existem algumas formas de melhorar a precisão do reconhecimento com o SDK do discurso. Vamos ver as Listas de Frases. As listas de frases são usadas para identificar frases conhecidas em dados áudio, como o nome de uma pessoa ou uma localização específica. Palavras simples ou frases completas podem ser adicionadas a uma Lista de Frases. Durante o reconhecimento, é utilizada uma entrada numa lista de frases se for incluída no áudio uma correspondência exata para toda a frase. Se não for encontrado um jogo exato com a frase, o reconhecimento não é assistido.

> [!IMPORTANT]
> A funcionalidade Lista de Frases só está disponível em inglês.

Para utilizar uma lista de frases, primeiro crie um [`PhraseListGrammar`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?preserve-view=true&view=azure-python) objeto e, em seguida, adicione palavras e frases específicas com [`addPhrase`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python#addphrase-phrase--str-) .

Quaisquer alterações que [`PhraseListGrammar`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?preserve-view=true&view=azure-python) entrem em vigor no próximo reconhecimento ou após uma reconexão ao serviço Discurso.

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

Se precisar de limpar a sua lista de frases: 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Outras opções para melhorar a precisão do reconhecimento

As listas de frases são apenas uma opção para melhorar a precisão do reconhecimento. Também pode: 

* [Melhorar a precisão com Discurso Personalizado](../../../custom-speech-overview.md)
* [Melhorar a precisão com modelos de inquilinos](../../../tutorial-tenant-model.md)