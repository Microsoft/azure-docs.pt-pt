---
title: Exporte um projeto de caderno Jupyter a partir da Pré-visualização de Cadernos Azure
description: Exporte rapidamente um projeto de caderno Jupyter.
ms.topic: quickstart
ms.date: 06/29/2020
ms.openlocfilehash: 31e32a6ebb39429078c7c6747bc6b10f177699ac
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85802724"
---
# <a name="quickstart-export-a-jupyter-notebook-project-in-azure-notebooks-preview"></a>Quickstart: Export a Jupyter notebook project in Azure Notebooks Preview

Neste quickstart, você irá baixar um projeto Azure Notebooks para ser usado em outras soluções de cadernos Jupyter. 

## <a name="prerequisites"></a>Pré-requisitos
Um projeto de Cadernos Azure existente.

## <a name="export-an-azure-notebooks-project"></a>Exportar um projeto de Cadernos Azure

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Vá aos [Cadernos Azure](https://notebooks.azure.com) e inscreva-se. Para mais detalhes, consulte [Quickstart - Inicie súm em Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Na sua página de perfil público, selecione **Os Meus Projetos** no topo da página:

    ![O meu link de projetos no topo da janela do navegador](media/quickstarts/my-projects-link.png)

1. Selecione um projeto.
1. Clique no botão "Descarregar" para ativar um download de ficheiros zip que contém todos os seus ficheiros do projeto.
1. Em alternativa, a partir de uma página específica do projeto, clique no botão "Download Project" para descarregar todos os ficheiros de um determinado projeto.

Depois de descarregar os seus ficheiros do projeto, pode usá-los com outras soluções Jupyter Notebook. Algumas opções descritas nas secções abaixo incluem: 
- [Visual Studio Code](#use-notebooks-in-visual-studio-code)
- [Espaços de código do estúdio visual](#use-notebooks-in-visual-studio-codespaces)
- [Azure Machine Learning](#use-notebooks-with-azure-machine-learning)
- [Azure Lab Services](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="download-the-requirements-file-used-by-azure-notebooks"></a>Descarregue o ficheiro de requisitos utilizado pelos Cadernos Azure

Se quiser criar um ambiente que corresponda ao da Pré-visualização dos Cadernos Azure, pode utilizar o ficheiro **AzureNotebooksRequirements.txt** fornecido no GitHub.

1. Navegue para o [repositório de Cadernos Azure GitHub](https://github.com/microsoft/AzureNotebooks) ou pode [aceder diretamente ao ficheiro.](https://aka.ms/aznbrequirementstxt)
1. Descarregue o ficheiro **AzureNotebooksRequirements.txt** que contém as dependências de pacotes para o ambiente Azure Notebooks. 
1. A partir de um pedido de comando, navegue até ao diretório que pretende utilizar para os seus projetos e utilize o ficheiro de requisitos para instalar os pacotes apropriados.

    > [!Note]
    > Embora não seja necessário, geralmente você gostaria de criar um novo ambiente virtual para a instalação de pacotes, como mostrado abaixo.

    ```bash
    cd your-project-dir
    python -m venv .venv
    python -m pip install -r AzureNotebooksRequirements.txt
    ```

Para obter mais informações sobre os ficheiros de requisitos, consulte os [pip docs]( https://pip.pypa.io/en/stable/user_guide/#requirements-files).

## <a name="use-notebooks-in-visual-studio-code"></a>Use cadernos no código do estúdio visual

[O CÓDIGO VS](https://code.visualstudio.com/) é um editor de código gratuito que pode utilizar localmente ou ligado a computação remota. Combinado com a extensão Python, oferece um ambiente completo para o desenvolvimento python, incluindo uma rica experiência nativa para trabalhar com cadernos Jupyter. 

![VS Código Jupyter Suporte de caderno](media/vs-code-jupyter-notebook.png)

Depois de [descarregar](#export-an-azure-notebooks-project) os seus ficheiros de projeto, pode usá-los com código VS. Para obter orientações utilizando o Código VS com cadernos Jupyter, consulte os [cadernos de trabalho com o Jupyter em código de estúdio visual](https://code.visualstudio.com/docs/python/jupyter-support) e ciência de dados em tutoriais do Código do Estúdio [Visual.](https://code.visualstudio.com/docs/python/data-science-tutorial)

Também pode utilizar os requisitos de [Azure Notebooks](#download-the-requirements-file-used-by-azure-notebooks) com Código de Estúdio Visual para criar um ambiente que corresponda à pré-visualização dos Cadernos Azure.

## <a name="use-notebooks-in-visual-studio-codespaces"></a>Use cadernos em espaços de código do estúdio visual

O Visual Studio Codespaces fornece ambientes hospedados na nuvem onde pode editar os seus cadernos usando o Código do Estúdio Visual ou o seu navegador web. Oferece a mesma grande experiência Jupyter que o VS Code, mas sem necessidade de instalar nada no seu dispositivo. Se você não quer configurar um ambiente local e preferir uma solução apoiada por nuvens, então criar um espaço de código é uma ótima opção. Para começar:

1. [Descarregue](#export-an-azure-notebooks-project) os seus ficheiros de projeto.
1. [Crie um repositório GitHub](https://help.github.com/github/getting-started-with-github/create-a-repo) para armazenar os seus cadernos. 
1. [Adicione os seus ficheiros](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) ao repositório.
1. Configurar os codespaces do estúdio visual no [navegador,](https://docs.microsoft.com/visualstudio/online/how-to/browser) [Visual Studio](https://docs.microsoft.com/visualstudio/online/how-to/vside)ou Visual [Studio Code](https://docs.microsoft.com/visualstudio/online/how-to/vscode).

## <a name="use-notebooks-with-azure-machine-learning"></a>Use cadernos com aprendizagem automática Azure

O Azure Machine Learning fornece uma plataforma de aprendizagem automática de ponta a ponta para permitir aos utilizadores construir e implementar modelos mais rapidamente no Azure. O Azure ML permite-lhe executar Cadernos Jupyter num VM ou num ambiente de computação de cluster partilhado. Se necessitar de uma solução baseada na nuvem para a sua carga de trabalho ML com rastreio de experiências, gestão de conjuntos de dados e muito mais, recomendamos Azure Machine Learning. Para começar com Azure ML:

1. [Descarregue](#export-an-azure-notebooks-project) os seus ficheiros de projeto.
1. [Criar um espaço de trabalho](../machine-learning/how-to-manage-workspace.md) no portal Azure.

   ![Criar uma área de trabalho](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. Abra o [Azure Studio (pré-visualização)](https://ml.azure.com/).
1. Utilizando a barra de navegação do lado esquerdo, selecione **Cadernos**.
1. Clique no botão **'Carregar ficheiros'** e faça o upload dos ficheiros do projeto que descarregou a partir de Azure Notebooks.

Para obter informações adicionais sobre Azure ML e executar cadernos Jupyter, pode rever a [documentação](../machine-learning/how-to-run-jupyter-notebooks.md) ou experimentar o módulo [de Intro para Machine Learning](https://docs.microsoft.com/learn/modules/intro-to-azure-machine-learning-service/) no Microsoft Learn.


## <a name="use-azure-lab-services"></a>Utilizar serviços de laboratório Azure

[Os Serviços Azure Lab](https://azure.microsoft.com/services/lab-services/) permitem aos educadores configurar e fornecer acesso a pedido a VMs pré-configurados para uma sala inteira. Se procura uma forma de trabalhar com os Cadernos Jupyter e a computação em nuvem num ambiente de sala de aula personalizado, os Serviços de Laboratório são uma ótima opção.

![image](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

 Depois de [descarregar](#export-an-azure-notebooks-project) os seus ficheiros do projeto, pode usá-los com os Serviços Azure Lab. Para obter orientações sobre a criação de um laboratório, consulte [criar um laboratório para ensinar ciência de dados com os cadernos Python e Jupyter](../lab-services/class-type-jupyter-notebook.md)

## <a name="use-github"></a>Use GitHub

O GitHub fornece uma forma gratuita e apoiada por controlo de fontes para armazenar cadernos (e outros ficheiros), partilhar os seus cadernos com outros e trabalhar colaborativamente. Se procura uma forma de partilhar os seus projetos e colaborar com outros, o GitHub é uma ótima opção e pode ser combinado com [o Visual Studio Codespaces](#use-notebooks-in-visual-studio-codespaces) para uma grande experiência de desenvolvimento. Para começar com GitHub

1. [Descarregue](#export-an-azure-notebooks-project) os seus ficheiros de projeto.
1. [Crie um repositório GitHub](https://help.github.com/github/getting-started-with-github/create-a-repo) para armazenar os seus cadernos. 
1. [Adicione os seus ficheiros](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) ao repositório.
