---
title: 'Quickstart: Reconheça o discurso de um microfone, Python - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Utilize este guia para criar uma aplicação de consola de voz a texto que utilize o SDK de Fala para Python. Quando terminar, pode utilizar o microfone do seu computador para converter voz em texto em tempo real.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: a2d5614aeeedb88cd9b2c41baed317761443d17c
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925832"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md)
> * Certifique-se de que tem acesso a um microfone para captura de áudio

## <a name="support-and-updates"></a>Suporte e atualizações

As atualizações do pacote Speech SDK Python são distribuídas via PyPI e anunciadas nas notas de [lançamento.](~/articles/cognitive-services/Speech-Service/releasenotes.md)
Se uma nova versão estiver disponível, pode atualizá-la com o comando `pip install --upgrade azure-cognitiveservices-speech`.
Verifique qual a versão atualmente instalada inspecionando a variável `azure.cognitiveservices.speech.__version__`.

Se tiver algum problema, ou se estiver a perder uma funcionalidade, consulte [opções](~/articles/cognitive-services/Speech-Service/support.md)de Suporte e Ajuda .

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Crie uma aplicação Python que use o SDK de Fala

### <a name="run-the-sample"></a>Executar o exemplo

Pode copiar o código da [amostra](#sample-code) a partir deste quickstart para um ficheiro fonte `quickstart.py` e executá-lo no iDE ou na consola:

```sh
python quickstart.py
```

Ou pode descarregar este tutorial de início rápido como um caderno [Jupyter](https://jupyter.org) do [repositório de amostras Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) e executá-lo como um caderno.

### <a name="sample-code"></a>Código de exemplo

> [!NOTE]
> O SDK do Discurso não irá reconhecer o uso de en-us para a língua, consulte [especificar a linguagem fonte para a fala a texto](../../../../how-to-specify-source-language.md) para obter informações sobre a escolha da língua de origem.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instale e use o SDK de Fala com código de estúdio visual

1. Descarregue e instale uma versão de 64 bits de [Python](https://www.python.org/downloads/), 3.5 a 3.8, no seu computador.
1. Descarregue e instale o Código do [Estúdio Visual.](https://code.visualstudio.com/Download)
1. Abra o Código do Estúdio Visual e instale a extensão Python. Selecione **File** > **Preferências** > **Extensões** do menu. Pesquisa por **Python.**

   ![Instale a extensão Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Crie uma pasta para armazenar o projeto. Um exemplo é através da utilização do Windows Explorer.
1. No Código do Estúdio Visual, selecione o ícone **do Ficheiro.** Em seguida, abra a pasta que criou.

   ![Abra uma pasta](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Crie um novo ficheiro de origem Python, `speechsdk.py`, selecionando o novo ícone de ficheiro.

   ![Criar um ficheiro](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Copie, cole e guarde o [código Python](#sample-code) para o ficheiro recém-criado.
1. Insira as informações de subscrição do serviço Speech.
1. Se selecionado, um intérprete Python exibe no lado esquerdo da barra de estado na parte inferior da janela.
   Caso contrário, enumere uma lista de intérpretes python disponíveis. Abra a paleta de comando (Ctrl+Shift+P) e **introduza Python: Select Interpreter**. Escolha um apropriado.
1. Pode instalar o pacote Speech SDK Python a partir do Código do Estúdio Visual. Faça isso se ainda não estiver instalado para o intérprete Python que selecionou.
   Para instalar o pacote Speech SDK, abra um terminal. Volte a levantar a paleta de comando (Ctrl+Shift+P) e introduza o **Terminal: Criar novo terminal integrado**.
   No terminal que se abre, introduza o comando `python -m pip install azure-cognitiveservices-speech` ou o comando adequado para o seu sistema.
1. Para executar o código da amostra, clique em algum lugar dentro do editor. Selecione **Executar ficheiro Python no terminal**.
   Diga algumas palavras quando for solicitado. O texto transcrito mostra pouco depois.

   ![Executar uma amostra](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run.png)

Se tiver problemas seguindo estas instruções, consulte o tutorial de Código Python do [Estúdio Visual](https://code.visualstudio.com/docs/python/python-tutorial)mais extenso.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [footer](./footer.md)]
