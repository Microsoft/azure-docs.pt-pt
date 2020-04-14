---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: dapine
ms.openlocfilehash: 34d9dc2bdf99737e6b4c17abe37a18f795c19df8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274763"
---
## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de subscrição Azure para o serviço De Fala. [Arranja um de graça.](~/articles/cognitive-services/Speech-Service/get-started.md)
* [Python 3.5 a 3.8](https://www.python.org/downloads/).
* O pacote Python Speech SDK está disponível para estes sistemas operativos:
    * Janelas: x64 e x86.
    * Mac: macOS X versão 10.12 ou posterior.
    * Linux: Ubuntu 16.04/18.04, Debian 9, RHEL 7/8, CentOS 7/8 em x64.
* No Linux, execute estes comandos para instalar os pacotes necessários:

# <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debiano 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel--centos-8"></a>[RHEL / CentOS 8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl python3
```

> [!NOTE]
> Em RHEL/CentOS 7/8, siga as instruções sobre como configurar o [OpenSSL para Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

---

* No Windows, é necessário o [Microsoft Visual C++ Redistribuável para o Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para a sua plataforma.

## <a name="install-the-speech-sdk"></a>Instale o SDK do Discurso

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Este comando instala o pacote Python da [PyPI](https://pypi.org/) para o SDK de Fala:

```Bash
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Suporte e atualizações

As atualizações do pacote Speech SDK Python são distribuídas via PyPI e anunciadas nas notas de [lançamento.](~/articles/cognitive-services/Speech-Service/releasenotes.md)
Se uma nova versão estiver disponível, pode `pip install --upgrade azure-cognitiveservices-speech`atualizá-la com o comando .
Verifique qual a versão atualmente instalada `azure.cognitiveservices.speech.__version__` inspecionando a variável.

Se tiver algum problema, ou se estiver a perder uma funcionalidade, consulte [opções](~/articles/cognitive-services/Speech-Service/support.md)de Suporte e Ajuda .

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Crie uma aplicação Python que use o SDK de Fala

### <a name="run-the-sample"></a>Executar o exemplo

Pode copiar o código da [amostra](#sample-code) a `quickstart.py` partir deste quickstart para um ficheiro fonte e executá-lo no iDE ou na consola:

```Bash
python quickstart.py
```

Ou pode descarregar este tutorial de início rápido como um caderno [Jupyter](https://jupyter.org) do [repositório de amostras Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) e executá-lo como um caderno.

### <a name="sample-code"></a>Código de exemplo

````python
import azure.cognitiveservices.speech as speechsdk

# Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "helloworld.wav"
audio_output = speechsdk.audio.AudioOutputConfig(filename=audio_filename)

# Creates a synthesizer with the given settings
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_output)

# Synthesizes the text to speech.
# Replace with your own text.
text = "Hello world!"
result = speech_synthesizer.speak_text_async(text).get()

# Checks result.
if result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
    print("Speech synthesized to [{}] for text [{}]".format(audio_filename, text))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech synthesis canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        if cancellation_details.error_details:
            print("Error details: {}".format(cancellation_details.error_details))
    print("Did you update the subscription info?")
````

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instale e use o SDK de Fala com código de estúdio visual

1. Descarregue e instale uma versão de 64 bits de [Python](https://www.python.org/downloads/), 3.5 a 3.8, no seu computador.
1. Descarregue e instale o Código do [Estúdio Visual.](https://code.visualstudio.com/Download)
1. Abra o Código do Estúdio Visual e instale a extensão Python. Selecione**extensões** de**preferências** > de **ficheiro** > sacar do menu. Pesquisa por **Python.**

   ![Instale a extensão Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Crie uma pasta para armazenar o projeto. Um exemplo é através da utilização do Windows Explorer.
1. No Código do Estúdio Visual, selecione o ícone **do Ficheiro.** Em seguida, abra a pasta que criou.

   ![Abra uma pasta](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Crie um novo `speechsdk.py`ficheiro de origem Python, selecionando o novo ícone de ficheiro.

   ![Criar um ficheiro](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Copie, cole e guarde o [código Python](#sample-code) para o ficheiro recém-criado.
1. Insira as informações de subscrição do serviço Speech.
1. Se selecionado, um intérprete Python exibe no lado esquerdo da barra de estado na parte inferior da janela.
   Caso contrário, enumere uma lista de intérpretes python disponíveis. Abra a paleta de comando<kbd>(Ctrl+Shift+P</kbd>) e **introduza Python: Select Interpreter**. Escolha um apropriado.
1. Pode instalar o pacote Speech SDK Python a partir do Código do Estúdio Visual. Faça isso se ainda não estiver instalado para o intérprete Python que selecionou.
   Para instalar o pacote Speech SDK, abra um terminal. Volte a levantar a paleta de comando<kbd>(Ctrl+Shift+P</kbd>) e introduza o **Terminal: Criar novo terminal integrado**.
   No terminal que se abre, `python -m pip install azure-cognitiveservices-speech` introduza o comando ou o comando apropriado para o seu sistema.
1. Para executar o código da amostra, clique em algum lugar dentro do editor. Selecione **Executar ficheiro Python no terminal**.
   O seu texto é convertido em fala e guardado nos dados áudio especificados.

   ```console
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

Se tiver problemas seguindo estas instruções, consulte o tutorial de Código Python do [Estúdio Visual](https://code.visualstudio.com/docs/python/python-tutorial)mais extenso.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Gravar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
