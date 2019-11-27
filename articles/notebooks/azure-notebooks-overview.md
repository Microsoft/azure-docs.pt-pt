---
title: Descrição geral dos blocos de notas do Azure
description: Execute blocos de notas do Jupyter na cloud com o serviço de blocos de notas do Azure gratuito, onde não é necessária nenhuma configuração ou a configuração.
ms.topic: overview
ms.date: 04/05/2019
ms.openlocfilehash: b3dfa6cec962809fad1a03f5100c55315ff6a9ad
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277670"
---
# <a name="overview-of-azure-notebooks"></a>Descrição geral dos blocos de notas do Azure

O Azure Notebooks é um serviço alojado gratuito que serve para desenvolver e executar blocos de notas Jupyter na cloud sem instalação. O [Jupyter](https://jupyter.org/) (anteriormente conhecido como ipython) é um projeto de software livre que permite combinar facilmente o texto de redução, o código executável, os dados persistentes, os gráficos e as visualizações em uma única tela compartilhável, o *Notebook* (cortesia da imagem de jupyter.org):

[![exemplos de notebooks Jupyter](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

Devido a esta poderosa combinação de código, gráficos e texto explicativo, Jupyter tornou conhecido por muitos usos, incluindo instruções de ciência de dados, limpeza de dados e transformação, simulação numérica, modelação estatística e o desenvolvimento de modelos de Machine learning.

## <a name="hassle-free-experience"></a>Experiência sem preocupações

Blocos de notas do Azure ajuda-o a começar rapidamente a criação de protótipos, ciência de dados, investigação académica ou learning para o programa de Python:

- Um cientista de dados tem acesso instantâneo a um ambiente completo de Anaconda sem instalação.
- Um professor pode fornecer um ambiente de Python obstáculos para estudantes.
- Um apresentador pode permitir que um tipo conversa ou webinar sem pedir participantes para gastar 45 minutos a instalação de software.
- Um desenvolvedor ou entusiasta pode usar blocos de notas como um bloco de rascunho do código rápido.

Blocos de notas se tornar ainda mais poderosos quando as pessoas podem colaborar nos mesmos através de um serviço cloud acessível para o navegador como blocos de notas do Azure (em pré-visualização). Na cloud, os utilizadores não precisam instalar o Jupyter localmente ou se eles próprios com a manutenção de um ambiente. A cloud também simplifica a partilha de blocos de notas (e arquivos de dados associados) com outros utilizadores autorizados, evitando as complicações do compartilhamento de blocos de notas através de meios externos, como repositórios de controlo de origem. Com blocos de notas do Azure, os utilizadores podem também blocos de notas de cópia (ou "clonar") em sua própria conta para modificação ou a experimentação, o que é especialmente útil para fins de instrução.

Como blocos de notas do Azure é um código geral de criação, execução e a partilha de plataforma, pode usá-lo para vários cenários diferentes:

- Aprenda uma nova linguagem de programação – Experimente um dos [tutoriais do FrontPage](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb)
- Aprenda sobre a ciência de dados – Experimente o [livro Jake VanderPlas](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)
- [Ensinar um curso](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas) para centenas de alunos
- Dar um webinar online ou numa conferência sem tempo gastos sobre a instalação 
- Permitir que os usuários do GitHub carreguem e executem blocos de anotações diretamente [criando uma notificação de lançamento do GitHub](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- Dê ao [PowerPoint como apresentações de slides](https://notebooks.azure.com/help/jupyter-notebooks/slides) em que o código nos slides é executável!

Em resumo, blocos de notas do Azure ajuda-o a realizar seu trabalho com mais eficiência e, portanto, obter muito mais.

> [!Note]
> Mais informações sobre o Jupyter em si podem ser encontradas em [jupyter.org](https://jupyter.org/) e na [documentação do Jupyter](https://jupyter-notebook.readthedocs.io/en/latest/).

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

Também pode personalizar um projeto para criar um ambiente para todos os blocos de notas desse projeto. Para obter mais informações, consulte [início rápido: criar um projeto com um ambiente personalizado](quickstart-create-jupyter-notebook-project-environment.md).

Além das distribuições de bases, os blocos de notas do Azure vem pré-instalada com muitos pacotes adicionais que são úteis para os cientistas de dados. Também pode instalar seus próprios pacotes usando o processo normal para cada idioma.

## <a name="pre-configured-jupyter-extensions"></a>Extensões de Jupyter pré-configurados

Blocos de notas do Azure é pré-configurado com as seguintes extensões de Jupyter:

- [Elevação](https://github.com/damianavila/RISE): uma extensão de apresentação de slides Jupyter (também conhecida como live_reveal). Para obter mais informações, consulte [executar uma apresentação de slides do notebook](present-jupyter-notebooks-slideshow.md).
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): um ambiente computacional completo para trabalhar com notebooks Jupyter.
- [Altair](https://github.com/ellisonbg/altair): uma biblioteca de visualização estatística declarativa para Python.
- [BQPlot](https://github.com/bloomberg/bqplot): uma estrutura de plotagem interativa para notebooks Jupyter.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): WIDGETs HTML interativos para notebooks Jupyter.

## <a name="issues-and-getting-help"></a>Problemas e obter ajuda

Uma vez que blocos de notas do Azure ainda está em pré-visualização, o serviço poderá experienciar falhas temporárias de que podem ser maior ou mais duradouras relativamente a outros serviços do Azure. Algumas funcionalidades podem estar incompletos ou conter bugs.

No momento, é recomendável contra a utilização de pré-visualização de blocos de notas do Azure para aplicações críticas para a empresa, ou blocos de notas confidenciais e dados.

Para discutir suas perguntas sobre Azure Notebooks, execute um problema no [repositório do GitHub](https://github.com/Microsoft/AzureNotebooks/issues).

## <a name="next-steps"></a>Passos seguintes  

- [Explorar blocos de anotações de exemplo](azure-notebooks-samples.md)

- Inícios rápidos:

  - [Criar e compartilhar um bloco de anotações](quickstart-create-share-jupyter-notebook.md)
  - [Clonar um bloco de anotações](quickstart-clone-jupyter-notebook.md)
  - [Migrar um notebook Jupyter local](quickstart-migrate-local-jupyter-notebook.md)
  - [Usar um ambiente personalizado](quickstart-create-jupyter-notebook-project-environment.md)
  - [Entrar e definir uma ID de usuário](quickstart-sign-in-azure-notebooks.md)

- Tutoriais:

  - [Criar e executar um bloco de anotações](tutorial-create-run-jupyter-notebook.md  )

- Artigos de procedimentos:
  
  - [Criar e clonar projetos](create-clone-jupyter-notebooks.md)
  - [Configurar e gerenciar projetos](configure-manage-azure-notebooks-projects.md)
  - [Instalar pacotes de dentro de um bloco de anotações](install-packages-jupyter-notebook.md)
  - [Apresentar uma apresentação de slides](present-jupyter-notebooks-slideshow.md)
  - [Trabalhar com arquivos de dados](work-with-project-data-files.md)
  - [Acessar recursos de dados](access-data-resources-jupyter-notebooks.md)
  - [Usar Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
