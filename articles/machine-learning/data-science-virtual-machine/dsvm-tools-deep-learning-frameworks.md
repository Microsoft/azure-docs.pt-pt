---
title: Deep Learning & estruturas de IA
titleSuffix: Azure Data Science Virtual Machine
description: Disponíveis quadros e ferramentas de aprendizagem profunda na Azure Data Science Virtual Machine.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 29f1949cbe9998ac569b0389986023a72e7845b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89070921"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Deep learning e ia frameworks para o Azure Data Science VM
Os quadros de aprendizagem profunda no DSVM estão listados abaixo.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

| Categoria | Valor |
| ------------- | ------------- |
| Versão(s) suportadas | |
| Edições DSVM suportadas      | Ubuntu 16.04    |
| Como é configurado / instalado no DSVM?  | Caffe está instalado em `/opt/caffe` .   As amostras estão em `/opt/caffe/examples` .|
| Como executá-lo      | utilize o X2Go para iniciar serção no seu VM e, em seguida, iniciar um novo terminal e introduzir o seguinte:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Uma nova janela do navegador abre com cadernos de amostras. Os binários são instalados em /opt/caffe/build/install/bin.<br/><br/>A versão instalada do Caffe requer Python 2.7 e não funcionará com python 3.5, que é ativado por padrão. Para mudar para Python 2.7, corra `source activate root` para mudar para o ambiente de Anaconda.|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

| Categoria | Valor |
| ------------- | ------------- |
| Versão(s) suportadas | |
| Edições DSVM suportadas      | Ubuntu 16.04     |
| Como é configurado / instalado no DSVM?  | Caffe2 está instalado no ambiente conda [Python 2.7 (raiz). |
| Como executá-lo      | Terminal: Inicie Python e importe Caffe2. <br/> * JupyterHub: [Conecte-se ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e, em seguida, vá ao diretório caffe2 para encontrar cadernos de amostras. Alguns cadernos exigem que a raiz de Caffe2 seja definida no código Python; entrar /opt/caffe2. |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

| Categoria | Valor |
| ------------- | ------------- |
| Versão(s) suportadas | 5.2 |
| Edições DSVM suportadas      | Ubuntu 16.04    |
| Como é configurado / instalado no DSVM?  | Chainer está instalado em Python 3.5. |
| Como executá-lo      | Terminal: Ative o ambiente Python 3.5, corra `python` e, em seguida, `import chainer` . <br/> * JupyterHub: [Ligue-se ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e, em seguida, vá ao diretório Chainer para encontrar cadernos de amostras.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[Controlador CUDA, cuDNN, NVIDIA](https://developer.nvidia.com/cuda-toolkit)

| Categoria | Valor |
| ------------- | ------------- |
| Versão(s) suportadas | 10.0.130|
| Edições DSVM suportadas      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016<br> Ubuntu 16.04  |
| Como é configurado / instalado no DSVM?  |_nvidia-smi_ está disponível no caminho do sistema.  |
| Como executá-lo      | Abra um pedido de comando (no Windows) ou um terminal (em Linux) e, em seguida, executar _nvidia-smi_. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

| Categoria | Valor |
| ------------- | ------------- |
| Versão(s) suportadas | 0.16.1|
| Edições DSVM suportadas      | Ubuntu 18.04<br> Ubuntu 16.04   |
| Como é configurado / instalado no DSVM?  | Horovod está instalado em Python 3.5 |
| Como executá-lo      | Ative o ambiente correto no terminal e, em seguida, executar Python. |

## <a name="keras"></a>[Keras](https://keras.io/)

| Categoria | Valor |
| ------------- | ------------- |
| Versão(s) suportadas | 2.2.4 |
| Edições DSVM suportadas      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 <br> Ubuntu 16.04   |
| Como é configurado / instalado no DSVM?  | Keras está instalado em Python 3.6 em Windows e em Python 3.5 em Linux |
| Como executá-lo      | Ative o ambiente correto no terminal e, em seguida, executar Python. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

| Categoria | Valor |
| ------------- | ------------- |
| Versão(s) suportadas | 2.5.1 |
| Edições DSVM suportadas      | Windows 2016 <br> Ubuntu 16.04   |
| Como é configurado / instalado no DSVM?  | CNTK está instalado em Python 3.6 no [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) e em Python 3.5 em [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Como executá-lo      | Terminal: Ative o ambiente correto e executar Python. <br/>Jupyter: Ligue-se ao [Jupyter](provision-vm.md) ou [ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e, em seguida, abra o diretório CNTK para amostras. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
| Categoria | Valor |
| ------------- | ------------- |
| Versão(s) suportadas | 1.3.0 |
| Edições DSVM suportadas      | Windows 2016 <br> Ubuntu 16.04    |
| Como é configurado / instalado no DSVM?  | O MXNet está instalado `C:\dsvm\tools\mxnet` no Windows e no `/dsvm/tools/mxnet` Ubuntu. As ligações Python são instaladas no Python 3.6 no [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) e em Python 3.5 no [Linux](./dsvm-tools-languages.md#python-linux-edition)) as ligações R também estão incluídas no Ubuntu DSVM. |
| Como executá-lo      | Terminal: Ative o ambiente conda correto e, em seguida, executar `import mxnet` . <br/>Jupyter: Ligue-se ao [Jupyter](provision-vm.md#access-the-dsvm) ou [ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e, em seguida, abra o `mxnet` diretório para amostras. |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

| Categoria | Valor |
| ------------- | ------------- |
| Versão(s) suportadas | 1.0.1 |
| Edições DSVM suportadas      | Windows 2016 <br> Ubuntu 16.04    |
| Como é configurado / instalado no DSVM?  | O MXNet Model Server está instalado em Python 3.6 no [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) e em Python 3.5 no [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Como executá-lo      | Terminal: Corra `sudo systemctl stop jupyterhub` primeiro para parar o serviço JupyterHub, porque ambos ouvem na mesma porta. Em seguida, ativar o ambiente conda correto e executar `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[Interface de Gestão do Sistema NVidia (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

| Categoria | Valor |
| ------------- | ------------- |
| Versão(s) suportadas |  |
| Edições DSVM suportadas      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016<br> Ubuntu 16.04   |
| Para que é? | Ferramenta NVIDIA para consulta da atividade da GPU |
| Como é configurado / instalado no DSVM?  | `nvidia-smi` está no caminho do sistema. |
| Como executá-lo      | Numa máquina virtual **com GPU,,** abra um pedido de comando (no Windows) ou um terminal (em Linux) e, em seguida, corra `nvidia-smi` . |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

| Categoria | Valor |
| ------------- | ------------- |
| Versão(s) suportadas | 1.2.0 (Ubuntu 16.04), 1.4.0 (Ubuntu 18.04, Windows 2019) |
| Edições DSVM suportadas      | Windows Server 2019<br>Ubuntu 18.04<br> Ubuntu 16.04 |
| Como é configurado / instalado no DSVM?  | Instalado em [Python 3.5](dsvm-tools-languages.md#python-linux-edition). Amostra Os cadernos jupyter estão incluídos, e as amostras estão em /dsvm/samples/pytorch. |
| Como executá-lo      | Terminal: Ative o ambiente correto e, em seguida, executar Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Ligue e, em seguida, abra o diretório PyTorch para amostras.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

| Categoria | Valor |
| ------------- | ------------- |
| Versão(s) suportadas | 1.13 |
| Edições DSVM suportadas      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 <br> Ubuntu 16.04 |
| Como é configurado / instalado no DSVM?  | Instalado em Python 3.5 em [Linux](dsvm-tools-languages.md#python-linux-edition) e Python 3.6 no [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| Como executá-lo      | Terminal: Ative o ambiente correto e, em seguida, executar Python. <br/> * Jupyter: Ligue-se ao [Jupyter](provision-vm.md) ou [ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e, em seguida, abra o diretório tensorFlow para amostras.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

| Categoria | Valor |
| ------------- | ------------- |
| Versão(s) suportadas | 1.12 |
| Edições DSVM suportadas      | Ubuntu 16.04 |
| Como é configurado / instalado no DSVM?  | tensorflow_model_server está disponível no terminal. |
| Como executá-lo      |  As amostras estão disponíveis [online.](https://www.tensorflow.org/serving/)   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

| Categoria | Valor |
| ------------- | ------------- |
| Versão(s) suportadas | 1.0.3 |
| Edições DSVM suportadas      | Ubuntu 16.04 |
| Como é configurado / instalado no DSVM?  |Theano está instalado em Python 2.7 _(raiz),_ e em ambiente Python 3.5 _(py35)._ |
| Como executá-lo      |  Terminal: Ative a versão Python que deseja (raiz ou py35), corra Python e, em seguida, importe Theano.<br/>* Jupyter: Selecione o núcleo Python 2.7 ou 3.5 e, em seguida, importe Theano.  <br/>Para trabalhar em torno de um bug recente da biblioteca de kernel de matemática (MKL), você precisa primeiro definir a camada de rosca MKL da seguinte forma:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |
