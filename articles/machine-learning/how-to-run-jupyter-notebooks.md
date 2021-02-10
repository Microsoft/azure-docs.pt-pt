---
title: Executar cadernos Jupyter no seu espaço de trabalho
titleSuffix: Azure Machine Learning
description: Saiba como executar um caderno Jupyter sem deixar o seu espaço de trabalho no estúdio Azure Machine Learning.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 01/19/2021
ms.openlocfilehash: 06ae46eb96db39f44cd052e6e9b0d1a19f898007
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100091573"
---
# <a name="run-jupyter-notebooks-in-your-workspace"></a>Executar cadernos Jupyter no seu espaço de trabalho

Aprenda a executar os seus cadernos Jupyter diretamente no seu espaço de trabalho no estúdio Azure Machine Learning. Enquanto pode lançar [Jupyter](https://jupyter.org/) ou [JupyterLab,](https://jupyterlab.readthedocs.io)também pode editar e executar os seus cadernos sem sair do espaço de trabalho.

Para obter informações sobre como criar e gerir ficheiros, incluindo cadernos, consulte [Criar e gerir ficheiros no seu espaço de trabalho.](how-to-manage-files.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* Um espaço de trabalho de aprendizagem automática. Ver [Criar um espaço de trabalho de aprendizagem de máquinas Azure](how-to-manage-workspace.md).

## <a name="edit-a-notebook"></a>Editar um caderno

Para editar um bloco de notas, abra qualquer caderno localizado na secção de **ficheiros** do Utilizador do seu espaço de trabalho. Clique na célula que deseja editar.  Se não tiver cadernos nesta secção, consulte [Criar e gerir ficheiros no seu espaço de trabalho.](how-to-manage-files.md)

Pode editar o caderno sem se ligar a uma instância de computação.  Quando pretender executar as células no caderno, selecione ou crie uma instância de computação.  Se selecionar uma instância de computação parada, começará automaticamente quando executar a primeira célula.

Quando uma instância computacional está em execução, também pode utilizar a conclusão de código, alimentada pelo [Intellisense,](https://code.visualstudio.com/docs/editor/intellisense)em qualquer caderno Python.

Também pode lançar Jupyter ou JupyterLab a partir da barra de ferramentas do portátil.  O Azure Machine Learning não fornece atualizações e corre bugs do Jupyter ou do JupyterLab, uma vez que são produtos Open Source fora do limite do Microsoft Support.

## <a name="focus-mode"></a>Modo de detalhe

Utilize o modo de focagem para expandir a sua visão atual para que possa concentrar-se nos separadores ativos. O modo focus esconde o explorador de ficheiros Notebooks.

1. Na barra de ferramentas da janela do terminal, selecione **o modo Focus** para ligar o modo de focagem. Dependendo da largura da janela, este pode estar localizado sob o item do **menu na** sua barra de ferramentas.
1. Enquanto está no modo de focagem, volte à vista padrão selecionando a **vista Standard**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Modo de foco de alternação / visão padrão":::

## <a name="use-intellisense"></a>Utilizar o IntelliSense

[O IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) é um auxílio de conclusão de código que inclui uma série de funcionalidades: Membros da Lista, Informações de Parâmetros, Informações Rápidas e Palavra Completa. Estas funcionalidades ajudam-no a aprender mais sobre o código que está a usar, a acompanhar os parâmetros que está a digitar e a adicionar chamadas a propriedades e métodos com apenas algumas teclas.  

Ao escrever código, utilize ctrl+Space para ativar o IntelliSense.

## <a name="clean-your-notebook-preview"></a>Limpe o seu caderno (pré-visualização)

> [!IMPORTANT]
> A funcionalidade de recolha está atualmente em pré-visualização pública.
> A versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ao longo da criação de um caderno, normalmente acaba-se com células que utiliza para a exploração de dados ou depuragem. A funcionalidade *de recolha* irá ajudá-lo a produzir um caderno limpo sem estas células extra-antigas.

1. Executar todas as suas celas de caderno.
1. Selecione a célula que contém o código que deseja que o novo caderno possa ser executado. Por exemplo, o código que submete uma experiência, ou talvez o código que regista um modelo.
1. Selecione o ícone **'Recolha'** que aparece na barra de ferramentas da célula.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="Screenshot: selecione o ícone 'Recolha', selecione":::
1. Insira o nome do seu novo caderno "recolhido".  

O novo caderno contém apenas células de código, com todas as células necessárias para produzir os mesmos resultados que a célula selecionada para recolha.

## <a name="save-and-checkpoint-a-notebook"></a>Guardar e checkpoint um caderno

A Azure Machine Learning cria um ficheiro de checkpoint quando cria um ficheiro *ipynb.*

Na barra de ferramentas do portátil, selecione o menu e, em seguida, **&gt; Guarde e registe** para guardar manualmente o portátil e adicionará um ficheiro de verificação associado ao portátil.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Screenshot da ferramenta de salvamento na barra de ferramentas do portátil":::

Todos os cadernos são automaticamente a cada 30 segundos. Faça automaticamente atualizações apenas no ficheiro *ipynb* inicial, não no ficheiro de verificação.
 
Selecione **Checkpoints** no menu do portátil para criar um ponto de verificação nomeado e reverter o caderno para um ponto de verificação guardado.

## <a name="export-a-notebook"></a>Exportar um caderno

Na barra de ferramentas do portátil, selecione o menu e, em seguida, **Exporte Como** para exportar o caderno como qualquer um dos tipos suportados:

* Bloco de Notas
* Python
* HTML
* LaTeX

:::image type="content" source="media/how-to-run-jupyter-notebooks/export-notebook.png" alt-text="Exporte um caderno para o seu computador":::

O ficheiro exportado é guardado no seu computador.

## <a name="run-a-notebook-or-python-script"></a>Executar um caderno ou script Python

Para executar um caderno ou um script Python, você primeiro se conecta a uma [instância de cálculo de execução](concept-compute-instance.md).

* Se não tiver uma instância computacional, use estes passos para criar um:

    1. No portátil ou na barra de ferramentas do script, à direita do dropdown compute, selecione **+ New Compute**. Dependendo do tamanho do seu ecrã, este pode estar localizado sob um **...** menu.
        :::image type="content" source="media/how-to-run-jupyter-notebooks/new-compute.png" alt-text="Criar um novo cálculo":::
    1. Nomeie o Compute e escolha um **tamanho de máquina virtual.** 
    1. Selecione **Criar**.
    1. A instância de cálculo está ligada automaticamente ao ficheiro.  Agora pode executar as células de caderno ou o script Python usando a ferramenta à esquerda da instância computacional.

* Se tiver uma instância de computação parada,  **selecione Start compute** à direita da queda do Compute. Dependendo do tamanho do seu ecrã, este pode estar localizado sob um **...** menu.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/start-compute.png" alt-text="Iniciar instância de computação":::

Só você pode ver e usar as instâncias de cálculo que cria.  Os **ficheiros do** utilizador são armazenados separadamente do VM e são partilhados entre todas as instâncias computacional no espaço de trabalho.

### <a name="view-logs-and-output"></a>Ver registos e saída

Utilize [widgets de portátil](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py) para ver o progresso da execução e dos registos. Um widget é assíncronoso e fornece atualizações até que o treino termine. Os widgets Azure Machine Learning também são suportados no Jupyter e no JupterLab.

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="Screenshot: Widget de caderno Jupyter ":::

## <a name="explore-variables-in-the-notebook"></a>Explore variáveis no caderno

Na barra de ferramentas do portátil, utilize a ferramenta **exploradora Variável** para mostrar o nome, o tipo, o comprimento e os valores da amostra para todas as variáveis que tenham sido criadas no seu caderno.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="Screenshot: Ferramenta exploradora variável":::

Selecione a ferramenta para mostrar a janela do explorador variável.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="Screenshot: Janela do explorador variável":::

## <a name="navigate-with-a-toc"></a>Navegue com um TOC

Na barra  **de ferramentas** do portátil, utilize a ferramenta tabela de conteúdos para visualizar ou ocultar a tabela de conteúdos.  Inicie uma célula de marcação com um título para adicioná-la à tabela de conteúdos. Clique numa entrada na tabela para deslocar para a célula no caderno.  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="Screenshot: Tabela de conteúdos no caderno":::

## <a name="change-the-notebook-environment"></a>Alterar o ambiente do caderno

A barra de ferramentas do portátil permite-lhe alterar o ambiente em que o seu caderno funciona.  

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

## <a name="add-new-kernels"></a>Adicione novos núcleos

[Utilize o terminal ](how-to-access-terminal.md#add-new-kernels) para criar e adicionar novos núcleos à sua instância de computação. O portátil irá automaticamente encontrar todos os núcleos jupyter instalados na instância de computação conectada.

Utilize o recuo do núcleo no direito de alterar para qualquer um dos núcleos instalados.  


### <a name="status-indicators"></a>Indicadores de estado

Um indicador ao lado da queda do **Compute** mostra o seu estado.  O estado também é mostrado na própria queda.  

|Cor |Estado do cálculo |
|---------|---------| 
| Green | Execução de cálculo |
| Red |O cálculo falhou | 
| Negra | Computo parou |
|  Azul claro |Criar, iniciar, reiniciar, configurar |
|  Cinzento |Computação eliminando, parando |

Um indicador ao lado da queda do **Kernel** mostra o seu estado.

|Cor |Estatuto de kernel |
|---------|---------|
|  Green |Kernel conectado, ocioso, ocupado|
|  Cinzento |Kernel não ligado |

## <a name="find-compute-details"></a>Encontre detalhes do cálculo

Encontre detalhes sobre as suas instâncias de computação na página **compute** em [estúdio](https://ml.azure.com).

## <a name="troubleshooting"></a>Resolução de problemas

* Se não conseguir ligar-se a um bloco de notas, certifique-se de que a comunicação da tomada web **não** está desativada. Para que a funcionalidade do Jupyter funcione, a comunicação da tomada web deve ser ativada. Certifique-se de que a sua rede permite ligações websocket a *.instances.azureml.net e *.instances.azureml.ms. 

* Quando a instância de computação é implantada num espaço de trabalho de ligação privada, só pode ser acedida a partir de uma rede virtual. Se estiver a utilizar o ficheiro DNS ou hostis personalizado, por favor adicione uma entrada para <nome de exemplo <region>>. instances.azureml.ms com endereço IP privado do ponto final privado do espaço de trabalho. Para mais informações consulte o artigo [DNS personalizado.](https://docs.microsoft.com/azure/machine-learning/how-to-custom-dns?tabs=azure-cli)
    
## <a name="next-steps"></a>Passos seguintes

* [Executar a sua primeira experiência](tutorial-1st-experiment-sdk-train.md)
* [Faça backup do armazenamento do seu ficheiro com instantâneos](../storage/files/storage-snapshots-files.md)
* [Trabalhar em ambientes seguros](https://docs.microsoft.com/azure/machine-learning/how-to-secure-training-vnet#compute-instance)
