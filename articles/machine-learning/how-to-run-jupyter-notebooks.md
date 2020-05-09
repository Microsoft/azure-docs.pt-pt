---
title: Como executar cadernos jupyter no seu espaço de trabalho
titleSuffix: Azure Machine Learning
description: Saiba como executar um Caderno Jupyter sem deixar o seu espaço de trabalho no estúdio Azure Machine Learning.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 67da2cb31d59838bb3ad2b964530d85d8be9be4c
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783659"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>Como executar cadernos jupyter no seu espaço de trabalho
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a executar os seus Cadernos Jupyter diretamente no seu espaço de trabalho no estúdio Azure Machine Learning. Enquanto pode lançar [jupyter](https://jupyter.org/) ou [JupyterLab,](https://jupyterlab.readthedocs.io)também pode editar e executar os seus cadernos sem sair do espaço de trabalho.

Veja como pode:

* Crie cadernos jupyter no seu espaço de trabalho
* Executar uma experiência a partir de um caderno
* Mude o ambiente do caderno
* Encontre detalhes das instâncias computadas usadas para executar os seus cadernos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* Um espaço de trabalho de Aprendizagem automática. Consulte Criar um espaço de [trabalho de aprendizagem automática Azure](how-to-manage-workspace.md).

## <a name="create-notebooks"></a><a name="create"></a>Criar cadernos

No seu espaço de trabalho Azure Machine Learning, crie um novo caderno Jupyter e comece a trabalhar. O caderno recém-criado é armazenado no armazenamento padrão do espaço de trabalho. Este caderno pode ser partilhado com qualquer pessoa com acesso ao espaço de trabalho. 

Para criar um novo caderno: 

1. Abra o seu espaço de trabalho no [estúdio Azure Machine Learning.](https://ml.azure.com)
1. Do lado esquerdo, selecione **Cadernos**. 
1. Selecione o novo ícone de **ficheiro Seleção** acima da lista **Ficheiros do Utilizador** na secção Meus **ficheiros.**

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Criar novo ficheiro":::

1. Diga o nome do ficheiro. 
1. Para Ficheiros De Caderno Jupyter, selecione **Python Notebook** como o tipo de ficheiro.
1. Selecione um diretório de ficheiros.
1. Selecione **Criar**.

> [!TIP]
> Também pode criar ficheiros de texto.  Selecione **Texto** como o tipo de ficheiro e adicione a extensão ao nome (por exemplo, myfile.py ou myfile.txt)  

Também pode carregar pastas e ficheiros, incluindo cadernos, com as ferramentas no topo da página dos Cadernos.  Os cadernos e a maioria dos tipos de ficheiros de texto exibem na secção de pré-visualização.  Não existe pré-visualização para a maioria dos outros tipos de ficheiros.

### <a name="clone-samples"></a>Amostras de clone

O seu espaço de trabalho contém uma pasta **de Amostras** com cadernos projetados para ajudá-lo a explorar o SDK e servir como exemplos para os seus próprios projetos de aprendizagem automática.  Pode clonar estes cadernos na sua própria pasta no seu recipiente de armazenamento de espaço de trabalho.  

Por exemplo, consulte [Tutorial: Crie a sua primeira experiência ML](tutorial-1st-experiment-sdk-setup.md#azure).

### <a name="a-nameterminal-use-files-from-git-and-version-my-files"></a><a name="terminal">Use ficheiros do Git e verie os meus ficheiros

Pode aceder a todas as operações da Git utilizando uma janela terminal. Todos os ficheiros e pastas Git serão armazenados no seu sistema de ficheiros espaço de trabalho.

> [!NOTE]
> Adicione os seus ficheiros e pastas em qualquer lugar sob a pasta **~/cloudfiles/code/Users** para que fiquem visíveis em todos os seus ambientes Jupyter.

Para aceder ao terminal:

1. Abra o seu espaço de trabalho no [estúdio Azure Machine Learning.](https://ml.azure.com)
1. Do lado esquerdo, selecione **Cadernos**.
1. Selecione qualquer caderno localizado na secção **de ficheiros do Utilizador** no lado esquerdo.  Se não tiver cadernos lá, primeiro [crie um caderno](#create)
1. Selecione um alvo Compute ou **crie** um novo e aguarde até que esteja em funcionamento.
1. Selecione o ícone do **terminal Aberto.**

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="Terminal aberto":::

1. Se não vir o ícone, selecione o **...** à direita do alvo da computação e, em seguida, selecione **o terminal Aberto** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Terminal aberto de ...":::


Saiba mais sobre a clonagem de [repositórios Git no seu sistema](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)de ficheiros espaço de trabalho .


### <a name="share-notebooks-and-other-files"></a>Partilhar cadernos e outros ficheiros

Copie e cole o URL para partilhar um caderno ou ficheiro.  Apenas outros utilizadores do espaço de trabalho poderão aceder a este URL.  Saiba mais sobre a concessão de [acesso ao seu espaço de trabalho.](how-to-assign-roles.md)

## <a name="edit-a-notebook"></a>Editar um caderno

Para editar um caderno, abra qualquer caderno localizado na secção **de ficheiros do Utilizador** do seu espaço de trabalho. Clique na célula que deseja editar. 

Quando uma instância de cálculo está em execução, também pode usar a conclusão do código, alimentada pelo [Intellisense,](https://code.visualstudio.com/docs/editor/intellisense)em qualquer Caderno Python.

Também pode lançar jupyter ou JupyterLab a partir da barra de ferramentas do Caderno.  O Azure Machine Learning não fornece atualizações e corrige bugs da Jupyter ou do JupyterLab, uma vez que são produtos Open Source fora dos limites do Microsoft Support.

### <a name="useful-keyboard-shortcuts"></a>Atalhos de teclado úteis

|Teclado  |Ação  |
|---------|---------|
|Shift+Enter     |  Executar uma célula       |
|Ctrl+M (Janelas)     |  Ativar/desativar a captura do separador no caderno.       |
|Ctrl+Shift+M (Mac & Linux)     |    Ativar/desativar a captura do separador no caderno.     |
|Separador (quando a armadilha do separador ativada) | Adicione um personagem '\t' (travessão)
|Separador (quando a armadilha do separador estiver desativada) | Mude o foco para o próximo item focalizável (eliminar o botão de telemóvel, executar o botão, etc.)

## <a name="delete-a-notebook"></a>Apagar um caderno

*Não pode* apagar os cadernos **samples.**  Estes cadernos fazem parte do estúdio e são atualizados cada vez que um novo SDK é publicado.  

*Pode* eliminar os ficheiros **do Utilizador** de qualquer uma destas formas:

* No estúdio, selecione o **...** no final de uma pasta ou ficheiro.  Certifique-se de utilizar um navegador suportado (Microsoft Edge, Chrome ou Firefox).
* A partir de qualquer barra de ferramentas Portátil, selecione [**terminal aberto**](#terminal) para aceder à janela do terminal para a instância de cálculo.
* Em Jupyter ou JupyterLab com as suas ferramentas.

## <a name="run-an-experiment"></a>Fazer uma experiência

Para executar uma experiência a partir de um Caderno, você primeiro se conecta a uma instância de computação em [execução](concept-compute-instance.md). Se não tiver uma instância de cálculo, use estes passos para criar um: 

1. Selecione **+** na barra de ferramentas do Caderno. 
2. Nomeie o Compute e escolha um **tamanho de máquina virtual**. 
3. Selecione **Criar**.
4. A instância computacional está ligada automaticamente ao Caderno e agora pode executar as suas células.

Só você pode ver e usar as instâncias computadas que cria.  Os seus **ficheiros User** são armazenados separadamente do VM e são partilhados entre todos os casos de computação no espaço de trabalho.

### <a name="view-logs-and-output"></a>Ver registos e saída

Utilize [widgets de Notebook](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) para ver o progresso da execução e dos registos. Um widget é assíncrono e fornece atualizações até que o treino termine. Os widgets Azure Machine Learning também são suportados no Jupyter e no JupterLab.

## <a name="change-the-notebook-environment"></a>Mude o ambiente do caderno

A barra de ferramentas Do Portátil permite alterar o ambiente em que o seu Caderno funciona.  

Estas ações não alterarão o estado do caderno ou os valores de quaisquer variáveis no caderno:

|Ação  |Resultado  |
|---------|---------| --------|
|Pare o núcleo     |  Detém qualquer célula de corrida. A execução de uma célula reiniciará automaticamente o núcleo. |
|Navegue para outra secção do espaço de trabalho     |     As células de funcionamento estão paradas. |

Estas ações vão repor o estado do caderno e redefinirão todas as variáveis no caderno.

|Ação  |Resultado  |
|---------|---------| --------|
| Mude o núcleo | Caderno usa novo núcleo |
| Comute compute    |     O notebook utiliza automaticamente o novo cálculo. |
| Reset compute | Começa de novo quando se tenta gerir uma célula |
| Parar a computação     |    Nenhuma célula vai funcionar  |
| Caderno aberto em Jupyter ou JupyterLab     |    O caderno abriu num novo separador.  |

### <a name="add-new-kernels"></a>Adicione novos núcleos

O Caderno encontrará automaticamente todos os núcleos de Jupyter instalados na instância de cálculo conectada.  Para adicionar um núcleo à instância computacional:

1. [**Selecione terminal aberto**](#terminal) na barra de ferramentas do Caderno.
1. Utilize a janela do terminal para criar um novo ambiente.
1. Ative o ambiente.  Por exemplo, `newenv`após a criação:

    ```shell
    source activate newenv
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Qualquer um dos [Núcleos Jupyter disponíveis](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) pode ser instalado.

### <a name="status-indicators"></a>Indicadores de estado

Um indicador ao lado da queda do **Compute** mostra o seu estado.  O estatuto também é mostrado na própria queda.  

|Cor |Estado do cálculo |
|---------|---------| 
| Verde | Corrida computacional |
| Vermelho |A computação falhou | 
| Preto | A computação parou |
|  Azul claro |Criação de computação, início, reinício, configuração |
|  Cinzento |Apagando computação, parando |

Um indicador ao lado da queda do **Kernel** mostra o seu estado.

|Cor |Estado do kernel |
|---------|---------|
|  Verde |Kernel conectado, ocioso, ocupado|
|  Cinzento |Kernel não ligado |

## <a name="find-compute-details"></a>Encontre detalhes computacionais 

Encontre detalhes sobre os seus casos de computação na página **Compute** em [estúdio](https://ml.azure.com).

## <a name="next-steps"></a>Passos seguintes

* [Executa a sua primeira experiência](tutorial-1st-experiment-sdk-train.md)
* [Faça backup do armazenamento de ficheiros com instantâneos](../storage/files/storage-snapshots-files.md)
