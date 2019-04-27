---
title: Exemplos e orientações passo a passo para dados de máquinas virtuais de ciência - Azure | Documentos da Microsoft
description: Saiba mais sobre os exemplos e orientações passo a passo que lhe explicam como realizar tarefas comuns e cenários com a máquina de Virtual de ciência de dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: e61f0f4ba30b29fea1b2fd5f2a2ab253d3a6710c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60502863"
---
# <a name="samples-on-data-science-virtual-machines"></a>Exemplos de máquinas de virtuais de ciência de dados

Máquinas de virtuais de ciência de dados do Azure inclui um conjunto abrangente de código de exemplo. O código de exemplo está na forma de blocos de notas do Jupyter e scripts em linguagens como o Python e R. 
> [!NOTE]
> Para obter mais informações sobre como executar blocos de notas do Jupyter nas suas máquinas de virtuais de ciência de dados, consulte a [Jupyter acesso](#access-jupyter) secção.

## <a name="quick-reference-of-samples"></a>Referência rápida de amostras
| Categoria de exemplos | Descrição | Localizações |
| ------------- | ------------- | ------------- |
| Linguagem R  | Amostras em R explicam cenários, como se conectar com arquivos de dados de cloud do Azure. Eles também explicam como comparar R open-source e a Microsoft R. E eles explicam como operacionalizar modelos no Microsoft R Server ou SQL Server. <br/> [Linguagem R](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Linguagem Python  | Amostras de Python explicam cenários, como se conectar com arquivos de dados de cloud do Azure e trabalhar com o Azure Machine Learning.  <br/> [Linguagem Python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Linguagem Julia  | Exemplo em Julia que explica em detalhe de desenho e a aprendizagem profunda no Julia. Explica também chamada de C e Python da Julia. <br/> [Linguagem Julia](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Crie modelos de Machine Learning de aprendizagem profunda e de aprendizagem automática. Implemente modelos em qualquer lugar. Utilize a aprendizagem automática e a otimização de hiper-parâmetros inteligente. Também pode utilize gestão de modelos e distribuído de treinamento. <br/> [Machine Learning](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Blocos de notas PyTorch  | Aprendizagem exemplos que utilizam com base em PyTorch redes neurais. Intervalo de blocos de notas de iniciantes a cenários avançados.  <br/> [Blocos de notas PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Exemplos de rede neural diferentes e técnicas implementadas utilizando a estrutura do TensorFlow. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Aprendizagem exemplos publicados pela equipe de ferramentas cognitivas da Microsoft.  <br/> [Conjunto de ferramentas cognitivos](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Aprendizagem exemplos que utilizam com base em caffe2 redes neurais. Vários blocos de notas familiarizar os usuários com caffe2 e como usá-lo com eficiência. Os exemplos incluem a criação de conjunto de dados e processamento prévio de imagens. Eles também incluem regressão e como utilizar modelos com formação prévia. <br/> [caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Exemplos baseados em Python que utilizam H2O para problemas de cenário do mundo real. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Idioma de SparkML  | Exemplos que utilizam recursos do toolkit Spark MLLib por meio de pySpark e MMLSpark – Microsoft Machine Learning para Apache Spark no Apache Spark 2.x.  <br/> [Idioma de SparkML](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Padrão aprendizagem exemplos no XGBoost para cenários como a classificação e regressão. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Jupyter de acesso 

Para acessar o Jupyter, selecione o `Jupyter` ícone no menu de área de trabalho ou aplicação. Também pode acessar Jupyter em edições para Linux de máquinas de virtuais de ciência de dados. Pode aceder remotamente a partir de um navegador da web, visite a página `https://<Full Domain Name or IP Address of the DSVM>:8000` no Ubuntu.

Para adicionar exceções e disponibilizar Jupyter aceder através de um browser, consulte a seguinte captura de ecrã.


![Habilitar a exceção do Jupyter](./media/ubuntu-jupyter-exception.png)


Inicie sessão com a mesma palavra-passe como início de sessão para máquinas de virtuais de ciência de dados.
<br/>

**Página inicial do Jupyter**
<br/>![Página inicial do Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Linguagem R 
<br/>![Exemplos de R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Linguagem Python
<br/>![Amostras de Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Linguagem Julia 
<br/>![Exemplos de Julia](./media/julia-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![Exemplos de AzurekML](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Exemplos de PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Exemplos do TensorFlow](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![Exemplos CNTK](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![Exemplos de caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Exemplos da H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Exemplos de SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Exemplos de XGBoost](./media/xgboost-samples.png)<br/>

