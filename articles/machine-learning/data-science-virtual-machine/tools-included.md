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
ms.date: 09/27/2019
ms.openlocfilehash: eef8ddf31f038afffddfa6b818c266c1cb4a3d44
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973678"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Quais ferramentas estão incluídas no Máquina Virtual de Ciência de Dados do Azure?

A seguir, incluímos uma lista atualizada de ferramentas incluídas no Máquina Virtual de Ciência de Dados, juntamente com links para entender como cada ferramenta é configurada.


| **Ferramenta**                                                           | **Windows DSVM** | **Linux DSVM** | **Notas de uso** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) com pacotes populares pré-instalados   |<span class='green-check'>&#9989;</span></br> (3.4.3)                    |<span class='green-check'>&#9989;</span></br> (3.4.3)  | [R no DSVM](./dsvm-languages.md#r)           |
| [Microsoft Machine Learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) A Developer Edition inclui: <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) a estrutura paralela e distribuída de alto desempenho (R e Python)<br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package), novos algoritmos de aprendizado de máquina de última geração da Microsoft <br />  &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [R e operacionalização Python](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>  </br> (9.3.0)                 |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) PROPLUS com ativação compartilhada: Excel, Word e PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| [Anaconda Python](https://www.continuum.io/) 2,7 e 3,5 com pacotes populares pré-instalados    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span></br> (4,2)        | |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) com pacotes populares para a linguagem Julia pré-instalados                         |<span class='green-check'>&#9989;</span> </br> (0.6.4)                   |<span class='green-check'>&#9989;</span></br> (0.6.2)              | [Julia no DSVM](./dsvm-languages.md#julia) |
| Bases de dados relacionais                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | [SQL Server no DSVM](./dsvm-data-platforms.md#sql-server-2017-developer-edition) |
| Ferramentas de bases de dados                                                       |  SQL Server Management Studio <br/> Serviços de Integração do SQL Server<br/> [BCP, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  Drivers ODBC/JDBC|  [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/) (ferramenta de consulta), <br />  BCP, sqlcmd <br />  Drivers ODBC/JDBC| |
| Análise no banco de dados escalável com SQL Server serviços de Machine Learning (R, Python) |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| [Jupyter Notebook Server](https://jupyter.org/) com os seguintes kernels:                                  |<span class='green-check'>&#9989;</span></br> 5\.5.0   |<span class='green-check'>&#9989;</span> | [Exemplos de Jupyter Notebook](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp; @ NO__T-1 @ NO__T-2 @ NO__T-3 R |<span class='green-check'>&#9989;</span></br> (3.4.3) |<span class='green-check'>&#9989;</span></br> (3.4.3) | [Exemplos de Jupyter de R](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Python |<span class='green-check'>&#9989;</span></br> Beta |<span class='green-check'>&#9989;</span> | [Exemplos de Jupyter do Python](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Julia |<span class='green-check'>&#9989;</span></br> (0.6.4) |<span class='green-check'>&#9989;</span></br> (0.6.2) | [Exemplos de Julia Jupyter](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Exemplos de pySpark Jupyter](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (Somente Ubuntu) | |
|     &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Sparkr     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (servidor de notebook multiusuário)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab (servidor de notebook multiusuário) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (Somente Ubuntu) | |
| Ferramentas de desenvolvimento, IDEs e editores de código:| | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) com plug-in git, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [node. js](https://github.com/Microsoft/nodejstools), [python](https://aka.ms/ptvs)e [Ferramentas do R para Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Visual Studio no DSVM](./dsvm-tools-development.md#visual-studio-2017) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span></br> (1.31.1) |<span class='green-check'>&#9989;</span></br> (1,31) | [Visual Studio Code no DSVM](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span></br> (1.1.463) |<span class='green-check'>&#9989;</span></br> (1.1.456) | [RStudio desktop DSVM](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [Servidor RStudio no DSVM](./dsvm-tools-development.md#rstudio--server)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) |<span class='green-check'>&#9989;</span></br> (2018.1.4)  |<span class='green-check'>&#9989;</span></br> (2018.2.3) | [PyCharm DSVM](./dsvm-tools-development.md#pycharm)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.26.1) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Juno (Julia IDE)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span> | [Juno DSVM](./dsvm-tools-development.md#juno)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [vim](https://www.vim.org) |<span class='green-check'>&#9989;</span></br> 8\.1.5 |<span class='green-check'>&#9989;</span></br> (7.4.1689) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Emacs](https://www.gnu.org/software/emacs) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (24.5.1) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [git](https://git-scm.com/) e git bash |<span class='green-check'>&#9989;</span></br> (2.20.1) |<span class='green-check'>&#9989;</span></br> (0.6.2) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [OpenJDK](https://openjdk.java.net) |<span class='green-check'>&#9989;</span></br> (1.8.0 _201) |<span class='green-check'>&#9989;</span></br> (1.8.0 _222) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 .NET Framework |<span class='green-check'>&#9989;</span></br> 4\.7.2 |<span class='red-x'>&#10060;</span>   | |
| [Power BI Desktop](https://powerbi.microsoft.com/en-us/) |<span class='green-check'>&#9989;</span></br> (2.65.5313.1381) |<span class='red-x'>&#10060;</span>   |
| SDKs para aceder aos serviços do Azure e do Cortana Intelligence Suite |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Power BI área de trabalho no DSVM](./dsvm-tools-development.md#power-bi-desktop) |
| Ferramentas de gerenciamento e movimentação de dados: | | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/en-us/features/storage-explorer/) |<span class='green-check'>&#9989;</span></br> 1\.6.2 |<span class='green-check'>&#9989;</span></br> (0.7.20160129.1) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CLI do Azure](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span></br> (2.0.56) |<span class='green-check'>&#9989;</span></br> (2.0.58) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span></br> (8.1.0) |<span class='red-x'>&#10060;</span>   | [Azcopy DSVM](./dsvm-tools-ingestion.md#azcopy)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Driver de fusível de blob](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.0.2 | [blobfuse DSVM](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Adlcopy (Azure data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span></br> (1.0.13) |<span class='red-x'>&#10060;</span>   | [Adlcopy DSVM](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Azure Cosmos DB ferramenta de migração de dados](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Cosmos DB no DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 ferramentas de linha de comando Unix/Linux |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| Exploração de pesquisa de dados do [Apache](https://drill.apache.org) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>(1.14.0) | [O Apache Drill on the DSVM](./dsvm-tools-explore-and-visualize.md#apache-drill)
| Ferramentas de Machine Learning: ||||
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 integração com [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) |<span class='green-check'>&#9989;</span></br> (0.2.7) |<span class='green-check'>&#9989;</span></br> (1.0.45) | [SDK do ML do Azure](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span></br> (0,81) |<span class='green-check'>&#9989;</span></br> (0,80) | [XGBoost DSVM](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (8,1) | [Vowpal Wabbit DSVM](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [weka](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span></br> (3,8) |<span class='green-check'>&#9989;</span></br> (3.8.0) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Rattle](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span></br> 5\.1.0 |<span class='green-check'>&#9989;</span></br> 5\.2.2 | [Weka DSVM](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 2\.1.2 | [LightGBM DSVM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.10.3) | |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [H2O](https://www.h2o.ai/h2o/), [água de faíscaização](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (3.20.0.7) | [H20 DSVM](./dsvm-ml-data-science-tools.md#h2o) |
| Ferramentas de aprendizado profundo que funcionam em uma GPU ou CPU: |  |  |  |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Caffe](https://github.com/BVLC/caffe) e [Caffe2](https://github.com/facebookarchive/caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [Caffe](./dsvm-deep-learning-ai-frameworks.md#caffe) e [Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) no DSVM |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Chainer](https://chainer.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 5\.2.0 | [Encadeamento no DSVM](./dsvm-deep-learning-ai-frameworks.md#chainer) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [CUDA, cuDNN, Driver Nvidia](https://developer.nvidia.com/cuda-toolkit) |<span class='green-check'>&#9989;</span></br> (10.0.130) |<span class='green-check'>&#9989;</span></br> (10.0.130) | [CUDA, cuDNN, Driver NVIDIA no DSVM](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Horovod](https://github.com/horovod/horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.16.1) | [Horovod DSVM](./dsvm-deep-learning-ai-frameworks.md#horovod) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Keras](https://keras.io) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 2\.2.4 | [Keras DSVM](./dsvm-deep-learning-ai-frameworks.md#keras) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Microsoft cognitive Toolkit (CNTK)](https://docs.microsoft.com/en-us/cognitive-toolkit/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 2\.5.1 | [CNTK DSVM](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MXNet](https://mxnet.apache.org/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 1\.3.0 | [MXNet DSVM](./dsvm-deep-learning-ai-frameworks.md#mxnet) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [MXNet servidor de modelo](https://github.com/awslabs/mxnet-model-server#quick-start) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> 1\.0.1 | [Servidor de modelo MXNet no DSVM](./dsvm-deep-learning-ai-frameworks.md#mxnet-model-server) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [NVIDIA System Management Interface (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>| [NVIDIA-SMI no DSVM](./dsvm-deep-learning-ai-frameworks.md#nvidia-system-management-interface-nvidia-smi) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [PyTorch](https://pytorch.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.2.0 | [PyTorch DSVM](./dsvm-deep-learning-ai-frameworks.md#pytorch) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorFlow](https://www.tensorflow.org) |<span class='green-check'>&#9989;</span></br> (1,13) |<span class='green-check'>&#9989;</span></br> (1,13) | [TensorFlow DSVM](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [TensorFlow servindo](https://www.tensorflow.org/tfx/guide/serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> 1\.12.0 | [TensorFlow servindo na DSVM](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 [Theano](https://github.com/Theano/Theano) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> 1\.0.3 | [Theano DSVM](./dsvm-deep-learning-ai-frameworks.md#theano)

