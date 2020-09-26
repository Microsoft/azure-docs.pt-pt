---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/20/2020
ms.author: trbye
ms.openlocfilehash: 9365b4bbf6e6fb37156b6e9f24b923591945ea52
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376344"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de discurso azul](../../../../overview.md#try-the-speech-service-for-free)
> * [Crie uma aplicação LUIS e obtenha uma chave de ponto final](../../../../quickstarts/create-luis.md)
> * [Configurar o seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="support-and-updates"></a>Suporte e atualizações

As atualizações do pacote Speech SDK Python são distribuídas via PyPI e anunciadas nas [notas de lançamento](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Se estiver disponível uma nova versão, pode atualizá-la com o comando `pip install --upgrade azure-cognitiveservices-speech` .
Verifique qual a versão que está atualmente instalada inspecionando a `azure.cognitiveservices.speech.__version__` variável.

Se tiver algum problema ou se estiver a perder uma funcionalidade, consulte as [opções de Apoio e ajuda](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Crie uma aplicação Python que utilize o SDK de discurso

### <a name="run-the-sample"></a>Executar o exemplo

Pode copiar o código de [amostra](#sample-code) deste arranque rápido para um ficheiro de origem `quickstart.py` e executá-lo no seu IDE ou na consola:

```Bash
python quickstart.py
```

Ou você pode baixar este tutorial de arranque rápido como um caderno [Jupyter](https://jupyter.org) do [repositório de amostras do Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) e executá-lo como um caderno.

### <a name="sample-code"></a>Código de exemplo

> [!NOTE]
> O SDK de discurso não reconhecerá a utilização do linguístico para a língua, consulte especificar a [língua de origem para falar para texto](../../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.

```python
import azure.cognitiveservices.speech as speechsdk

# Creates an instance of a speech config with specified subscription key and service region.
# Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.audio.AudioConfig(filename=audio_filename)

# Creates a recognizer with the given settings
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)

print("Recognizing first result...")

# Starts speech recognition, and returns after a single utterance is recognized. The end of a
# single utterance is determined by listening for silence at the end or until a maximum of 15
# seconds of audio is processed.  The task returns the recognition text as result. 
# Note: Since recognize_once() returns only a single utterance, it is suitable only for single
# shot recognition like command or query. 
# For long-running multi-utterance recognition, use start_continuous_recognition() instead.
result = speech_recognizer.recognize_once()

# Checks result.
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

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instale e utilize o Speech SDK com código de estúdio visual

1. Faça o download e instale uma versão de 64 bits de [Python](https://www.python.org/downloads/), 3.5 a 3.8, no seu computador.
1. Transfira e instale o [Visual Studio Code](https://code.visualstudio.com/Download).
1. Abra o Código do Estúdio Visual e instale a extensão Python. Selecione **File**  >  **extensões de preferências**de  >  **ficheiros** no menu. Procure **por Python.**

   ![Instale a extensão Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Crie uma pasta para armazenar o projeto. Um exemplo é utilizar o Windows Explorer.
1. No Código do Estúdio Visual, selecione o ícone **'Ficheiro'.** Em seguida, abra a pasta que criou.

   ![Abrir uma pasta](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Crie um novo ficheiro de origem Python, `speechsdk.py` selecionando o novo ícone de ficheiro.

   ![Criar um ficheiro](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Copie, cole e guarde o [código Python](#sample-code) para o ficheiro recém-criado.
1. Insira as informações de subscrição do serviço Desema.
1. Se selecionado, um intérprete Python aparece no lado esquerdo da barra de estado na parte inferior da janela.
   Caso contrário, traga uma lista de intérpretes python disponíveis. Abra a paleta de comando <kbd>Ctrl+Shift+P</kbd> e introduza **Python: Selecione Intérprete**. Escolha um apropriado.
1. Pode instalar o pacote Speech SDK Python a partir do Código do Estúdio Visual. Faça isso se ainda não estiver instalado para o intérprete Python que selecionou.
   Para instalar o pacote Speech SDK, abra um terminal. Volte a trazer a paleta de comando <kbd>Ctrl+Shift+P</kbd> e introduza **terminal: Criar novo terminal integrado**.
   No terminal que abre, insira o comando `python -m pip install azure-cognitiveservices-speech` ou o comando apropriado para o seu sistema.
1. Para executar o código de amostra, clique com o botão direito em algum lugar dentro do editor. Selecione **executar o ficheiro Python no terminal**.
   Os primeiros 15 segundos de entrada de fala do seu ficheiro áudio serão reconhecidos e registados na janela da consola.

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

Se tiver problemas seguindo estas instruções, consulte o tutorial mais extenso [do Código do Estúdio Visual Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]