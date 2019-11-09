---
title: Ferramentas incluídas na DSVM
titleSuffix: Azure Data Science Virtual Machine tools
description: Uma lista de ferramentas incluídas nas imagens do Windows e do Ubuntu DSVM
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: overview
ms.date: 10/10/2019
ms.openlocfilehash: 594879df6e1e88c861a2730160d050194cc884ea
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891069"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Quais ferramentas estão incluídas no Máquina Virtual de Ciência de Dados do Azure?

A seguir, incluímos uma lista atualizada de ferramentas incluídas no Máquina Virtual de Ciência de Dados, juntamente com links para entender como cada ferramenta é configurada.


| **Ferramenta**                                                           | **DSVM do Windows** | **DSVM Linux** | **Notas de uso** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) com pacotes populares pré-instalados   |<span class='green-check'>&#9989;</span></br> (3.4.3)                    |<span class='green-check'>&#9989;</span></br> (3.4.3)  | [R no DSVM](./dsvm-languages.md#r)           |
| [Microsoft Machine Learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) A Developer Edition inclui: <br />  &nbsp;&nbsp;&nbsp;&nbsp; estrutura [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) paralela e distribuída de alto desempenho (R e Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp; [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), novos algoritmos de aprendizado de máquina de última geração da Microsoft <br />  &nbsp;&nbsp;&nbsp;[operacional &nbsp; R e Python](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>  </br> (9.3.0)                 |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://www.microsoft.com/p/office-365-proplus/CFQ7TTC0K8R0) PROPLUS com ativação compartilhada: Excel, Word e PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| [Anaconda Python](https://www.continuum.io/) 2,7 e 3,5 com pacotes populares pré-instalados    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span></br> (4,2)        | |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) com pacotes populares para a linguagem Julia pré-instalados                         |<span class='green-check'>&#9989;</span> </br> (0.6.4)                   |<span class='green-check'>&#9989;</span></br> (0.6.2)              | [Julia no DSVM](./dsvm-languages.md#julia) |
| Bases de dados relacionais                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | [SQL Server no DSVM](./dsvm-data-platforms.md#sql-server-2017-developer-edition) |
| Ferramentas de bases de dados                                                       |  SQL Server Management Studio <br/> Serviços de Integração do SQL Server<br/> [BCP, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  Drivers ODBC/JDBC|  [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/) (ferramenta de consulta), <br />  BCP, sqlcmd <br />  Drivers ODBC/JDBC| |
| Análise no banco de dados escalável com SQL Server serviços de Machine Learning (R, Python) |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| [Jupyter Notebook Server](https://jupyter.org/) com os seguintes kernels:                                  |<span class='green-check'>&#9989;</span></br> 5\.5.0   |<span class='green-check'>&#9989;</span> | [Exemplos de Jupyter Notebook](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp;&nbsp;&nbsp;&nbsp; R |<span class='green-check'>&#9989;</span></br> (3.4.3) |<span class='green-check'>&#9989;</span></br> (3.4.3) | [Exemplos de Jupyter de R](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python |<span class='green-check'>&#9989;</span></br> Beta |<span class='green-check'>&#9989;</span> | [Exemplos de Jupyter do Python](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia |<span class='green-check'>&#9989;</span></br> (0.6.4) |<span class='green-check'>&#9989;</span></br> (0.6.2) | [Exemplos de Julia Jupyter](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Exemplos de pySpark Jupyter](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp;&nbsp;&nbsp;&nbsp; [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (Somente Ubuntu) | |
|     &nbsp;&nbsp;&nbsp;&nbsp; Sparkr     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (servidor de notebook multiusuário)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab (servidor de notebook multiusuário) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> | |
| Ferramentas de desenvolvimento, IDEs e editores de código:| | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) com plug-in git, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [node. js](https://github.com/Microsoft/nodejstools), [python](https://aka.ms/ptvs)e [Ferramentas do R para Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Visual Studio no DSVM](./dsvm-tools-development.md#visual-studio-community-2017) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span></br> (1.31.1) |<span class='green-check'>&#9989;</span></br> (1,31) | [Visual Studio Code no DSVM](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span></br> (1.2.50 XX) |<span class='green-check'>&#9989;</span></br> (1.1.456) | [RStudio desktop DSVM](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [Servidor RStudio no DSVM](./dsvm-tools-development.md#rstudio--server)
| &nbsp;&nbsp;&nbsp;&nbsp; [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) |<span class='green-check'>&#9989;</span></br> (19.2.3)  |<span class='green-check'>&#9989;</span></br> (2018.2.3) | [PyCharm DSVM](./dsvm-tools-development.md#pycharm)
| &nbsp;&nbsp;&nbsp;&nbsp; [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.26.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno (Julia IDE)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span> | [Juno DSVM](./dsvm-tools-development.md#juno)
| &nbsp;&nbsp;&nbsp;&nbsp; [vim](https://www.vim.org) |<span class='green-check'>&#9989;</span></br> 8\.1.5 |<span class='green-check'>&#9989;</span></br> (7.4.1689) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Emacs](https://www.gnu.org/software/emacs) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (24.5.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [git](https://git-scm.com/) e git bash |<span class='green-check'>&#9989;</span></br> (2.20.1) |<span class='green-check'>&#9989;</span></br> (0.6.2) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [OpenJDK](https://openjdk.java.net) |<span class='green-check'>&#9989;</span></br> (1.8.0 _201) |<span class='green-check'>&#9989;</span></br> (1.8.0 _222) | |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET Framework |<span class='green-check'>&#9989;</span></br> 4\.7.2 |<span class='red-x'>&#10060;</span>   | |
| [Power BI Desktop](https://powerbi.microsoft.com/) |<span class='green-check'>&#9989;</span></br> (2.73.55 XX) |<span class='red-x'>&#10060;</span>   |
| SDKs para aceder aos serviços do Azure e do Cortana Intelligence Suite |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Power BI área de trabalho no DSVM](./dsvm-tools-development.md#power-bi-desktop) |
| Ferramentas de gerenciamento e movimentação de dados: | | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) |<span class='green-check'>&#9989;</span></br> 1\.10.1 |<span class='green-check'>&#9989;</span></br> (0.7.20160129.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [CLI do Azure](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span></br> (2.0.56) |<span class='green-check'>&#9989;</span></br> (2.0.58) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span></br> (8.1.0) |<span class='red-x'>&#10060;</span>   | [Azcopy DSVM](./dsvm-tools-ingestion.md#azcopy)
| &nbsp;&nbsp;&nbsp;[Driver de fusível de BLOBs](https://github.com/Azure/azure-storage-fuse) de &nbsp; |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.0.2 | [blobfuse DSVM](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp;&nbsp;&nbsp;&nbsp; [Adlcopy (Azure data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span></br> (1.0.13) |<span class='red-x'>&#10060;</span>   | [Adlcopy DSVM](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [ferramenta de migração de dados Azure Cosmos DB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Cosmos DB no DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp;&nbsp;&nbsp;&nbsp; ferramentas de linha de comando Unix/Linux |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| Exploração de pesquisa de dados do [Apache](https://drill.apache.org) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>(1.14.0) | [O Apache Drill on the DSVM](./dsvm-tools-explore-and-visualize.md#apache-drill)
| Ferramentas de Machine Learning: ||||
| &nbsp;&nbsp;&nbsp;&nbsp; integração com o [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) |<span class='green-check'>&#9989;</span></br> (0.2.7) |<span class='green-check'>&#9989;</span></br> (1.0.45) | [SDK do ML do Azure](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp;&nbsp;&nbsp;&nbsp; [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span></br> (0,81) |<span class='green-check'>&#9989;</span></br> (0,80) | [XGBoost DSVM](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (8,1) | [Vowpal Wabbit DSVM](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp;&nbsp;&nbsp;&nbsp; [weka](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span></br> (3,8) |<span class='green-check'>&#9989;</span></br> (3.8.0) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Rattle](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span></br> 5\.1.0 |<span class='green-check'>&#9989;</span></br> 5\.2.2 | [Weka DSVM](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp;&nbsp;&nbsp;&nbsp; [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 2\.1.2 | [LightGBM DSVM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp;&nbsp;&nbsp;&nbsp; [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.10.3) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/), [água de despertar](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (3.20.0.7) | [H20 DSVM](./dsvm-ml-data-science-tools.md#h2o) |
| Ferramentas de aprendizado profundo que funcionam em uma GPU ou CPU: |  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp; [Caffe](https://github.com/BVLC/caffe) e [Caffe2](https://github.com/facebookarchive/caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [Caffe](./dsvm-deep-learning-ai-frameworks.md#caffe) e [Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) no DSVM |
| &nbsp;&nbsp;&nbsp;[encadeamento](https://chainer.org) &nbsp; |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 5\.2.0 | [Encadeamento no DSVM](./dsvm-deep-learning-ai-frameworks.md#chainer) |
| &nbsp;&nbsp;&nbsp;&nbsp; [CUDA, cuDNN, NVIDIA Driver](https://developer.nvidia.com/cuda-toolkit) |<span class='green-check'>&#9989;</span></br> (10.0.130) |<span class='green-check'>&#9989;</span></br> (10.0.130) | [CUDA, cuDNN, Driver NVIDIA no DSVM](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](https://github.com/horovod/horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.16.1) | [Horovod DSVM](./dsvm-deep-learning-ai-frameworks.md#horovod) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Keras](https://keras.io) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 2\.2.4 | [Keras DSVM](./dsvm-deep-learning-ai-frameworks.md#keras) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 2\.5.1 | [CNTK DSVM](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet](https://mxnet.apache.org/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 1\.3.0 | [MXNet DSVM](./dsvm-deep-learning-ai-frameworks.md#mxnet) |
| &nbsp;&nbsp;&nbsp;&nbsp; [servidor de modelo MXNet](https://github.com/awslabs/mxnet-model-server#quick-start) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 1\.0.1 | [Servidor de modelo MXNet no DSVM](./dsvm-deep-learning-ai-frameworks.md#mxnet-model-server) |
| &nbsp;&nbsp;&nbsp;&nbsp; [interface de gerenciamento do sistema NVIDIA (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>| [NVIDIA-SMI no DSVM](./dsvm-deep-learning-ai-frameworks.md#nvidia-system-management-interface-nvidia-smi) |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyTorch](https://pytorch.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.2.0 | [PyTorch DSVM](./dsvm-deep-learning-ai-frameworks.md#pytorch) |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](https://www.tensorflow.org) |<span class='green-check'>&#9989;</span></br> (1,13) |<span class='green-check'>&#9989;</span></br> (1,13) | [TensorFlow DSVM](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow servindo](https://www.tensorflow.org/tfx/guide/serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.12.0 | [TensorFlow servindo na DSVM](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Theano](https://github.com/Theano/Theano) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> 1\.0.3 | [Theano DSVM](./dsvm-deep-learning-ai-frameworks.md#theano)

