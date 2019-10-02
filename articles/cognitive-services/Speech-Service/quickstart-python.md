---
title: 'Início rápido: Reconhecer fala, serviço de fala em Python'
titleSuffix: Azure Cognitive Services
description: Use este guia para criar um aplicativo de console de conversão de fala para texto que usa o SDK de fala para Python. Quando terminar, pode utilizar o microfone do seu computador para converter voz em texto em tempo real.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 3f473e8ad15d3df053cca100580574b1b115f492
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803262"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-python"></a>Início rápido: Reconhecer a fala com o SDK de fala para Python

Os guias de início rápido também estão disponíveis para [fala-síntese](quickstart-text-to-speech-python.md)

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Este artigo mostra como usar os serviços de fala por meio do SDK de fala para Python. Ele ilustra como reconhecer a fala da entrada do microfone.

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de assinatura do Azure para os serviços de fala. [Obtenha um gratuitamente](get-started.md).
* [Python 3,5 ou posterior](https://www.python.org/downloads/).
* O pacote do SDK de fala do Python está disponível para estes sistemas operacionais:
    * Windows: x64 e x86.
    * Mac: macOS X versão 10,12 ou posterior.
    * Linux: Ubuntu 16, 4, Ubuntu 18, 4, Debian 9 em x64.
* No Linux, execute estes comandos para instalar os pacotes necessários:

  * no Ubuntu:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

  * No Debian 9:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.2 libasound2
    ```

* No Windows, você precisa do [Microsoft Visual C++ redistribuível para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para sua plataforma.

## <a name="install-the-speech-sdk"></a>Instalar o SDK de fala

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Este comando instala o pacote do Python do [PyPI](https://pypi.org/) para o SDK de fala:

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Suporte e atualizações

As atualizações do pacote do kit de fala do SDK do Speech são distribuídas por meio do PyPI e anunciadas nas [notas de versão](./releasenotes.md).
Se uma nova versão estiver disponível, você poderá atualizá-la com o comando `pip install --upgrade azure-cognitiveservices-speech`.
Verifique qual versão está instalada no momento inspecionando a variável `azure.cognitiveservices.speech.__version__`.

Se você tiver um problema ou se não tiver um recurso, consulte [Opções de suporte e ajuda](./support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Criar um aplicativo Python que usa o SDK de fala

### <a name="run-the-sample"></a>Executar o exemplo

Você pode copiar o [código de exemplo](#sample-code) deste guia de início rápido para um arquivo de origem `quickstart.py` e executá-lo em seu IDE ou no console do:

```sh
python quickstart.py
```

Ou você pode baixar este tutorial de início rápido como um notebook [Jupyter](https://jupyter.org) do [repositório de exemplo do SDK de fala](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) e executá-lo como um bloco de anotações.

### <a name="sample-code"></a>Código de exemplo

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalar e usar o SDK de fala com Visual Studio Code

1. Baixe e instale uma versão de 64 bits do [Python](https://www.python.org/downloads/), 3,5 ou posterior em seu computador.
1. Baixe e instale o [Visual Studio Code](https://code.visualstudio.com/Download).
1. Abra Visual Studio Code e instale a extensão do Python. Selecione **arquivo** > **preferências** > **extensões** no menu. Procure por **Python**.

   ![Instalar a extensão do Python](media/sdk/qs-python-vscode-python-extension.png)

1. Crie uma pasta na qual armazenar o projeto. Um exemplo é o uso do Windows Explorer.
1. Em Visual Studio Code, selecione o ícone **arquivo** . Em seguida, abra a pasta que você criou.

   ![Abrir uma pasta](media/sdk/qs-python-vscode-python-open-folder.png)

1. Crie um novo arquivo de origem Python, `speechsdk.py`, selecionando o ícone novo arquivo.

   ![Criar um ficheiro](media/sdk/qs-python-vscode-python-newfile.png)

1. Copie, Cole e salve o [código Python](#sample-code) no arquivo recém-criado.
1. Insira as informações de assinatura dos serviços de fala.
1. Se selecionado, um intérprete do Python é exibido no lado esquerdo da barra de status na parte inferior da janela.
   Caso contrário, traga uma lista de intérpretes de Python disponíveis. Abra a paleta de comandos (Ctrl + Shift + P) e insira **Python: Selecione intérprete**. Escolha um apropriado.
1. Você pode instalar o pacote Python do SDK de fala de dentro do Visual Studio Code. Faça isso se ele ainda não estiver instalado para o intérprete do Python selecionado.
   Para instalar o pacote do SDK de fala, abra um terminal. Ative a paleta de comandos novamente (Ctrl + Shift + P) e insira **Terminal: Crie um novo terminal integrado @ no__t-0.
   No terminal que é aberto, insira o comando `python -m pip install azure-cognitiveservices-speech` ou o comando apropriado para seu sistema.
1. Para executar o código de exemplo, clique com o botão direito do mouse em algum lugar dentro do editor. Selecione **Executar arquivo Python no terminal**.
   Fale algumas palavras quando você for solicitado. O texto transcrita é exibido logo em seguida.

   ![Executar um exemplo](media/sdk/qs-python-vscode-python-run.png)

Se você tiver problemas ao seguir essas instruções, consulte o tutorial mais extensivo [Visual Studio Code Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore os exemplos do Python no GitHub](https://aka.ms/csspeech/samples)
