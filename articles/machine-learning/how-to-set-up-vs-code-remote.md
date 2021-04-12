---
title: Conecte-se à instância computecional no Código do Estúdio Visual (pré-visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a conectar-se a um computo Azure Machine Learning no Código do Estúdio Visual para executar o Jupyter Notebook interativo e cargas de trabalho de desenvolvimento remoto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 04/08/2021
ms.openlocfilehash: 14f0d15d48193267c224f3497c24651ca3249b0b
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028595"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Conecte-se a uma instância computacional de aprendizagem de máquinas Azure no Código do Estúdio Visual (pré-visualização)

Neste artigo, você vai aprender a conectar-se a uma instância de cálculo Azure Machine Learning usando Visual Studio Code.

[A Azure Machine Learning Compute Instance](concept-compute-instance.md) é uma estação de trabalho totalmente gerida baseada na nuvem para cientistas de dados e fornece capacidades de gestão e prontidão empresarial para administradores de TI.

Existem duas formas de se ligar a uma instância computacional do Código do Estúdio Visual:

* Caso de computação remota. Esta opção proporciona-lhe um ambiente de desenvolvimento completo para a construção dos seus projetos de aprendizagem automática.
* Servidor remoto do Bloco de Notas do Jupyter. Esta opção permite-lhe definir uma instância de cálculo como um servidor remoto do Jupyter Notebook.

## <a name="configure-a-remote-compute-instance"></a>Configurar uma instância de computação remota

Para configurar uma instância de computação remota para o desenvolvimento, você precisará de alguns pré-requisitos.

* Extensão do Código do Estúdio Visual Azure Machine Learning. Para obter mais informações, consulte o [guia de configuração do código do estúdio visual Azure Machine Learning.](tutorial-setup-vscode-extension.md)
* Espaço de trabalho de aprendizagem automática Azure. [Utilize a extensão do Código de Estúdio Visual Azure Machine Learning para criar um novo espaço de trabalho](how-to-manage-resources-vscode.md#create-a-workspace) se ainda não tiver um.
* Exemplo de computação de aprendizagem automática Azure. [Utilize a extensão Azure Machine Learning Visual Studio Code para criar uma nova instância de computação](how-to-manage-resources-vscode.md#create-compute-instance) se não tiver uma.

Para ligar à sua instância de computação remota:

# <a name="vs-code"></a>[Código VS](#tab/extension)

### <a name="azure-machine-learning-extension"></a>Extensão de aprendizagem de máquinas Azure

1. No Código VS, lance a extensão Azure Machine Learning.
1. Expanda o nó **de instâncias computacional** na sua extensão.
1. Clique com o botão direito na instância de cálculo a que pretende ligar e selecione **Connect to Compute Instance**.

:::image type="content" source="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png" alt-text="Conecte-se à instância de computação Visual Studio Code Azure ML Extensão" lightbox="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png":::

### <a name="command-palette"></a>Paleta de comando

1. No Código VS, abra a paleta de comando selecionando **a Paleta de Comando Ver >**.
1. Introduza na caixa de texto **Azure ML: Conecte-se à Instância de Cálculo**.
1. Selecione a sua subscrição.
1. Selecione o seu espaço de trabalho.
1. Selecione a sua instância de cálculo ou crie uma nova.

# <a name="studio"></a>[Studio](#tab/studio)

Navegue até [ml.azure.com](https://ml.azure.com)

> [!IMPORTANT]
> Para se ligar à sua instância de computação remota a partir do Código do Estúdio Visual, certifique-se de que a conta em que está iniciado no estúdio Azure Machine Learning é a mesma que utiliza no Código do Estúdio Visual.

### <a name="compute"></a>Computação

1. Selecione o **separador Compute**
1. Na coluna URI de *aplicação,* selecione **O Código VS** para a instância de cálculo a que pretende ligar.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png" alt-text="Conecte-se ao Compute Instance VS Code Azure ML studio" lightbox="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png":::

### <a name="notebook"></a>Bloco de Notas

1. Selecione o **separador Caderno**
1. No *separador Portátil,* selecione o ficheiro que pretende editar.
1. Selecione **Editores > Editar em Código VS (pré-visualização)**.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png" alt-text="Conecte-se a Compute Instance VS Code Azure ML Notebook" lightbox="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png":::

---

Uma nova janela lança-se para a sua instância de computação remota. Ao tentar estabelecer uma ligação a uma instância de computação remota, estão a decorrer as seguintes tarefas:

1. Autorização. Algumas verificações são efetuadas para garantir que o utilizador que tenta fazer uma ligação está autorizado a utilizar a instância de cálculo.
1. VS Code Remote Server é instalado na instância computacional.
1. Uma ligação WebSocket é estabelecida para interação em tempo real.

Uma vez estabelecida a ligação, persiste. Um token é emitido no início da sessão que é atualizado automaticamente para manter a ligação com a sua instância de computação.

Depois de ligar à sua instância de computação remota, utilize o editor para:

* [Autore e gere ficheiros na sua instância de computação remota ou partilha de ficheiros](https://code.visualstudio.com/docs/editor/codebasics).
* Utilize o [terminal integrado do Código VS](https://code.visualstudio.com/docs/editor/integrated-terminal) para executar [comandos e aplicações na sua instância de computação remota.](how-to-access-terminal.md)
* [Depurar os seus scripts e aplicações](https://code.visualstudio.com/Docs/editor/debugging)
* [Use o Código VS para gerir os seus repositórios Git](concept-train-model-git-integration.md)

## <a name="configure-compute-instance-as-remote-notebook-server"></a>Configurar a instância computacional como servidor de caderno remoto

Para configurar uma instância computacional como um servidor remoto do Jupyter Notebook, você precisará de alguns pré-requisitos:

* Extensão do Código do Estúdio Visual Azure Machine Learning. Para obter mais informações, consulte o [guia de configuração do código do estúdio visual Azure Machine Learning.](tutorial-setup-vscode-extension.md)
* Espaço de trabalho de aprendizagem automática Azure. [Utilize a extensão do Código de Estúdio Visual Azure Machine Learning para criar um novo espaço de trabalho](how-to-manage-resources-vscode.md#create-a-workspace) se ainda não tiver um.

Para ligar a uma instância computacional:

1. Abra um Caderno Jupyter em Código de Estúdio Visual.
1. Quando o portátil integrado experimentar cargas, selecione **Jupyter Server**.

    > [!div class="mx-imgBorder"]
    > ![Lançamento Azure Machine Learning remoto Jupyter Notebook servidor dropdown](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    Em alternativa, também utiliza a paleta de comando:

    1. Abra a paleta de comando selecionando **a Paleta de Comando Ver >** da barra de menu.
    1. Introduza na caixa de texto `Azure ML: Connect to Compute instance Jupyter server` .

1. Escolha `Azure ML Compute Instances` entre a lista de opções do servidor Jupyter.
1. Selecione a sua subscrição a partir da lista de subscrições. Se já configurado previamente o seu espaço de trabalho de Aprendizagem automática Azure, este passo é ignorado.
1. Selecione o seu espaço de trabalho.
1. Selecione a sua instância de cálculo da lista. Se não tiver um, **selecione Criar a nova Instância de Computação Azure ML** e siga as instruções para criar uma.
1. Para que as alterações entrem em vigor, tem de recarregar o Código do Estúdio Visual.
1. Abre um Caderno Jupyter e dirige uma cela.

> [!IMPORTANT]
> **Tem de** executar uma célula para estabelecer a ligação.

Neste momento, pode continuar a executar células no seu Caderno Jupyter.

> [!TIP]
> Também pode trabalhar com ficheiros de script Python (.py) que contêm células de código semelhantes a Jupyter. Para mais informações, consulte a [documentação interativa Visual Studio Code Python](https://code.visualstudio.com/docs/python/jupyter-support-py).

## <a name="next-steps"></a>Passos seguintes

Agora que configuraste o Visual Studio Code Remote, podes usar uma instância computacional como computação remota do Código do Estúdio Visual para [depurar interativamente o teu código.](how-to-debug-visual-studio-code.md)

[Tutorial: Treine o seu primeiro modelo ML](tutorial-1st-experiment-sdk-train.md) mostra como usar uma instância computacional com um caderno integrado.
