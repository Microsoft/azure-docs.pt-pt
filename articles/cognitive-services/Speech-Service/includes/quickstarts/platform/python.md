---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: a7e47ebd9695b57163aacdfa5faf3ad82fc7e12d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "85805966"
---
Este guia mostra como instalar o [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) para Python. Se só quer que o nome do pacote seja iniciado por conta própria, `pip install azure-cognitiveservices-speech` corra.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

- O pacote Python Speech SDK está disponível para estes sistemas operativos:
  - Janelas: x64 e x86
  - Mac: macOS X versão 10.12 ou mais tarde
  - Linux: Ubuntu 16.04/18.04, Debian 9, RHEL 7/8, CentOS 7/8 em x64

## <a name="prerequisites"></a>Pré-requisitos

- As plataformas de Linux suportadas exigirão a instalação de certas bibliotecas `libssl` (para suporte de camadas de tomadas seguras e `libasound2` para suporte sonoro). Consulte a sua distribuição abaixo para obter os comandos necessários para instalar as versões corretas destas bibliotecas.

  - Em Ubuntu, executar os seguintes comandos para instalar os pacotes necessários:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

  - Em Debian 9, executar os seguintes comandos para instalar os pacotes necessários:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.2 libasound2
    ```

  - No RHEL/CentOS, executar os seguintes comandos para instalar os pacotes necessários:

    ```sh
    sudo yum update
    sudo yum install alsa-lib openssl python3
    ```

> [!NOTE]
> - No RHEL/CentOS 7, siga as instruções sobre [como configurar RHEL/CentOS 7 para A SDK de fala](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - No RHEL/CentOS 8, siga as instruções sobre como configurar o [OpenSSL para o Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- No Windows, precisa do [Microsoft Visual C++ Redistributable para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para a sua plataforma. Tenha em atenção que a instalação deste facto pela primeira vez poderá exigir que reinicie o Windows antes de continuar com este guia.
- E, finalmente, vai precisar de [Python 3,5 a 3,8](https://www.python.org/downloads/). Para verificar a sua instalação, abra uma solicitação de comando e digite o comando `python --version` e verifique o resultado. Se estiver instalado corretamente, terá uma resposta "Python 3.5.1" ou similar.

## <a name="install-the-speech-sdk-from-pypi"></a>Instalar o SDK de discurso a partir de PyPI

Se estiver a utilizar o seu próprio ambiente ou a construir ferramentas, executar o seguinte comando para instalar o SDK de fala a partir de [PyPI](https://pypi.org/). Para os utilizadores do Código do Estúdio Visual, salte para a sub-secção seguinte para instalação guiada.

```sh
pip install azure-cognitiveservices-speech
```

Se estiver no macOS, poderá ter de executar o seguinte comando para que o `pip` comando acima funcione:

```sh
python3 -m pip install --upgrade pip
```

Uma vez utilizado com sucesso `pip` para `azure-cognitiveservices-speech` instalar, pode utilizar o Speech SDK importando o espaço de nome para os seus projetos Python.

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Instale o Speech SDK utilizando o Código do Estúdio Visual

1. Descarregue e instale a versão mais recente suportada do [Python](https://www.python.org/downloads/) para a sua plataforma, de 3.5 a 3.8.
   - Os utilizadores do Windows certifiquem-se de selecionar "Adicionar Python ao seu PATH" durante o processo de instalação.
1. Transfira e instale o [Visual Studio Code](https://code.visualstudio.com/Download).
1. Abra o Código do Estúdio Visual e instale a extensão Python. Selecione **File**  >  **extensões de preferências**de  >  **ficheiros** no menu. Procure **por Python** e clique **em Instalar.**

   ![Instale a extensão Python](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. Também a partir do Código do Estúdio Visual, instale o pacote Speech SDK Python a partir da linha de comando integrada:
   1. Abra um terminal (a partir dos menus suspensos, **Terminal**  >  **Novo Terminal)**
   1. No terminal que abre, entre no comando `python -m pip install azure-cognitiveservices-speech`

Se é novo no Código do Estúdio Visual, consulte a [documentação](https://code.visualstudio.com/docs)mais extensa do Código do Estúdio Visual . Para mais informações sobre Visual Studio Code e Python, consulte [o tutorial visual Studio Code Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="support-and-updates"></a>Suporte e atualizações

As atualizações do pacote Speech SDK Python são distribuídas via PyPI e anunciadas nas [notas de lançamento](~/articles/cognitive-services/speech-service/releasenotes.md).
Se estiver disponível uma nova versão, pode atualizá-la com o comando `pip install --upgrade azure-cognitiveservices-speech` .
Verifique qual a versão que está atualmente instalada inspecionando a `azure.cognitiveservices.speech.__version__` variável.

Se tiver algum problema ou se estiver a perder uma funcionalidade, consulte as [opções de Apoio e ajuda](~/articles/cognitive-services/speech-service/support.md).

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [windows](../quickstart-list.md)]
