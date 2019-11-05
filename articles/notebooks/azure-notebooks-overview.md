---
title: Visão geral do Azure Notebooks
description: Execute blocos de anotações do Jupyter na nuvem usando o serviço de Azure Notebooks gratuito, em que nenhuma instalação ou configuração é necessária.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 9cea5a8e-c52d-4bdc-9e4a-cecdc1ad02c1
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 04/05/2019
ms.author: kraigb
ms.openlocfilehash: ad4a1d89809ebd1b7ac933ef391965accbfd1a6c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496732"
---
# <a name="overview-of-azure-notebooks"></a>Visão geral do Azure Notebooks

O Azure Notebooks é um serviço alojado gratuito que serve para desenvolver e executar blocos de notas Jupyter na cloud sem instalação. O [Jupyter](https://jupyter.org/) (anteriormente conhecido como ipython) é um projeto de software livre que permite combinar facilmente o texto de redução, o código executável, os dados persistentes, os gráficos e as visualizações em uma única tela compartilhável, o *Notebook* (cortesia da imagem de jupyter.org):

[![exemplos de notebooks Jupyter](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

Devido a essa poderosa combinação de código, gráficos e texto explicativo, a Jupyter tornou-se popular para muitos usos, incluindo instrução de ciência de dados, limpeza e transformação de dados, simulação numérica, modelagem estatística e desenvolvimento de modelos de aprendizado de máquina.

## <a name="hassle-free-experience"></a>Experiência sem complicações

Azure Notebooks ajuda você a começar rapidamente a usar protótipos, ciência de dados, pesquisa acadêmica ou aprender a programar Python:

- Um cientista de dados tem acesso instantâneo a um ambiente Anaconda completo sem instalação.
- Um professor pode fornecer um ambiente Python sem complicações para alunos.
- Um apresentador pode dar a um gosto de falar ou webinar sem pedir que os participantes gastem 45 minutos instalando o software.
- Um desenvolvedor ou entusiasta pode usar blocos de anotações como um bloco de rascunho de código rápido.

Os notebooks se tornam ainda mais eficientes quando as pessoas podem colaborar com eles por meio de um serviço de nuvem acessível por navegador como Azure Notebooks (em versão prévia). Na nuvem, os usuários não precisam instalar o Jupyter localmente ou se preocupar com a manutenção de um ambiente. A nuvem também facilita o compartilhamento de notebooks (e arquivos de dados associados) com outros usuários autorizados, evitando as complicações de compartilhar blocos de anotações por meio de meios externos, como repositórios de controle de origem. Com o Azure Notebooks, os usuários também podem copiar (ou "clonar") blocos de anotações em sua própria conta para modificação ou experimentação, o que é especialmente útil para fins de instrução.

Como Azure Notebooks é uma plataforma de criação de código geral, execução e compartilhamento, você pode usá-la para vários cenários diferentes:

- Aprenda uma nova linguagem de programação – Experimente um dos [tutoriais do FrontPage](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb)
- Aprenda sobre a ciência de dados – Experimente o [livro Jake VanderPlas](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)
- [Ensinar um curso](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas) para centenas de alunos
- Fornecer um webinar online ou em uma conferência sem gastar tempo na instalação 
- Permitir que os usuários do GitHub carreguem e executem blocos de anotações diretamente [criando uma notificação de lançamento do GitHub](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- Dê ao [PowerPoint como apresentações de slides](https://notebooks.azure.com/help/jupyter-notebooks/slides) em que o código nos slides é executável!

Em suma, Azure Notebooks ajuda você a realizar seu trabalho com mais eficiência e, portanto, obter mais.

> [!Note]
> Mais informações sobre o Jupyter em si podem ser encontradas em [jupyter.org](https://jupyter.org/) e na [documentação do Jupyter](https://jupyter-notebook.readthedocs.io/en/latest/).

## <a name="pricing-and-quotas"></a>Preços e quotas

Azure Notebooks é um serviço gratuito, mas cada projeto é limitado a 4 GB de memória e 1GB de dados para evitar abusos. Usuários legítimos que excedem esses limites confiram um desafio CAPTCHA para continuar executando blocos de anotações.

Para liberar todos os limites, entre Azure Notebooks com uma conta usando Azure Active Directory (como uma conta corporativa). Se essa conta estiver associada a uma assinatura do Azure, você poderá se conectar a qualquer instância do Azure Máquina Virtual de Ciência de Dados dentro dessa assinatura. Para obter mais informações, consulte [gerenciar e configurar projetos – camada de computação](configure-manage-azure-notebooks-projects.md#compute-tier).

Os servidores de notebook têm a garantia de existir por no máximo 8 horas. Na maioria dos casos, o contêiner não está sujeito a esse limite e continua a ser executado além desse tempo, mas sessões de longa duração podem ocasionalmente ser desligadas para a estabilidade do sistema.

## <a name="available-kernels-and-environments"></a>Kernels e ambientes disponíveis

Para cada notebook, você seleciona o kernel (ou seja, o ambiente de tempo de execução) que é usado para executar qualquer célula de código. O Azure Notebooks dá suporte aos seguintes kernels:

- Python 2,7 + Anaconda2-5.3.0
- Python 3,6 + Anaconda3-5.3.0
- Python 3,5 + Anaconda3-4.2.0 (será preterido)
- R 3.4.1 + Microsoft R Open 3.4.1
- F#4.1.9

O Azure Notebooks também inclui pacotes extras além das distribuições base. Os kernels do Python, por exemplo, incluem as bibliotecas numpy, pandas, scikit-learn, matplotlib e bokeh.

Você também pode personalizar um projeto para criar um ambiente para todos os blocos de anotações nesse projeto. Para obter mais informações, consulte [início rápido: criar um projeto com um ambiente personalizado](quickstart-create-jupyter-notebook-project-environment.md).

Além das distribuições base, Azure Notebooks vem pré-instalado com muitos pacotes extras que são úteis para cientistas de dados. Você também pode instalar seus próprios pacotes usando o processo típico para cada idioma.

## <a name="pre-configured-jupyter-extensions"></a>Extensões Jupyter pré-configuradas

Azure Notebooks é pré-configurado com as seguintes extensões Jupyter:

- [Elevação](https://github.com/damianavila/RISE): uma extensão de apresentação de slides Jupyter (também conhecida como live_reveal). Para obter mais informações, consulte [executar uma apresentação de slides do notebook](present-jupyter-notebooks-slideshow.md).
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): um ambiente computacional completo para trabalhar com notebooks Jupyter.
- [Altair](https://github.com/ellisonbg/altair): uma biblioteca de visualização estatística declarativa para Python.
- [BQPlot](https://github.com/bloomberg/bqplot): uma estrutura de plotagem interativa para notebooks Jupyter.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): WIDGETs HTML interativos para notebooks Jupyter.

## <a name="issues-and-getting-help"></a>Problemas e obter ajuda

Como Azure Notebooks ainda está em visualização, o serviço pode apresentar interrupções temporárias que podem ser mais frequentes ou mais duradouras do que outros serviços do Azure. Alguns recursos podem estar incompletos ou conter bugs.

No momento, recomendamos o uso de Azure Notebooks visualização para aplicativos críticos para os negócios, ou blocos de anotações e dados confidenciais.

Para discutir suas perguntas sobre Azure Notebooks, execute um problema no [repositório do GitHub](https://github.com/Microsoft/AzureNotebooks/issues).

## <a name="next-steps"></a>Passos seguintes  

- [Explorar blocos de anotações de exemplo](azure-notebooks-samples.md)

- Guias

  - [Criar e compartilhar um bloco de anotações](quickstart-create-share-jupyter-notebook.md)
  - [Clonar um bloco de anotações](quickstart-clone-jupyter-notebook.md)
  - [Migrar um notebook Jupyter local](quickstart-migrate-local-jupyter-notebook.md)
  - [Usar um ambiente personalizado](quickstart-create-jupyter-notebook-project-environment.md)
  - [Entrar e definir uma ID de usuário](quickstart-sign-in-azure-notebooks.md)

- Tutoriais:

  - [Criar e executar um bloco de anotações](tutorial-create-run-jupyter-notebook.md  )

- Artigos de instruções:
  
  - [Criar e clonar projetos](create-clone-jupyter-notebooks.md)
  - [Configurar e gerenciar projetos](configure-manage-azure-notebooks-projects.md)
  - [Instalar pacotes de dentro de um bloco de anotações](install-packages-jupyter-notebook.md)
  - [Apresentar uma apresentação de slides](present-jupyter-notebooks-slideshow.md)
  - [Trabalhar com arquivos de dados](work-with-project-data-files.md)
  - [Acessar recursos de dados](access-data-resources-jupyter-notebooks.md)
  - [Usar Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
