---
title: 'Início rápido: Reconhecer a conversão de voz, Python - serviços de voz'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para criar uma aplicação de consola de voz em texto que utiliza o SDK de voz para Python. Quando terminar, pode utilizar o microfone do seu computador para converter voz em texto em tempo real.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: chlandsi
ms.openlocfilehash: 1464d1ca8fcf1ad5a955d69aa76caec9aa568dad
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886122"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-python"></a>Início rápido: Reconhecer a conversão de voz com o SDK de voz para Python

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Este artigo mostra como utilizar os serviços de voz através do SDK de voz para Python. Ela ilustra como reconhecer voz da entrada do microfone.

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de subscrição do Azure para os serviços de voz. [Obter uma gratuitamente](get-started.md).
* [Python 3.5 ou posterior](https://www.python.org/downloads/).
* O pacote do Python SDK de voz está disponível para estes sistemas operativos:
    * Windows: x64 e x86.
    * MAC: versão do macOS X 10.12 ou posterior.
    * Linux: Ubuntu 16.04 ou 18.04 em x64.
* No Ubuntu, execute estes comandos para instalar os pacotes necessários:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libasound2 wget
  ```

* No Windows, também terá do [Microsoft Visual C++ Redistributable para Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para a sua plataforma.

## <a name="install-the-speech-sdk"></a>Instalar o SDK de voz

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Este comando instala o pacote do Python partir [PyPI](https://pypi.org/) para o SDK de voz:

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Atualizações e suporte

Atualizações para o pacote do Python SDK de voz são distribuídas por meio de PyPI e anunciadas no [notas de versão](./releasenotes.md).
Se uma nova versão estiver disponível, pode atualizar para o mesmo com o comando `pip install --upgrade azure-cognitiveservices-speech`.
Verifique qual é a versão está atualmente instalada ao inspecionar o `azure.cognitiveservices.speech.__version__` variável.

Se tem um problema ou não tem uma funcionalidade, consulte [opções de suporte e ajuda](./support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Criar uma aplicação de Python que utiliza o SDK de voz

### <a name="run-the-sample"></a>Executar o exemplo

Pode copiar o [código de exemplo](#sample-code) este início rápido para um ficheiro de origem `quickstart.py` e executá-la no seu IDE ou na consola do:

```sh
python quickstart.py
```

Ou pode baixar este tutorial de início rápido, como um [Jupyter](https://jupyter.org) bloco de notas do [repositório de exemplo do SDK de voz](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) e executá-lo como um bloco de notas.

### <a name="sample-code"></a>Código de exemplo

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalar e utilizar o SDK de voz com o Visual Studio Code

1. Transferir e instalar uma versão de 64 bits do [Python](https://www.python.org/downloads/), 3.5 ou posterior, no seu computador.
1. Transfira e instale [Visual Studio Code](https://code.visualstudio.com/Download).
1. Abra o Visual Studio Code e instalar a extensão de Python. Selecione **arquivo** > **preferências** > **extensões** no menu. Procure **Python**.

   ![Instalar a extensão de Python](media/sdk/qs-python-vscode-python-extension.png)

1. Crie uma pasta para armazenar o projeto no. Um exemplo é através do Explorador do Windows.
1. No Visual Studio Code, selecione o **ficheiro** ícone. Em seguida, abra a pasta que criou.

   ![Abrir uma pasta](media/sdk/qs-python-vscode-python-open-folder.png)

1. Crie um novo ficheiro de origem do Python, `speechsdk.py`, ao selecionar o ícone de ficheiro novo.

   ![Criar um ficheiro](media/sdk/qs-python-vscode-python-newfile.png)

1. Copiar, colar e guardar a [código de Python](#sample-code) para o ficheiro recentemente criado.
1. Insira suas informações de subscrição de serviços de voz.
1. Se selecionado, apresenta um interpretador de Python no lado esquerdo da barra de status na parte inferior da janela.
   Caso contrário, apresentada uma lista de disponíveis interprety de Python. Abra a paleta de comandos (Ctrl + Shift + P) e introduza **Python: Selecione o interpretador**. Escolha um método adequado.
1. Pode instalar o pacote de Python SDK de voz de dentro do Visual Studio Code. Opte por fazê-lo se não estiver instalado, mas para o interpretador de Python que selecionou.
   Para instalar o pacote do SDK de voz, abra um terminal. Abra novamente a paleta de comandos (Ctrl + Shift + P) e introduza **Terminal: Criar novo Terminal integrado**.
   No terminal que se abre, introduza o comando `python -m pip install azure-cognitiveservices-speech` ou o comando adequado para o seu sistema.
1. Para executar o código de exemplo, com o botão direito em algum lugar dentro do editor. Selecione **execute o ficheiro de Python no Terminal**.
   Quando lhe for pedido, dizer algumas palavras. Texto transcrito apresenta um pouco mais tarde.

   ![Executar um exemplo](media/sdk/qs-python-vscode-python-run.png)

Se tiver problemas a seguir estas instruções, consulte a mais extensa [tutorial do Python de código do Visual Studio](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Explorar amostras de Python no GitHub](https://aka.ms/csspeech/samples)
