---
title: Ambientes organizados
titleSuffix: Azure Machine Learning
description: Conheça os ambientes curados da Azure Machine Learning, um conjunto de ambientes pré-configurados que ajudam a reduzir os tempos de experimentação e preparação de implantação.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 11/16/2020
ms.openlocfilehash: 0e6817e03c5e5363ad925367b0632e26fc2da4a2
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701403"
---
# <a name="azure-machine-learning-curated-environments"></a>Ambientes curados de aprendizagem de máquinas Azure

Este artigo lista os ambientes curados em Azure Machine Learning, e os pacotes e canais que estão pré-instalados neles. Ambientes curados são fornecidos pela Azure Machine Learning e estão disponíveis no seu espaço de trabalho por padrão. São apoiados por imagens em cache do Docker, reduzindo o custo de preparação de execução e permitindo um tempo de implementação mais rápido. Use estes ambientes para começar rapidamente com várias estruturas de aprendizagem automática.

> [!NOTE]
> Esta lista é atualizada a partir de setembro de 2020. Use o Python SDK para obter a lista mais atualizada. Para mais informações, consulte o [artigo ambientes.](./how-to-use-environments.md#use-a-curated-environment)

## <a name="azureml-automl"></a>AzureML-AutoML

**Canais de pacote:**

* anaconda
* conda-forja
* pytorch

**Pacotes Conda:**

* python
* numpy
* scikit-learn
* pandas
* py-xgboost
* fbprophet
* feriados
* setuptools-git
* psutil

**Pacotes pip:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-padrão
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inferência-esquema
* py-cpuinfo

## <a name="azureml-automl-dnn"></a>AzureML-AutoML-DNN

**Canais de pacote:**

* anaconda
* conda-forja
* pytorch

**Pacotes Conda:**

* python
* numpy
* scikit-learn
* pandas
* py-xgboost
* fbprophet
* feriados
* setuptools-git
* pytorch
* cudatoolkit
* psutil

**Pacotes pip:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-padrão
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inferência-esquema
* pytorch-transformers
* espláse
* en_core_web_sm
* py-cpuinfo

## <a name="azureml-automl-dnn-gpu"></a>AzureML-AutoML-DNN-GPU

**Canais de pacote:**

* anaconda
* conda-forja
* pytorch

**Pacotes Conda:**

* python
* numpy
* scikit-learn
* pandas
* fbprophet
* feriados
* setuptools-git
* pytorch
* cudatoolkit
* psutil

**Pacotes pip:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-padrão
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inferência-esquema
* horovod
* pytorch-transformers
* espláse
* en_core_web_sm
* py-cpuinfo

## <a name="azureml-automl-dnn-vision-gpu"></a>AzureML-AutoML-DNN-Vision-GPU

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-dataset-runtime
* azureml-contrib-dataset
* azureml-telemetry
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-padrão
* azureml-interpret
* azureml-explain-model
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-contrib-automl-dnn-vision

## <a name="azureml-automl-gpu"></a>AzureML-AutoML-GPU

**Canais de pacote:**

* anaconda
* conda-forja
* pytorch

**Pacotes Conda:**

* python
* numpy
* scikit-learn
* pandas
* fbprophet
* feriados
* setuptools-git
* psutil

**Pacotes pip:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-padrão
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inferência-esquema
* py-cpuinfo

## <a name="azureml-chainer-510-cpu"></a>AzureML-Chainer-5.1.0-CPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* acorrentador
* mpi4py

## <a name="azureml-chainer-510-gpu"></a>AzureML-Chainer-5.1.0-GPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* acorrentador
* cupy-cuda90
* mpi4py

## <a name="azureml-dask-cpu"></a>AzureML-Dask-CPU

**Canais de pacote:**

* conda-forja
* pytorch
* predefinições

**Pacotes Conda:**

* python

**Pacotes pip:**

* adlfs
* azureml-core
* azureml-dataset-runtime
* dask[completo]
* dask-ml[completo]
* distribuído
* fastparquet
* fsspec
* joblib
* jupyterlab
* Rio Iz4
* mpi4py
* bloco de notas
* pyarrow

## <a name="azureml-dask-gpu"></a>AzureML-Dask-GPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python
* matplotlib

**Pacotes pip:**

* azureml-padrão
* adlfs
* azureml-core
* dask[completo]
* dask-ml[completo]
* distribuído
* fastparquet
* fsspec
* joblib
* jupyterlab
* Rio Iz4
* mpi4py
* bloco de notas
* pyarrow

## <a name="azureml-hyperdrive-forecastdnn"></a>AzureML-Hyperdrive-ForecastDNN

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-padrão
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-contrib-automl-dnn-previsão

## <a name="azureml-minimal"></a>AzureML-Minimal

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão

## <a name="azureml-pyspark-mmlspark-015"></a>AzureML-PySpark-MmlSpark-0.15

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core

## <a name="azureml-pytorch-10-cpu"></a>AzureML-PyTorch-1.0-CPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tocha
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-10-gpu"></a>AzureML-PyTorch-1.0-GPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tocha
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-11-cpu"></a>AzureML-PyTorch-1.1-CPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tocha
* torchvision
* mkl
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-11-gpu"></a>AzureML-PyTorch-1.1-GPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tocha
* torchvision
* mkl
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-12-cpu"></a>AzureML-PyTorch-1.2-CPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tocha
* torchvision
* mkl
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-12-gpu"></a>AzureML-PyTorch-1.2-GPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tocha
* torchvision
* mkl
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-13-cpu"></a>AzureML-PyTorch-1.3-CPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tocha
* torchvision
* mkl
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-13-gpu"></a>AzureML-PyTorch-1.3-GPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tocha
* torchvision
* mkl
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-14-cpu"></a>AzureML-PyTorch-1.4-CPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tocha
* torchvision
* mkl
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-14-gpu"></a>AzureML-PyTorch-1.4-GPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tocha
* torchvision
* mkl
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-15-cpu"></a>AzureML-PyTorch-1.5-CPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tocha
* torchvision
* mkl
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-15-gpu"></a>AzureML-PyTorch-1.5-GPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tocha
* torchvision
* mkl
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-16-cpu"></a>AzureML-PyTorch-1.6-CPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tocha
* torchvision
* mkl
* horovod
* tensorboard
* futuro

## <a name="azureml-pytorch-16-gpu"></a>AzureML-PyTorch-1.6-GPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tocha
* torchvision
* mkl
* horovod
* tensorboard
* futuro

## <a name="azureml-scikit-learn-0203"></a>AzureML-Scikit-learn-0.20.3

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* scikit-learn
* scipy
* joblib

## <a name="azureml-tensorflow-110-cpu"></a>AzureML-TensorFlow-1.10-CPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorfluor
* horovod

## <a name="azureml-tensorflow-110-gpu"></a>AzureML-TensorFlow-1.10-GPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-112-cpu"></a>AzureML-TensorFlow-1.12-CPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorfluor
* horovod

## <a name="azureml-tensorflow-112-gpu"></a>AzureML-TensorFlow-1.12-GPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-113-cpu"></a>AzureML-TensorFlow-1.13-CPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorfluor
* horovod

## <a name="azureml-tensorflow-113-gpu"></a>AzureML-TensorFlow-1.13-GPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-20-cpu"></a>AzureML-TensorFlow-2.0-CPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorfluor
* horovod

## <a name="azureml-tensorflow-20-gpu"></a>AzureML-TensorFlow-2.0-GPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-21-cpu"></a>AzureML-TensorFlow-2.1-CPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorfluor
* horovod

## <a name="azureml-tensorflow-21-gpu"></a>AzureML-TensorFlow-2.1-GPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-22-cpu"></a>AzureML-TensorFlow-2.2-CPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorfluor
* horovod

## <a name="azureml-tensorflow-22-gpu"></a>AzureML-TensorFlow-2.2-GPU

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tutorial"></a>AzureML-Tutorial

**Canais de pacote:**

* anaconda
* conda-forja

**Pacotes Conda:**

* python
* pandas
* numpy
* tqdm
* scikit-learn
* matplotlib

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-telemetry
* azureml-train-restclients-hyperdrive
* azureml-train-core
* azureml-widgets
* azureml-pipeline-core
* azureml-pipeline-steps
* azureml-opendatasets
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-train
* azureml-sdk
* azureml-interpret
* azureml-tensorboard
* azureml-mlflow
* mlflow
* sklearn-pandas

## <a name="azureml-vowpalwabbit-880"></a>AzureML-VowpalWabbit-8.8.0

**Canais de pacote:**

* conda-forja

**Pacotes Conda:**

* python

**Pacotes pip:**

* azureml-core
* azureml-padrão
* azureml-dataset-runtime[fusível,pandas]
