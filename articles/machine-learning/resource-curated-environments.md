---
title: Ambientes curados
titleSuffix: Azure Machine Learning
description: Coleção de ambientes curados disponíveis no Azure Machine Learning
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 72ccf2a765f50358635e4a803ed0b92e60bd7d19
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012217"
---
# <a name="azure-machine-learning-curated-environments"></a>Ambientes curados de aprendizagem de máquinas Azure

Este artigo lista os ambientes curados em Azure Machine Learning, e os pacotes e canais que estão pré-instalados neles. Ambientes curados são fornecidos pela Azure Machine Learning e estão disponíveis no seu espaço de trabalho por padrão. São apoiados por imagens em cache do Docker, reduzindo o custo de preparação de execução e permitindo um tempo de implementação mais rápido. Use estes ambientes para começar rapidamente com várias estruturas de aprendizagem automática.

> [!NOTE]
> Esta lista é atualizada a partir de junho de 2020. Use o Python SDK para obter a lista mais atualizada. Para mais informações, consulte o [artigo ambientes.](./how-to-use-environments.md#use-a-curated-environment)

## <a name="azure-automl"></a>Azure AutoML

- [AzureML AutoML](#azureml-automl)
- [AzureML AutoML GPU](#azureml-automl-gpu)
- [AzureML AutoML DNN](#azureml-automl-dnn)
- [AzureML AutoML DNN GPU](#azureml-automl-dnn-gpu)
- [Azure AutoML DNN Vision GPU](#azureml-automl-dnn-vision-gpu)

### <a name="azureml-automl"></a>AzureML AutoML

canais de pacotes:
- anaconda
- conda-forja
- pytorch

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-pipeline-core==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-defaults==1.8.0
  - azureml-interpret=1.8.0
  - azureml-explain-model==1.8.0
  - azureml-automl-core==1.8.0
  - azureml-automl-runtime==1.8.0
  - azureml-train-automl-client==1.8.0
  - azureml-train-automl-runtime==1.8.0.post1
  - inferência-esquema
  - pyarrow==0.17.0
  - py-cpuinfo==5.0.0
- >numpy =1.16.0,<=1.16.2
- pandas>=0.21.0<=0,23.4
- py-xgboost<=0,90
- fbprophet==0,5
- setuptools-git
- psutil>5.0.0,<6.0.0

### <a name="azureml-automl-gpu"></a>AzureML AutoML GPU

canais de pacotes:
- anaconda
- conda-forja
- pytorch

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-pipeline-core==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-defaults==1.8.0
  - azureml-interpret=1.8.0
  - azureml-explain-model==1.8.0
  - azureml-automl-core==1.8.0
  - azureml-automl-runtime==1.8.0
  - azureml-train-automl-client==1.8.0
  - azureml-train-automl-runtime==1.8.0.post1
  - inferência-esquema
  - pyarrow==0.17.0
  - py-cpuinfo==5.0.0
- >numpy =1.16.0,<=1.16.2
- pandas>=0.21.0<=0,23.4
- fbprophet==0,5
- setuptools-git
- psutil>5.0.0,<6.0.0

### <a name="azureml-automl-dnn"></a>AzureML AutoML DNN

canais de pacotes:
- anaconda
- conda-forja
- pytorch

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-pipeline-core==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-defaults==1.8.0
  - azureml-interpret=1.8.0
  - azureml-explain-model==1.8.0
  - azureml-automl-core==1.8.0
  - azureml-automl-runtime==1.8.0
  - azureml-train-automl-client==1.8.0
  - azureml-train-automl-runtime==1.8.0.post1
  - inferência-esquema
  - pytorch-transformers==1.0.0
  - spacy==2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow==0.17.0
  - py-cpuinfo==5.0.0
- pandas>=0.21.0<=0,23.4
- >numpy =1.16.0,<=1.16.2
- py-xgboost<=0,90
- fbprophet==0,5
- setuptools-git
- pytorch=1.4.0
- cudatoolkit=10.0.130
- psutil>5.0.0,<6.0.0

### <a name="azureml-automl-dnn-gpu"></a>AzureML AutoML DNN GPU

canais de pacotes:
- anaconda
- conda-forja
- pytorch

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-pipeline-core==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-defaults==1.8.0
  - azureml-interpret=1.8.0
  - azureml-explain-model==1.8.0
  - azureml-automl-core==1.8.0
  - azureml-automl-runtime==1.8.0
  - azureml-train-automl-client==1.8.0
  - azureml-train-automl-runtime==1.8.0.post1
  - inferência-esquema
  - horovod==0.19.4
  - pytorch-transformers==1.0.0
  - spacy==2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow==0.17.0
  - py-cpuinfo==5.0.0
- pandas>=0.21.0<=0,23.4
- >numpy =1.16.0,<=1.16.2
- fbprophet==0,5
- setuptools-git
- pytorch=1.4.0
- cudatoolkit=10.0.130
- psutil>5.0.0,<6.0.0

### <a name="azureml-automl-dnn-vision-gpu"></a>GPU de Visão AutoML AzureML

dependências:
- python=3.7
- Pip:
  - azureml-automl-core==1.8.0
  - azureml-core==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-automl-client==1.8.0
  - azureml-automl-runtime==1.8.0
  - azureml-interpret=1.8.0
  - azureml-defaults==1.8.0
  - azureml-explain-model==1.8.0
  - azureml-train-automl-runtime==1.8.0.post1
  - azureml-train-automl==1.8.0
  - azureml-contrib-dataset==1.8.0
  - azureml-pipeline-core==1.8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - azureml-pipeline-steps==1.8.0
  - azureml-pipeline==1.8.0
  - azureml-train==1.8.0
  - azureml-sdk==1.8.0
  - azureml-contrib-automl-dnn-vision=1.8.0

## <a name="azure-ml-designer"></a>Azure ML Designer

- [AzureML Designer](#azureml-designer)
- [AzureML Designer CV](#azureml-designer-cv)
- [AzureML Designer CV Transform](#azureml-designer-cv-transform)
- [AzureML Designer IO](#azureml-designer-io)
- [AzureML Designer NLP](#azureml-designer-nlp)
- [AzureML Designer PyTorch](#azureml-designer-pytorch)
- [AzureML Designer PyTorch Train](#azureml-designer-pytorch-train)
- [AzureML Designer R](#azureml-designer-r)
- [Recomendador de designers AzureML](#azureml-designer-recommender)
- [Pontuação do designer AzureML](#azureml-designer-score)
- [Transformação de Designer AzureML](#azureml-designer-transform)

### <a name="azureml-designer"></a>AzureML Designer

canais de pacotes:
- conda-forja

dependências:
- python=3.6.8
- scikit-surprise=1.0.6
- Pip:
  - azureml-designer-classic-modules==0.0.124
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - spacy==2.1.7

### <a name="azureml-designer-cv"></a>AzureML Designer CV

canais de pacotes:
- predefinições

dependências:
- python=3.6.8
- Pip:
  - azureml-designer-cv-módulos==0.0.6

### <a name="azureml-designer-cv-transform"></a>AzureML Designer CV Transform

canais de pacotes:
- predefinições

dependências:
- python=3.6.8
- Pip:
  - azureml-designer-cv-módulos[pytorch]=0,0.6

### <a name="azureml-designer-io"></a>AzureML Designer IO

canais de pacotes:
- predefinições

dependências:
- python=3.6.8
- Pip:
  - azureml-dataprep>=1,6
  - azureml-designer-dataio-modules==0.0.30

### <a name="azureml-designer-nlp"></a>AzureML Designer NLP

canais de pacotes:
- predefinições

dependências:
- python=3.6.8
- Pip:
  - azureml-designer-classic-modules==0.0.121
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - spacy==2.1.7

### <a name="azureml-designer-pytorch"></a>AzureML Designer PyTorch

canais de pacotes:
- predefinições

dependências:
- python=3.6.8
- Pip:
  - azureml-designer-pytorch-modules==0.0.8

### <a name="azureml-designer-pytorch-train"></a>AzureML Designer PyTorch Train

canais de pacotes:
- predefinições

dependências:
- python=3.6.8
- Pip:
  - azureml-designer-pytorch-modules==0.0.8

### <a name="azureml-designer-r"></a>AzureML Designer R

canais de pacotes:
- conda-forja

dependências:
- python=3.6.8
- r-caret=6.0
- r-catools=1.17.1
- r-cluster=2.1.0
- r-dplyr=0.8.5
- r-e1071=1.7
- r-forcats=0,5.0
- r-previsão=8.12
- r-glmnet=2.0
- r-óxido=1.2.4
- r-matriz=1.2
- r-mclust=5.4.6
- r-mgcv=1,8
- r-nlme=3.1
- r-nnet=7.3
- r-plyr=1.8.6
- r-randomforest=4.6
- r-reticulato=1,12
- r-rocr=1.0
- r-rodbc=1.3
- r-rpart=4.1
- r-stringr=1.4.0
- r-tidyverse=1.2.1
- r-timedate=3043.102
- r-tseries=0,10
- r=3.5.1
- Pip:
  - azureml-designer-classic-modules==0.0.124

### <a name="azureml-designer-recommender"></a>Recomendador de designers AzureML

canais de pacotes:
- predefinições

dependências:
- python=3.6.8
- Pip:
  - azureml-designer-recomendador-módulos==0.0.5

### <a name="azureml-designer-score"></a>Pontuação do designer AzureML

canais de pacotes:
- predefinições

dependências:
- conda
- python=3.6.8
- Pip:
  - azureml-designer-score-modules==0.0.5

### <a name="azureml-designer-transform"></a>Transformação de Designer AzureML

canais de pacotes:
- predefinições

dependências:
- python=3.6.8
- Pip:
  - azureml-designer-datatransform-modules==0.0.49

## <a name="azureml-hyperdrive-forecastdnn"></a>Hiperdrive AzureML PrevisãoNN

dependências:
- python=3.7
- Pip:
  - azureml-core==1.8.0
  - azureml-pipeline-core==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-defaults==1.8.0
  - azureml-automl-core==1.8.0
  - azureml-automl-runtime==1.8.0
  - azureml-train-automl-client==1.8.0
  - azureml-train-automl-runtime==1.8.0.post1
  - azureml-contrib-automl-dnn-forecasting=1.8.0

## <a name="azureml-minimal"></a>AzureML Mínimo

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0

## <a name="azureml-sidecar"></a>AzureML Sidecar

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2

## <a name="azureml-tutorial"></a>AzureML Tutorial

canais de pacotes:
- anaconda
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - azureml-widgets==1.8.0
  - azureml-pipeline-core==1.8.0
  - azureml-pipeline-steps==1.8.0
  - azureml-opendatasets==1.8.0
  - azureml-automl-core==1.8.0
  - azureml-automl-runtime==1.8.0
  - azureml-train-automl-client==1.8.0
  - azureml-train-automl-runtime==1.8.0.post1  
  - azureml-train-automl==1.8.0
  - azureml-train==1.8.0
  - azureml-sdk==1.8.0
  - azureml-interpret=1.8.0
  - azureml-tensorboard==1.8.0
  - azureml-mlflow==1,8.0
  - mlflow
  - sklearn-pandas
- pandas
- numpy
- tqdm
- scikit-learn
- matplotlib

## <a name="azureml-vowpalwabbit-880"></a>AzureML VowpalWabbit 8.8.0

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-dataprep[fusível,pandas]

## <a name="dask"></a>Dask

- [AzureML Dask CPU](#azureml-dask-cpu)
- [AzureML Dask GPU](#azureml-dask-gpu)

### <a name="azureml-dask-cpu"></a>AzureML Dask CPU

canais de pacotes:
- conda-forja
- pytorch
- predefinições

dependências:
- python=3.6.9
- Pip:
  - adlfs
  - azureml-core==1.8.0
  - azureml-dataprep
  - dask[completo]
  - dask-ml[completo]
  - distribuído
  - fastparquet
  - fsspec
  - joblib
  - jupyterlab
  - Rio Iz4
  - mpi4py
  - caderno
  - pyarrow

### <a name="azureml-dask-gpu"></a>AzureML Dask GPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.9
- Pip:
  - azureml-defaults==1.8.0
  - adlfs
  - azureml-core==1.8.0
  - azureml-dataprep
  - dask[completo]
  - dask-ml[completo]
  - distribuído
  - fastparquet
  - fsspec
  - joblib
  - jupyterlab
  - Rio Iz4
  - mpi4py
  - caderno
  - pyarrow
- matplotlib

## <a name="chainer"></a>Chainer

- [AzureML Chainer 5.1.0 CPU](#azureml-chainer-510-cpu)
- [AzureML Chainer 5.1.0 GPU](#azureml-chainer-510-gpu)

### <a name="azureml-chainer-510-cpu"></a>AzureML Chainer 5.1.0 CPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - chainer==5.1.0
  - mpi4py==3.0.0

### <a name="azureml-chainer-510-gpu"></a>AzureML Chainer 5.1.0 GPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - chainer==5.1.0
  - cupy-cuda90==5.1.0
  - mpi4py==3.0.0

## <a name="pyspark"></a>PySpark

### <a name="azureml-pyspark-mmlspark-015"></a>AzureML PySpark MmlSpark 0.15

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0

## <a name="pytorch"></a>PyTorch

- [AzureML PyTorch 1.0 CPU](#azureml-pytorch-10-cpu)
- [AzureML PyTorch 1.0 GPU](#azureml-pytorch-10-gpu)
- [AzureML PyTorch 1.1 CPU](#azureml-pytorch-11-cpu)
- [AzureML PyTorch 1.1 GPU](#azureml-pytorch-11-gpu)
- [AzureML PyTorch 1.2 CPU](#azureml-pytorch-12-cpu)
- [AzureML PyTorch 1.2 GPU](#azureml-pytorch-12-gpu)
- [AzureML PyTorch 1.3 CPU](#azureml-pytorch-13-cpu)
- [AzureML PyTorch 1.3 GPU](#azureml-pytorch-13-gpu)
- [AzureML PyTorch 1.4 CPU](#azureml-pytorch-14-cpu)
- [AzureML PyTorch 1.4 GPU](#azureml-pytorch-14-gpu)
- [AzureML PyTorch 1.5 CPU](#azureml-pytorch-15-cpu)
- [AzureML PyTorch 1.5 GPU](#azureml-pytorch-15-gpu)

### <a name="azureml-pytorch-10-cpu"></a>AzureML PyTorch 1.0 CPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tocha==1.0
  - torchvision==0.2.1
  - mkl==2018.0.3
  - horovod==0.16.1

### <a name="azureml-pytorch-10-gpu"></a>AzureML PyTorch 1.0 GPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tocha==1.0
  - torchvision==0.2.1
  - mkl==2018.0.3
  - horovod==0.16.1

### <a name="azureml-pytorch-11-cpu"></a>AzureML-PyTorch 1.1 CPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tocha==1.1
  - torchvision==0.2.1
  - mkl==2018.0.3
  - horovod==0.16.1
  - tensorboard==1.14.0
  - futuro==0.17.1

### <a name="azureml-pytorch-11-gpu"></a>AzureML PyTorch 1.1 GPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tocha==1.1
  - torchvision==0.2.1
  - mkl==2018.0.3
  - horovod==0.16.1
  - tensorboard==1.14.0
  - futuro==0.17.1

### <a name="azureml-pytorch-12-cpu"></a>AzureML PyTorch 1.2 CPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tocha==1.2
  - torchvision==0.4.0
  - mkl==2018.0.3
  - horovod==0.16.1
  - tensorboard==1.14.0
  - futuro==0.17.1

### <a name="azureml-pytorch-12-gpu"></a>AzureML PyTorch 1.2 GPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tocha==1.2
  - torchvision==0.4.0
  - mkl==2018.0.3
  - horovod==0.16.1
  - tensorboard==1.14.0
  - futuro==0.17.1

### <a name="azureml-pytorch-13-cpu"></a>AzureML PyTorch 1.3 CPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tocha==1.3
  - torchvision==0.4.1
  - mkl==2018.0.3
  - horovod==0.18.1
  - tensorboard==1.14.0
  - futuro==0.17.1

### <a name="azureml-pytorch-13-gpu"></a>AzureML PyTorch 1.3 GPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tocha==1.3
  - torchvision==0.4.1
  - mkl==2018.0.3
  - horovod==0.18.1
  - tensorboard==1.14.0
  - futuro==0.17.1

### <a name="azureml-pytorch-14-cpu"></a>AzureML PyTorch 1.4 CPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tocha==1.4.0
  - torchvision==0.5.0
  - mkl==2018.0.3
  - horovod==0.18.1
  - tensorboard==1.14.0
  - futuro==0.17.1

### <a name="azureml-pytorch-14-gpu"></a>AzureML PyTorch 1.4 GPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tocha==1.4.0
  - torchvision==0.5.0
  - mkl==2018.0.3
  - horovod==0.18.1
  - tensorboard==1.14.0
  - futuro==0.17.1

### <a name="azureml-pytorch-15-cpu"></a>AzureML PyTorch 1.5 CPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tocha==1,5.0
  - torchvision==0.5.0
  - mkl==2018.0.3
  - horovod==0.19.1
  - tensorboard==1.14.0
  - futuro==0.17.1

### <a name="azureml-pytorch-15-gpu"></a>AzureML PyTorch 1.5 GPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tocha==1,5.0
  - torchvision==0.5.0
  - mkl==2018.0.3
  - horovod==0.19.1
  - tensorboard==1.14.0
  - futuro==0.17.1

## <a name="scikit-learn"></a>Scikit Aprender

### <a name="azureml-scikit-learn-0203"></a>AzureML Scikit-learn 0.20.3

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - scikit-learn=0.20.3
  - scipy==1.2.1
  - joblib==0.13.2

## <a name="tensorflow"></a>TensorFlow

- [Azure ML TensorFlow 1.10 CPU](#azureml-tensorflow-110-cpu)
- [Azure ML TensorFlow 1.10 GPU](#azureml-tensorflow-110-gpu)
- [Azure ML TensorFlow 1.12 CPU](#azureml-tensorflow-112-cpu)
- [Azure ML TensorFlow 1.12 GPU](#azureml-tensorflow-112-gpu)
- [Azure ML TensorFlow 1.13 CPU](#azureml-tensorflow-113-cpu)
- [Azure ML TensorFlow 1.13 GPU](#azureml-tensorflow-113-gpu)
- [Azure ML TensorFlow 2.0 CPU](#azureml-tensorflow-20-cpu)
- [Azure ML TensorFlow 2.0 GPU](#azureml-tensorflow-20-gpu)
- [Azure ML TensorFlow 2.1 CPU](#azureml-tensorflow-21-cpu)
- [Azure ML TensorFlow 2.1 GPU](#azureml-tensorflow-21-gpu)

### <a name="azureml-tensorflow-110-cpu"></a>AzureML TensorFlow 1.10 CPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tensorflow==1,10
  - horovod==0.15.2

### <a name="azureml-tensorflow-110-gpu"></a>AzureML TensorFlow 1.10 GPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tensorflow-gpu=1.10.0
  - horovod==0.15.2

### <a name="azureml-tensorflow-112-cpu"></a>AzureML TensorFlow 1.12 CPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tensorflow==1,12
  - horovod==0.15.2

### <a name="azureml-tensorflow-112-gpu"></a>AzureML TensorFlow 1.12 GPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tensorflow-gpu=1.12.0
  - horovod==0.15.2

### <a name="azureml-tensorflow-113-cpu"></a>AzureML-TensorFlow 1.13 CPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tensorflow==1.13.1
  - horovod==0.16.1

### <a name="azureml-tensorflow-113-gpu"></a>AzureML TensorFlow 1.13 GPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tensorflow-gpu=1.13.1
  - horovod==0.16.1

### <a name="azureml-tensorflow-20-cpu"></a>AzureML TensorFlow 2.0 CPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tensorflow==2.0
  - horovod==0.18.1

### <a name="azureml-tensorflow-20-gpu"></a>AzureML TensorFlow 2.0 GPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tensorflow-gpu==2.0.0
  - horovod==0.18.1

### <a name="azureml-tensorflow-21-cpu"></a>AzureML TensorFlow 2.1 CPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tensorflow==2.1.0
  - horovod==0.19.1

### <a name="azureml-tensorflow-21-gpu"></a>AzureML TensorFlow 2.1 GPU

canais de pacotes:
- conda-forja

dependências:
- python=3.6.2
- Pip:
  - azureml-core==1.8.0
  - azureml-defaults==1.8.0
  - azureml-telemetria==1,8.0
  - azureml-train-restclients-hyperdrive==1.8.0
  - azureml-train-core==1.8.0
  - tensorflow-gpu=2.1.0
  - horovod==0.19.1

