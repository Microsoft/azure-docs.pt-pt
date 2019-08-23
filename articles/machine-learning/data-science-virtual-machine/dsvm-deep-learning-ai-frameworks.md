---
title: Aprendizado profundo & estruturas de ia
titleSuffix: Azure Data Science Virtual Machines
description: Estruturas e ferramentas de aprendizado profundo no Azure Máquina Virtual de Ciência de Dados, incluindo TensorFlow, PyTorch, Keras, Caffe, MXNet, Horovod, Theano, chainer e muito mais.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2019
ms.author: vijetaj
ms.openlocfilehash: adf6a68d6da4236874dce403bb4d7043087049b2
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970353"
---
# <a name="deep-learning-and-ai-frameworks"></a>Arquiteturas de Aprendizagem Profunda e AI
O [máquina virtual de ciência de dados](https://aka.ms/dsvm) (DSVM) dá suporte a várias estruturas de aprendizado profundo para ajudar a criar aplicativos de ia (inteligência artificial) com análise preditiva e recursos cognitivas, como reconhecimento de imagem e linguagem.

As estruturas de aprendizado profundo disponíveis no DSVM incluem:

+ TensorFlow
+ PyTorch
+ Keras
+ Caffe
+ Caffe2
+ Torch
+ Modelo de MXNet Server
+ MXNet
+ Horovod
+ Theano
+ Chainer
+ Água profunda
+ NVIDIA DÍGITOS
+ NVIDIA smi
+ Disponibilização de TensorFlow
+ TensorRT
+ Microsoft Cognitive Toolkit

|Ferramentas&nbsp;&nbsp;deDLnoDSVM&nbsp;|Windows|Linux|Notas&nbsp;de uso|
|---------|:-------------------:|:------------------:|-----|
|[TensorFlow](https://www.tensorflow.org/) | Sim (Windows 2016) | Sim |Instalado no Python 3,5 no [Linux e no windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e no Python 3,6 no [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Os notebooks Jupyter de exemplo estão incluídos no DSVM.<br/><br/>**Para executá-lo**:<br/>* Terminal: Ative o ambiente correto e execute Python. <br/> Jupyter Conecte-se ao [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)e, em seguida, abra o diretório TensorFlow para obter exemplos.  |
|[PyTorch](https://pytorch.org/)| Não | Sim |Instalado no [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Os notebooks Jupyter de exemplo estão incluídos e os exemplos estão em/dsvm/Samples/pytorch.    <br/><br/>**Para executá-lo**<br/>* Terminal: Ative o ambiente correto e execute Python.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux): Conecte-se e, em seguida, abra o diretório PyTorch para obter exemplos.  |
|[Keras](https://keras.io/)| Sim | Sim |A API é instalada no Python 3,5 no [Linux e no windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e no Python 3,6 no [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). [Amostras](https://github.com/fchollet/keras/tree/master/examples)<br/><br/>**Para executá-lo**:<br/>* Terminal: Ative o ambiente correto e execute Python. <br/> Jupyter Baixe os exemplos do local do GitHub, conecte-se a [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)e, em seguida, abra o diretório de exemplo. |
|[Caffe](https://github.com/caffe2/caffe2) | Não |Sim (Ubuntu)|Caffe está instalado no `/opt/caffe`.   Os exemplos estão `/opt/caffe/examples`em. <br/><br/>**Para executá-lo**, use X2Go para fazer logon em sua VM e, em seguida, inicie um novo terminal e digite:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Uma nova janela do browser abre-se com blocos de notas de exemplo. Os binários são instalados no /opt/caffe/build/install/bin.<br/><br/>Versão instalada do Caffe requer o Python 2.7 e não funcionará com o Python 3.5 ativado por predefinição. Para alternar para o Python 2,7, `source activate root` execute para alternar o ambiente Anaconda.|
|[Caffe2](https://github.com/caffe2/caffe2) | Não |Sim (Ubuntu)|Caffe2 está instalado no [Python 2.7 (root) conda ambiente](dsvm-languages.md#python-linux-and-windows-server-2012-edition). A origem está no `/opt/caffe2`.<br/>Blocos de notas de exemplo estão incluídos no JupyterHub.<br/><br/>**Para executá-lo**:<br/>* No terminal: Ativar a [ambiente do Python raiz](dsvm-languages.md#python-linux-and-windows-server-2012-edition), inicie o Python e importar caffe2. <br/> * JupyterHub em: [ligue-se ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), em seguida, navegue para o diretório de Caffe2 para localizar os blocos de notas de exemplo. Alguns notebooks requerem a raiz de Caffe2 ser definida no código de Python; Introduza /opt/caffe2. |
|[Torch](http://torch.ch/) | Não |Sim (Ubuntu)|Maçarico está instalado no `/dsvm/tools/torch`. PyTorch está instalado no Python 2.7 (_raiz_), bem como o Python 3.5 (_py35_) ambiente. Os exemplos de Torch `/dsvm/samples/torch` estão em e os exemplos `/dsvm/samples/pytorch`de PyTorch estão em. |
|[MXNet](https://mxnet.io/) | Sim (Windows 2016) | Sim|MXNet é instalado na `C:\dsvm\tools\mxnet` em Windows e `/dsvm/tools/mxnet` no Linux. Enlaces de Python são instalados no Python 3.5 no [Linux e Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e o Python 3.6 na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Enlaces de R também são instalados no Ubuntu.<br/><br/>Blocos de notas do Jupyter exemplo são incluídos. <br/><br/>**Para executá-lo**:<br/>Componentes Ative o ambiente correto e execute Python. <br/> Jupyter Conecte-se ao [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)e, em seguida, abra o diretório mxnet para obter exemplos.|
|[Servidor de modelo MXNet](https://github.com/awslabs/mxnet-model-server) | Não | Sim |Um servidor para criar pontos de extremidade HTTP para modelos MXNet e ONNX. _servidor de modelo mxnet_ está disponível no terminal. Exemplos na [página do servidor de modelos do MXNet](https://github.com/awslabs/mxnet-model-server).|
|[Horovod](https://github.com/uber/horovod) | Não | Sim (Ubuntu) |Estrutura de aprendizado profundo distribuída para TensorFlow. Horovod está instalado no Python 3.5 [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  [Consulte os exemplos](https://github.com/uber/horovod/tree/master/examples)<br/><br/>**Para executá-lo**, ative o ambiente correto no terminal e execute Python. |
|[Theano](https://github.com/Theano/Theano) | Não | Sim (Ubuntu) |Theano está instalado no Python 2.7 (_raiz_), bem como o Python 3.5 (_py35_) ambiente.<br/><br/>**Para executá-lo**: <br/>Componentes Ative a versão do Python que você deseja (raiz ou py35), execute o Python e, em seguida, importe o Theano.<br/>Jupyter Selecione o kernel Python 2,7 ou 3,5 e importe Theano.  <br/>Para contornar um bug MKL recente, precisa primeiro definir MKL threading camada:<br/><br/>_exportar MKL_THREADING_LAYER = GNU_|
|[Chainer](https://chainer.org/) |Não | Sim |Chainer é instalado na [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL e ChainerCV também são instalados. <br/><br/>Blocos de notas de exemplo estão incluídos no JupyterHub.<br/><br/>**Para executá-lo**: <br/>Componentes Ative o ambiente [python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) , execute _Python_e, em seguida, importe o Chainr. <br/> JupyterHub [Conecte-se ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)e navegue até o diretório do encadeamento para localizar os blocos de anotações de exemplo.|
|[Dígitos NVidia](https://github.com/NVIDIA/DIGITS) | Não | Sim (Ubuntu) |O sistema de aprendizado profundo da NVIDIA para treinar rapidamente modelos de aprendizado profundo. DÍGITOS é instalado na `/dsvm/tools/DIGITS` e está disponível um serviço chamado _dígitos_.  <br/><br/>**Para executá-lo**: <br/>Iniciar sessão na VM com X2Go. Em um terminal, inicie o serviço ```sudo systemctl start digits```. <br/><br/>A iniciar, o serviço demora cerca de um minuto. Inicie um browser e navegue para `http://localhost:5000`. Tenha em atenção que os dígitos não fornece um início de sessão seguro e não deve ser exposta fora da VM.|
|[CUDA, cuDNN, Driver NVIDIA](https://developer.nvidia.com/cuda-toolkit) |Sim | Sim | |
|NVIDIA smi|Sim | Sim |Ferramenta NVIDIA para consultar a atividade de GPU. _NVIDIA smi_ está disponível no caminho do sistema. <br/><br/>Inicie uma linha de comandos (no Windows) ou um terminal (no Linux), em seguida, execute _nvidia smi_.|
|[Serviço de TensorFlow](https://www.tensorflow.org/serving/) | Não | Sim |Um servidor para inferência em um modelo TensorFlow. _tensorflow_model_server_ está disponível no terminal. Os exemplos estão disponíveis [online](https://www.tensorflow.org/serving/).|
|[TensorRT](https://developer.nvidia.com/tensorrt) |  Não | Sim (Ubuntu) |Uma aprendizagem profunda server de inferência de tipos da NVIDIA. TensorRT é instalado como uma _apt_ pacote. Os exemplos estão disponíveis [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|Sim | Sim | Instalado no Python 3,5 no [Linux e no windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e no Python 3,6 no [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Os notebooks Jupyter de exemplo estão incluídos no DSVM. <br/><br/>**Para executá-lo**: <br/>Componentes Ative o ambiente correto e execute o Python. <br/>Jupyter Conecte-se ao [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)e, em seguida, abra o diretório CNTK para obter exemplos. |
|Água profunda|Não | Sim (Ubuntu) |A estrutura de aprendizado profundo para H2O, água profunda é instalada no [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e também está `/dsvm/tools/deep_water`disponível no. Blocos de notas de exemplo estão incluídos no JupyterHub. Água profunda requer CUDA 8 cuDNN 5.1. Isso não está no caminho de biblioteca por padrão, como outras estruturas de aprendizagem profunda utilização CUDA 9 e cuDNN 7. Para utilizar CUDA 8 + cuDNN 5.1 para profunda de água:<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>Para utilizar profunda de água:<br/>* Terminal: Ative o ambiente [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e execute _Python_. <br/>* JupyterHub: [Conecte-se ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)e, em seguida, navegue até o diretório Deep_Water para encontrar blocos de anotações de exemplo.|
