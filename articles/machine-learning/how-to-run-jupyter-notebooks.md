---
title: Como executar os Jupyter Notebooks na área de trabalho
titleSuffix: Azure Machine Learning
description: Saiba como gerir um Jupyter Notebook sem deixar o seu espaço de trabalho no estúdio Azure Machine Learning.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 04/21/2020
ms.openlocfilehash: b8869eee4e44001f5d4aeafbbdb32f93f0a7e0c8
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433329"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace-preview"></a>Como executar Cadernos Jupyter no seu espaço de trabalho (pré-visualização)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a executar os seus Cadernos Jupyter diretamente no seu espaço de trabalho no estúdio Azure Machine Learning. Enquanto pode lançar [Jupyter](https://jupyter.org/) ou [JupyterLab,](https://jupyterlab.readthedocs.io)também pode editar e executar os seus cadernos sem sair do espaço de trabalho.

Veja como pode:

* Crie cadernos Jupyter no seu espaço de trabalho
* Executar uma experiência a partir de um caderno
* Alterar o ambiente do caderno
* Encontre detalhes das instâncias computacional usadas para executar os seus cadernos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* Um espaço de trabalho de aprendizagem automática. Ver [Criar um espaço de trabalho de aprendizagem de máquinas Azure](how-to-manage-workspace.md).

## <a name="create-notebooks"></a><a name="create"></a>Criar cadernos

No seu espaço de trabalho Azure Machine Learning, crie um novo caderno Jupyter e comece a trabalhar. O caderno recém-criado é armazenado no armazenamento padrão do espaço de trabalho. Este caderno pode ser partilhado com qualquer pessoa com acesso ao espaço de trabalho. 

Para criar um novo caderno: 

1. Abra o seu espaço de trabalho no [estúdio Azure Machine Learning.](https://ml.azure.com)
1. Do lado esquerdo, selecione **Cadernos**. 
1. Selecione o novo ícone **de ficheiro** acima da lista **Ficheiros do utilizador** na secção **'Os meus ficheiros'.**

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Criar novo ficheiro":::

1. Diga o nome do ficheiro. 
1. Para ficheiros de cadernos Jupyter, selecione **Python Notebook** como o tipo de ficheiro.
1. Selecione um diretório de ficheiros.
1. Selecione **Criar**.

> [!TIP]
> Também pode criar ficheiros de texto.  Selecione **Texto** como o tipo de ficheiro e adicione a extensão ao nome (por exemplo, myfile.py ou myfile.txt)  

Também pode carregar pastas e ficheiros, incluindo cadernos, com as ferramentas no topo da página De Cadernos.  Os blocos de notas e a maioria dos tipos de ficheiros de texto são apresentados na secção de pré-visualização.  Não está disponível nenhuma pré-visualização para a maioria dos outros tipos de ficheiros.

### <a name="clone-samples"></a>Amostras de clones

O seu espaço de trabalho contém uma pasta **Samples** com cadernos concebidos para o ajudar a explorar o SDK e servir como exemplos para os seus próprios projetos de aprendizagem automática.  Pode clonar estes cadernos na sua própria pasta no seu recipiente de armazenamento de espaço de trabalho.  

Por exemplo, consulte [Tutorial: Crie a sua primeira experiência ML](tutorial-1st-experiment-sdk-setup.md#azure).

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a>Use ficheiros do Git e veri os meus ficheiros

Pode aceder a todas as operações do Git utilizando uma janela terminal. Todos os ficheiros e pastas Git serão armazenados no seu sistema de ficheiros do espaço de trabalho.

> [!NOTE]
> Adicione os seus ficheiros e pastas em qualquer lugar sob a pasta **~/cloudfiles/código/Utilizadores** para que fiquem visíveis em todos os seus ambientes Jupyter.

Para aceder ao terminal:

1. Abra o seu espaço de trabalho no [estúdio Azure Machine Learning.](https://ml.azure.com)
1. Do lado esquerdo, selecione **Cadernos**.
1. Selecione qualquer caderno localizado na secção **de ficheiros do Utilizador** no lado esquerdo.  Se não tem nenhum caderno lá, primeiro [crie um caderno](#create)
1. Selecione um alvo **compute** ou crie um novo e espere até que esteja em funcionamento.
1. Selecione o ícone **do terminal Aberto.**

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="Terminal aberto":::

1. Se não vir o ícone, selecione o **...** à direita do alvo do cálculo e, em seguida, selecione **Open terminal** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Terminal aberto de ...":::


Saiba mais sobre [a clonagem de repositórios de Git no seu sistema de ficheiros de espaço de trabalho.](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)


### <a name="share-notebooks-and-other-files"></a>Partilhar cadernos e outros ficheiros

Copie e cole o URL para partilhar um caderno ou ficheiro.  Apenas outros utilizadores do espaço de trabalho poderão aceder a este URL.  Saiba mais sobre [a concessão de acesso ao seu espaço de trabalho.](how-to-assign-roles.md)

## <a name="edit-a-notebook"></a>Editar um caderno

Para editar um bloco de notas, abra qualquer caderno localizado na secção de **ficheiros** do Utilizador do seu espaço de trabalho. Clique na célula que deseja editar. 

Quando uma instância computacional está em execução, também pode utilizar a conclusão de código, alimentada pelo [Intellisense,](https://code.visualstudio.com/docs/editor/intellisense)em qualquer Notebook Python.

Também pode lançar Jupyter ou JupyterLab a partir da barra de ferramentas Notebook.  O Azure Machine Learning não fornece atualizações e corre bugs do Jupyter ou do JupyterLab, uma vez que são produtos Open Source fora do limite do Microsoft Support.

### <a name="useful-keyboard-shortcuts"></a>Atalhos de teclado úteis

|Teclado  |Ação  |
|---------|---------|
|Shift+Enter     |  Executar uma célula       |
|Ctrl+M(Janelas)     |  Ativar/desativar a aba de armadilhagem no caderno.       |
|Ctrl+Shift+M(Mac & Linux)     |    Ativar/desativar a aba de armadilhagem no caderno.     |
|Separador (quando a patilha estiver ativada) | Adicione um caráter '\t' (travessão)
|Separador (quando a patilha é desativada) | Mude o foco para o próximo item focalizável (apagar botão de célula, botão de funcionação, etc.)

## <a name="delete-a-notebook"></a>Apagar um caderno

*Não pode* apagar os cadernos **samples.**  Estes cadernos fazem parte do estúdio e são atualizados cada vez que um novo SDK é publicado.  

*Pode* eliminar **os ficheiros do Utilizador** de qualquer forma:

* No estúdio, selecione o **...** no final de uma pasta ou arquivo.  Certifique-se de que utiliza um browser suportado (Microsoft Edge, Chrome ou Firefox).
* A partir de qualquer barra de ferramentas Portátil, selecione [**Open terminal**](#terminal) para aceder à janela do terminal para a instância computacional.
* No Jupyter ou no JupyterLab com as suas ferramentas.

## <a name="run-an-experiment"></a>Fazer uma experiência

Para executar uma experiência a partir de um Caderno, você primeiro se conecta a uma [instância de cálculo de execução](concept-compute-instance.md). Se não tiver uma instância computacional, use estes passos para criar um: 

1. Selecione **+** na barra de ferramentas Do Notebook. 
2. Nomeie o Compute e escolha um **tamanho de máquina virtual.** 
3. Selecione **Criar**.
4. A instância computacional está ligada automaticamente ao Caderno e agora pode executar as suas células.

Só você pode ver e usar as instâncias de cálculo que cria.  Os **ficheiros do** utilizador são armazenados separadamente do VM e são partilhados entre todas as instâncias computacional no espaço de trabalho.

### <a name="view-logs-and-output"></a>Ver registos e saída

Utilize [widgets notebook](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) para ver o progresso da execução e dos registos. Um widget é assíncronoso e fornece atualizações até que o treino termine. Os widgets Azure Machine Learning também são suportados no Jupyter e no JupterLab.

## <a name="change-the-notebook-environment"></a>Alterar o ambiente do caderno

A barra de ferramentas Portátil permite-lhe alterar o ambiente em que funciona o seu Caderno.  

Estas ações não alterarão o estado do caderno nem os valores de quaisquer variáveis no caderno:

|Ação  |Resultado  |
|---------|---------| --------|
|Pare o núcleo     |  Para qualquer cela de corrida. Executar uma célula reiniciará automaticamente o núcleo. |
|Navegue para outra secção de espaço de trabalho     |     As celas de corrida estão paradas. |

Estas ações irão redefinir o estado do caderno e redefinirão todas as variáveis no caderno.

|Ação  |Resultado  |
|---------|---------| --------|
| Mude o núcleo | Notebook usa novo núcleo |
| Computação switch    |     O Notebook utiliza automaticamente o novo computação. |
| Reset compute | Começa de novo quando se tenta gerir uma célula |
| Parar o cálculo     |    Nenhuma célula vai correr  |
| Notebook aberto em Jupyter ou JupyterLab     |    O caderno abriu com uma nova conta.  |

### <a name="add-new-kernels"></a>Adicione novos núcleos

O Notebook irá automaticamente encontrar todos os núcleos jupyter instalados na instância de computação conectada.  Para adicionar um núcleo à instância de cálculo:

1. Selecione [**o terminal aberto**](#terminal) na barra de ferramentas do Bloco de Notas.
1. Utilize a janela do terminal para criar um novo ambiente.
1. Ative o ambiente.  Por exemplo, depois de `newenv` criar:

    ```shell
    source activate newenv
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Qualquer um dos [Jupyter Kernels disponíveis](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) pode ser instalado.

### <a name="status-indicators"></a>Indicadores de estado

Um indicador ao lado da queda do **Compute** mostra o seu estado.  O estado também é mostrado na própria queda.  

|Cor |Estado do cálculo |
|---------|---------| 
| Verde | Execução de cálculo |
| Vermelho |O cálculo falhou | 
| Preto | Computo parou |
|  Azul claro |Criar, iniciar, reiniciar, configurar |
|  Cinzento |Computação eliminando, parando |

Um indicador ao lado da queda do **Kernel** mostra o seu estado.

|Cor |Estatuto de kernel |
|---------|---------|
|  Verde |Kernel conectado, ocioso, ocupado|
|  Cinzento |Kernel não ligado |

## <a name="find-compute-details"></a>Encontre detalhes do cálculo 

Encontre detalhes sobre as suas instâncias de computação na página **compute** em [estúdio](https://ml.azure.com).

## <a name="next-steps"></a>Passos seguintes

* [Executar a sua primeira experiência](tutorial-1st-experiment-sdk-train.md)
* [Faça backup do armazenamento do seu ficheiro com instantâneos](../storage/files/storage-snapshots-files.md)
