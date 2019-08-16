---
title: Estruturas de aprendizagem profunda e AI - Azure | Documentos da Microsoft
description: Saiba mais sobre a estruturas e ferramentas suportadas na máquina de Virtual de ciência de dados de aprendizagem profunda.
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
ms.openlocfilehash: e2bcda25ed6897f8955d7e74981bc4f04a145c99
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534918"
---
# <a name="deep-learning-and-ai-frameworks"></a>Arquiteturas de Aprendizagem Profunda e AI
O [máquina virtual de ciência de dados](https://aka.ms/dsvm) (DSVM) dá suporte a várias estruturas de aprendizado profundo para ajudar a criar aplicativos de ia (inteligência artificial) com análise preditiva e recursos cognitivas, como reconhecimento de imagem e linguagem.

Aqui estão os detalhes sobre todas as estruturas disponíveis na DSVM de aprendizagem a profunda.


## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| O que é?   | Arquitetura de aprendizagem profunda      |
| Edições DSVM suportados      | Windows, Linux     |
| Como é ele configurado / instalado no DSVM?  | TensorFlow está instalado no Python 3.5 [Linux e Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e o Python 3.6 na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).  |
| Links para amostras      | Blocos de notas do Jupyter exemplo são incluídos.     |
| Ferramentas relacionadas na DSVM      | Keras      |
| Como utilizar / executá-lo?    | * Num terminal: Ativar o ambiente correto, em seguida, execute o Python. <br/> * Em Jupyter: Conecte-se ao [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)e, em seguida, abra o diretório TensorFlow para obter exemplos.  |

## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| O que é?   | Arquitetura de aprendizagem profunda      |
| Edições DSVM suportados      | Linux     |
| Como é ele configurado / instalado no DSVM?  | PyTorch é instalado na [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Links para amostras      | Blocos de notas do Jupyter exemplo estão incluídos, e também pode ver exemplos no /dsvm/samples/pytorch.      |
| Ferramentas relacionadas na DSVM      | Torch      |
| Como utilizar / executá-lo |* Num terminal: Ativar o ambiente correto, em seguida, execute o Python. <br/> * Em Jupyter: Conecte-se ao [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)e, em seguida, abra o diretório PyTorch para obter exemplos.  |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| O que é?   | API de alto nível de aprendizagem profunda      |
| Edições DSVM suportados      | Windows, Linux     |
| Como é ele configurado / instalado no DSVM?  | TensorFlow está instalado no Python 3.5 [Linux e Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e o Python 3.6 na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). |
| Links para amostras      | https://github.com/fchollet/keras/tree/master/examples      |
| Ferramentas relacionadas na DSVM      | Microsoft Cognitive Toolkit, TensorFlow, Theano      |
| Como utilizar / executá-lo?    | * Num terminal: Ativar o ambiente correto, em seguida, execute o Python. <br/> * Em Jupyter: Baixe os exemplos do local do GitHub, conecte-se a [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)e, em seguida, abra o diretório de exemplo. |

## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| O que é?   | Arquitetura de aprendizagem profunda      |
| Edições DSVM suportados      | Ubuntu     |
| Como é ele configurado / instalado no DSVM?  | Caffe está instalado no `/opt/caffe`.    |
| Como mudar para o Python 2.7 | Execute `source activate root` |
| Links para amostras      | Exemplos estão incluídos no `/opt/caffe/examples`.      |
| Ferramentas relacionadas na DSVM      | Caffe2      |

### <a name="how-to-use--run-it"></a>Como utilizar / executá-lo?

Utilizar X2Go para iniciar sessão na sua VM, em seguida, iniciar um novo terminal e introduza

```
cd /opt/caffe/examples
source activate root
jupyter notebook
```

Uma nova janela do browser abre-se com blocos de notas de exemplo.

Os binários são instalados no /opt/caffe/build/install/bin.

Versão instalada do Caffe requer o Python 2.7 e não funcionará com o Python 3.5 ativado por predefinição. Executar `source activate root` para mudar de ambiente de Anaconda.

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| O que é?   | Arquitetura de aprendizagem profunda      |
| Edições DSVM suportados      | Ubuntu     |
| Como é ele configurado / instalado no DSVM?  | Caffe2 está instalado no [Python 2.7 (root) conda ambiente](dsvm-languages.md#python-linux-and-windows-server-2012-edition). A origem está no `/opt/caffe2`. |
| Links para amostras      | Blocos de notas de exemplo estão incluídos no JupyterHub. |
| Ferramentas relacionadas na DSVM      | Caffe      |
| Como utilizar / executá-lo?    | * No terminal: Ativar a [ambiente do Python raiz](dsvm-languages.md#python-linux-and-windows-server-2012-edition), inicie o Python e importar caffe2. <br/> * JupyterHub em: [ligue-se ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), em seguida, navegue para o diretório de Caffe2 para localizar os blocos de notas de exemplo. Alguns notebooks requerem a raiz de Caffe2 ser definida no código de Python; Introduza /opt/caffe2. |
| Criar notas | Caffe2 é criado a partir da origem no Linux e inclui CUDA, cuDNN e Intel MKL. A consolidação de atual é 0d9c0d48c6f20143d6404b99cc568efd29d5a4be, que foi escolhido para estabilidade em todos os GPUs e exemplos testados. |

## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| O que é?   | Arquitetura de aprendizagem profunda      |
| Edições DSVM suportados      | Ubuntu     |
| Como é ele configurado / instalado no DSVM?  | Maçarico está instalado no `/dsvm/tools/torch`. PyTorch está instalado no Python 2.7 (_raiz_), bem como o Python 3.5 (_py35_) ambiente.   |
| Links para amostras      | Exemplos de Maçarico estão localizados em `/dsvm/samples/torch`. Exemplos de PyTorch estão localizados em `/dsvm/samples/pytorch`.      |




## <a name="mxnet-model-server"></a>Modelo de MXNet Server

|    |           |
| ------------- | ------------- |
| O que é?   | Um servidor para criar pontos finais HTTP para modelos de MXNet e ONNX      |
| Edições DSVM suportados      | Linux     |
| Como é ele configurado / instalado no DSVM?  | _servidor de modelo mxnet_ está disponível no terminal.   |
| Links para amostras      | Procure exemplos atualizados [página do servidor do modelo de MXNet](https://github.com/awslabs/mxnet-model-server).    |
| Ferramentas relacionadas na DSVM      | MXNet      |


## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| O que é?   | Arquitetura de aprendizagem profunda      |
| Edições DSVM suportados      | Windows, Linux     |
| Como é ele configurado / instalado no DSVM?  | MXNet é instalado na `C:\dsvm\tools\mxnet` em Windows e `/dsvm/tools/mxnet` no Linux. Enlaces de Python são instalados no Python 3.5 no [Linux e Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e o Python 3.6 na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Enlaces de R também são instalados no Ubuntu.   |
| Links para amostras      | Blocos de notas do Jupyter exemplo são incluídos.    |
| Ferramentas relacionadas na DSVM      | Keras      |
| Como utilizar / executá-lo?    | * Num terminal: Ativar o ambiente correto, em seguida, execute o Python. <br/> * Em Jupyter: Conecte-se ao [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)e, em seguida, abra o diretório mxnet para obter exemplos.  |
 | Criar notas | MXNet baseia-se de origem no Linux. Esta compilação inclui CUDA, cuDNN, NCCL e MKL. |






## <a name="horovod"></a>Horovod

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo distribuída para TensorFlow      |
| Edições DSVM suportados      | Ubuntu     |
| Como é ele configurado / instalado no DSVM?  | Horovod está instalado no Python 3.5 [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Links para amostras      | [https://github.com/uber/horovod/tree/master/examples](https://github.com/uber/horovod/tree/master/examples)     |
| Ferramentas relacionadas na DSVM      | TensorFlow      |
| Como utilizar / executá-lo?    | Num terminal: Ativar o ambiente correto, em seguida, execute o Python. |



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| O que é?   | Arquitetura de aprendizagem profunda      |
| Edições DSVM suportados      | Ubuntu     |
| Como é ele configurado / instalado no DSVM?  | Theano está instalado no Python 2.7 (_raiz_), bem como o Python 3.5 (_py35_) ambiente.   |
| Ferramentas relacionadas na DSVM      | Keras      |
| Como utilizar / executá-lo?    | * Num terminal, ative a versão de Python que pretende (raiz ou py35), execute o python e importar theano. <br/> * Em Jupyter, selecione o kernel Python 2,7 ou 3,5 e importe Theano.  <br/>Para contornar um bug MKL recente, precisa primeiro definir MKL threading camada:<br/><br/>_exportar MKL_THREADING_LAYER = GNU_|


## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| O que é?   | Arquitetura de aprendizagem profunda      |
| Edições DSVM suportados      | Windows, Linux     |
| Como é ele configurado / instalado no DSVM?  | Chainer é instalado na [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL e ChainerCV também são instalados.   |
| Links para amostras      | Blocos de notas de exemplo estão incluídos no JupyterHub. |
| Ferramentas relacionadas na DSVM      | Caffe      |
| Como utilizar / executá-lo?  | * No terminal: Ativar a [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) ambiente, execute _python_, em seguida, importar chainer. <br/> * Em JupyterHub: [Conecte-se ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)e, em seguida, navegue até o diretório do encadeamento para localizar os blocos de anotações de exemplo.


## <a name="deep-water"></a>Água profunda

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizagem profunda para H2O      |
| Edições DSVM suportados      | Ubuntu     |
| Como é ele configurado / instalado no DSVM?  | Profunda água está instalada no [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e também está disponível no `/dsvm/tools/deep_water`.   |
| Links para amostras      | Blocos de notas de exemplo estão incluídos no JupyterHub.      |
| Ferramentas relacionadas na DSVM      | H2O, o Sparkling Water      |

### <a name="how-to-use--run-it"></a>Como utilizar / executá-lo?

Água profunda requer CUDA 8 cuDNN 5.1. Isso não está no caminho de biblioteca por padrão, como outras estruturas de aprendizagem profunda utilização CUDA 9 e cuDNN 7. Para utilizar CUDA 8 + cuDNN 5.1 para profunda de água:

```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
export CUDA_ROOT=/usr/local/cuda-8.0
```

Para utilizar profunda de água:
* No terminal: Ativar a [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) ambiente, em seguida, execute _python_. <br/>
* JupyterHub em: [ligue-se ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), em seguida, navegue para o diretório de deep_water para localizar os blocos de notas de exemplo.


## <a name="nvidia-digits"></a>NVIDIA DÍGITOS

|    |           |
| ------------- | ------------- |
| O que é?   | Sistema a partir de NVIDIA aprendizagem para treinamento rapidamente os modelos de aprendizagem aprofundada      |
| Edições DSVM suportados      | Ubuntu     |
| Como é ele configurado / instalado no DSVM?  | DÍGITOS é instalado na `/dsvm/tools/DIGITS` e está disponível um serviço chamado _dígitos_.   |

### <a name="how-to-use--run-it"></a>Como utilizar / executá-lo?

Iniciar sessão na VM com X2Go. No terminal, inicie o serviço:

    sudo systemctl start digits

A iniciar, o serviço demora cerca de um minuto. Inicie um browser e navegue para `http://localhost:5000`. Tenha em atenção que os dígitos não fornece um início de sessão seguro e não deve ser exposta fora da VM.



## <a name="nvidia-smi"></a>NVIDIA smi

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta NVIDIA para consultar a atividade GPU      |
| Edições DSVM suportados      | Windows, Linux     |
| Como é ele configurado / instalado no DSVM?  | _NVIDIA smi_ está disponível no caminho do sistema.   |
| Como utilizar / executá-lo? | Inicie uma linha de comandos (no Windows) ou um terminal (no Linux), em seguida, execute _nvidia smi_.




## <a name="tensorflow-serving"></a>Disponibilização de TensorFlow

|    |           |
| ------------- | ------------- |
| O que é?   | Um servidor para executar inferência num modelo do TensorFlow      |
| Edições DSVM suportados      | Linux     |
| Como é ele configurado / instalado no DSVM?  | _tensorflow_model_server_ está disponível no terminal.   |
| Links para amostras      | Os exemplos estão disponíveis [online](https://www.tensorflow.org/serving/).      |
| Ferramentas relacionadas na DSVM      | TensorFlow      |

## <a name="tensorrt"></a>TensorRT

|    |           |
| ------------- | ------------- |
| O que é?   | Uma aprendizagem profunda server de inferência de tipos da NVIDIA. |
| Edições DSVM suportados      | Ubuntu     |
| Como é ele configurado / instalado no DSVM?  | TensorRT é instalado como uma _apt_ pacote.   |
| Links para amostras      | Os exemplos estão disponíveis [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).      |
| Ferramentas relacionadas na DSVM      | TensorFlow que serve o modelo de MXNet Server  |


## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| O que é?   | Arquitetura de aprendizagem profunda      |
| Edições DSVM suportados      | Windows, Linux     |
| Como é ele configurado / instalado no DSVM?  | Microsoft Cognitive Toolkit (CNTK) está instalado no Python 3.5 [Linux e Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e o Python 3.6 na [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).   |
| Links para amostras      | Blocos de notas do Jupyter exemplo são incluídos.     |
| Ferramentas relacionadas na DSVM      | Keras      |
| Como utilizar / executá-lo?    | * Num terminal: Ativar o ambiente correto, em seguida, execute o Python. <br/> * Em Jupyter: Conecte-se ao [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)e, em seguida, abra o diretório CNTK para obter exemplos. |

