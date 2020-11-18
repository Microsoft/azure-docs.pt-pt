---
title: Exportar um projeto Jupyter Notebook a partir da Pré-visualização de Cadernos Azure
description: Exporte rapidamente um projeto jupyter Notebook.
ms.topic: quickstart
ms.date: 06/29/2020
ms.openlocfilehash: ea16bd61b542217fb6f2d5ba1d926a0bc19d4cce
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844425"
---
# <a name="quickstart-export-a-jupyter-notebook-project-in-azure-notebooks-preview"></a>Quickstart: Export a Jupyter Notebook project in Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Neste quickstart, você irá baixar um projeto Azure Notebooks para ser usado em outras soluções Jupyter Notebook. 

## <a name="prerequisites"></a>Pré-requisitos

Um projeto de Cadernos Azure existente.

## <a name="export-an-azure-notebooks-project"></a>Exportar um projeto de Cadernos Azure

1. Vá aos [Cadernos Azure](https://notebooks.azure.com) e inscreva-se. Para mais detalhes, consulte [Quickstart - Inicie súm em Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Na sua página de perfil público, selecione **Os Meus Projetos** no topo da página:

    ![O meu link de projetos no topo da janela do navegador](media/quickstarts/my-projects-link.png)

1. Selecione um projeto.
1. Clique no botão "Descarregar" para ativar um download de ficheiros zip que contém todos os seus ficheiros do projeto.
1. Em alternativa, a partir de uma página específica do projeto, clique no botão "Download Project" para descarregar todos os ficheiros de um determinado projeto.

Depois de descarregar os seus ficheiros do projeto, pode usá-los com outras soluções Jupyter Notebook. Algumas opções descritas nas secções abaixo incluem: 
- [Visual Studio Code](#use-notebooks-in-visual-studio-code)
- [Espaçoes de código GitHub](#use-notebooks-in-github-codespaces)
- [Azure Machine Learning](#use-notebooks-with-azure-machine-learning)
- [Azure Lab Services](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="create-an-environment-for-notebooks"></a>Criar um ambiente para cadernos

Se quiser criar um ambiente que corresponda ao da Pré-visualização dos Cadernos Azure, pode utilizar o ficheiro de script fornecido no GitHub.

1. Navegue para o [repositório de Cadernos Azure GitHub](https://github.com/microsoft/AzureNotebooks) ou [aceda diretamente à pasta do ambiente.](https://aka.ms/aznbrequirementstxt)
1. A partir de um pedido de comando, navegue até ao diretório que pretende utilizar para os seus projetos.
1. Descarregue o conteúdo da pasta ambiente e siga as instruções README para instalar as dependências do pacote Azure Notebooks.


## <a name="use-notebooks-in-visual-studio-code"></a>Use cadernos no código do estúdio visual

[O CÓDIGO VS](https://code.visualstudio.com/) é um editor de código gratuito que pode utilizar localmente ou ligado a computação remota. Combinado com a extensão Python, oferece um ambiente completo para o desenvolvimento python, incluindo uma rica experiência nativa para trabalhar com cadernos Jupyter. 

![VS Código Jupyter Suporte de caderno](media/vs-code-jupyter-notebook.png)

Depois de [descarregar](#export-an-azure-notebooks-project) os seus ficheiros de projeto, pode usá-los com código VS. Para obter orientações utilizando o Código VS com cadernos Jupyter, consulte os [cadernos de trabalho com o Jupyter em código de estúdio visual](https://code.visualstudio.com/docs/python/jupyter-support) e ciência de dados em tutoriais do Código do Estúdio [Visual.](https://code.visualstudio.com/docs/python/data-science-tutorial)

Também pode utilizar o [script ambiente Azure Notebooks](#create-an-environment-for-notebooks) com Visual Studio Code para criar um ambiente que corresponda à pré-visualização dos Cadernos Azure.

## <a name="use-notebooks-in-github-codespaces"></a>Use cadernos em espaço de código GitHub

O GitHub Codespaces fornece ambientes hospedados em nuvem onde pode editar os seus cadernos usando o Código do Estúdio Visual ou no seu navegador web. Oferece a mesma grande experiência Jupyter que o VS Code, mas sem necessidade de instalar nada no seu dispositivo. Se você não quer configurar um ambiente local e preferir uma solução apoiada por nuvens, então criar um espaço de código é uma ótima opção. Para começar:
1. [Descarregue](#export-an-azure-notebooks-project) os seus ficheiros de projeto.
1. [Crie um repositório GitHub](https://help.github.com/github/getting-started-with-github/create-a-repo) para armazenar os seus cadernos.   
1. [Adicione os seus ficheiros](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) ao repositório.
1. [Solicitar acesso à pré-visualização de codespaces do GitHub](https://github.com/features/codespaces)

## <a name="use-notebooks-with-azure-machine-learning"></a>Use cadernos com aprendizagem automática Azure

O Azure Machine Learning fornece uma plataforma de aprendizagem automática de ponta a ponta para permitir aos utilizadores construir e implementar modelos mais rapidamente no Azure. O Azure ML permite-lhe executar Cadernos Jupyter num VM ou num ambiente de computação de cluster partilhado. Se necessitar de uma solução baseada na nuvem para a sua carga de trabalho ML com rastreio de experiências, gestão de conjuntos de dados e muito mais, recomendamos Azure Machine Learning. Para começar com Azure ML:

1. [Descarregue](#export-an-azure-notebooks-project) os seus ficheiros de projeto.
1. [Criar um espaço de trabalho](../machine-learning/how-to-manage-workspace.md) no portal Azure.

   ![Criar uma área de trabalho](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. Abra o [Azure Studio (pré-visualização)](https://ml.azure.com/).
1. Utilizando a barra de navegação do lado esquerdo, selecione **Cadernos**.
1. Clique no botão **'Carregar ficheiros'** e faça o upload dos ficheiros do projeto que descarregou a partir de Azure Notebooks.

Para obter informações adicionais sobre Azure ML e executar cadernos Jupyter, pode rever a [documentação](../machine-learning/how-to-run-jupyter-notebooks.md) ou experimentar o módulo [de Intro para Machine Learning](/learn/modules/intro-to-azure-machine-learning-service/) no Microsoft Learn.


## <a name="use-azure-lab-services"></a>Utilizar serviços de laboratório Azure

[Os Serviços Azure Lab](https://azure.microsoft.com/services/lab-services/) permitem aos educadores configurar e fornecer acesso a pedido a VMs pré-configurados para uma sala inteira. Se procura uma forma de trabalhar com os Cadernos Jupyter e a computação em nuvem num ambiente de sala de aula personalizado, os Serviços de Laboratório são uma ótima opção.

![image](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

 Depois de [descarregar](#export-an-azure-notebooks-project) os seus ficheiros do projeto, pode usá-los com os Serviços Azure Lab. Para obter orientações sobre a criação de um laboratório, consulte [criar um laboratório para ensinar ciência de dados com os cadernos Python e Jupyter](../lab-services/class-type-jupyter-notebook.md)

## <a name="use-github"></a>Use GitHub

O GitHub fornece uma forma gratuita e apoiada por controlo de fontes para armazenar cadernos (e outros ficheiros), partilhar os seus cadernos com outros e trabalhar colaborativamente. Se procura uma forma de partilhar os seus projetos e colaborar com outros, o GitHub é uma ótima opção e pode ser combinado com [o GitHub Codespaces](#use-notebooks-in-github-codespaces) para uma grande experiência de desenvolvimento. Para começar com GitHub

1. [Descarregue](#export-an-azure-notebooks-project) os seus ficheiros de projeto.
1. [Crie um repositório GitHub](https://help.github.com/github/getting-started-with-github/create-a-repo) para armazenar os seus cadernos. 
1. [Adicione os seus ficheiros](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) ao repositório.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre Python no Código do Estúdio Visual](https://code.visualstudio.com/docs/python/python-tutorial)
- [Saiba mais sobre Azure Machine Learning e Jupyter Notebooks](../machine-learning/how-to-run-jupyter-notebooks.md)
- [Saiba mais sobre o GitHub Codespaces](https://github.com/features/codespaces)
- [Saiba mais sobre os Serviços Azure Lab](https://azure.microsoft.com/services/lab-services/)
- [Saiba mais sobre o GitHub](https://help.github.com/github/getting-started-with-github/)