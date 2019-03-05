---
title: Introdução à Máquina Virtual de Ciência de Dados do Azure para Linux e Windows | Microsoft Docs
description: Principais cenários e componentes de análise para Máquinas Virtuais de Ciência de Dados do Windows e Linux.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/27/2017
ms.author: gokuma
ms.openlocfilehash: 35d5e5d76dbcb4f1d995a9da13a81ef8b184150c
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337350"
---
# <a name="introduction-to-azure-data-science-virtual-machine-for-linux-and-windows"></a>Introdução à Máquina Virtual de Ciência de Dados do Azure para Linux e Windows

A Máquina Virtual de Ciência de Dados (DSVM) é uma imagem de VM personalizada na cloud do Microsoft Azure, concebida especificamente para fazer ciência de dados. Tem muitas ferramentas populares de ciência de dados e outras pré-instaladas e pré-configuradas para permitir a rápida criação de aplicações inteligentes para análises avançadas. Está disponível no Windows Server e Linux. Oferecemos a edição Windows da DSVM no Server 2016 e no Server 2012. Oferecemos edições para Linux do DSVM no Ubuntu 16.04 LTS e no CentOS 7.4.

Este tópico aborda o que pode fazer com a VM de Ciência de Dados, descreve alguns dos principais cenários para utilizá-la, detalha as funcionalidades-chave disponíveis nas versões do Windows e do Linux e disponibiliza instruções para começar a utilizá-la.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>O que posso fazer com a Máquina Virtual de Ciência de Dados?
O objetivo da Máquina Virtual de Ciência de Dados (DSVM) é dar aos profissionais de dados, independentemente do nível de competências e em vários setores, um ambiente de ciência de dados totalmente integrado, pré-configurado e isento de problemas. Em vez de implementar uma área de trabalho comparável por si, pode aprovisionar uma DSVM - poupando dias ou mesmo _semanas_ na instalação, na configuração e nos processos de gestão de pacotes. Quando a DSVM estiver alocada, pode começar a trabalhar imediatamente no seu projeto de ciência de dados.

A VM de Ciência de Dados foi concebida e configurada para trabalhar com uma vasta gama de cenários de utilização. Pode aumentar ou reduzir verticalmente o ambiente, à medida que as necessidades do projeto mudam, utilizar a sua linguagem preferida para programar tarefas de ciência de dados e instalar outras ferramentas para personalizar o sistema de acordo com as suas necessidades exatas.

## <a name="key-scenarios"></a>Principais cenários
Esta secção sugere alguns principais cenários para os quais a VM de Ciência de Dados pode ser implementada.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Ambiente de trabalho de análise pré-configurado na cloud
A VM de Ciência de Dados disponibiliza uma configuração de linha base às equipas de ciência de dados que pretendem substituir os ambientes de trabalho locais por um ambiente de trabalho gerido na cloud. Esta linha base garante que todos os cientistas de dados de uma equipa têm uma configuração consistente com a qual podem verificar as experimentações e promover a colaboração. Também reduz os custos ao diminuir a carga dos administradores do sistema e ao poupar o tempo necessário para avaliar, instalar e manter os vários pacotes de software que as análises avançadas exigem.

### <a name="data-science-training-and-education"></a>Formação e educação em ciência de dados
Normalmente, os formadores e educadores que ensinam as turmas de ciência de dados disponibilizam uma imagem de máquina virtual, de modo a garantir que os alunos têm uma configuração consistente e que os exemplos funcionam de forma previsível. A VM de Ciência de Dados cria um ambiente a pedido com uma configuração consistente que facilita os desafios de suporte e de incompatibilidade. Os casos em que estes ambientes têm de ser criados constantemente, especialmente para aulas de formação mais pequenas, retiram benefícios significativos destas características.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Capacidade elástica a pedido para projetos de grande escala
As hackathons/competições de ciência de dados ou a modelação e exploração de dados em grande escala exigem capacidade de hardware aumentada horizontalmente, normalmente para durações curtas. A VM de Ciência de Dados pode ajudar a replicar o ambiente de ciência de dados rapidamente a pedido, em servidores aumentados horizontalmente que permitem que sejam executadas experimentações que exigem recursos de computação de alta potência.

### <a name="short-term-experimentation-and-evaluation"></a>Experimentação e avaliação a curto prazo
A VM de Ciência de Dados pode ser utilizada para avaliar ou aprender, com um esforço de configuração mínimo, ferramentas como o Microsoft ML Server, o SQL Server, as ferramentas do Visual Studio, o Jupyter, as ferramentas de aprendizagem profunda / ML e ferramentas novas populares na comunidade. Uma vez que a VM de Ciência de Dados pode ser configurada rapidamente, pode ser aplicada a outros cenários de utilização de curto prazo, como a replicação de experimentações publicadas, a execução de demonstrações, o acompanhamento das instruções em sessões online e tutoriais de conferência.

### <a name="deep-learning"></a>Aprendizagem aprofundada
A VM de Ciência de Dados pode ser utilizada para preparar modelos através da utilização de algoritmos de aprendizagem aprofundada em GPU (unidades de processamento gráfico) com base em hardware. Ao utilizar as capacidades de dimensionamento de VMs da cloud do Azure, a DSVM ajuda-o a utilizar hardware baseado em GPU na cloud, consoante as necessidades. O utilizador pode mudar para uma VM baseada em GPU quando estiverem a preparar modelos grandes ou precisarem de computações de alta velocidade, mas tenham de manter o mesmo disco do SO.  A edição do Windows Server 2016 da DSVM vem pré-instalada com controladores de GPU, estruturas e versões de GPU de estruturas de aprendizagem aprofundada. Na edição do Linux, a aprendizagem aprofundada em GPU está ativada nas DSVMs do CentOS e do Ubuntu. Pode implementar a edição Ubuntu, CentOS ou Windows 2016 da VM de Ciência de Dados numa máquina virtual do Azure não baseada em GPU, caso em que todas as estruturas de aprendizagem aprofundada reverterão para o modo CPU.

## <a name="whats-included-in-the-data-science-vm"></a>O que está incluído na VM de Ciência de Dados?
A Máquina Virtual de Ciência de Dados tem já instaladas e configuradas muitas ferramentas populares de ciência de dados e aprendizagem aprofundada. Também inclui ferramentas que tornam mais fácil trabalhar com vários dados e produtos de análise do Azure, como o Microsoft ML Server (R, Python) para a criação de modelos preditivos ou o SQL Server 2017 para a exploração de conjuntos de dados em grande escala. Também está incluído um conjunto de ferramentas da comunidade de código aberto e da Microsoft, bem como códigos de exemplo e blocos de notas. A tabela seguinte detalha e compara os principais componentes incluídos nas edições do Windows e do Linux da Máquina Virtual de Ciência de Dados.


| **Ferramenta**                                                           | **Edição do Windows** | **Edição do Linux** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) com pacotes populares pré-instalados   |S                      | S             |
| [Microsoft ML Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition inclui <br />  Estrutura de elevado desempenho paralela e distribuída &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) (R e Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) - novos algoritmos de ML de última geração da Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [Operacionalização de R e Python](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |S                      | S |
| [Microsoft Office](https://products.office.com/en-us/business/office-365-proplus-business-software) Pro-Plus com ativação partilhada - Excel, Word e PowerPoint   |S                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2.7, 3.5 com pacotes populares pré-instalados    |S                      |S              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) com pacotes populares para a linguagem Julia pré-instalados                         |S                      |S              |
| Bases de Dados Relacionais                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Ferramentas de bases de dados                                                       | * SQL Server Management Studio <br/>* Serviços de Integração do SQL Server<br/>* [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * controladores ODBC/JDBC| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (ferramenta de consultas), <br /> * bcp, sqlcmd <br /> * controladores ODBC/JDBC|
| Análises dimensionáveis dentro da base de dados com os serviços SQL Server ML (R, Python) | S     |N              |
| **[Jupyter Notebook Server](http://jupyter.org/) com os kernels seguintes**                                  | S     | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (apenas Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | S |
| JupyterHub (servidor de blocos de nota multiutilizadores)| N | S |
| JupyterLab (servidor de blocos de nota multiutilizadores) | N | Y (apenas Ubuntu) |
| **Ferramentas de desenvolvimento, IDEs e editores de código**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) com Plug-in do Git, Azure HDInsight (Hadoop), Data Lake, SQL Server Data tools, [Node.js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs) e [R Tools for Visual Studio (RTVS)](http://microsoft.github.io/RTVS-docs/) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (IDE Julia)](http://junolab.org/)| S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim e Emacs | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git e GitBash | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* .NET Framework | S | N |
| PowerBI Desktop | S | N |
| SDKs para aceder aos serviços do Azure e do Cortana Intelligence Suite | S | S |
| **Ferramentas de movimento e gestão de dados** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Explorador de Armazenamento do Azure | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CLI do Azure](https://docs.microsoft.com/cli/azure) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Controlador Blob FUSE](https://github.com/Azure/azure-storage-fuse) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy (Armazenamento do Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Ferramenta de Migração de Dados DocDB ](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx): Mover dados entre o local e na Cloud | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Utilitários de Linha de Comando Unix/Linux | S | S |
| [Apache Drill](http://drill.apache.org) para Exploração de dados | S | S |
| **Ferramentas de Machine Learning** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Integração com o [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](http://www.cs.waikato.ac.nz/ml/weka/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](https://togaware.com/rattle/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (apenas Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CatBoost](https://tech.yandex.com/catboost/) | N | Y (apenas Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/), [o Sparkling Water](https://www.h2o.ai/sparkling-water/) | N | Y (apenas Ubuntu) |
| **Ferramentas de Aprendizagem Aprofundada** <br>Todas as ferramentas funcionarão num GPU ou CPU |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | S (Windows 2016) | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Horovod](https://github.com/uber/horovod) | N | Y (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](http://mxnet.io/) | S (Windows 2016) | S|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe & Caffe2](https://github.com/caffe2/caffe2) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Chainer](https://chainer.org/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyTorch](http://pytorch.org/)| N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet Model Server](https://github.com/awslabs/mxnet-model-server) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow Serving](https://www.tensorflow.org/serving/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorRT](https://developer.nvidia.com/tensorrt) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Controlador CUDA, cuDNN, NVIDIA](https://developer.nvidia.com/cuda-toolkit) | S | S |
| **Plataforma de Macrodados (apenas Dev/test)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* [Spark](http://spark.apache.org/) local autónomo | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Hadoop](http://hadoop.apache.org/) local (HDFS, YARN) | N | S |

## <a name="get-started"></a>Introdução

### <a name="windows-data-science-vm"></a>VM de Ciência de Dados do Windows
* Para obter mais informações sobre como criar e utilizar uma DSVM do Windows, veja [Provision the Windows Data Science Virtual Machine](provision-vm.md) (Aprovisionar a Máquina Virtual de Ciência de Dados do Windows). Para obter mais informações sobre como fazer várias tarefas necessárias para o seu projeto de ciência de dados na DSVM do Windows, veja [Ten things you can do on the Data Science Virtual Machine](vm-do-ten-things.md) (Dez coisas que pode fazer na Máquina Virtual de Ciência de Dados).

### <a name="linux-data-science-vm"></a>VM de Ciência de Dados do Linux
* Para obter mais informações sobre como criar e utilizar uma DSVM do Ubuntu, veja [Provision the Data Science Virtual Machine for Linux (Ubuntu)](dsvm-ubuntu-intro.md) (Aprovisionar a Máquina Virtual de Ciência de Dados do Linux [Ubuntu]). Para obter mais informações sobre como criar e utilizar uma DSVM do CentOS, veja [Provision a Linux CentOS Data Science Virtual Machine on Azure](linux-dsvm-intro.md) (Aprovisionar uma Máquina Virtual de Ciência de Dados de CentOS do Linux).
* Para obter instruções que lhe mostram como fazer várias tarefas de ciência de dados comuns com a VM do Linux, tanto para CentOS, como para Ubuntu, veja [Data science on the Linux Data Science Virtual Machine](linux-dsvm-walkthrough.md) (Ciência de dados na Máquina Virtual de Ciência de Dados do Linux).

## <a name="next-steps"></a>Passos Seguintes
[Guia do programador em R para o Azure](../r-developers-guide.md)
