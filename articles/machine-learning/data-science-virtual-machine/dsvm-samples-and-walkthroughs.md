---
title: Programas de amostra& walkthroughs ml
titleSuffix: Azure Data Science Virtual Machine
description: Através destas amostras e walkthroughs, aprenda a lidar com tarefas e cenários comuns com a Máquina Virtual da Ciência dos Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d064e940997d13e51be4fd38e9f678b778ce31f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74900054"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Amostras em Máquinas Virtuais de Ciência de Dados Azure

As Máquinas Virtuais da Ciência dos Dados Azure (DSVMs) incluem um conjunto abrangente de código de amostra. Estas amostras incluem cadernos jupyter e scripts em línguas como Python e R.
> [!NOTE]
> Para obter mais informações sobre como executar os cadernos Jupyter nas suas máquinas virtuais de ciência de dados, consulte a secção [Access Jupyter.](#access-jupyter)

## <a name="prerequisites"></a>Pré-requisitos

Para executar estas amostras, deve ter aprovisionado uma Máquina Virtual de Ciência de Dados. Veja as quickstarts para [Windows](./provision-vm.md) e [Ubuntu](./dsvm-ubuntu-intro.md).

## <a name="available-samples"></a>Exemplos disponíveis
| Categoria de amostras | Descrição | Localizações |
| ------------- | ------------- | ------------- |
| Linguagem R  | As amostras ilustram cenários como a ligação com as lojas de dados em nuvem baseadas no Azure e como comparar o R de código aberto e o Microsoft Machine Learning Server. Também explicam como operacionalizar modelos no Microsoft Machine Learning Server e no SQL Server. <br/> [Linguagem R](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Linguagem Python  | As amostras explicam cenários como como conectar-se com lojas de dados em nuvem baseadas em Azure e como trabalhar com o Azure Machine Learning.  <br/> [Linguagem Python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Língua júlia  | Fornece uma descrição detalhada da conspiração e da aprendizagem profunda em Julia. Também explica como ligar para C e Python de Julia. <br/> [Língua júlia](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Ilustra como construir modelos de aprendizagem automática e de aprendizagem profunda com machine learning. Implementar modelos em qualquer lugar. Utilize aprendizagem automática de máquinas e afinação inteligente do hiperparâmetro. Utilize também a gestão de modelos e a formação distribuída. <br/> [Aprendizagem automática](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Cadernos PyTorch  | Amostras de aprendizagem profunda que usam redes neurais baseadas em PyTorch. Os cadernos vão desde cenários de principiante a cenários avançados.  <br/> [Cadernos PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Uma variedade de amostras e técnicas de rede neural implementadas utilizando a estrutura TensorFlow. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Amostras de aprendizagem profunda publicadas pela equipa cognitive Toolkit da Microsoft.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Amostras de aprendizagem profunda que usam redes neurais baseadas em Caffe2. Vários cadernos familiarizam os utilizadores com o Caffe2 e como usá-lo de forma eficaz. Exemplos incluem pré-processamento de imagem e criação de conjunto de dados. Incluem também a regressão e a utilização de modelos pré-treinados. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Amostras baseadas em Python que usam H2O para cenários de problemas no mundo real. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Língua SparkML  | Amostras que utilizam funcionalidades do kit de ferramentas Apache Spark MLLib através do pySpark e do MMLSpark: Microsoft Machine Learning for Apache Spark em Apache Spark 2.x.  <br/> [Língua SparkML](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Amostras padrão de aprendizagem automática em XGBoost para cenários como classificação e regressão. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Jupyter de acesso 

Para aceder ao Jupyter, selecione o ícone **Jupyter** no menu de desktop ou aplicação. Você também pode aceder a Jupyter em uma edição Linux de um DSVM. Para aceder remotamente a partir `https://<Full Domain Name or IP Address of the DSVM>:8000` de um navegador web, vá a Ubuntu.

Para adicionar exceções e disponibilizar o acesso à Jupyter através de um browser, utilize as seguintes orientações:


![Permitir a exceção de Jupyter](./media/ubuntu-jupyter-exception.png)


Inicie sessão com a mesma palavra-passe que utiliza para iniciar sessão na Máquina Virtual da Ciência dos Dados.
<br/>

**Jupyter casa**
<br/>![Jupyter casa](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Linguagem R 
<br/>![Amostras R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Linguagem Python
<br/>![Amostras de Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Língua júlia 
<br/>![Amostras de julia](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Amostras de aprendizagem automática azure](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Amostras de PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Amostras TensorFlow](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![Amostras CNTK](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![amostras de caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Amostras de H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Amostras SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Amostras XGBoost](./media/xgboost-samples.png)<br/>

