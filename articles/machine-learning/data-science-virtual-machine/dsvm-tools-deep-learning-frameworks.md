---
title: Quadros de IA & de Aprendizagem Profunda
titleSuffix: Azure Data Science Virtual Machine
description: Quadros e ferramentas de aprendizagem profunda disponíveis na Máquina Virtual de Ciência de Dados Azure.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d8a5cf428f41b130e6faf68ac87a075c15211099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270072"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Deep learning e quadros de IA para o Azure Data Science VM
Os quadros de aprendizagem profunda no DSVM estão listados abaixo.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Versão ou s suportado | |
| Edições DSVM suportadas      | Linux (Ubuntu)     |
| Como é configurado /instalado no DSVM?  | O Caffe está `/opt/caffe`instalado em .   As amostras estão dentro. `/opt/caffe/examples`|
| Como executá-lo      | use x2Go para iniciar o seu VM e, em seguida, iniciar um novo terminal e entrar no seguinte:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Uma nova janela do navegador abre com cadernos de amostras. Os binários são instalados em /opt/caffe/build/install/bin.<br/><br/>A versão instalada do Caffe requer Python 2.7 e não funcionará com python 3.5, que é ativado por padrão. Para mudar para Python 2.7, corra `source activate root` para mudar para o ambiente Anaconda.|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Versão ou s suportado | |
| Edições DSVM suportadas      | Linux (Ubuntu)     |
| Como é configurado /instalado no DSVM?  | O Caffe2 está instalado no ambiente conda [Python 2.7 (raiz). |
| Como executá-lo      | Terminal: Iniciar Python e importar Caffe2. <br/> * JupyterHub: [Ligue-se ao JupyterHub,](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e depois vá ao diretório Caffe2 para encontrar cadernos de amostras. Alguns cadernos exigem que a raiz do Caffe2 seja definida no código Python; entrar /opt/caffe2. |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Versão ou s suportado | 5.2 |
| Edições DSVM suportadas      | Linux (Ubuntu)     |
| Como é configurado /instalado no DSVM?  | A corrente está instalada em Python 3.5. |
| Como executá-lo      | Terminal: Ativar o ambiente Python `python`3.5, executar, e depois `import chainer`. <br/> * JupyterHub: [Ligue-se ao JupyterHub,](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e depois vá ao diretório chainer para encontrar cadernos de amostras.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[Controlador CUDA, cuDNN, NVIDIA](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Versão ou s suportado | 10.0.130|
| Edições DSVM suportadas      | Windows e Linux   |
| Como é configurado /instalado no DSVM?  |_nvidia-smi_ está disponível no caminho do sistema.  |
| Como executá-lo      | Abra um pedido de comando (no Windows) ou um terminal (em Linux) e, em seguida, executar _nvidia-smi_. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Versão ou s suportado | 0.16.1|
| Edições DSVM suportadas      | Linux (Ubuntu)   |
| Como é configurado /instalado no DSVM?  | Horovod está instalado em Python 3.5 |
| Como executá-lo      | Ativar o ambiente correto no terminal e, em seguida, executar Python. |

## <a name="keras"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Versão ou s suportado | 2.2.4 |
| Edições DSVM suportadas      | Windows e Linux   |
| Como é configurado /instalado no DSVM?  | Keras está instalado em Python 3.6 no Windows e em Python 3.5 em Linux |
| Como executá-lo      | Ativar o ambiente correto no terminal e, em seguida, executar Python. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Versão ou s suportado | 2.5.1 |
| Edições DSVM suportadas      | Windows e Linux   |
| Como é configurado /instalado no DSVM?  | A CNTK está instalada em Python 3.6 no [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) e em Python 3.5 no [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Como executá-lo      | Terminal: Ative o ambiente correto e execute python. <br/>Jupyter: Ligue-se a [Jupyter](provision-vm.md) ou [JupyterHub,](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e depois abra o diretório CNTK para amostras. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| Versão ou s suportado | 1.3.0 |
| Edições DSVM suportadas      | Windows e Linux   |
| Como é configurado /instalado no DSVM?  | O MXNet está `C:\dsvm\tools\mxnet` instalado `/dsvm/tools/mxnet` no Windows e no Ubuntu. As encadernações Python estão instaladas em Python 3.6 no [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) e em Python 3.5 no [Linux](./dsvm-tools-languages.md#python-linux-edition)– As encadernações R também estão incluídas no Ubuntu DSVM. |
| Como executá-lo      | Terminal: Ativar o ambiente de `import mxnet`conda correto e, em seguida, executar . <br/>Jupyter: Ligue-se a [Jupyter](provision-vm.md#access-the-dsvm) ou [JupyterHub,](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e depois abra o `mxnet` diretório para amostras. |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| Versão ou s suportado | 1.0.1 |
| Edições DSVM suportadas      | Windows e Linux   |
| Como é configurado /instalado no DSVM?  | MXNet Model Server está instalado em Python 3.6 no [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) e em Python 3.5 no [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Como executá-lo      | Terminal: `sudo systemctl stop jupyterhub` Corra para parar primeiro o serviço JupyterHub, porque ambos ouvem na mesma porta. Em seguida, ativar o ambiente de conda correto e executar`mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[Interface de Gestão do Sistema NVidia (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| Versão ou s suportado |  |
| Edições DSVM suportadas      | Windows e Linux   |
| Para que é? | Ferramenta NVIDIA para consulta da atividade da GPU |
| Como é configurado /instalado no DSVM?  | `nvidia-smi`está no caminho do sistema. |
| Como executá-lo      | Numa máquina virtual **com GPU's,** abra um pedido de comando (no Windows) `nvidia-smi`ou um terminal (em Linux) e, em seguida, executar . |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Versão ou s suportado | 1.2.0 (Ubuntu 16.04, Windows 2016), 1.4.0 (Ubuntu 18.04, Windows 2019) |
| Edições DSVM suportadas      | Linux |
| Como é configurado /instalado no DSVM?  | Instalado em [Python 3.5](dsvm-tools-languages.md#python-linux-edition). Os cadernos jupyter da amostra estão incluídos, e as amostras estão em /dsvm/samples/pytorch. |
| Como executá-lo      | Terminal: Ativar o ambiente correto e, em seguida, executar python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Conecte e abra o diretório PyTorch para amostras.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Versão ou s suportado | 1.13 |
| Edições DSVM suportadas      | Windows, Linux |
| Como é configurado /instalado no DSVM?  | Instalado em Python 3.5 em [Linux](dsvm-tools-languages.md#python-linux-edition) e Python 3.6 no [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| Como executá-lo      | Terminal: Ativar o ambiente correto e, em seguida, executar python. <br/> * Jupyter: Ligue-se ao [Jupyter](provision-vm.md) ou [ao JupyterHub,](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)e depois abra o diretório TensorFlow para amostras.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Versão ou s suportado | 1.12 |
| Edições DSVM suportadas      | Linux |
| Como é configurado /instalado no DSVM?  | tensorflow_model_server está disponível no terminal. |
| Como executá-lo      |  As amostras estão disponíveis [online.](https://www.tensorflow.org/serving/)   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Versão ou s suportado | 1.0.3 |
| Edições DSVM suportadas      | Linux |
| Como é configurado /instalado no DSVM?  |Theano está instalado em Python 2.7 (_raiz)_ e em ambiente Python 3.5 _(py35_). |
| Como executá-lo      |  Terminal: Ative a versão Python que deseja (raiz ou py35), executar Python e, em seguida, importar Theano.<br/>* Jupyter: Selecione o python 2.7 ou 3,5 kernel e, em seguida, importe Theano.  <br/>Para contornar um recente bug da biblioteca de kernel de matemática (MKL), você precisa primeiro definir a camada de rosca MKL da seguinte forma:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |