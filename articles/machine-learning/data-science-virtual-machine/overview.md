---
title: O que é o Máquina Virtual de Ciência de Dados do Azure
description: Principais cenários e componentes de análise para Máquinas Virtuais de Ciência de Dados do Windows e Linux.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 02/22/2019
ms.openlocfilehash: 3295a59ee4496d332f0d886c89ca900ab6b4bcd1
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191906"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>O que é o Máquina Virtual de Ciência de Dados do Azure para Linux e Windows?

O Máquina Virtual de Ciência de Dados (DSVM) é uma imagem de VM personalizada na plataforma de nuvem do Azure criada especificamente para a ciência de dados. Tem muitas ferramentas populares de ciência de dados e outras pré-instaladas e pré-configuradas para permitir a rápida criação de aplicações inteligentes para análises avançadas. 

As configurações de ferramentas são rigorosamente testadas por cientistas de dados e desenvolvedores na Microsoft e pela comunidade de ciência de dados mais ampla. Esse teste ajuda a garantir a estabilidade e a viabilidade geral.

O DSVM está disponível em:
+ Windows Server 2016, Windows Server 2012
+ Ubuntu 16, 4 LTS e CentOS 7,4

> [!NOTE]
> Todas as ferramentas de VM para aprendizado profundo foram dobradas na Máquina Virtual de Ciência de Dados. 


## <a name="what-can-i-do-with-the-dsvm"></a>O que posso fazer com o DSVM?
O objetivo do Máquina Virtual de Ciência de Dados é fornecer aos profissionais de dados de todos os níveis de habilidade e entre os setores um ambiente de ciência de dados sem interrupções, pré-configurado e totalmente integrado. Em vez de distribuir um espaço de trabalho comparável por conta própria, você pode provisionar um DSVM. Essa escolha pode economizar dias ou até mesmo _semanas_ nos processos de instalação, configuração e gerenciamento de pacotes. Quando a DSVM estiver alocada, pode começar a trabalhar imediatamente no seu projeto de ciência de dados.

O DSVM é projetado e configurado para trabalhar com uma ampla gama de cenários de uso. Você pode dimensionar seu ambiente para cima ou para baixo conforme os requisitos do seu projeto mudam. Você também pode usar seu idioma preferido para programar tarefas de ciência de dados e instalar outras ferramentas para personalizar o sistema para suas necessidades.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Ambiente de trabalho de análise pré-configurado na cloud
O DSVM fornece uma configuração de linha de base para equipes de ciência de dados que querem substituir suas áreas de trabalho locais por uma área de trabalho de nuvem gerenciada. Esta linha base garante que todos os cientistas de dados de uma equipa têm uma configuração consistente com a qual podem verificar as experimentações e promover a colaboração. Ele também reduz os custos reduzindo a carga sysadmin. Essa redução de carga economiza o tempo necessário para avaliar, instalar e manter pacotes de software para análise avançada.

### <a name="data-science-training-and-education"></a>Formação e educação em ciência de dados
Os treinadores e educadores empresariais que ensinam classes de ciência de dados geralmente fornecem uma imagem de máquina virtual. A imagem garante que os alunos tenham uma configuração consistente e que os exemplos funcionem de modo previsível. 

O DSVM cria um ambiente sob demanda com uma configuração consistente que facilita os desafios de suporte e incompatibilidade. Os casos em que estes ambientes têm de ser criados constantemente, especialmente para aulas de formação mais pequenas, retiram benefícios significativos destas características.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Capacidade elástica a pedido para projetos de grande escala
A ciência de dados hackathons/competições ou a modelagem e a exploração de dados em larga escala exigem capacidade de hardware expandida, normalmente por duração curta. O DSVM pode ajudar a replicar o ambiente de ciência de dados rapidamente sob demanda, em servidores expandidos que permitem testes que os recursos de computação de alta potência podem executar.

### <a name="custom-compute-power-for-azure-notebooks"></a>Potência de computação personalizada para Azure Notebooks
[Azure notebooks](../../notebooks/azure-notebooks-overview.md) é um serviço hospedado gratuito para desenvolver, executar e compartilhar blocos de anotações do Jupyter na nuvem sem instalação. A camada de serviço gratuita é limitada a 4 GB de memória e 1 GB de dados. 

Para liberar todos os limites, você pode anexar um projeto de blocos de anotações a um DSVM ou a qualquer outra VM em execução em um servidor Jupyter. Se você entrar no Azure Notebooks com uma conta usando Azure Active Directory (como uma conta corporativa), os blocos de anotações mostrarão automaticamente DSVMs em todas as assinaturas associadas a essa conta. Você pode [anexar um DSVM ao Azure notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) para expandir a capacidade de computação disponível.

### <a name="short-term-experimentation-and-evaluation"></a>Experimentação e avaliação a curto prazo
Você pode usar o DSVM para avaliar ou aprender ferramentas como essas, com esforço mínimo de configuração:

- Microsoft Machine Learning Server
- SQL Server
- Ferramentas do Visual Studio
- Jupyter
- Kits de informações de aprendizado profundo e aprendizado de máquina
- Novas ferramentas populares na Comunidade 

Como você pode configurar o DSVM rapidamente, você pode aplicá-lo em outros cenários de uso de curto prazo. Esses cenários incluem a replicação de experimentos publicados, a execução de demonstrações e as orientações a seguir em sessões online e tutoriais de conferência.

### <a name="deep-learning"></a>Aprendizagem aprofundada
No DSVM, seus modelos de treinamento podem usar algoritmos de aprendizado profundo em hardware baseado em unidades de processamento gráfico (GPUs). Aproveitando os recursos de dimensionamento de VM da plataforma Azure, o DSVM ajuda a usar o hardware baseado em GPU na nuvem de acordo com suas necessidades. Você pode alternar para uma VM baseada em GPU quando estiver treinando modelos grandes ou quando precisar de cálculos de alta velocidade mantendo o mesmo disco do sistema operacional.  

A edição 2016 do Windows Server do DSVM vem pré-instalada com drivers de GPU, estruturas e versões de GPU de estruturas de aprendizado profundo. Na edição do Linux, o aprendizado profundo sobre GPUs está habilitado no CentOS e no Ubuntu DSVMs. 

Você também pode implantar a edição Ubuntu, CentOS ou Windows 2016 do DSVM em uma máquina virtual do Azure que não se baseia em GPUs. Nesse caso, todas as estruturas de aprendizado profundo retornarão para o modo de CPU.
 
[Saiba mais sobre o aprendizado profundo e as estruturas de ia disponíveis](dsvm-deep-learning-ai-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>O que está incluído no DSVM?
A Máquina Virtual de Ciência de Dados tem já instaladas e configuradas muitas ferramentas populares de ciência de dados e aprendizagem aprofundada. Também inclui ferramentas que simplificam o trabalho com vários produtos de dados e análises do Azure. Esses produtos incluem Microsoft Machine Learning Server (R, Python) para criar modelos de previsão e SQL Server 2017 para a exploração em larga escala de conjuntos de dados. O DSVM inclui outras ferramentas da comunidade de software livre e da Microsoft, juntamente com o [código de exemplo e](dsvm-samples-and-walkthroughs.md)os notebooks. 

Aqui está uma lista de ferramentas e plataformas:
+ [Linguagens de programação com suporte](dsvm-languages.md)

+ [Plataformas de dados com suporte](dsvm-data-platforms.md)

+ [Ferramentas de desenvolvimento e IDEs](dsvm-tools-development.md)

+ [Estruturas de aprendizado profundo e de ia](dsvm-deep-learning-ai-frameworks.md)

+ [Ferramentas de ciência de dados e aprendizado de máquina](dsvm-ml-data-science-tools.md)

+ [Ferramentas de ingestão de dados](dsvm-tools-ingestion.md)

+ [Ferramentas de exploração e visualização de dados](dsvm-tools-explore-and-visualize.md)

A tabela seguinte detalha e compara os principais componentes incluídos nas edições do Windows e do Linux da Máquina Virtual de Ciência de Dados.

| **Ferramenta**                                                           | **Edição do Windows** | **Edição do Linux** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) com pacotes populares pré-instalados   |S                      | S             |
| [Microsoft Machine Learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) A Developer Edition inclui: <br />  &nbsp;&nbsp;&nbsp;&nbsp;Estrutura de alto desempenho [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) paralela e distribuída (R e Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;[MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), novos algoritmos de aprendizado de máquina de última geração da Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp;[Operacionalização de R e Python](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |S                      | S |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) PROPLUS com ativação compartilhada: Excel, Word e PowerPoint   |S                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2,7 e 3,5 com pacotes populares pré-instalados    |S                      |S              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) com pacotes populares para a linguagem Julia pré-instalados                         |S                      |S              |
| Bases de dados relacionais                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Ferramentas de bases de dados                                                       |  SQL Server Management Studio <br/> Serviços de Integração do SQL Server<br/> [BCP, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  Drivers ODBC/JDBC|  [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/) (ferramenta de consulta), <br />  BCP, sqlcmd <br />  Drivers ODBC/JDBC|
| Análise no banco de dados escalável com SQL Server serviços de Machine Learning (R, Python) | S     |N              |
| [Jupyter Notebook Server](https://jupyter.org/) com os seguintes kernels:                                  | S     | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;D | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;Python | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;Lúcia | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;PySpark | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;[Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (apenas Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;SparkR     | N | S |
| JupyterHub (servidor de notebook multiusuário)| N | S |
| JupyterLab (servidor de notebook multiusuário) | N | Y (apenas Ubuntu) |
| Ferramentas de desenvolvimento, IDEs e editores de código:| | |
| &nbsp;&nbsp;&nbsp;&nbsp;[Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/) com plug-in git, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [node. js](https://github.com/Microsoft/nodejstools), [python](https://aka.ms/ptvs)e [Ferramentas do R para Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;[Visual Studio Code](https://code.visualstudio.com/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[Área de trabalho RStudio](https://www.rstudio.com/products/rstudio/#Desktop) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[Servidor RStudio](https://www.rstudio.com/products/rstudio/#Server) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[Atom](https://atom.io/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[Juno (Julia IDE)](https://junolab.org/)| S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;Vim e Emacs | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;Git e git bash | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;OpenJDK | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;.NET Framework | S | N |
| Power BI Desktop | S | N |
| SDKs para aceder aos serviços do Azure e do Cortana Intelligence Suite | S | S |
| Ferramentas de gerenciamento e movimentação de dados: | | |
| &nbsp;&nbsp;&nbsp;&nbsp;Gerenciador de Armazenamento do Azure | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[CLI do Azure](https://docs.microsoft.com/cli/azure) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;Azure PowerShell | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;[Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;[Driver de fusível de blob](https://github.com/Azure/azure-storage-fuse) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[Adlcopy (Azure data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;[Ferramenta de migração de dados Azure Cosmos DB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;[Gateway do Microsoft gerenciamento de dados](https://msdn.microsoft.com/library/dn879362.aspx): mover dados entre o local e a nuvem | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;Ferramentas de linha de comando Unix/Linux | S | S |
| Exploração de pesquisa de dados do [Apache](https://drill.apache.org) | S | S |
| Ferramentas de Machine Learning: |||
| &nbsp;&nbsp;&nbsp;&nbsp;Integração com o [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[XGBoost](https://github.com/dmlc/xgboost) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[Weka](https://www.cs.waikato.ac.nz/ml/weka/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[Rattle](https://togaware.com/rattle/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (apenas Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;[CatBoost](https://tech.yandex.com/catboost/) | N | Y (apenas Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;[H2O](https://www.h2o.ai/h2o/), [água de despertar](https://www.h2o.ai/sparkling-water/) | N | Y (apenas Ubuntu) |
| Ferramentas de aprendizado profundo que funcionam em uma GPU ou CPU: |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;[Microsoft cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[TensorFlow](https://www.tensorflow.org/) | S (Windows 2016) | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[Horovod](https://github.com/uber/horovod) | N | Y (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;[MXNet](https://mxnet.io/) | S (Windows 2016) | S|
| &nbsp;&nbsp;&nbsp;&nbsp;[Caffe e Caffe2](https://github.com/caffe2/caffe2) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[Encadeamento](https://chainer.org/) de | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[Torch](http://torch.ch/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[Theano](https://github.com/Theano/Theano) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[Keras](https://keras.io/)| N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[PyTorch](https://pytorch.org/)| N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[Dígitos NVIDIA](https://github.com/NVIDIA/DIGITS) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[Servidor de modelo MXNet](https://github.com/awslabs/mxnet-model-server) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[Serviço de TensorFlow](https://www.tensorflow.org/serving/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[TensorRT](https://developer.nvidia.com/tensorrt) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;[CUDA, cuDNN, Driver Nvidia](https://developer.nvidia.com/cuda-toolkit) | S | S |

## <a name="next-steps"></a>Passos Seguintes

Saiba mais com estes artigos:

+ Windows:
  + [Configurar uma DSVM do Windows](provision-vm.md)
  + [Dez coisas que você pode fazer em um DSVM do Windows](vm-do-ten-things.md)

+ Linux:
  + [Configurar um Linux DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Configurar um DSVM do Linux (CentOS)](linux-dsvm-intro.md)
  + [Ciência de dados em um DSVM do Linux](linux-dsvm-walkthrough.md)
