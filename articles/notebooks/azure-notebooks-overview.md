---
title: Descrição geral dos blocos de notas do Azure
description: Execute blocos de notas do Jupyter na cloud com o serviço de blocos de notas do Azure gratuito, onde não é necessária nenhuma configuração ou a configuração.
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
ms.openlocfilehash: 12d9a1a92c0b008244c7db4ec23d5a28888b042a
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973171"
---
# <a name="overview-of-azure-notebooks"></a>Descrição geral dos blocos de notas do Azure

O Azure Notebooks é um serviço alojado gratuito que serve para desenvolver e executar blocos de notas Jupyter na cloud sem instalação. [Jupyter](https://jupyter.org/) (anteriormente denominado IPython) é um projeto de código-fonte aberto que lhe permite facilmente combinar o texto de Markdown, código executável, dados persistentes, gráficos e visualizações para uma tela partilháveis única, o *bloco de notas* (imagem cortesia jupyter.org):

[![Examples de blocos de anotações do Jupyter](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

Devido a esta poderosa combinação de código, gráficos e texto explicativo, Jupyter tornou conhecido por muitos usos, incluindo instruções de ciência de dados, limpeza de dados e transformação, simulação numérica, modelação estatística e o desenvolvimento de modelos de Machine learning.

## <a name="hassle-free-experience"></a>Experiência sem preocupações

Blocos de notas do Azure ajuda-o a começar rapidamente a criação de protótipos, ciência de dados, investigação académica ou learning para o programa de Python:

- Um cientista de dados tem acesso instantâneo a um ambiente completo de Anaconda sem instalação.
- Um professor pode fornecer um ambiente de Python obstáculos para estudantes.
- Um apresentador pode permitir que um tipo conversa ou webinar sem pedir participantes para gastar 45 minutos a instalação de software.
- Um desenvolvedor ou entusiasta pode usar blocos de notas como um bloco de rascunho do código rápido.

Blocos de notas se tornar ainda mais poderosos quando as pessoas podem colaborar nos mesmos através de um serviço cloud acessível para o navegador como blocos de notas do Azure (em pré-visualização). Na cloud, os utilizadores não precisam instalar o Jupyter localmente ou se eles próprios com a manutenção de um ambiente. A cloud também simplifica a partilha de blocos de notas (e arquivos de dados associados) com outros utilizadores autorizados, evitando as complicações do compartilhamento de blocos de notas através de meios externos, como repositórios de controlo de origem. Com blocos de notas do Azure, os utilizadores podem também blocos de notas de cópia (ou "clonar") em sua própria conta para modificação ou a experimentação, o que é especialmente útil para fins de instrução.

Como blocos de notas do Azure é um código geral de criação, execução e a partilha de plataforma, pode usá-lo para vários cenários diferentes:

- Aprenda uma nova linguagem de programação – experimente um do [tutoriais do frontpage](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb)
- Saiba de ciência de dados – experimente [livro de Jake VanderPlas](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)
- [Ensinar um curso](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas) para centenas de estudantes
- Dar um webinar online ou numa conferência sem tempo gastos sobre a instalação 
- Permitir que os utilizadores GitHub diretamente carregar e executar blocos de notas por [criando um destaque de iniciação do GitHub](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- Dê [PowerPoint como apresentações de slides](https://notebooks.azure.com/help/jupyter-notebooks/slides) onde o código em slides é executável!

Em resumo, blocos de notas do Azure ajuda-o a realizar seu trabalho com mais eficiência e, portanto, obter muito mais.

> [!Note]
> Obter mais informações sobre o Jupyter em si podem ser encontradas no [jupyter.org](https://jupyter.org/) e, no [Jupyter documentação](https://jupyter-notebook.readthedocs.io/en/latest/).

## <a name="pricing-and-quotas"></a>Preços e quotas

Azure Notebooks é um serviço gratuito, mas cada projeto é limitado a 4 GB de memória e 1GB de dados para evitar abusos. Os usuários legítimos que excederem estes limites veem um desafio de Captcha para continuar a executar blocos de notas.

Para liberar todos os limites, entre Azure Notebooks com uma conta usando Azure Active Directory (como uma conta corporativa). Se essa conta estiver associada a uma assinatura do Azure, você poderá se conectar a qualquer instância do Azure Máquina Virtual de Ciência de Dados dentro dessa assinatura. Para obter mais informações, consulte [gerenciar e configurar projetos – camada de computação](configure-manage-azure-notebooks-projects.md#compute-tier).

Os servidores de notebook têm a garantia de existir por no máximo 8 horas. Na maioria dos casos, o contêiner não está sujeito a esse limite e continua a ser executado além desse tempo, mas sessões de longa duração podem ocasionalmente ser desligadas para a estabilidade do sistema.

## <a name="available-kernels-and-environments"></a>Kernels disponíveis e ambientes

Para cada bloco de notas, selecione o kernel (ou seja, o ambiente de tempo de execução), que é utilizado para executar qualquer células de código. Blocos de notas do Azure suporta os kernels seguintes:

- Python 2.7 + Anaconda2 5.3.0
- Python 3.6 + Anaconda3 5.3.0
- Python 3.5 + Anaconda3-4.2.0 (vão ser preteridos)
- R 3.4.1 + Microsoft R Open 3.4.1
- F#4.1.9

Blocos de notas do Azure também inclui pacotes adicionais para além das distribuições de bases. Os kernels do Python, por exemplo, incluem a numpy, pandas, scikit-saiba, matplotlib e bokeh bibliotecas.

Também pode personalizar um projeto para criar um ambiente para todos os blocos de notas desse projeto. Para obter mais informações, consulte [Quickstart: Crie um projeto com um ambiente personalizado @ no__t-0.

Além das distribuições de bases, os blocos de notas do Azure vem pré-instalada com muitos pacotes adicionais que são úteis para os cientistas de dados. Também pode instalar seus próprios pacotes usando o processo normal para cada idioma.

## <a name="pre-configured-jupyter-extensions"></a>Extensões de Jupyter pré-configurados

Blocos de notas do Azure é pré-configurado com as seguintes extensões de Jupyter:

- [AUMENTO](https://github.com/damianavila/RISE): Uma extensão de apresentação de slides Jupyter (também conhecida como live_reveal). Para obter mais informações, consulte [executar uma apresentação de slides do bloco de notas](present-jupyter-notebooks-slideshow.md).
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): Um ambiente computacional completo para trabalhar com notebooks Jupyter.
- [Altair](https://github.com/ellisonbg/altair): Uma biblioteca de visualização estatística declarativa para Python.
- [BQPlot](https://github.com/bloomberg/bqplot): Uma estrutura de plotagem interativa para notebooks Jupyter.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): Widgets HTML interativos para notebooks Jupyter.

## <a name="issues-and-getting-help"></a>Problemas e obter ajuda

Uma vez que blocos de notas do Azure ainda está em pré-visualização, o serviço poderá experienciar falhas temporárias de que podem ser maior ou mais duradouras relativamente a outros serviços do Azure. Algumas funcionalidades podem estar incompletos ou conter bugs.

No momento, é recomendável contra a utilização de pré-visualização de blocos de notas do Azure para aplicações críticas para a empresa, ou blocos de notas confidenciais e dados.

Para discutir suas perguntas sobre blocos de notas do Azure, enviar um problema no [repositório do GitHub](https://github.com/Microsoft/AzureNotebooks/issues).

## <a name="next-steps"></a>Passos Seguintes  

- [Explore os blocos de notas de exemplo](azure-notebooks-samples.md)

- Inícios rápidos:

  - [Criar e partilhar um bloco de notas](quickstart-create-share-jupyter-notebook.md)
  - [Clonar um bloco de notas](quickstart-clone-jupyter-notebook.md)
  - [Migrar de um bloco de notas Jupyter local](quickstart-migrate-local-jupyter-notebook.md)
  - [Utilizar um ambiente personalizado](quickstart-create-jupyter-notebook-project-environment.md)
  - [Iniciar sessão e defina um ID de utilizador](quickstart-sign-in-azure-notebooks.md)

- Tutoriais:

  - [Criar e executar um bloco de notas](tutorial-create-run-jupyter-notebook.md  )

- Artigos de procedimentos:
  
  - [Criar e clonar projetos](create-clone-jupyter-notebooks.md)
  - [Configurar e gerir projetos](configure-manage-azure-notebooks-projects.md)
  - [Instalar pacotes a partir de um bloco de notas](install-packages-jupyter-notebook.md)
  - [Apresentar uma apresentação de diapositivos](present-jupyter-notebooks-slideshow.md)
  - [Trabalhar com arquivos de dados](work-with-project-data-files.md)
  - [Aceder a recursos de dados](access-data-resources-jupyter-notebooks.md)
  - [Utilizar o Azure Machine Learning Services](use-machine-learning-services-jupyter-notebooks.md)
