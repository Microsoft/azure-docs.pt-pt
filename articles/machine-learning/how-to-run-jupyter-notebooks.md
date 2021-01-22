---
title: Como executar cadernos Jupyter no seu espaço de trabalho
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
ms.openlocfilehash: d6832238b0c76059079e2a1330d31eed3212b242
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685583"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>Como executar os Jupyter Notebooks na área de trabalho

Aprenda a executar os seus cadernos Jupyter diretamente no seu espaço de trabalho no estúdio Azure Machine Learning. Enquanto pode lançar [Jupyter](https://jupyter.org/) ou [JupyterLab,](https://jupyterlab.readthedocs.io)também pode editar e executar os seus cadernos sem sair do espaço de trabalho.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* Um espaço de trabalho de aprendizagem automática. Ver [Criar um espaço de trabalho de aprendizagem de máquinas Azure](how-to-manage-workspace.md).

## <a name="create-notebooks"></a><a name="create"></a> Criar cadernos

No seu espaço de trabalho Azure Machine Learning, crie um novo caderno Jupyter e comece a trabalhar. O caderno recém-criado é armazenado no armazenamento padrão do espaço de trabalho. Este caderno pode ser partilhado com qualquer pessoa com acesso ao espaço de trabalho. 

Para criar um novo caderno: 

1. Abra o seu espaço de trabalho no [estúdio Azure Machine Learning.](https://ml.azure.com)
1. Do lado esquerdo, selecione **Cadernos**. 
1. Selecione o novo ícone **de ficheiro** acima da lista **Ficheiros do utilizador** na secção **'Os meus ficheiros'.**

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Criar novo ficheiro":::

1. Diga o nome do ficheiro. 
1. Para ficheiros de cadernos Jupyter, selecione **Notebook** como o tipo de ficheiro.
1. Selecione um diretório de ficheiros.
1. Selecione **Criar**.

Também pode criar ficheiros de texto.  Selecione **Texto** como o tipo de ficheiro e adicione a extensão ao nome (por exemplo, myfile.py ou myfile.txt)  

Também pode carregar pastas e ficheiros, incluindo cadernos, com as ferramentas no topo da página De Cadernos.  Os blocos de notas e a maioria dos tipos de ficheiros de texto são apresentados na secção de pré-visualização.  Não está disponível nenhuma pré-visualização para a maioria dos outros tipos de ficheiros.

> [!IMPORTANT]
> Os conteúdos em cadernos e scripts podem potencialmente ler dados das suas sessões e aceder a dados sem a sua organização em Azure.  Apenas carregue ficheiros de fontes fidedignas. Para obter mais informações, consulte [as melhores práticas do código Secure](concept-secure-code-best-practice.md#azure-ml-studio-notebooks).

### <a name="clone-samples"></a>Amostras de clones

O seu espaço de trabalho contém uma pasta **Samples** com cadernos concebidos para o ajudar a explorar o SDK e servir como exemplos para os seus próprios projetos de aprendizagem automática.  Pode clonar estes cadernos na sua própria pasta no seu recipiente de armazenamento de espaço de trabalho.  

Por exemplo, consulte [Tutorial: Crie a sua primeira experiência ML](tutorial-1st-experiment-sdk-setup.md#azure).

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a> Use ficheiros do Git e veri os meus ficheiros

Pode aceder a todas as operações do Git utilizando uma janela terminal. Todos os ficheiros e pastas Git serão armazenados no seu sistema de ficheiros do espaço de trabalho.

> [!NOTE]
> Adicione os seus ficheiros e pastas em qualquer lugar sob a pasta **~/cloudfiles/código/Utilizadores** para que fiquem visíveis em todos os seus ambientes Jupyter.

Para aceder ao terminal:

1. Abra o seu espaço de trabalho no [estúdio Azure Machine Learning.](https://ml.azure.com)
1. Do lado esquerdo, selecione **Cadernos**.
1. Selecione qualquer caderno localizado na secção **de ficheiros do Utilizador** no lado esquerdo.  Se não tem nenhum caderno lá, primeiro [crie um caderno](#create)
1. Selecione um alvo **compute** ou crie um novo e espere até estar em funcionamento.
1. Selecione o ícone **do terminal Aberto.**

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="Terminal aberto":::

1. Se não vir o ícone, selecione o **...** à direita do alvo do cálculo e, em seguida, selecione **Open terminal**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Terminal aberto de ...":::


Saiba mais sobre [a clonagem de repositórios de Git no seu sistema de ficheiros de espaço de trabalho.](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)

### <a name="copy-and-paste-in-terminal"></a>Copiar e colar no terminal

> * Janelas: `Ctrl-Insert` copiar e utilizar ou `Ctrl-Shift-v` `Shift-Insert` colar.
> * Mac OS: `Cmd-c` copiar e `Cmd-v` colar.
> * O FireFox/IE pode não suportar adequadamente as permissões de prancheta.

### <a name="share-notebooks-and-other-files"></a>Partilhar cadernos e outros ficheiros

Copie e cole o URL para partilhar um caderno ou ficheiro.  Apenas outros utilizadores do espaço de trabalho podem aceder a este URL.  Saiba mais sobre [a concessão de acesso ao seu espaço de trabalho.](how-to-assign-roles.md)

## <a name="edit-a-notebook"></a>Editar um caderno

Para editar um bloco de notas, abra qualquer caderno localizado na secção de **ficheiros** do Utilizador do seu espaço de trabalho. Clique na célula que deseja editar. 

Pode editar o caderno sem se ligar a uma instância de computação.  Quando pretender executar as células no caderno, selecione ou crie uma instância de computação.  Se selecionar uma instância de computação parada, começará automaticamente quando executar a primeira célula.

Quando uma instância computacional está em execução, também pode utilizar a conclusão de código, alimentada pelo [Intellisense,](https://code.visualstudio.com/docs/editor/intellisense)em qualquer caderno Python.

Também pode lançar Jupyter ou JupyterLab a partir da barra de ferramentas do portátil.  O Azure Machine Learning não fornece atualizações e corre bugs do Jupyter ou do JupyterLab, uma vez que são produtos Open Source fora do limite do Microsoft Support.

### <a name="focus-mode"></a>Modo de detalhe

Utilize o modo de focagem para expandir a sua visão atual para que possa concentrar-se nos separadores ativos. O modo focus esconde o explorador de ficheiros Notebooks.

1. Na barra de ferramentas da janela do terminal, selecione **o modo Focus** para ligar o modo de focagem. Dependendo da largura da janela, este pode estar localizado sob o item do **menu na** sua barra de ferramentas.
1. Enquanto está no modo de focagem, volte à vista padrão selecionando a **vista Standard**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Modo de foco de alternação / visão padrão":::


### <a name="use-intellisense"></a>Utilizar o IntelliSense

[O IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) é um auxílio de conclusão de código que inclui uma série de funcionalidades: Membros da Lista, Informações de Parâmetros, Informações Rápidas e Palavra Completa. Estas funcionalidades ajudam-no a aprender mais sobre o código que está a usar, a acompanhar os parâmetros que está a digitar e a adicionar chamadas a propriedades e métodos com apenas algumas teclas.  

Ao escrever código, utilize ctrl+Space para ativar o IntelliSense.

### <a name="clean-your-notebook-preview"></a>Limpe o seu caderno (pré-visualização)

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

### <a name="save-and-checkpoint-a-notebook"></a>Guardar e checkpoint um caderno

A Azure Machine Learning cria um ficheiro de checkpoint quando cria um ficheiro *ipynb.*

Na barra de ferramentas do portátil, selecione o menu e, em seguida, **&gt; Guarde e registe** para guardar manualmente o portátil e adicionará um ficheiro de verificação associado ao portátil.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Screenshot da ferramenta de salvamento na barra de ferramentas do portátil":::

Todos os cadernos são automaticamente a cada 30 segundos. Faça automaticamente atualizações apenas no ficheiro *ipynb* inicial, não no ficheiro de verificação.
 
Selecione **Checkpoints** no menu do portátil para criar um ponto de verificação nomeado e reverter o caderno para um ponto de verificação guardado.

## <a name="delete-a-notebook"></a>Eliminar um bloco de notas

*Não pode* apagar os cadernos **samples.**  Estes cadernos fazem parte do estúdio e são atualizados cada vez que um novo SDK é publicado.  

*Pode* eliminar **os ficheiros do Utilizador** de qualquer forma:

* No estúdio, selecione o **...** no final de uma pasta ou arquivo.  Certifique-se de que utiliza um browser suportado (Microsoft Edge, Chrome ou Firefox).
* A partir de qualquer barra de ferramentas de portátil, selecione [**Open terminal**](#terminal)  para aceder à janela do terminal para a instância computacional.
* No Jupyter ou no JupyterLab com as suas ferramentas.

## <a name="run-a-notebook-or-python-script"></a>Executar um caderno ou script Python

Para executar um caderno ou um script Python, você primeiro se conecta a uma [instância de cálculo de execução](concept-compute-instance.md). Se não tiver uma instância computacional, use estes passos para criar um: 

1. Selecione **+** no bloco de ferramentas do caderno ou do script. 
2. Nomeie o Compute e escolha um **tamanho de máquina virtual.** 
3. Selecione **Criar**.
4. A instância de cálculo está ligada automaticamente ao ficheiro.  Agora pode executar as células de caderno ou o script Python usando a ferramenta à esquerda da instância computacional

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

### <a name="add-new-kernels"></a>Adicione novos núcleos

O portátil irá automaticamente encontrar todos os núcleos jupyter instalados na instância de computação conectada.  Para adicionar um núcleo à instância de cálculo:

1. Selecione [**o terminal aberto**](#terminal) na barra de ferramentas do portátil.
1. Utilize a janela do terminal para criar um novo ambiente.  Por exemplo, o código abaixo `newenv` cria:
    ```shell
    conda create -y --name newenv
    ```
1. Ative o ambiente.  Por exemplo, depois de `newenv` criar:

    ```shell
    conda activate newenv
    ```
1. Instale o pacote pip e ipykernel para o novo ambiente e crie um núcleo para esse conda env

    ```shell
    conda install -y pip
    conda install -y ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```
1. Depois de ter instalado o núcleo, por favor, refresque a página e abra um caderno. Agora verão o novo núcleo na lista de núcleos.

> [!NOTE]
> Para a gestão de pacotes dentro de um caderno, utilize funções mágicas **%pip** ou **%conda** para instalar automaticamente pacotes no **núcleo atualmente em funcionamento**, em vez de **!pip** ou **!conda,** que se refere a todas as embalagens (incluindo pacotes fora do núcleo atualmente em funcionamento)

Qualquer um dos [Jupyter Kernels disponíveis](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) pode ser instalado.

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

## <a name="shortcut-keys"></a>Chaves de atalho
Semelhante aos Cadernos Jupyter, os cadernos Azure Machine Learning Studio têm uma interface de utilizador modal. O teclado faz coisas diferentes dependendo do modo em que a célula de portátil está. Os cadernos Azure Machine Learning Studio suportam os seguintes dois modos para uma determinada célula de código: modo de comando e modo de edição.

### <a name="command-mode-shortcuts"></a>Atalhos do modo de comando

Uma célula está no modo de comando quando não há cursor de texto que lhe ordene a escrever. Quando uma célula está no modo Comando, pode editar o caderno como um todo, mas não digitar em células individuais. Introduza o modo de comando premindo `ESC` ou utilizando o rato para selecionar fora da área de editor de uma célula.  A borda esquerda da célula ativa é azul e sólida, e o botão **Run** é azul.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/command-mode.png" alt-text="Célula de portátil no modo de comando ":::

| Atalho                      | Description                          |
| ----------------------------- | ------------------------------------|
| ENTER                         | Entrar no modo de edição             |        
| Shift + Enter                 | Executar célula, selecione abaixo         |     
| Controlo/Comando + Introdução       | Célula de execução                            |
| Alt + Entrar                   | Executar célula, inserir célula de código abaixo    |
| Controlo/Comando + Alt + Entrar | Executar célula, inserir a célula de marcação abaixo|
| Alt + R                       | Executar tudo      |                       
| Y                             | Converter célula em código    |                         
| M                             | Converter célula para marcação  |                       
| Up/K                          | Selecione a célula acima    |               
| Down/J                        | Selecione a célula abaixo    |               
| A                             | Insira a célula de código acima  |            
| B                             | Insira a célula de código abaixo   |           
| Controlo/Comando + Mudança + A   | Insira a célula de marcação acima    |      
| Controlo/Comando + Mudança + B   | Insira a célula de marcação abaixo   |       
| X                             | Corte a célula selecionada    |               
| C                             | Copiar célula selecionada   |               
| Mudança + V                     | Colar célula selecionada acima           |
| V                             | Colar célula selecionada abaixo    |       
| D D                           | Excluir célula selecionada|                
| O                             | Saída de alternância         |              
| Mudança + O                     | Deslocamento de saída de alternância   |          
| I I                           | Interromper o núcleo |                   
| 0 0                           | Reiniciar o núcleo |                     
| Mudança + Espaço                 | Rolar para cima  |                         
| Espaço                         | Rolar para baixo|
| Tecla de Tabulação                           | Mude o foco para o próximo item focalizável (quando a armadilha do separador for desativada)|
| Controlo/Comando + S           | Salvar caderno |                      
| 1                             | Mudar para h1|                       
| 2                             | Mudar para h2|                        
| 3                             | Mudar para h3|                        
| 4                             | Mudar para h4 |                       
| 5                             | Mudar para h5 |                       
| 6                             | Alteração para h6 |                       

### <a name="edit-mode-shortcuts"></a>Atalhos de modo de edição

O modo de edição é indicado por um cursor de texto que o leva a escrever na área do editor. Quando uma célula está em modo de edição, pode digitar na célula. Introduza o modo de edição premindo `Enter` ou utilizando o rato para selecionar na área de editor de uma célula. A fronteira esquerda da célula ativa é verde e eclodida, e o botão **Run** é verde. Também vê o cursor no modo editar na célula.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/edit-mode.png" alt-text="Célula de portátil no modo de edição":::

Utilizando os seguintes atalhos de teclas, pode navegar e executar código mais facilmente em cadernos Azure Machine Learning quando estiver no modo Editar.

| Atalho                      | Description|                                     
| ----------------------------- | ----------------------------------------------- |
| Esc                        | Insira o modo de comando|  
| Controlo/Comando + Espaço       | Ativar o IntelliSense |
| Shift + Enter                 | Executar célula, selecione abaixo |                         
| Controlo/Comando + Introdução       | Célula de execução  |                                      
| Alt + Entrar                   | Executar célula, inserir célula de código abaixo  |              
| Controlo/Comando + Alt + Entrar | Executar célula, inserir a célula de marcação abaixo  |          
| Alt + R                       | Executar todas as células     |                              
| Cima                            | Mover cursor para cima ou célula anterior    |             
| Baixo                          | Mover cursor para baixo ou próxima célula |                  
| Controlo/Comando + S           | Salvar caderno   |                                
| Controlo/Comando + Up          | Vá para o início da célula   |                             
| Controlo/Comando + Para baixo        | Ir para o fim da cela |                                 
| Tecla de Tabulação                           | Conclusão do código ou travessão (se a patilha estiver ativada) |
| Controlo/Comando + M           | Ativar/desativar a armadilha do separador  |                       
| Controlo/Comando + ]           | Travessão |                                         
| Controlo/Comando +           | Dedent  |                                        
| Controlo/Comando + A           | Selecionar tudo|                                      
| Controlo/Comando + Z           | Anular |                                           
| Controlo/Comando + Mudança + Z   | Refazer |                                           
| Controlo/Comando + Y           | Refazer |                                           
| Controlo/Comando + Casa        | Vá para o início da célula|                                
| Controlo/Comando + Fim         | Ir para o fim da cela   |                               
| Controlo/Comando + Esquerda        | Vai uma palavra à esquerda |                               
| Controlo/Comando + Direito       | Vai uma palavra à direita |                              
| Controlo/Comando + Backspace   | Apagar palavra antes |                             
| Controlo/Comando + Eliminar      | Apagar palavra depois |                              
| Controlo/Comando + /           | Toggle comentário sobre cu

## <a name="find-compute-details"></a>Encontre detalhes do cálculo

Encontre detalhes sobre as suas instâncias de computação na página **compute** em [estúdio](https://ml.azure.com).

## <a name="troubleshooting"></a>Resolução de problemas

* Se não conseguir ligar-se a um bloco de notas, certifique-se de que a comunicação da tomada web **não** está desativada. Para que a funcionalidade do Jupyter funcione, a comunicação da tomada web deve ser ativada. Certifique-se de que a sua rede permite ligações websocket a *.instances.azureml.net e *.instances.azureml.ms. 

* Quando a instância de computação é implantada num espaço de trabalho de ligação privada, só pode ser acedida a partir de uma rede virtual. Se estiver a utilizar o ficheiro DNS ou hostis personalizado, por favor adicione uma entrada para <nome de exemplo <region>>. instances.azureml.ms com endereço IP privado do ponto final privado do espaço de trabalho. Para mais informações consulte o artigo [DNS personalizado.](https://docs.microsoft.com/azure/machine-learning/how-to-custom-dns?tabs=azure-cli)
    
## <a name="next-steps"></a>Passos seguintes

* [Executar a sua primeira experiência](tutorial-1st-experiment-sdk-train.md)
* [Faça backup do armazenamento do seu ficheiro com instantâneos](../storage/files/storage-snapshots-files.md)
* [Trabalhar em ambientes seguros](https://docs.microsoft.com/azure/machine-learning/how-to-secure-training-vnet#compute-instance)
