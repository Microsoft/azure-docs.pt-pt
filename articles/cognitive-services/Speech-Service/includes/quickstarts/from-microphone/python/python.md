---
title: 'Início rápido: reconhecer a fala de um microfone, serviço de fala em Python'
titleSuffix: Azure Cognitive Services
description: Use este guia para criar um aplicativo de console de conversão de fala para texto que usa o SDK de fala para Python. Quando terminar, pode utilizar o microfone do seu computador para converter voz em texto em tempo real.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 086a77a9d7096870b8d381d301bfc0f31ab390f9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467293"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md)
> * [Criar um projeto de exemplo vazio](../../../../quickstarts/create-project.md)
> * Verifique se você tem acesso a um microfone para captura de áudio

## <a name="support-and-updates"></a>Suporte e atualizações

As atualizações do pacote do kit de fala do SDK do Speech são distribuídas por meio do PyPI e anunciadas nas [notas de versão](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Se uma nova versão estiver disponível, você poderá atualizá-la com o comando `pip install --upgrade azure-cognitiveservices-speech`.
Verifique qual versão está instalada no momento inspecionando a variável `azure.cognitiveservices.speech.__version__`.

Se você tiver um problema ou se não tiver um recurso, consulte [Opções de suporte e ajuda](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Criar um aplicativo Python que usa o SDK de fala

### <a name="run-the-sample"></a>Executar o exemplo

Você pode copiar o [código de exemplo](#sample-code) deste guia de início rápido para um arquivo de origem `quickstart.py` e executá-lo no IDE ou no console do:

```sh
python quickstart.py
```

Ou você pode baixar este tutorial de início rápido como um notebook [Jupyter](https://jupyter.org) do [repositório de exemplo do SDK de fala](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) e executá-lo como um bloco de anotações.

### <a name="sample-code"></a>Código de exemplo

> [!NOTE]
> O SDK de fala usará como padrão o reconhecimento do uso de en-US para a linguagem, consulte [especificar o idioma de origem de fala para texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

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
1. Insira as informações de assinatura do serviço de fala.
1. Se selecionado, um intérprete do Python é exibido no lado esquerdo da barra de status na parte inferior da janela.
   Caso contrário, traga uma lista de intérpretes de Python disponíveis. Abra a paleta de comandos (Ctrl + Shift + P) e digite **Python: selecionar intérprete**. Escolha um apropriado.
1. Você pode instalar o pacote Python do SDK de fala de dentro do Visual Studio Code. Faça isso se ele ainda não estiver instalado para o intérprete do Python selecionado.
   Para instalar o pacote do SDK de fala, abra um terminal. Ative a paleta de comandos novamente (Ctrl + Shift + P) e insira **terminal: criar novo terminal integrado**.
   No terminal que é aberto, insira o comando `python -m pip install azure-cognitiveservices-speech` ou o comando apropriado para seu sistema.
1. Para executar o código de exemplo, clique com o botão direito do mouse em algum lugar dentro do editor. Selecione **Executar arquivo Python no terminal**.
   Fale algumas palavras quando você for solicitado. O texto transcrita é exibido logo em seguida.

   ![Executar um exemplo](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run.png)

Se você tiver problemas ao seguir essas instruções, consulte o tutorial mais extensivo [Visual Studio Code Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
