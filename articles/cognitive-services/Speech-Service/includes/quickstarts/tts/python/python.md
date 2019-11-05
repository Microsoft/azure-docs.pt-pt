---
title: 'Início rápido: sintetizar fala, serviço de fala em Python'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar a fala em Python usando o SDK de fala
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/14/2019
ms.author: yulili
ms.openlocfilehash: 36e48a5d513daa951c1e92017ba9a2322b009703
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503410"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md)
```

## Support and updates

Updates to the Speech SDK Python package are distributed via PyPI and announced in the [Release notes](~/articles/cognitive-services/Speech-Service/releasenotes.md).
If a new version is available, you can update to it with the command `pip install --upgrade azure-cognitiveservices-speech`.
Check which version is currently installed by inspecting the `azure.cognitiveservices.speech.__version__` variable.

If you have a problem, or you're missing a feature, see [Support and help options](~/articles/cognitive-services/Speech-Service/support.md).

## Create a Python application that uses the Speech SDK

### Run the sample

You can copy the [sample code](#sample-code) from this quickstart to a source file `quickstart.py` and run it in your IDE or in the console:

```sh
python quickstart.py
```

Ou você pode baixar este tutorial de início rápido como um notebook [Jupyter](https://jupyter.org) do [repositório de exemplo do SDK de fala](https://aka.ms/csspeech/samples) e executá-lo como um bloco de anotações.

### <a name="sample-code"></a>Código de exemplo

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/text-to-speech/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalar e usar o SDK de fala com Visual Studio Code

1. Baixe e instale uma versão de 64 bits do [Python](https://www.python.org/downloads/), 3,5 ou posterior em seu computador.
1. Baixe e instale o [Visual Studio Code](https://code.visualstudio.com/Download).
1. Abra Visual Studio Code e instale a extensão do Python. Selecione **arquivo** > **preferências** > **extensões** no menu. Procure por **Python**.

   ![Instalar a extensão do Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Crie uma pasta na qual armazenar o projeto. Um exemplo é o uso do Windows Explorer.
1. Em Visual Studio Code, selecione o ícone **arquivo** . Em seguida, abra a pasta que você criou.

   ![Abrir uma pasta](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Crie um novo arquivo de origem Python, `speechsdk.py`, selecionando o ícone novo arquivo.

   ![Criar um ficheiro](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Copie, Cole e salve o [código Python](#sample-code) no arquivo recém-criado.
1. Insira as informações de assinatura dos serviços de fala.
1. Se selecionado, um intérprete do Python é exibido no lado esquerdo da barra de status na parte inferior da janela.
   Caso contrário, traga uma lista de intérpretes de Python disponíveis. Abra a paleta de comandos (Ctrl + Shift + P) e digite **Python: selecionar intérprete**. Escolha um apropriado.
1. Você pode instalar o pacote Python do SDK de fala de dentro do Visual Studio Code. Faça isso se ele ainda não estiver instalado para o intérprete do Python selecionado.
   Para instalar o pacote do SDK de fala, abra um terminal. Ative a paleta de comandos novamente (Ctrl + Shift + P) e insira **terminal: criar novo terminal integrado**.
   No terminal que é aberto, insira o comando `python -m pip install azure-cognitiveservices-speech` ou o comando apropriado para seu sistema.
1. Para executar o código de exemplo, clique com o botão direito do mouse em algum lugar dentro do editor. Selecione **Executar arquivo Python no terminal**.
   Digite algum texto quando você for solicitado. O áudio sintetizado é reproduzido logo em seguida.

   ![Executar um exemplo](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run-tts.png)

Se você tiver problemas ao seguir essas instruções, consulte o tutorial mais extensivo [Visual Studio Code Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Consultar também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
