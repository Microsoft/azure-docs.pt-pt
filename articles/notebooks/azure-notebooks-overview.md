---
title: Visão geral da pré-visualização dos cadernos azure
description: Executar os cadernos Jupyter na nuvem utilizando o serviço gratuito de pré-visualização de cadernos Azure, onde não é necessária configuração ou configuração.
ms.topic: overview
ms.date: 04/05/2019
ms.openlocfilehash: bd2355bdefcedca5026a25915dc1da55fdc33a36
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75646335"
---
# <a name="overview-of-azure-notebooks-preview"></a>Visão geral da pré-visualização dos cadernos azure

O Azure Notebooks é um serviço alojado gratuito que serve para desenvolver e executar blocos de notas Jupyter na cloud sem instalação. [Jupyter](https://jupyter.org/) (anteriormente IPython) é um projeto de código aberto que permite combinar facilmente texto Markdown, código executável, dados persistentes, gráficos e visualizações em uma única tela sharable, o *caderno* (imagem cortesia de jupyter.org):

[![Exemplos de cadernos Jupyter](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

Devido a esta poderosa combinação de código, gráficos e texto explicativo, Jupyter tornou-se popular para muitos usos, incluindo instrução de ciência de dados, limpeza e transformação de dados, simulação numérica, modelação estatística, e o desenvolvimento de modelos de aprendizagem automática.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="hassle-free-experience"></a>Experiência sem aborrecimentos

Os Cadernos Azure ajudam-no a começar rapidamente em prototipagem, ciência de dados, investigação académica ou aprendizagem para programar Python:

- Um cientista de dados tem acesso instantâneo a um ambiente completo da Anaconda sem instalação.
- Um professor pode fornecer um ambiente python livre de aborrecimentos aos alunos.
- Um apresentador pode dar uma palestra semelhante ou webinar sem pedir aos participantes que passem 45 minutos a instalar software.
- Um desenvolvedor ou hobby pode usar Cadernos como uma raspadinha de código rápido.

Os cadernos tornam-se ainda mais poderosos quando as pessoas podem colaborar neles através de um serviço de nuvem acessível ao navegador como o Azure Notebooks (em Pré-visualização). Na nuvem, os utilizadores não precisam de instalar a Jupyter localmente nem se preocuparem em manter um ambiente. A nuvem também torna simples partilhar cadernos (e ficheiros de dados associados) com outros utilizadores autorizados, evitando as complicações de partilhar cadernos através de meios externos, como repositórios de controlo de fonte. Com os Cadernos Azure, os utilizadores também podem copiar (ou "clone") cadernos na sua própria conta para modificação ou experimentação, o que é especialmente útil para fins de instrução.

Como o Azure Notebooks é uma plataforma de autor, execução e partilha de códigos gerais, pode usá-lo para muitos cenários diversos:

- Aprenda uma nova linguagem de programação - experimente um dos [tutoriais](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb) de primeira página
- Learn Data Science - experimente [o livro de Jake VanderPlas](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)
- [Ensine um curso](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas) para centenas de alunos
- Dê um webinar on-line ou em uma conferência sem gastar tempo na instalação 
- Ativar os utilizadores do GitHub para carregar e executar diretamente os cadernos criando um crachá de [lançamento GitHub](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- Dê ao [PowerPoint como slideshows](https://notebooks.azure.com/help/jupyter-notebooks/slides) onde o código em diapositivos é executável!

Em suma, os Cadernos Azure ajudam-no a realizar o seu trabalho de forma mais eficiente e assim conseguir mais.

> [!Note]
> Mais informações sobre o próprio Jupyter podem ser encontradas em [jupyter.org](https://jupyter.org/) e na [documentação de Jupyter.](https://jupyter-notebook.readthedocs.io/en/latest/)

## <a name="pricing-and-quotas"></a>Preços e quotas

O Azure Notebooks é um serviço gratuito, mas cada projeto está limitado a memória de 4GB e dados de 1GB para prevenir abusos. Os utilizadores legítimos que excedam estes limites vêem um desafio captcha para continuar a executar cadernos.

Para libertar todos os limites, assine nos Cadernos Azure com uma conta utilizando o Azure Ative Directory (como uma conta corporativa). Se essa conta estiver associada a uma subscrição do Azure, pode ligar-se a quaisquer instâncias da Máquina Virtual de Ciência de Dados do Azure dentro dessa subscrição. Para mais informações, consulte [Gerir e configurar projetos - Compute tier](configure-manage-azure-notebooks-projects.md#compute-tier).

Os servidores de cadernos são garantidos para existir durante o máximo 8 horas. Na maioria dos casos, o seu contentor não está sujeito a este limite e continua a correr para além deste tempo, mas as sessões de longa duração podem ocasionalmente ser encerradas para estabilidade do sistema.

## <a name="available-kernels-and-environments"></a>Núcleos e ambientes disponíveis

Para cada caderno, selecione o núcleo (isto é, o ambiente de tempo de execução) que é usado para executar quaisquer células de código. Os Cadernos Azure suportam os seguintes núcleos:

- Python 2.7 + Anaconda2-5.3.0
- Python 3.6 + Anaconda3-5.3.0
- Python 3.5 + Anaconda3-4.2.0 (será depreciado)
- R 3.4.1 + Microsoft R Open 3.4.1
- F# 4.1.9

Os Cadernos Azure também incluem pacotes extra para além das distribuições de base. Os núcleos python, por exemplo, incluem as bibliotecas dormentes, pandas, scikit-learn, matplotlib e bokeh.

Também pode personalizar um projeto para criar um ambiente para todos os cadernos desse projeto. Para mais informações, consulte [Quickstart: Crie um projeto com um ambiente personalizado.](quickstart-create-jupyter-notebook-project-environment.md)

Além das distribuições de base, os Cadernos Azure vêm pré-instalados com muitos pacotes extra que são úteis para os cientistas de dados. Também pode instalar os seus próprios pacotes utilizando o processo típico para cada idioma.

## <a name="pre-configured-jupyter-extensions"></a>Extensões jupyter pré-configuradas

Os Cadernos Azure são pré-configurados com as seguintes extensões Jupyter:

- [ASCENSÃO](https://github.com/damianavila/RISE): Uma extensão de apresentação de diapositivos de jupyter (também conhecida como live_reveal). Para mais informações, consulte Executar uma apresentação de [diapositivos portátil](present-jupyter-notebooks-slideshow.md).
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): Um ambiente computacional completo para trabalhar com cadernos Jupyter.
- [Altair](https://github.com/ellisonbg/altair): Uma biblioteca de visualização estatística declarativa para Python.
- [BQPlot](https://github.com/bloomberg/bqplot): Um quadro de conspiração interativa para os Cadernos Jupyter.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): Widgets HTML Interativos para Cadernos Jupyter.

## <a name="issues-and-getting-help"></a>Problemas e obtenção de ajuda

Como os Cadernos Azure ainda se encontra em Pré-visualização, o serviço pode experimentar interrupções temporárias que podem ser mais frequentes ou mais duradouras do que outros serviços Azure. Algumas funcionalidades podem estar incompletas ou conter bugs.

Atualmente, recomendamos não utilizar os Cadernos Azure Preview para aplicações críticas ao negócio, ou cadernos e dados sensíveis.

Para discutir as suas perguntas sobre os Cadernos Azure, apresente um problema no [repositório GitHub.](https://github.com/Microsoft/AzureNotebooks/issues)

## <a name="next-steps"></a>Passos seguintes  

- [Explore os cadernos de amostras](azure-notebooks-samples.md)

- Guias de Início Rápido:

  - [Criar e partilhar um bloco de notas](quickstart-create-share-jupyter-notebook.md)
  - [Clonar um bloco de notas](quickstart-clone-jupyter-notebook.md)
  - [Migrar um bloco de notas Jupyter local](quickstart-migrate-local-jupyter-notebook.md)
  - [Utilizar um ambiente personalizado](quickstart-create-jupyter-notebook-project-environment.md)
  - [Iniciar sessão e definir um ID de utilizador](quickstart-sign-in-azure-notebooks.md)

- Tutoriais:

  - [Criar e executar um bloco de notas](tutorial-create-run-jupyter-notebook.md  )

- Artigos:
  
  - [Criar e clonar projetos](create-clone-jupyter-notebooks.md)
  - [Configurar e gerir projetos](configure-manage-azure-notebooks-projects.md)
  - [Instale pacotes dentro de um caderno](install-packages-jupyter-notebook.md)
  - [Apresentar diapositivos](present-jupyter-notebooks-slideshow.md)
  - [Trabalhar com ficheiros de dados](work-with-project-data-files.md)
  - [Aceder a recursos de dados](access-data-resources-jupyter-notebooks.md)
  - [Utilizar o Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
