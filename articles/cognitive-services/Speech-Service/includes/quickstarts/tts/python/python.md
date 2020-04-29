---
title: 'Quickstart: Discurso sintetizador, Python - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Aprenda a sintetizar o discurso em Python usando o SDK do Discurso
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: e6daa0aaaca283dec11937d961886f95f1972b49
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400368"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:

> [!div class="checklist"]
> * [Criar um recurso de fala azure](../../../../get-started.md)
> * [Crie o seu ambiente de desenvolvimento e crie um projeto vazio](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)

## <a name="support-and-updates"></a>Suporte e atualizações

As atualizações do pacote Speech SDK Python são distribuídas via PyPI e anunciadas nas notas de [lançamento.](~/articles/cognitive-services/Speech-Service/releasenotes.md)
Se uma nova versão estiver disponível, pode `pip install --upgrade azure-cognitiveservices-speech`atualizá-la com o comando .
Verifique qual a versão atualmente instalada `azure.cognitiveservices.speech.__version__` inspecionando a variável.

Se tiver algum problema, ou se estiver a perder uma funcionalidade, consulte [opções](~/articles/cognitive-services/Speech-Service/support.md)de Suporte e Ajuda .

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Crie uma aplicação Python que use o SDK de Fala

### <a name="run-the-sample"></a>Executar o exemplo

Pode copiar o código da [amostra](#sample-code) a `quickstart.py` partir deste quickstart para um ficheiro fonte e executá-lo no iDE ou na consola:

```sh
python quickstart.py
```

Ou pode descarregar este tutorial de início rápido como um caderno [Jupyter](https://jupyter.org) do [repositório de amostras Speech SDK](https://aka.ms/csspeech/samples) e executá-lo como um caderno.

### <a name="sample-code"></a>Código de exemplo

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/text-to-speech/quickstart.py#code)]

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
   Caso contrário, enumere uma lista de intérpretes python disponíveis. Abra a paleta de comando (Ctrl+Shift+P) e **introduza Python: Select Interpreter**. Escolha um apropriado.
1. Pode instalar o pacote Speech SDK Python a partir do Código do Estúdio Visual. Faça isso se ainda não estiver instalado para o intérprete Python que selecionou.
   Para instalar o pacote Speech SDK, abra um terminal. Volte a levantar a paleta de comando (Ctrl+Shift+P) e introduza o **Terminal: Criar novo terminal integrado**.
   No terminal que se abre, `python -m pip install azure-cognitiveservices-speech` introduza o comando ou o comando apropriado para o seu sistema.
1. Para executar o código da amostra, clique em algum lugar dentro do editor. Selecione **Executar ficheiro Python no terminal**.
   Escreva uma mensagem quando for solicitada. O áudio sintetizado é reproduzido pouco depois.

   ![Executar uma amostra](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run-tts.png)

Se tiver problemas seguindo estas instruções, consulte o tutorial de Código Python do [Estúdio Visual](https://code.visualstudio.com/docs/python/python-tutorial)mais extenso.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Consulte também

- [Criar uma voz personalizada](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Gravar amostras de voz personalizadas](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
