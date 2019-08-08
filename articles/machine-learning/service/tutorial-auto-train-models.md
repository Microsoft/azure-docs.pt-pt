---
title: 'Tutorial do modelo de regressão: ML Automatizado'
titleSuffix: Azure Machine Learning service
description: Saiba como gerar um modelo de aprendizado de máquina usando o aprendizado de máquina automatizado. Azure Machine Learning pode executar o pré-processamento de dados, a seleção de algoritmos e a seleção de hiperparâmetro de forma automatizada para você. Em seguida, o modelo final é implantado com Azure Machine Learning serviço.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: trbye
ms.date: 04/11/2019
ms.custom: seodec18
ms.openlocfilehash: 70a95cdba2a8b41c7b2fc3ee4b2664f049a84e95
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846005"
---
# <a name="tutorial-use-automated-machine-learning-to-build-your-regression-model"></a>Tutorial: Use o Machine Learning automatizado para criar seu modelo de regressão

Este tutorial é a **segunda parte de uma série composta por duas partes**. No tutorial anterior, [preparado os dados de táxis NYC para modelação de regressão](tutorial-data-prep.md).

Agora você está pronto para começar a criar seu modelo com o serviço Azure Machine Learning. Nesta parte do tutorial, você usa os dados preparados e gera automaticamente um modelo de regressão para prever preços de Tarifa de táxi. Usando os recursos de Machine Learning automatizados do serviço, você define suas metas e restrições de aprendizado de máquina. Você inicia o processo de aprendizado de máquina automatizado. Em seguida, permita que a seleção de algoritmo e o ajuste de hiperparâmetro aconteça para você. A técnica de Machine Learning automatizada itera em várias combinações de algoritmos e hiperparâmetros até encontrar o melhor modelo com base no seu critério.

![Diagrama de fluxo](./media/tutorial-auto-train-models/flow2.png)

Neste tutorial, você aprende as seguintes tarefas:

> [!div class="checklist"]
> * Configure um ambiente do Python e importe os pacotes do SDK.
> * Configurar um espaço de trabalho de serviço do Azure Machine Learning.
> * Treine o autotreinamento de um modelo de regressão.
> * Execute o modelo localmente com parâmetros personalizados.
> * Explore os resultados.

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do serviço de Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

>[!NOTE]
> O código deste artigo foi testado com Azure Machine Learning SDK versão 1.0.39.

## <a name="prerequisites"></a>Pré-requisitos

Pule para [configurar seu ambiente de desenvolvimento](#start) para ler as etapas do bloco de anotações ou use as instruções abaixo para obter o bloco de anotações e executá-lo em Azure notebooks ou em seu próprio servidor de notebook. Para executar o bloco de anotações, você precisará de:

* [Executar o tutorial de preparação de dados](tutorial-data-prep.md).
* Um servidor de notebook Python 3,6 com o seguinte instalado:
    * O SDK do Azure Machine Learning para Python `automl` com `notebooks` e extras
    * `matplotlib`
* O bloco de notas do tutorial
* Um espaço de trabalho do Machine Learning
* O arquivo de configuração do espaço de trabalho no mesmo diretório que o bloco de anotações

Obtenha todos esses pré-requisitos de qualquer uma das seções a seguir.

* Usar um [servidor de bloco de anotações de nuvem em seu espaço de trabalho](#azure)
* Use [seu próprio servidor de notebook](#server)

### <a name="azure"></a>Usar um servidor de bloco de anotações de nuvem em seu espaço de trabalho

É fácil começar com seu próprio servidor de notebook baseado em nuvem. O [SDK do Azure Machine Learning para Python](https://aka.ms/aml-sdk) já está instalado e configurado para você depois de criar esse recurso de nuvem.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Depois de iniciar a página da Web do bloco de anotações, execute o bloco de anotações **tutoriais/regression-part2-Automated-ml. ipynb** .

### <a name="server"></a>Use seu próprio servidor do Jupyter Notebook

Utilize estes passos para criar um servidor de bloco de notas Jupyter local no seu computador.  Certifique-se de instalar `matplotlib` o e `automl` o `notebooks` e os extras em seu ambiente.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Depois de concluir as etapas, execute o bloco de anotações **tutoriais/regression-part2-Automated-ml. ipynb** .

## <a name="start"></a>Configurar seu ambiente de desenvolvimento

Toda a configuração para o seu trabalho de desenvolvimento pode ser feita num bloco de notas Python. A instalação inclui as seguintes ações:

* Instalar o SDK
* Importação de pacotes Python
* Configurar seu espaço de trabalho

### <a name="install-and-import-packages"></a>Instalar e importar pacotes

Se você estiver seguindo o tutorial em seu próprio ambiente do Python, use o seguinte para instalar os pacotes necessários.

```shell
pip install azureml-sdk[automl,notebooks] matplotlib
```

Importe os pacotes do Python que você precisa neste tutorial:

```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
import logging
import os
```

### <a name="configure-workspace"></a>Configurar a área de trabalho

Crie um objeto de área de trabalho a partir da área de trabalho existente. Um [espaço de trabalho](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) é uma classe que aceita suas informações de assinatura e recursos do Azure. Ele também cria um recurso de nuvem para monitorar e rastrear suas execuções de modelo.

`Workspace.from_config()` lê o ficheiro **config.json** e carrega os detalhes para um objeto com o nome `ws`.  `ws` é utilizado em todo o restante código neste tutorial.

Depois de ter um objeto de espaço de trabalho, especifique um nome para o experimento. Crie e registre um diretório local com o espaço de trabalho. O histórico de todas as execuções é registrado no experimento especificado e no [portal do Azure](https://portal.azure.com).


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

Use o objeto de fluxo de dados criado no tutorial anterior. Para resumir, a parte 1 deste tutorial limpou os dados de táxi de NYC para que pudesse ser usado em um modelo de aprendizado de máquina. Agora, você usa vários recursos do conjunto de dados e permite que um modelo automatizado crie relações entre os recursos e o preço de uma viagem de táxi. Abra e execute o fluxo de dados e examine os resultados:


```python
import azureml.dataprep as dprep

file_path = os.path.join(os.getcwd(), "dflows.dprep")

dflow_prepared = dprep.Dataflow.open(file_path)
dflow_prepared.get_profile()
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Mín.</th>
      <th>Máx.</th>
      <th>Count</th>
      <th>Contagem ausente</th>
      <th>Contagem não ausente</th>
      <th>Percentagem em falta</th>
      <th>Contagem de erros</th>
      <th>Contagem de vazia</th>
      <th>0,1% Quantil</th>
      <th>1% Quantil</th>
      <th>5% Quantil</th>
      <th>25% Quantil</th>
      <th>50% Quantil</th>
      <th>75% Quantil</th>
      <th>95% Quantil</th>
      <th>99% Quantil</th>
      <th>99,9% Quantil</th>
      <th>média</th>
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

Preparar os dados para a experimentação, adicionando colunas a `dflow_x` ser funcionalidades para a criação de nosso modelo. Você define `dflow_y` para ser nosso valor de previsão, **custo**:

```python
dflow_X = dflow_prepared.keep_columns(
    ['pickup_weekday', 'pickup_hour', 'distance', 'passengers', 'vendor'])
dflow_y = dflow_prepared.keep_columns('cost')
```

### <a name="split-the-data-into-train-and-test-sets"></a>Dividir os dados em conjuntos de treinamento e teste

Agora você divide os dados em conjuntos de treinamento e teste usando a `train_test_split` função `sklearn` na biblioteca. Essa função segrega os dados no x, **recursos**, DataSet para treinamento de modelo e y, **valores a prever**, conjunto de dados para teste. O `test_size` parâmetro determina a percentagem de dados para alocar ao teste. O `random_state` parâmetro define uma semente para o gerador aleatório, de forma que suas divisões de treinamento/teste sejam sempre determinísticas:

```python
from sklearn.model_selection import train_test_split

x_df = dflow_X.to_pandas_dataframe()
y_df = dflow_y.to_pandas_dataframe()

x_train, x_test, y_train, y_test = train_test_split(
    x_df, y_df, test_size=0.2, random_state=223)
# flatten y_train to 1d array
y_train.values.flatten()
```

A finalidade desta etapa é ter pontos de dados para testar o modelo concluído que não foi usado para treinar o modelo, a fim de medir a precisão real. Em outras palavras, um modelo bem treinado deve ser capaz de fazer previsões com precisão dos dados que ele ainda não viu. Agora você tem os pacotes e dados necessários prontos para treinar o seu modelo.

## <a name="automatically-train-a-model"></a>Preparar automaticamente um modelo

Para treinar um modelo automaticamente, execute as seguintes etapas:
1. Defina as configurações para a execução do experimento. Anexe seus dados de treinamento à configuração e modifique as configurações que controlam o processo de treinamento.
1. Envie o experimento para o ajuste do modelo. Depois de enviar o experimento, o processo é iterado por meio de diferentes algoritmos de aprendizado de máquina e configurações de hiperparâmetro, respeitando suas restrições definidas. Ele escolhe o modelo de melhor ajuste, otimizando uma métrica de precisão.

### <a name="define-settings-for-autogeneration-and-tuning"></a>As definições para a geração automática e Otimização

Defina o parâmetro experimento e as configurações de modelo para geração e ajuste automáticas. Ver a lista completa dos [definições](how-to-configure-auto-train.md). O envio do experimento com essas configurações padrão levará aproximadamente 10-15 min, mas se você quiser um tempo de execução menor, reduza `iterations` ou `iteration_timeout_minutes`.


|Propriedade| Valor neste tutorial |Descrição|
|----|----|---|
|**iteration_timeout_minutes**|10|Limite de tempo em minutos para cada iteração. Reduza esse valor para diminuir o tempo de execução total.|
|**iterations**|30|Número de iterações. Em cada iteração, um novo modelo de aprendizado de máquina é treinado com seus dados. Esse é o valor principal que afeta o tempo de execução total.|
|**primary_metric**| spearman_correlation | Métrica que pretende otimizar. O modelo de melhor ajuste será escolhido com base nessa métrica.|
|**preprocess**| True | Usando **true**, o experimento pode pré-processar os dados de entrada (manipulando dados ausentes, convertendo texto em numérico, etc.)|
|**Verbosidade**| logging.INFO | Controla o nível de registo.|
|**n_cross_validations**|5|Número de divisões de validação cruzada a serem executadas quando os dados de validação não forem especificados.|



```python
automl_settings = {
    "iteration_timeout_minutes": 10,
    "iterations": 30,
    "primary_metric": 'spearman_correlation',
    "preprocess": True,
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

Use as configurações de treinamento definidas como um parâmetro para `AutoMLConfig` um objeto. Além disso, especifique os dados de treinamento e o tipo de modelo, `regression` que é nesse caso.

```python
from azureml.train.automl import AutoMLConfig

# local compute
automated_ml_config = AutoMLConfig(task='regression',
                                   debug_log='automated_ml_errors.log',
                                   path=project_folder,
                                   X=x_train.values,
                                   y=y_train.values.flatten(),
                                   **automl_settings)
```

> [!NOTE]
> As etapas de pré-processamento automatizado do Machine Learning (normalização de recursos, manipulação de dados ausentes, conversão de texto em numeric, etc.) tornam-se parte do modelo subjacente. Ao usar o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

### <a name="train-the-automatic-regression-model"></a>Preparar o modelo de regressão automática

Inicie a experimentação para ser executada localmente. Passe o objeto `automated_ml_config` definido para o experimento. Defina a saída como `True` para exibir o andamento durante o experimento:


```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, experiment_name)
local_run = experiment.submit(automated_ml_config, show_output=True)
```

A saída mostra atualizações ativas conforme o experimento é executado. Para cada iteração, você vê o tipo de modelo, a duração da execução e a precisão do treinamento. O campo `BEST` controla a melhor pontuação de treinamento em execução com base em seu tipo de métrica.

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

### <a name="option-1-add-a-jupyter-widget-to-see-results"></a>Opção 1: Adicionar um widget Jupyter para ver os resultados

Se você usar um notebook Jupyter, use este [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) para ver um grafo e uma tabela de todos os resultados:


```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Jupyter de execução do](./media/tutorial-auto-train-models/automl-dash-output.png)
widget detalhes![Jupyter do widget](./media/tutorial-auto-train-models/automl-chart-output.png)

Os mesmos resultados são armazenados em seu espaço de trabalho.  Você pode obter um link para os resultados da execução:

```
local_run.get_portal_url()
```


### <a name="option-2-get-and-examine-all-run-iterations-in-python"></a>Opção 2: Obter e examinar todas as iterações de execução no Python

Você também pode recuperar o histórico de cada experimento e explorar as métricas individuais para cada execução de iteração. Examinando RMSE (root_mean_squared_error) para cada execução de modelo individual, você vê que a maioria das iterações está prevendo o custo justo de táxi dentro de uma margem razoável (US $3-4).

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items()
               if isinstance(v, float)}
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

Selecione o pipeline melhor nossas iterações. O `get_output` método no `automl_classifier` retorna a melhor execução e o modelo de ajustada para a invocação de acordo com a última. Usando as sobrecargas em `get_output`, você pode recuperar o melhor modelo de execução e ajustada para qualquer métrica registrada ou uma iteração específica:

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-the-best-model-accuracy"></a>A maior precisão do modelo de teste

Use o melhor modelo para executar previsões no conjunto de teste para prever tarifas de táxi. A função `predict` usa o melhor modelo e prevê os valores de y, **custo de viagem**, do `x_test` conjunto de espaço. Imprima os 10 primeiros valores de custo estimado de `y_predict`:

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Crie um gráfico de dispersão para visualizar os valores de custo previstos em comparação com os valores de custo reais. O código a seguir usa `distance` o recurso como o eixo x e a `cost` viagem como o eixo y. Para comparar a variação do custo previsto em cada valor de distância de viagem, os primeiros 100 valores de custo previsto e real são criados como séries separadas. Examinar a plotagem mostra que a relação de distância/custo é quase linear, e os valores de custo previsto estão na maioria dos casos, muito próximos dos valores de custo reais para a mesma distância de viagem.

```python
%matplotlib inline

import matplotlib.pyplot as plt

fig = plt.figure(figsize=(14, 10))
ax1 = fig.add_subplot(111)

distance_vals = [x[4] for x in x_test.values]
y_actual = y_test.values.flatten().tolist()

ax1.scatter(distance_vals[:100], y_predict[:100],
            s=18, c='b', marker="s", label='Predicted')
ax1.scatter(distance_vals[:100], y_actual[:100],
            s=18, c='r', marker="o", label='Actual')

ax1.set_xlabel('distance (mi)')
ax1.set_title('Predicted and Actual Cost/Distance')
ax1.set_ylabel('Cost ($)')

plt.legend(loc='upper left', prop={'size': 12})
plt.rcParams.update({'font.size': 14})
plt.show()
```

![Gráfico de dispersão de previsão](./media/tutorial-auto-train-models/automl-scatter-plot.png)

Calcule o `root mean squared error` dos resultados. Use o `y_test` dataframe. Converta-o em uma lista para comparar com os valores previstos. A função `mean_squared_error` usa duas matrizes de valores e calcula o erro de quadrado médio entre eles. Pegar a raiz quadrada do resultado resulta em um erro nas mesmas unidades que a variável y, **custo**. Indica aproximadamente até onde as previsões de Tarifa de táxi são provenientes das tarifas reais:

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

    3.2204936862688798

Execute o código a seguir para calcular o erro de porcentagem absoluta média (mape) usando `y_actual` os `y_predict` conjuntos de valores completo e. Essa métrica calcula uma diferença absoluta entre cada valor previsto e real e soma todas as diferenças. Em seguida, ele expressa essa soma como uma porcentagem do total dos valores reais:

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

Das métricas de precisão final da previsão, você verá que o modelo é muito bom na previsão de passagens de táxi dos recursos do conjunto de dados, normalmente dentro de +-$3. A processo de desenvolvimento do modelo de aprendizagem tradicional é altamente com muitos recursos e requer investimento de dados de conhecimento e a hora de domínio significativo para executar e comparar os resultados de dezenas de modelos. Usar o Machine Learning automatizado é uma ótima maneira de testar rapidamente vários modelos diferentes para seu cenário.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial de aprendizado de máquina automatizado, você fez as seguintes tarefas:

> [!div class="checklist"]
> * Configurou um espaço de trabalho e dados preparados para um experimento.
> * Treinado com o uso de um modelo de regressão automatizado localmente com parâmetros personalizados.
> * Resultados de treinamento explorados e revisados.

[Implementar o seu modelo](tutorial-deploy-models-with-aml.md) com o Azure Machine Learning.
