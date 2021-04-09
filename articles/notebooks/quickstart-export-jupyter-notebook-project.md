---
title: Use um Bloco de Notas Jupyter com ofertas da Microsoft
description: Saiba como os Cadernos Jupyter podem ser usados com as ofertas da Microsoft.
ms.topic: quickstart
ms.date: 02/01/2021
ms.openlocfilehash: 5679c28d9cc8a4f1893ffad72002b66ad59861e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99507382"
---
# <a name="use-a-jupyter-notebook-with-microsoft-offerings"></a>Use um Bloco de Notas Jupyter com ofertas da Microsoft

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Neste arranque rápido, você vai aprender a importar um Jupyter Notebook para uso em várias ofertas da Microsoft. 

Se tiver os Jupyter Notebooks existentes ou quiser iniciar um novo projeto, pode usá-los com muitas das ofertas da Microsoft. Algumas opções descritas nas secções abaixo incluem: 
- [Visual Studio Code](#use-notebooks-in-visual-studio-code)
- [Espaçoes de código GitHub](#use-notebooks-in-github-codespaces)
- [Azure Machine Learning](#use-notebooks-with-azure-machine-learning)
- [Azure Lab Services](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="create-an-environment-for-notebooks"></a>Criar um ambiente para cadernos

Se quiser criar um ambiente que corresponda ao da pré-visualização dos Cadernos Azure reformados, pode utilizar o ficheiro de script fornecido no GitHub.

1. Navegue para o [repositório de Cadernos Azure GitHub](https://github.com/microsoft/AzureNotebooks) ou [aceda diretamente à pasta do ambiente.](https://aka.ms/aznbrequirementstxt)
1. A partir de um pedido de comando, navegue até ao diretório que pretende utilizar para os seus projetos.
1. Descarregue o conteúdo da pasta ambiente e siga as instruções README para instalar as dependências do pacote Azure Notebooks.


## <a name="use-notebooks-in-visual-studio-code"></a>Use cadernos no código do estúdio visual

[O CÓDIGO VS](https://code.visualstudio.com/) é um editor de código gratuito que pode utilizar localmente ou ligado a computação remota. Combinado com a extensão Python, oferece um ambiente completo para o desenvolvimento python, incluindo uma rica experiência nativa para trabalhar com cadernos Jupyter. 

![VS Código Jupyter Suporte de caderno](media/vs-code-jupyter-notebook.png)

Se tiver ficheiros de projeto existentes ou quiser criar um novo caderno, pode utilizar o Código VS! Para obter orientações sobre a utilização do Código VS com cadernos Jupyter, consulte os [cadernos de trabalho com o Jupyter em código de estúdio visual](https://code.visualstudio.com/docs/python/jupyter-support) e ciência de dados em tutoriais de Código de Estúdio [Visual.](https://code.visualstudio.com/docs/python/data-science-tutorial)

Também pode utilizar o [script ambiente Azure Notebooks](#create-an-environment-for-notebooks) com Visual Studio Code para criar um ambiente que corresponda à pré-visualização dos Cadernos Azure.

## <a name="use-notebooks-in-github-codespaces"></a>Use cadernos em espaço de código GitHub

O GitHub Codespaces fornece ambientes hospedados em nuvem onde pode editar os seus cadernos usando o Código do Estúdio Visual ou no seu navegador web. Oferece a mesma grande experiência Jupyter que o VS Code, mas sem necessidade de instalar nada no seu dispositivo. Se você não quer configurar um ambiente local e preferir uma solução apoiada por nuvens, então criar um espaço de código é uma ótima opção. Para começar:
1. (Opcional) Recolha quaisquer ficheiros de projeto que queira utilizar com o GitHub Codespaces.
1. [Crie um repositório GitHub](https://help.github.com/github/getting-started-with-github/create-a-repo) para armazenar os seus cadernos.   
1. [Adicione os seus ficheiros](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) ao repositório.
1. [Solicitar acesso à pré-visualização de codespaces do GitHub](https://github.com/features/codespaces)

## <a name="use-notebooks-with-azure-machine-learning"></a>Use cadernos com aprendizagem automática Azure

O Azure Machine Learning fornece uma plataforma de aprendizagem automática de ponta a ponta para permitir aos utilizadores construir e implementar modelos mais rapidamente no Azure. O Azure ML permite-lhe executar Cadernos Jupyter num VM ou num ambiente de computação de cluster partilhado. Se necessitar de uma solução baseada na nuvem para a sua carga de trabalho ML com rastreio de experiências, gestão de conjuntos de dados e muito mais, recomendamos Azure Machine Learning. Para começar com Azure ML:

1. (Opcional) Reúna quaisquer ficheiros de projeto que queira utilizar com a Azure ML.
1. [Criar um espaço de trabalho](../machine-learning/how-to-manage-workspace.md) no portal Azure.

   ![Criar uma área de trabalho](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. Abra o [Azure Studio (pré-visualização)](https://ml.azure.com/).
1. Utilizando a barra de navegação do lado esquerdo, selecione **Cadernos**.
1. Clique no botão **'Carregar ficheiros'** e faça o upload dos ficheiros do projeto.

Para obter informações adicionais sobre Azure ML e executar cadernos Jupyter, pode rever a [documentação](../machine-learning/how-to-run-jupyter-notebooks.md) ou experimentar o módulo [de Intro para Machine Learning](/learn/modules/intro-to-azure-machine-learning-service/) no Microsoft Learn.


## <a name="use-azure-lab-services"></a>Utilizar serviços de laboratório Azure

[Os Serviços Azure Lab](https://azure.microsoft.com/services/lab-services/) permitem aos educadores configurar e fornecer acesso a pedido a VMs pré-configurados para uma sala inteira. Se procura uma forma de trabalhar com os Cadernos Jupyter e a computação em nuvem num ambiente de sala de aula personalizado, os Serviços de Laboratório são uma ótima opção.

![image](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

Se tiver ficheiros de projeto existentes ou quiser criar um novo caderno, pode utilizar os Serviços Azure Lab. Para obter orientações sobre a criação de um laboratório, consulte [criar um laboratório para ensinar ciência de dados com os cadernos Python e Jupyter](../lab-services/class-type-jupyter-notebook.md)

## <a name="use-github"></a>Use GitHub

O GitHub fornece uma forma gratuita e apoiada por controlo de fontes para armazenar cadernos (e outros ficheiros), partilhar os seus cadernos com outros e trabalhar colaborativamente. Se procura uma forma de partilhar os seus projetos e colaborar com outros, o GitHub é uma ótima opção e pode ser combinado com [o GitHub Codespaces](#use-notebooks-in-github-codespaces) para uma grande experiência de desenvolvimento. Para começar com GitHub

1. (Opcional) Reúna todos os ficheiros do projeto que gostaria de usar com o GitHub.
1. [Crie um repositório GitHub](https://help.github.com/github/getting-started-with-github/create-a-repo) para armazenar os seus cadernos. 
1. [Adicione os seus ficheiros](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) ao repositório.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre Python no Código do Estúdio Visual](https://code.visualstudio.com/docs/python/python-tutorial)
- [Saiba mais sobre Azure Machine Learning e Jupyter Notebooks](../machine-learning/how-to-run-jupyter-notebooks.md)
- [Saiba mais sobre o GitHub Codespaces](https://github.com/features/codespaces)
- [Saiba mais sobre os Serviços Azure Lab](https://azure.microsoft.com/services/lab-services/)
- [Saiba mais sobre o GitHub](https://help.github.com/github/getting-started-with-github/)
