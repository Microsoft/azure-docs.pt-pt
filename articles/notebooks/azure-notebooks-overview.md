---
title: Visão geral da pré-visualização dos cadernos Azure
description: Executar os cadernos Jupyter na nuvem utilizando o serviço gratuito de pré-visualização de cadernos Azure, onde não é necessária configuração ou configuração.
ms.topic: overview
ms.date: 04/05/2019
ms.openlocfilehash: d59faaf85a1fdbad776e954e974bafa300f93d85
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94845140"
---
# <a name="overview-of-azure-notebooks-preview"></a>Visão geral da pré-visualização dos cadernos Azure

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

O Azure Notebooks é um serviço alojado gratuito que serve para desenvolver e executar blocos de notas Jupyter na cloud sem instalação. [Jupyter](https://jupyter.org/) (anteriormente IPython) é um projeto de código aberto que permite combinar facilmente texto markdown, código executável, dados persistentes, gráficos e visualizações em uma única tela sharable, chamada *caderno* (imagem cortesia de jupyter.org):

[![Exemplos de cadernos Jupyter](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

Devido a esta poderosa combinação de código, gráficos e texto explicativo, Jupyter tornou-se popular para muitos usos, incluindo instrução de ciência de dados, limpeza e transformação de dados, simulação numérica, modelação estatística e desenvolvimento de modelos de aprendizagem automática.

## <a name="hassle-free-experience"></a>Experiência sem ações

Os Cadernos Azure ajudam-no a começar rapidamente em prototipagem, ciência de dados, pesquisa académica ou aprendizagem para programar Python:

- Um cientista de dados tem acesso instantâneo a um ambiente anaconda completo sem instalação.
- Um professor pode fornecer um ambiente Python livre de ações aos alunos.
- Um apresentador pode dar uma conversa semelhante ou webinar sem pedir aos participantes que passem 45 minutos a instalar software.
- Um desenvolvedor ou hobbyista pode usar os Cadernos como uma raspadinha de código rápida.

Os cadernos tornam-se ainda mais poderosos quando as pessoas podem colaborar neles através de um serviço de nuvem acessível ao navegador, como os Azure Notebooks (em Preview). Na nuvem, os utilizadores não precisam de instalar o Jupyter localmente ou preocupar-se em manter um ambiente. A nuvem também torna simples a partilha de cadernos (e ficheiros de dados associados) com outros utilizadores autorizados, evitando as complicações da partilha de cadernos através de meios externos, como repositórios de controlo de fontes. Com os Cadernos Azure, os utilizadores também podem copiar (ou "clones") cadernos na sua própria conta para modificação ou experimentação, o que é especialmente útil para fins de instrução.

Como o Azure Notebooks é uma plataforma de autoria, execução e partilha de códigos gerais, pode usá-lo para diversos cenários:

- Aprenda uma nova linguagem de programação – experimente um dos [tutoriais](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb) de primeira página
- Learn Data Science - experimente o [livro de Jake VanderPlas](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)
- [Ensinar um curso](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas) para centenas de alunos
- Dê um webinar on-line ou em uma conferência sem passar tempo na instalação 
- Permitir que os utilizadores do GitHub carreguem e executem diretamente cadernos [criando um crachá de lançamento do GitHub](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- Dê [ao PowerPoint como slideshows](https://notebooks.azure.com/help/jupyter-notebooks/slides) onde o código nos slides é executável!

Em suma, os Cadernos Azure ajudam-no a realizar o seu trabalho de forma mais eficiente e, assim, a conseguir mais.

> [!Note]
> Mais informações sobre o próprio Jupyter podem ser encontradas em [jupyter.org](https://jupyter.org/) e na documentação do [Jupyter.](https://jupyter-notebook.readthedocs.io/en/latest/)

## <a name="pricing-and-quotas"></a>Preços e quotas

O Azure Notebooks é um serviço gratuito, mas cada projeto está limitado a 4GB de memória e dados de 1GB para prevenir abusos. Os utilizadores legítimos que excedam estes limites vêem um desafio do Captcha para continuar a executar cadernos.

Para libertar todos os limites, inscreva-se em Azure Notebooks com uma conta utilizando o Azure Ative Directory (como uma conta corporativa). Se essa conta estiver associada a uma subscrição do Azure, pode ligar-se a quaisquer instâncias da Azure Data Science Virtual Machine dentro dessa subscrição. Para obter mais informações, consulte [Gerir e configurar projetos - Nível compute](configure-manage-azure-notebooks-projects.md#compute-tier).

Os servidores de portátil são garantidos que existem durante no máximo 8 horas. Na maioria dos casos, o seu recipiente não está sujeito a este limite e continua a ir além deste tempo, mas sessões de longa duração podem ocasionalmente ser encerradas para estabilidade do sistema.

## <a name="available-kernels-and-environments"></a>Núcleos e ambientes disponíveis

Para cada caderno, selecione o núcleo (isto é, o ambiente de tempo de execução) que é usado para executar quaisquer células de código. A Azure Notebooks suporta os seguintes núcleos:

- Python 2.7 + Anaconda2-5.3.0
- Python 3.6 + Anaconda3-5.3.0
- Python 3.5 + Anaconda3-4.2.0 (será depreciado)
- R 3.4.1 + Microsoft R Open 3.4.1
- F# 4.1.9

Os Cadernos Azure também incluem pacotes extra para além das distribuições base. Os núcleos python, por exemplo, incluem as bibliotecas dormentes, pandas, scikit-learn, matplotlib e bokeh.

Você também pode personalizar um projeto para criar um ambiente para todos os cadernos nesse projeto. Para mais informações, consulte [Quickstart: Criar um projeto com um ambiente personalizado.](quickstart-create-jupyter-notebook-project-environment.md)

Além das distribuições base, os Cadernos Azure vêm pré-instalados com muitos pacotes extra que são úteis para cientistas de dados. Também pode instalar os seus próprios pacotes usando o processo típico para cada idioma.

## <a name="pre-configured-jupyter-extensions"></a>Extensões jupyter pré-configuradas

Os Cadernos Azure estão pré-configurados com as seguintes extensões Jupyter:

- [RISE](https://github.com/damianavila/RISE): Uma extensão jupyter slideshow (também conhecida como live_reveal). Para obter mais informações, consulte [executar uma apresentação de diapositivos de caderno.](present-jupyter-notebooks-slideshow.md)
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): Um ambiente computacional completo para trabalhar com cadernos Jupyter.
- [Altair](https://github.com/ellisonbg/altair): Uma biblioteca de visualização estatística declarativa para Python.
- [BQPlot](https://github.com/bloomberg/bqplot): Uma estrutura de conspiração interativa para cadernos Jupyter.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): Widgets HTML interativos para cadernos Jupyter.

## <a name="issues-and-getting-help"></a>Problemas e obter ajuda

Como os Cadernos Azure ainda estão em Pré-visualização, o serviço pode sofrer interrupções temporárias que podem ser mais frequentes ou mais duradouras do que outros serviços Azure. Algumas funcionalidades podem estar incompletas ou conter bugs.

Atualmente, recomendamos que não utilize a Azure Notebooks Preview para aplicações críticas ao negócio, ou cadernos e dados sensíveis.

Para discutir as suas perguntas sobre os Cadernos Azure, preencha uma questão sobre o [repositório do GitHub.](https://github.com/Microsoft/AzureNotebooks/issues)

## <a name="next-steps"></a>Passos seguintes  

- [Explore cadernos de amostras](azure-notebooks-samples.md)

- Guias de Início Rápido:

  - [Criar e partilhar um caderno](quickstart-create-share-jupyter-notebook.md)
  - [Clone um caderno](quickstart-clone-jupyter-notebook.md)
  - [Migrar um caderno jupyter local](quickstart-migrate-local-jupyter-notebook.md)
  - [Utilizar um ambiente personalizado](quickstart-create-jupyter-notebook-project-environment.md)
  - [Iniciar sessão e definir um ID de utilizador](quickstart-sign-in-azure-notebooks.md)

- Tutoriais:

  - [Criar e executar um bloco de notas](tutorial-create-run-jupyter-notebook.md  )

- Artigos de como fazer:
  
  - [Criar e clonar projetos](create-clone-jupyter-notebooks.md)
  - [Configurar e gerir projetos](configure-manage-azure-notebooks-projects.md)
  - [Instalar pacotes a partir de um caderno](install-packages-jupyter-notebook.md)
  - [Apresentar diapositivos](present-jupyter-notebooks-slideshow.md)
  - [Trabalhar com ficheiros de dados](work-with-project-data-files.md)
  - [Aceder a recursos de dados](access-data-resources-jupyter-notebooks.md)
  - [Utilizar o Azure Machine Learning](../machine-learning/samples-notebooks.md)