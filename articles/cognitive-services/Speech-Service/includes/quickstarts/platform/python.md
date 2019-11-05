---
title: 'Início rápido: SDK de fala para instalação da plataforma Python – serviço de fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para configurar sua plataforma para usar o Python com o SDK dos serviços de fala.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/09/2019
ms.author: erhopf
ms.openlocfilehash: d4a81c6dd2b44efd432345627e78bb69fff1688d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502430"
---
Este guia mostra como instalar o [SDK de fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para Python.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

- O pacote do SDK de fala do Python está disponível para estes sistemas operacionais:
  - Windows: x64 e x86
  - Mac: macOS X versão 10,12 ou posterior
  - Linux: Ubuntu 16, 4, Ubuntu 18, 4, Debian 9 em x64

## <a name="prerequisites"></a>Pré-requisitos

- As plataformas Linux com suporte exigirão determinadas bibliotecas instaladas (`libssl` para suporte do Secure Sockets Layer e `libasound2` para suporte a som). Consulte a distribuição abaixo para os comandos necessários para instalar as versões corretas dessas bibliotecas.

  - No Ubuntu, execute os seguintes comandos para instalar os pacotes necessários:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - No Debian 9, execute os seguintes comandos para instalar os pacotes necessários:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

- No Windows, você precisa do [Microsoft Visual C++ redistribuível para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para sua plataforma. Observe que a instalação desta pela primeira vez pode exigir que você reinicie o Windows antes de continuar com este guia.
- E, finalmente, você precisará do [Python 3,5 ou posterior](https://www.python.org/downloads/). Para verificar sua instalação, abra um prompt de comando e digite o comando `python --version` e verifique o resultado. Se ele estiver instalado corretamente, você receberá uma resposta "Python 3.5.1" ou semelhante.

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Instalar o SDK de fala usando Visual Studio Code

1. Baixe e instale a versão mais recente com suporte do [Python](https://www.python.org/downloads/) para sua plataforma, 3,5 ou posterior.
   - Os usuários do Windows têm a certeza de selecionar "Adicionar Python ao seu caminho" durante o processo de instalação.
1. Baixe e instale o [Visual Studio Code](https://code.visualstudio.com/Download).
1. Abra Visual Studio Code e instale a extensão do Python. Selecione **arquivo** > **preferências** > **extensões** no menu. Procure **Python** e clique em **instalar**.

   ![Instalar a extensão do Python](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. Além disso, de dentro do Visual Studio Code, instale o pacote Python do SDK de fala da linha de comando integrada:
   1. Abrir um terminal (nos menus suspensos, **terminal** > **novo terminal**)
   1. No terminal que é aberto, insira o comando `python -m pip install azure-cognitiveservices-speech`

É isso, você está pronto para começar a codificar o SDK de fala no Python e pode passar para [as próximas etapas](#next-steps) abaixo. Se você for novo no Visual Studio Code, consulte a [documentação de Visual Studio Code](https://code.visualstudio.com/docs)mais extensiva. Para obter mais informações sobre Visual Studio Code e Python, consulte [Visual Studio Code tutorial do Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="install-the-speech-sdk-using-the-command-line"></a>Instalar o SDK de fala usando a linha de comando

Se você não estiver usando Visual Studio Code, o comando a seguir instalará o pacote do Python do [PyPI](https://pypi.org/) para o SDK de fala. Para os usuários de Visual Studio Code, pule para a próxima subseção.

```sh
pip install azure-cognitiveservices-speech
```

Se você estiver no macOS, talvez seja necessário executar o comando a seguir para obter o comando `pip` acima para funcionar:

```sh
python3 -m pip install --upgrade pip
```

Depois de usar o `pip` com êxito para instalar o `azure-cognitiveservices-speech`, você pode usar o SDK de fala importando o namespace para seus projetos do Python. Por exemplo:

```py
import azure.cognitiveservices.speech as speechsdk
```

Isso é mostrado mais detalhadamente nos exemplos de código listados nas [próximas etapas](#next-steps) abaixo.

## <a name="support-and-updates"></a>Suporte e atualizações

As atualizações do pacote do kit de fala do SDK do Speech são distribuídas por meio do PyPI e anunciadas nas [notas de versão](~/articles/cognitive-services/speech-service/releasenotes.md).
Se uma nova versão estiver disponível, você poderá atualizá-la com o comando `pip install --upgrade azure-cognitiveservices-speech`.
Verifique qual versão está instalada no momento inspecionando a variável `azure.cognitiveservices.speech.__version__`.

Se você tiver um problema ou se não tiver um recurso, consulte [Opções de suporte e ajuda](~/articles/cognitive-services/speech-service/support.md).

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [windows](../quickstart-list.md)]