---
title: 'Tutorial do modelo de regressão: ML automatizada'
titleSuffix: Azure Machine Learning service
description: Saiba como gerar um modelo de machine learning com aprendizagem automática. O Azure Machine Learning pode executar o pré-processamento de dados, a seleção de algoritmo e seleção de hiper-parâmetros de maneira automatizada para. Em seguida, o modelo final é implementado com o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: trbye
ms.date: 02/05/2019
ms.custom: seodec18
ms.openlocfilehash: c064874c7eeeae0ae0b1176e3756be24f225e7fb
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818634"
---
# <a name="tutorial-use-automated-machine-learning-to-build-your-regression-model"></a>Tutorial: Utilizar automatizada de machine learning para criar o seu modelo de regressão

Este tutorial é a **segunda parte de uma série composta por duas partes**. No tutorial anterior, [preparado os dados de táxis NYC para modelação de regressão](tutorial-data-prep.md).

Agora está pronto para começar a criar o seu modelo com o serviço Azure Machine Learning. Nesta parte do tutorial, utilize os dados preparados e gerar automaticamente um modelo de regressão para prever preços de Europeia táxis. Através da utilização de funcionalidades do serviço de aprendizagem automatizada, é possível definir o objetivos e as restrições da aprendizagem automática. Inicie a processo de aprendizagem automatizada. Em seguida, permitir a seleção de algoritmo e hiper-parâmetros ajuste para ocorrer para. A técnica de aprendizagem automática itera muitas combinações de algoritmos e hiperparâmetros até encontrar o melhor modelo com base no seu critério.

![Diagrama de fluxo](./media/tutorial-auto-train-models/flow2.png)

Neste tutorial, vai aprender as seguintes tarefas:

> [!div class="checklist"]
> * Configurar um ambiente de Python e importe os pacotes do SDK.
> * Configure uma área de trabalho do serviço do Azure Machine Learning.
> * Autotrain um modelo de regressão.
> * Execute o modelo localmente com parâmetros personalizados.
> * Explore os resultados.

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](http://aka.ms/AMLFree) hoje mesmo.

>[!NOTE]
> Código neste artigo foi testado com versão 1.0.0 do SDK do Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

Avance para o [configurar o ambiente de desenvolvimento](#start) para ler os passos de bloco de notas ou utilize as instruções abaixo para obter o bloco de notas e executá-lo em blocos de notas do Azure ou no seu próprio servidor de bloco de notas. Para executar o bloco de notas, terá de:

* [Executar o tutorial de preparação de dados](tutorial-data-prep.md).
* Um servidor de bloco de notas do Python 3.6 com o seguinte instalado:
    * O Azure Machine Learning SDK para Python com `automl` e `notebooks` extras
    * `matplotlib`
* O bloco de notas do tutorial
* Uma área de trabalho do machine learning
* O ficheiro de configuração para a área de trabalho no mesmo diretório como o bloco de notas

Obter todos os estes pré-requisitos a partir de qualquer uma das seções abaixo.

* Utilize [blocos de notas do Azure](#azure)
* Utilize [seu próprio servidor de bloco de notas](#server)

### <a name="azure"></a>Utilize blocos de notas do Azure: Blocos de notas do Jupyter gratuitos na cloud

É fácil começar com blocos de notas do Azure! O [do Azure Machine Learning SDK para Python](https://aka.ms/aml-sdk) já está instalado e configurado para si no [blocos de notas do Azure](https://notebooks.azure.com/). A instalação e as futuras atualizações são geridas automaticamente por meio de serviços do Azure.

Depois de concluir os passos abaixo, execute o **tutoriais/regressão-parte 2-automatizada-ml.ipynb** bloco de notas no seu **introdução** projeto.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

### <a name="server"></a>Utilizar o seu próprio servidor de bloco de notas do Jupyter

Utilize estes passos para criar um servidor de bloco de notas Jupyter local no seu computador.  Depois de concluir os passos, execute o **tutoriais/regressão-parte 2-automatizada-ml.ipynb** bloco de notas.

1. Concluir o [guia de início rápido do Azure Machine Learning Python](quickstart-create-workspace-with-python.md) para criar um ambiente de Miniconda e criar uma área de trabalho.
1. Instalar o `automl` e `notebooks` extras no seu ambiente utilizando `pip install azureml-sdk[automl,notebooks]`.
1. Instale `maplotlib` usando `pip install maplotlib`.
1. Clone o [repositório do GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Inicie o servidor de blocos de notas a partir do diretório clonado.

    ```shell
    jupyter notebook

## <a name="start"></a>Set up your development environment

All the setup for your development work can be accomplished in a Python notebook. Setup includes the following actions:

* Install the SDK
* Import Python packages
* Configure your workspace

### Install and import packages

If you are following the tutorial in your own Python environment, use the following to install necessary packages.

```shell
pip install azureml-sdk[automl,notebooks] matplotlib
```

Importe os pacotes de Python que tem neste tutorial:

```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
import logging
import os
```

### <a name="configure-workspace"></a>Configurar a área de trabalho

Crie um objeto de área de trabalho a partir da área de trabalho existente. A `Workspace` é uma classe que aceita as suas informações de recursos e subscrição do Azure. Ele também cria um recurso da nuvem para monitorizar e controlar suas execuções de modelo.

`Workspace.from_config()` lê o ficheiro **aml_config/config.json** e carrega os detalhes para um objeto com o nome `ws`.  `ws` é utilizado em todo o restante código neste tutorial.

Depois de ter um objeto de área de trabalho, especifique um nome para a experimentação. Crie e registe um diretório local com a área de trabalho. O histórico de todas as execuções é registrado na experimentação especificada e na [portal do Azure](https://portal.azure.com).


```python
ws = Workspace.from_config()
# choose a name for the run history container in the workspace
experiment_name = 'automated-ml-regression'
# project folder
project_folder = './automated-ml-regression'

output = {}
output['SDK version'] = azureml.core.VERSION
output['Subscription ID'] = ws.subscription_id
output['Workspace'] = ws.name
output['Resource Group'] = ws.resource_group
output['Location'] = ws.location
output['Project Directory'] = project_folder
pd.set_option('display.max_colwidth', -1)
pd.DataFrame(data=output, index=['']).T
```

## <a name="explore-data"></a>Explorar dados

Utilize o objeto de fluxo de dados criado no tutorial anterior. Para resumir, parte 1 deste tutorial limpos os dados de táxis de NYC para que ele poderia ser usado num modelo de machine learning. Agora, vai utilizar várias funcionalidades do conjunto de dados e permitir que um modelo automatizado criar relações entre os recursos e o preço de uma viagem de táxis. Abrir e executar o fluxo de dados e rever os resultados:


```python
import azureml.dataprep as dprep

file_path = os.path.join(os.getcwd(), "dflows.dprep")

package_saved = dprep.Package.open(file_path)
dflow_prepared = package_saved.dataflows[0]
dflow_prepared.get_profile()
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Mín.</th>
      <th>Máx.</th>
      <th>Contagem</th>
      <th>Contagem de em falta</th>
      <th>Não tem em falta contagem</th>
      <th>Percentagem em falta</th>
      <th>Contagem de erros</th>
      <th>Contagem de vazia</th>
      <th>0,1% quantile</th>
      <th>quantile de 1%</th>
      <th>quantile de 5%</th>
      <th>25% quantile</th>
      <th>quantile de 50%</th>
      <th>75% quantile</th>
      <th>95% quantile</th>
      <th>99% quantile</th>
      <th>99,9 quantile de %</th>
      <th>Média</th>
      <th>Desvio padrão</th>
      <th>Variância</th>
      <th>Assimetrias</th>
      <th>Kurtosis</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Fornecedor</th>
      <td>FieldType.STRING</td>
      <td>1</td>
      <td>VTS</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_weekday</th>
      <td>FieldType.STRING</td>
      <td>Sexta-feira</td>
      <td>Quarta-feira</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>2.90047</td>
      <td>2.69355</td>
      <td>9.72889</td>
      <td>16</td>
      <td>19.3713</td>
      <td>22.6974</td>
      <td>23</td>
      <td>23</td>
      <td>14.2731</td>
      <td>6.59242</td>
      <td>43.46</td>
      <td>-0.693723</td>
      <td>-0.570403</td>
    </tr>
    <tr>
      <th>pickup_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>4.99701</td>
      <td>4.95833</td>
      <td>14.1528</td>
      <td>29.3832</td>
      <td>44.6825</td>
      <td>56.4444</td>
      <td>58.9909</td>
      <td>59</td>
      <td>29.427</td>
      <td>17.4333</td>
      <td>303.921</td>
      <td>0.0120999</td>
      <td>-1.20981</td>
    </tr>
    <tr>
      <th>pickup_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.28131</td>
      <td>5</td>
      <td>14.7832</td>
      <td>29.9293</td>
      <td>44.725</td>
      <td>56.7573</td>
      <td>59</td>
      <td>59</td>
      <td>29.7443</td>
      <td>17.3595</td>
      <td>301.351</td>
      <td>-0.0252399</td>
      <td>-1.19616</td>
    </tr>
    <tr>
      <th>dropoff_weekday</th>
      <td>FieldType.STRING</td>
      <td>Sexta-feira</td>
      <td>Quarta-feira</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>dropoff_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>2.57153</td>
      <td>2</td>
      <td>9.58795</td>
      <td>15.9994</td>
      <td>19.6184</td>
      <td>22.8317</td>
      <td>23</td>
      <td>23</td>
      <td>14.2105</td>
      <td>6.71093</td>
      <td>45.0365</td>
      <td>-0.687292</td>
      <td>-0.61951</td>
    </tr>
    <tr>
      <th>dropoff_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.44383</td>
      <td>4.84694</td>
      <td>14.1036</td>
      <td>28.8365</td>
      <td>44.3102</td>
      <td>56.6892</td>
      <td>59</td>
      <td>59</td>
      <td>29.2907</td>
      <td>17.4108</td>
      <td>303.136</td>
      <td>0.0222514</td>
      <td>-1.2181</td>
    </tr>
    <tr>
      <th>dropoff_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.07801</td>
      <td>5</td>
      <td>14.5751</td>
      <td>29.5972</td>
      <td>45.4649</td>
      <td>56.2729</td>
      <td>59</td>
      <td>59</td>
      <td>29.772</td>
      <td>17.5337</td>
      <td>307.429</td>
      <td>-0.0212575</td>
      <td>-1.226</td>
    </tr>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>S</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0781</td>
      <td>-73.7459</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.0578</td>
      <td>-73.9639</td>
      <td>-73.9656</td>
      <td>-73.9508</td>
      <td>-73.9255</td>
      <td>-73.8529</td>
      <td>-73.8302</td>
      <td>-73.8238</td>
      <td>-73.7697</td>
      <td>-73.9123</td>
      <td>0.0503757</td>
      <td>0.00253771</td>
      <td>0.352172</td>
      <td>-0.923743</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5755</td>
      <td>40.8799</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.632</td>
      <td>40.7117</td>
      <td>40.7115</td>
      <td>40.7213</td>
      <td>40.7565</td>
      <td>40.8058</td>
      <td>40.8478</td>
      <td>40.8676</td>
      <td>40.8778</td>
      <td>40.7649</td>
      <td>0.0494674</td>
      <td>0.00244702</td>
      <td>0.205972</td>
      <td>-0.777945</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0857</td>
      <td>-73.7209</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.0775</td>
      <td>-73.9875</td>
      <td>-73.9882</td>
      <td>-73.9638</td>
      <td>-73.935</td>
      <td>-73.8755</td>
      <td>-73.8125</td>
      <td>-73.7759</td>
      <td>-73.7327</td>
      <td>-73.9202</td>
      <td>0.0584627</td>
      <td>0.00341789</td>
      <td>0.623622</td>
      <td>-0.262603</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5835</td>
      <td>40.8797</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.5973</td>
      <td>40.6928</td>
      <td>40.6911</td>
      <td>40.7226</td>
      <td>40.7567</td>
      <td>40.7918</td>
      <td>40.8495</td>
      <td>40.868</td>
      <td>40.8787</td>
      <td>40.7583</td>
      <td>0.0517399</td>
      <td>0.00267701</td>
      <td>0.0390404</td>
      <td>-0.203525</td>
    </tr>
    <tr>
      <th>passageiros</th>
      <td>FieldType.DECIMAL</td>
      <td>1</td>
      <td>6</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>5</td>
      <td>5</td>
      <td>6</td>
      <td>6</td>
      <td>2.39249</td>
      <td>1.83197</td>
      <td>3.3561</td>
      <td>0.763144</td>
      <td>-1.23467</td>
    </tr>
    <tr>
      <th>distância</th>
      <td>FieldType.DECIMAL</td>
      <td>0.01</td>
      <td>32.34</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0108744</td>
      <td>0.743898</td>
      <td>0.738194</td>
      <td>1.243</td>
      <td>2.40168</td>
      <td>4.74478</td>
      <td>10.5136</td>
      <td>14.9011</td>
      <td>21.8035</td>
      <td>3.5447</td>
      <td>3.2943</td>
      <td>10.8524</td>
      <td>1.91556</td>
      <td>4.99898</td>
    </tr>
    <tr>
      <th>custo</th>
      <td>FieldType.DECIMAL</td>
      <td>0.1</td>
      <td>88</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2.33837</td>
      <td>5.00491</td>
      <td>5</td>
      <td>6.93129</td>
      <td>10.524</td>
      <td>17.4811</td>
      <td>33.2343</td>
      <td>50.0093</td>
      <td>63.1753</td>
      <td>13.6843</td>
      <td>9.66571</td>
      <td>93.426</td>
      <td>1.78518</td>
      <td>4.13972</td>
    </tr>
  </tbody>
</table>

Preparar os dados para a experimentação, adicionando colunas a `dflow_x` ser funcionalidades para a criação de nosso modelo. Define `dflow_y` nosso valor de predição, **custo**:

```python
dflow_X = dflow_prepared.keep_columns(['pickup_weekday','pickup_hour', 'distance','passengers', 'vendor'])
dflow_y = dflow_prepared.keep_columns('cost')
```

### <a name="split-the-data-into-train-and-test-sets"></a>Dividir os dados em train e conjuntos de teste

Agora, dividir os dados em treinamento e teste conjuntos com o `train_test_split` funcionar o `sklearn` biblioteca. Esta função segrega os dados em x, **funcionalidades**, conjunto de dados para preparação de modelos e o y **valores para prever**, conjunto de dados para fins de teste. O `test_size` parâmetro determina a percentagem de dados para alocar ao teste. O `random_state` parâmetro define um seed aleatório no gerador, para que as divisões de treinar-testar sempre são determinísticos:

```python
from sklearn.model_selection import train_test_split

x_df = dflow_X.to_pandas_dataframe()
y_df = dflow_y.to_pandas_dataframe()

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
# flatten y_train to 1d array
y_train.values.flatten()
```

O objetivo deste passo é ter pontos de dados para testar o modelo concluído, que não foram usados para preparar o modelo, para poder medir precisão verdadeiro. Em outras palavras, um modelo bem preparado deve conseguir com precisão fazer predições dos dados que não tenha já viu. Tem agora os pacotes necessários e dados prontos para autotraining seu modelo.

## <a name="automatically-train-a-model"></a>Preparar automaticamente um modelo

Para preparar automaticamente um modelo, siga os passos seguintes:
1. Configurar as definições para a experimentação executar. Anexar os seus dados de preparação para a configuração e modificar as definições que controlam o processo de treinamento.
1. Submeta a experimentação para o ajuste do modelo. Depois de submeter a experimentação, o processo faz a iteração por meio de diferente de machine learning algoritmos e as definições de hiper-parâmetros, respeitando as restrições definidas pelo. Ele escolhe o modelo mais adequado ao otimizar uma métrica de precisão.

### <a name="define-settings-for-autogeneration-and-tuning"></a>As definições para a geração automática e Otimização

Defina o parâmetro de experimentação e as definições para a geração automática e a otimização de modelo. Ver a lista completa dos [definições](how-to-configure-auto-train.md). A submeter a experimentação com estas predefinições irá demorar cerca de 10 a 15 minutos, mas se pretender que um menor tempo de execução, reduzir `iterations` ou `iteration_timeout_minutes`.


|Propriedade| Valor neste tutorial |Descrição|
|----|----|---|
|**iteration_timeout_minutes**|10|Limite de tempo em minutos para cada iteração. Reduza este valor para diminuir o tempo de execução total.|
|**iterations**|30|Número de iterações. Em cada iteração, um novo modelo de aprendizagem automática é preparado com os seus dados. Este é o valor principal que afeta o tempo de execução total.|
|**primary_metric**| spearman_correlation | Métrica que pretende otimizar. O modelo mais adequado será escolhido com base nesta métrica.|
|**preprocess**| Verdadeiro | Usando **True**, a experimentação pode pré-processar os dados de entrada (processamento de dados em falta, converter o texto em numérico, etc.)|
|**Verbosidade**| logging.INFO | Controla o nível de registo.|
|**n_cross_validations**|5|Número de divisões de validação cruzada a efetuar quando os dados de validação não for especificados.|



```python
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 30,
    "primary_metric" : 'spearman_correlation',
    "preprocess" : True,
    "verbosity" : logging.INFO,
    "n_cross_validations": 5
}
```

Utilizar as definições de formação definidos como um parâmetro para um `AutoMLConfig` objeto. Além disso, especificar os dados de treinamento e o tipo de modelo, o que é `regression` neste caso.

```python
from azureml.train.automl import AutoMLConfig

# local compute
automated_ml_config = AutoMLConfig(task = 'regression',
                             debug_log = 'automated_ml_errors.log',
                             path = project_folder,
                             X = x_train.values,
                             y = y_train.values.flatten(),
                             **automl_settings)
```

### <a name="train-the-automatic-regression-model"></a>Preparar o modelo de regressão automática

Inicie a experimentação para ser executada localmente. Passar o definido `automated_ml_config` objeto para a experimentação. Definir a saída `True` para ver o progresso durante a experimentação:


```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, experiment_name)
local_run = experiment.submit(automated_ml_config, show_output=True)
```

A saída mostrada atualizações em direto como a experimentação é executada. Para cada iteração, verá o tipo de modelo, a duração de execução e a precisão de treinamento. O campo `BEST` controla o melhor com score de treinamento com base em seu tipo de métrica.

    Parent Run ID: AutoML_02778de3-3696-46e9-a71b-521c8fca0651
    *******************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    *******************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler ExtremeRandomTrees                0:00:08       0.9447    0.9447
             1   StandardScalerWrapper GradientBoosting         0:00:09       0.9536    0.9536
             2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.8580    0.9536
             3   StandardScalerWrapper RandomForest             0:00:08       0.9147    0.9536
             4   StandardScalerWrapper ExtremeRandomTrees       0:00:45       0.9398    0.9536
             5   MaxAbsScaler LightGBM                          0:00:08       0.9562    0.9562
             6   StandardScalerWrapper ExtremeRandomTrees       0:00:27       0.8282    0.9562
             7   StandardScalerWrapper LightGBM                 0:00:07       0.9421    0.9562
             8   MaxAbsScaler DecisionTree                      0:00:08       0.9526    0.9562
             9   MaxAbsScaler RandomForest                      0:00:09       0.9355    0.9562
            10   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            11   MaxAbsScaler LightGBM                          0:00:09       0.9553    0.9602
            12   MaxAbsScaler DecisionTree                      0:00:07       0.9484    0.9602
            13   MaxAbsScaler LightGBM                          0:00:08       0.9540    0.9602
            14   MaxAbsScaler RandomForest                      0:00:10       0.9365    0.9602
            15   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            16   StandardScalerWrapper ExtremeRandomTrees       0:00:49       0.9171    0.9602
            17   SparseNormalizer LightGBM                      0:00:08       0.9191    0.9602
            18   MaxAbsScaler DecisionTree                      0:00:08       0.9402    0.9602
            19   StandardScalerWrapper ElasticNet               0:00:08       0.9603    0.9603
            20   MaxAbsScaler DecisionTree                      0:00:08       0.9513    0.9603
            21   MaxAbsScaler SGD                               0:00:08       0.9603    0.9603
            22   MaxAbsScaler SGD                               0:00:10       0.9602    0.9603
            23   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            24   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            25   MaxAbsScaler SGD                               0:00:09       0.9603    0.9603
            26   TruncatedSVDWrapper ElasticNet                 0:00:09       0.9602    0.9603
            27   MaxAbsScaler SGD                               0:00:12       0.9413    0.9603
            28   StandardScalerWrapper ElasticNet               0:00:07       0.9603    0.9603
            29    Ensemble                                      0:00:38       0.9622    0.9622

## <a name="explore-the-results"></a>Explorar os resultados

Explore os resultados de treinamento automática com um widget de Jupyter ou, examinando o histórico de experimentação.

### <a name="option-1-add-a-jupyter-widget-to-see-results"></a>Opção 1: Adicionar um widget do Jupyter para ver os resultados

Se usar um bloco de notas do Jupyter, utilize este widget de bloco de notas do Jupyter para ver um gráfico e uma tabela de todos os resultados:


```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Detalhes da execução de widget de Jupyter](./media/tutorial-auto-train-models/automl-dash-output.png)
![plotagem de widget do Jupyter](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="option-2-get-and-examine-all-run-iterations-in-python"></a>Opção 2: Obter e examinar todas as iterações de execução em Python

Também pode obter o histórico de cada experimentação e explore as métricas individuais para cada execução de iteração. Ao examinar RMSE (root_mean_squared_error) para cada modelo individual ser executado, verá que a maioria das iterações são prever o custo razoável de táxis dentro de uma margem razoável (US $3 a 4).

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items() if isinstance(v, float)}
    metricslist[int(properties['iteration'])] = metrics

rundata = pd.DataFrame(metricslist).sort_index(1)
rundata
```

<div>
<style scoped> .dataframe tbody tr th: só de-de-type {vertical-align: intermédia;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>20</th>
      <th>21</th>
      <th>22</th>
      <th>23</th>
      <th>24</th>
      <th>25</th>
      <th>26</th>
      <th>27</th>
      <th>28</th>
      <th>29</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>explained_variance</th>
      <td>0.811037</td>
      <td>0.880553</td>
      <td>0.398582</td>
      <td>0.776040</td>
      <td>0.663869</td>
      <td>0.875911</td>
      <td>0.115632</td>
      <td>0.586905</td>
      <td>0.851911</td>
      <td>0.793964</td>
      <td>...</td>
      <td>0.850023</td>
      <td>0.883603</td>
      <td>0.883704</td>
      <td>0.880797</td>
      <td>0.881564</td>
      <td>0.883708</td>
      <td>0.881826</td>
      <td>0.585377</td>
      <td>0.883123</td>
      <td>0.886817</td>
    </tr>
    <tr>
      <th>mean_absolute_error</th>
      <td>2.189444</td>
      <td>1.500412</td>
      <td>5.480531</td>
      <td>2.626316</td>
      <td>2.973026</td>
      <td>1.550199</td>
      <td>6.383868</td>
      <td>4.414241</td>
      <td>1.743328</td>
      <td>2.294601</td>
      <td>...</td>
      <td>1.797402</td>
      <td>1.415815</td>
      <td>1.418167</td>
      <td>1.578617</td>
      <td>1.559427</td>
      <td>1.413042</td>
      <td>1.551698</td>
      <td>4.069196</td>
      <td>1.505795</td>
      <td>1.430957</td>
    </tr>
    <tr>
      <th>median_absolute_error</th>
      <td>1.438417</td>
      <td>0.850899</td>
      <td>4.579662</td>
      <td>1.765210</td>
      <td>1.594600</td>
      <td>0.869883</td>
      <td>4.266450</td>
      <td>3.627355</td>
      <td>0.954992</td>
      <td>1.361014</td>
      <td>...</td>
      <td>0.973634</td>
      <td>0.774814</td>
      <td>0.797269</td>
      <td>1.147234</td>
      <td>1.116424</td>
      <td>0.783958</td>
      <td>1.098464</td>
      <td>2.709027</td>
      <td>1.003728</td>
      <td>0.851724</td>
    </tr>
    <tr>
      <th>normalized_mean_absolute_error</th>
      <td>0.024908</td>
      <td>0.017070</td>
      <td>0.062350</td>
      <td>0.029878</td>
      <td>0.033823</td>
      <td>0.017636</td>
      <td>0.072626</td>
      <td>0.050219</td>
      <td>0.019833</td>
      <td>0.026105</td>
      <td>...</td>
      <td>0.020448</td>
      <td>0.016107</td>
      <td>0.016134</td>
      <td>0.017959</td>
      <td>0.017741</td>
      <td>0.016076</td>
      <td>0.017653</td>
      <td>0.046293</td>
      <td>0.017131</td>
      <td>0.016279</td>
    </tr>
    <tr>
      <th>normalized_median_absolute_error</th>
      <td>0.016364</td>
      <td>0.009680</td>
      <td>0.052101</td>
      <td>0.020082</td>
      <td>0.018141</td>
      <td>0.009896</td>
      <td>0.048538</td>
      <td>0.041267</td>
      <td>0.010865</td>
      <td>0.015484</td>
      <td>...</td>
      <td>0.011077</td>
      <td>0.008815</td>
      <td>0.009070</td>
      <td>0.013052</td>
      <td>0.012701</td>
      <td>0.008919</td>
      <td>0.012497</td>
      <td>0.030819</td>
      <td>0.011419</td>
      <td>0.009690</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_error</th>
      <td>0.047968</td>
      <td>0.037882</td>
      <td>0.085572</td>
      <td>0.052282</td>
      <td>0.065809</td>
      <td>0.038664</td>
      <td>0.109401</td>
      <td>0.071104</td>
      <td>0.042294</td>
      <td>0.049967</td>
      <td>...</td>
      <td>0.042565</td>
      <td>0.037685</td>
      <td>0.037557</td>
      <td>0.037643</td>
      <td>0.037513</td>
      <td>0.037560</td>
      <td>0.037465</td>
      <td>0.072077</td>
      <td>0.037249</td>
      <td>0.036716</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_log_error</th>
      <td>0.055353</td>
      <td>0.045000</td>
      <td>0.110219</td>
      <td>0.065633</td>
      <td>0.063589</td>
      <td>0.044412</td>
      <td>0.123433</td>
      <td>0.092312</td>
      <td>0.046130</td>
      <td>0.055243</td>
      <td>...</td>
      <td>0.046540</td>
      <td>0.041804</td>
      <td>0.041771</td>
      <td>0.045175</td>
      <td>0.044628</td>
      <td>0.041617</td>
      <td>0.044405</td>
      <td>0.079651</td>
      <td>0.042799</td>
      <td>0.041530</td>
    </tr>
    <tr>
      <th>r2_score</th>
      <td>0.810900</td>
      <td>0.880328</td>
      <td>0.398076</td>
      <td>0.775957</td>
      <td>0.642812</td>
      <td>0.875719</td>
      <td>0.021603</td>
      <td>0.586514</td>
      <td>0.851767</td>
      <td>0.793671</td>
      <td>...</td>
      <td>0.849809</td>
      <td>0.880142</td>
      <td>0.880952</td>
      <td>0.880586</td>
      <td>0.881347</td>
      <td>0.880887</td>
      <td>0.881613</td>
      <td>0.548121</td>
      <td>0.882883</td>
      <td>0.886321</td>
    </tr>
    <tr>
      <th>root_mean_squared_error</th>
      <td>4.216362</td>
      <td>3.329810</td>
      <td>7.521765</td>
      <td>4.595604</td>
      <td>5.784601</td>
      <td>3.398540</td>
      <td>9.616354</td>
      <td>6.250011</td>
      <td>3.717661</td>
      <td>4.392072</td>
      <td>...</td>
      <td>3.741447</td>
      <td>3.312533</td>
      <td>3.301242</td>
      <td>3.308795</td>
      <td>3.297389</td>
      <td>3.301485</td>
      <td>3.293182</td>
      <td>6.335581</td>
      <td>3.274209</td>
      <td>3.227365</td>
    </tr>
    <tr>
      <th>root_mean_squared_log_error</th>
      <td>0.243184</td>
      <td>0.197702</td>
      <td>0.484227</td>
      <td>0.288349</td>
      <td>0.279367</td>
      <td>0.195116</td>
      <td>0.542281</td>
      <td>0.405559</td>
      <td>0.202666</td>
      <td>0.242702</td>
      <td>...</td>
      <td>0.204464</td>
      <td>0.183658</td>
      <td>0.183514</td>
      <td>0.198468</td>
      <td>0.196067</td>
      <td>0.182836</td>
      <td>0.195087</td>
      <td>0.349935</td>
      <td>0.188031</td>
      <td>0.182455</td>
    </tr>
    <tr>
      <th>spearman_correlation</th>
      <td>0.944743</td>
      <td>0.953618</td>
      <td>0.857965</td>
      <td>0.914703</td>
      <td>0.939846</td>
      <td>0.956159</td>
      <td>0.828187</td>
      <td>0.942069</td>
      <td>0.952581</td>
      <td>0.935477</td>
      <td>...</td>
      <td>0.951287</td>
      <td>0.960335</td>
      <td>0.960195</td>
      <td>0.960279</td>
      <td>0.960288</td>
      <td>0.960323</td>
      <td>0.960161</td>
      <td>0.941254</td>
      <td>0.960293</td>
      <td>0.962158</td>
    </tr>
    <tr>
      <th>spearman_correlation_max</th>
      <td>0.944743</td>
      <td>0.953618</td>
      <td>0.953618</td>
      <td>0.953618</td>
      <td>0.953618</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>...</td>
      <td>0.960303</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.962158</td>
    </tr>
  </tbody>
</table>
<p>linhas de 12 colunas de 30 ×</p>
</div>

## <a name="retrieve-the-best-model"></a>Obter o melhor modelo

Selecione o pipeline melhor nossas iterações. O `get_output` método no `automl_classifier` retorna a melhor execução e o modelo de ajustada para a invocação de acordo com a última. Com as sobrecargas no `get_output`, pode obter a melhor execução e modelo ajustado para qualquer sessão iniciada, métrica ou uma iteração específica:

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-the-best-model-accuracy"></a>A maior precisão do modelo de teste

Utilize o melhor modelo para executar previsões no conjunto de dados de teste para prever fares táxis. A função `predict` utiliza o melhor modelo e prevê os valores de y, **enganar custo**, da `x_test` conjunto de dados. Imprimir os primeiros 10 prevista valores a partir de custos `y_predict`:

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Crie um gráfico de dispersão para visualizar os valores de custo previsto em comparação comparados os valores de custo real. O seguinte código utiliza a `distance` funcionalidade como o eixo x e viagem `cost` como o eixo y. Para comparar a variância de custo previsto em cada valor de distância de viagem, as primeiras 100 previstos e valores de custo real são criados como série separada. Examinar o desenho mostra que a relação de custo/distância é quase linear e os valores de custo previsto são na maioria dos casos muito próximo os valores de custo real para a mesma distância de viagem.

```python
import matplotlib.pyplot as plt

fig = plt.figure(figsize=(14, 10))
ax1 = fig.add_subplot(111)

distance_vals = [x[4] for x in x_test.values]
y_actual = y_test.values.flatten().tolist()

ax1.scatter(distance_vals[:100], y_predict[:100], s=18, c='b', marker="s", label='Predicted')
ax1.scatter(distance_vals[:100], y_actual[:100], s=18, c='r', marker="o", label='Actual')

ax1.set_xlabel('distance (mi)')
ax1.set_title('Predicted and Actual Cost/Distance')
ax1.set_ylabel('Cost ($)')

plt.legend(loc='upper left', prop={'size': 12})
plt.rcParams.update({'font.size': 14})
plt.show()
```

![Gráfico de dispersão de predição](./media/tutorial-auto-train-models/automl-scatter-plot.png)

Calcular o `root mean squared error` dos resultados. Utilize o `y_test` dataframe. Convertê-la a uma lista a comparar com os valores previstos. A função `mean_squared_error` usa duas matrizes de valores e calcula o média erro ao quadrado entre eles. Levando a raiz quadrada do resultado dá um erro na mesma unidade como a variável y, **custo**. Ele indica que são as previsões de Europeia táxis aproximadamente a distância das fares reais:

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

    3.2204936862688798

Execute o seguinte código para calcular erro percentagem absoluto mean (MAPE) utilizando o completo `y_actual` e `y_predict` conjuntos de dados. Esta métrica calcula uma diferença absoluta entre cada valor previsto e real e somas todas as diferenças. Em seguida, expressa que a soma como percentagem do total dos valores reais:

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.10545153869569586

    Model Accuracy:
    0.8945484613043041

Entre as métricas de precisão predições finais, verá que o modelo é razoavelmente bom em prever fares de táxis de recursos do conjunto de dados, normalmente dentro + - us $3,00. A processo de desenvolvimento do modelo de aprendizagem tradicional é altamente com muitos recursos e requer investimento de dados de conhecimento e a hora de domínio significativo para executar e comparar os resultados de dezenas de modelos. Utilizar a aprendizagem automática é uma excelente forma de rapidamente testar muitos modelos diferentes para o seu cenário.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

Nessa automático tutorial do machine learning, fez as seguintes tarefas:

> [!div class="checklist"]
> * Configurar uma área de trabalho e dados preparados para uma experimentação.
> * Formação utilizando um modelo de regressão automatizada localmente com parâmetros personalizados.
> * Resultados de treinamento explorada e revistos.

[Implementar o seu modelo](tutorial-deploy-models-with-aml.md) com o Azure Machine Learning.
