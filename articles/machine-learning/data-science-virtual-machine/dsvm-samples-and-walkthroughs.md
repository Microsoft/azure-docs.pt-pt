---
title: Programas de amostra & adiscrição de ML
titleSuffix: Azure Data Science Virtual Machine
description: Através destas amostras e de passagem, aprenda a lidar com tarefas e cenários comuns com a Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 98a1cd883867aa2f785b15f4c7ae4f2266a421ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87012574"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Amostras em Azure Data Science Virtual Machines

As máquinas virtuais de ciência de dados Azure (DSVMs) incluem um conjunto abrangente de código de amostra. Estas amostras incluem cadernos jupyter e scripts em línguas como Python e R.
> [!NOTE]
> Para obter mais informações sobre como executar os cadernos jupyter nas suas máquinas virtuais de ciência de dados, consulte a secção [Access Jupyter.](#access-jupyter)

## <a name="prerequisites"></a>Pré-requisitos

Para executar estas amostras, deve ter adhido uma [Máquina Virtual Ubuntu Data Science.](./dsvm-ubuntu-intro.md)

## <a name="available-samples"></a>Exemplos disponíveis
| Categoria amostras | Descrição | Localizações |
| ------------- | ------------- | ------------- |
| Linguagem R  | As amostras ilustram cenários como como conectar-se com lojas de dados em nuvem baseadas em Azure e como comparar o Open-Source R e o Microsoft Machine Learning Server. Também explicam como operacionalizar modelos no Microsoft Machine Learning Server e no SQL Server. <br/> [Linguagem R](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Linguagem Python  | As amostras explicam cenários como como conectar-se com lojas de dados em nuvem baseadas em Azure e como trabalhar com a Azure Machine Learning.  <br/> [Linguagem Python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Língua julia  | Fornece uma descrição detalhada de conspiração e aprendizagem profunda em Julia. Também explica como ligar para C e Python de Julia. <br/> [Língua julia](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Ilustra como construir modelos de aprendizagem automática e deep-learning com Machine Learning. Implemente modelos em qualquer lugar. Utilize aprendizagem automática de máquinas e afinação de hiperparímetro inteligente. Utilize também a gestão de modelos e formação distribuída. <br/> [Aprendizagem automática](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Cadernos PyTorch  | Amostras de aprendizagem profunda que usam redes neuronais baseadas em PyTorch. Os cadernos vão desde principiantes a cenários avançados.  <br/> [Cadernos PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Uma variedade de amostras e técnicas de rede neural implementadas utilizando a estrutura TensorFlow. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Amostras de aprendizagem profunda publicadas pela equipa do Cognitive Toolkit na Microsoft.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Amostras de aprendizagem profunda que usam redes neurais baseadas em Caffe2. Vários cadernos familiarizam os utilizadores com o Caffe2 e como usá-lo de forma eficaz. Exemplos incluem pré-processamento de imagem e criação de conjunto de dados. Também incluem regressão e como usar modelos pré-treinados. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Amostras baseadas em python que usam H2O para cenários de problemas no mundo real. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Língua SparkML  | Amostras que utilizam funcionalidades do conjunto de ferramentas Apache Spark MLLib através do pySpark e MMLSpark: Microsoft Machine Learning for Apache Spark on Apache Spark 2.x.  <br/> [Língua SparkML](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Amostras padrão de aprendizagem automática em XGBoost para cenários como classificação e regressão. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Acesso Jupyter 

Para aceder ao Jupyter, selecione o ícone **Jupyter** no ambiente de trabalho ou no menu de aplicações. Você também pode acessar Jupyter em uma edição Linux de um DSVM. Para aceder remotamente a partir de um navegador web, vá a `https://<Full Domain Name or IP Address of the DSVM>:8000` Ubuntu.

Para adicionar exceções e disponibilizar o acesso ao Jupyter sobre um browser, utilize as seguintes orientações:


![Permitir a exceção de Jupyter](./media/ubuntu-jupyter-exception.png)


Inicie sessão com a mesma palavra-passe que utiliza para iniciar sessão na Máquina Virtual da Ciência dos Dados.
<br/>

**Jupyter casa**
<br/>![Jupyter casa](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Linguagem R 
<br/>![Amostras R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Linguagem Python
<br/>![Amostras de Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Língua julia 
<br/>![Amostras de Julia](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Amostras de aprendizagem automática Azure](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Amostras de PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Amostras de TensorFlow](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![Amostras de CNTK](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![amostras de caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Amostras de H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Amostras de SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Amostras de XGBoost](./media/xgboost-samples.png)<br/>

