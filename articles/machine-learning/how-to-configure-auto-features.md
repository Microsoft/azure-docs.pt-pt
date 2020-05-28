---
title: Caracterização em experiências AutoML
titleSuffix: Azure Machine Learning
description: Saiba o que as configurações de funcionalidades Que o Azure Machine Learning oferece e como a engenharia de recursos é suportada em experiências automáticas de ml.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/28/2020
ms.custom: seodec18
ms.openlocfilehash: 93e18a95e30c21a44f9ca7df92925323930a9ce8
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84122431"
---
# <a name="featurization-with-automated-machine-learning"></a>Caracterização com machine learning automatizado

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste guia, saiba quais as definições de características oferecidas e como personalizá-las para as suas [experiências automatizadas](concept-automated-ml.md)de aprendizagem automática de máquinas.

A engenharia de funcionalidades é o processo de utilização do conhecimento de domínio dos dados para criar funcionalidades que ajudam os algoritmos ml a aprender melhor. No Azure Machine Learning, são aplicadas técnicas de escala de dados e de normalização para facilitar a engenharia de recursos. Coletivamente, estas técnicas e engenharia de recursos são referidas como caracterização em machine learning automatizado, AutoML, experiências.

Este artigo assume que já está familiarizado com a forma de configurar uma experiência AutoML. Consulte os seguintes artigos para mais detalhes,

* Para uma primeira experiência de código: [Configure experiências automatizadas de ML com o Python SDK](how-to-configure-auto-train.md).
* Para uma experiência de código baixo/sem código: [Criar, rever e implementar modelos automatizados](how-to-use-automated-ml-for-ml-models.md) de aprendizagem automática com o estúdio Azure Machine Learning

## <a name="configure-featurization"></a>Configurar a caracterização

Em todas as experiências automatizadas de aprendizagem automática de máquinas, as técnicas automáticas de [escala e normalização](#featurization) são aplicadas aos seus dados por padrão. Estas técnicas de escala e normalização são tipos de funcionalidades que ajudam *certos* algoritmos que são sensíveis a características em diferentes escalas. No entanto, também pode permitir uma funcionalidade adicional, como a **imputação de valores em falta, codificação** e **transformações.**

> [!NOTE]
> Os passos automatizados de funcionalidade de aprendizagem automática (normalização de recursos, manuseamento de dados em falta, conversão de texto em numérico, etc.) tornam-se parte do modelo subjacente. Ao utilizar o modelo para previsões, os mesmos passos de caracterização aplicados durante o treino são aplicados automaticamente aos seus dados de entrada.

Para experiências configuradas com o SDK, pode ativar/desativar a definição e especificar ainda mais os passos de `featurization` caracterização que devem ser utilizados para a sua experiência. Se estiver a utilizar o estúdio Azure Machine Learning, veja como ativar a caracterização [com estes passos.](how-to-use-automated-ml-for-ml-models.md#customize-featurization)

A tabela a seguir mostra as definições aceites `featurization` na [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Configuração de Características | Descrição|
------------- | ------------- |
|**`"featurization": 'auto'`**| Indica que, como parte do pré-processamento, os [guarda-costas de dados e os passos](#featurization) de caracterização são executados automaticamente. **Definição predefinida**.|
|**`"featurization": 'off'`**| Indica que os passos de caracterização não devem ser feitos automaticamente.|
|**`"featurization":`&nbsp;`'FeaturizationConfig'`**| Indica que deve ser utilizado um passo de caracterização personalizado. [Aprenda a personalizar a caracterização.](#customize-featurization)|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Caracterização automática

A tabela que se segue resume as técnicas aplicadas automaticamente aos seus dados. Isto acontece para experiências configuradas através do SDK ou do estúdio. Para desativar este comportamento, coloque `"featurization": 'off'` no seu `AutoMLConfig` objeto.

> [!NOTE]
> Se planeia exportar os seus modelos auto ML criados para um [modelo ONNX](concept-onnx.md), apenas as opções de funcionalidade indicadas com um * são suportadas no formato ONNX. Saiba mais sobre [a conversão de modelos para ONNX](concept-automated-ml.md#use-with-onnx). 

|Passos de caracterização &nbsp;| Descrição |
| ------------- | ------------- |
|**Largar alto cardeal ou sem características de variação*** |Retire-as de conjuntos de formação e validação, incluindo características com todos os valores em falta, o mesmo valor em todas as linhas ou com alta cardeal (por exemplo, hashes, IDs ou GUIDs).|
|**Impute valores em falta*** |Para características numéricas, impute com valores médios na coluna.<br/><br/>Para características categóricas, impute com valor mais frequente.|
|**Gerar recursos adicionais*** |Para as funcionalidades data: Ano, Mês, Dia, Dia da semana, Dia do ano, Trimestre, Semana do ano, Hora, Minuto, Segundo.<br/><br/>Para as características do texto: Frequência de termo baseada em unigramas, bi-gramas e tri-character-gramas.|
|**Transformar e codificar***|Características numéricas com poucos valores únicos são transformadas em características categóricas.<br/><br/>A codificação one-hot é realizada para a baixa cardeal categórica; para alta cardinalidade, uma codificação de hash-hot-hash.|
|**Incorporações de palavras**|O featurizer de texto que converte vetores de tokens de texto em vetores de frase usando um modelo pré-treinado. O vetor de incorporação de cada palavra num documento é agregado em conjunto para produzir um vetor de características documentais.|
|**Codificação de alvos**|Para características categóricas, mapeie cada categoria com valor-alvo médio para problemas de regressão, e para a probabilidade de classe para cada classe para problemas de classificação. A ponderação baseada na frequência e a validação cruzada k-fold são aplicadas para reduzir a adaptação do mapeamento e do ruído causados por categorias de dados escassas.|
|**Codificação do alvo de texto**|Para a entrada de texto, um modelo linear empilhado com saco de palavras é usado para gerar a probabilidade de cada classe.|
|**Peso da evidência (AI)**|Calcula o AE como uma medida de correlação de colunas categóricas com a coluna alvo. É calculado como o registo da relação entre probabilidades in-class vs fora de classe. Este passo produz uma coluna de características numéricas por classe e remove a necessidade de imputar explicitamente valores em falta e tratamento mais distante.|
|**Distância de aglomerado**|Treina um modelo de agrupamento k-significa em todas as colunas numéricas.  Saídas k novas funcionalidades, uma nova característica numérica por cluster, contendo a distância de cada amostra ao centroide de cada cluster.|

## <a name="data-guardrails"></a>Guarda-costas de dados

Os guarda-costas ajudam-no a identificar potenciais problemas com os seus dados (por exemplo, valores em falta, [desequilíbrio de classe)](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)e ajudam a tomar medidas corretivas para melhorar os resultados. 

São aplicados guarda-costas de dados 

* **Para experiências SDK,** quando os parâmetros `"featurization": 'auto'` ou são `validation=auto` especificados no seu `AutoMLConfig` objeto.
* **Para experiências**de estúdio, quando a *caracterização automática* estiver ativada.  

Pode rever os guarda-costas de dados relativos à sua experiência

* Ao `show_output=True` submeter uma experiência com o Python SDK.

* No estúdio no separador **Data guardrails** de você automatizado ML executar.

### <a name="data-guardrail-states"></a>Estados de guarda de dados

Os guarda-costas de dados exibirão um dos três estados: **Passado,** **Feito**ou **Alertado.**

|Estado| Descrição |
|----|---- |
|**Passado**| Não foram detetados problemas de dados e não é necessária qualquer ação do utilizador. |
|**Concluído**| Foram aplicadas alterações aos seus dados. Encorajamos os utilizadores a rever as ações corretivas que o AUTOMATED ML tomou para garantir que as alterações se alinham com os resultados esperados. |
|**Alertado**| Foi detetada uma questão de dados que não podia ser corrigida. Encorajamos os utilizadores a rever e corrigir o problema.| 

A tabela seguinte descreve os guarda-costas de dados atualmente suportados, e os estados associados que os utilizadores podem encontrar ao submeter a sua experiência.

Guarda-costas|Estado|Condição &nbsp; para &nbsp; o gatilho
---|---|---
**Falta de valores de características imputação** |*Passado* <br><br><br> *Concluído*| Não foram detetados valores de funcionalidade sem falta nos seus dados de treino. Saiba mais sobre a [falta de imputação de valor.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Os valores de funcionalidade em falta foram detetados nos seus dados de treino e imputados.
**Manipulação de recurso de alta cardinalidade** |*Passado* <br><br><br> *Concluído*| Os seus contributos foram analisados, e não foram detetados grandes características de cardinalidade. Saiba mais sobre a deteção de recurso de [alta cardinalidade.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Características de alta cardeal foram detetadas nas suas inputs e foram tratadas.
**Manipulação de validação dividida** |*Concluído*| A configuração de validação foi definida como 'auto' e os dados de formação continham menos de **20.000 linhas**. <br> Cada iteração do modelo treinado foi validada através da validação cruzada. Saiba mais sobre [os dados de validação.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data) <br><br> A configuração de validação foi definida como 'auto' e os dados de treino continham mais de **20.000 linhas**. <br> Os dados de entrada foram divididos num conjunto de dados de formação e num conjunto de dados de validação para validação do modelo.
**Deteção de equilíbrio de classes** |*Passado* <br><br><br><br><br> *Alertado* | As suas inputs foram analisadas, e todas as aulas são equilibradas nos seus dados de treino. Um conjunto de dados é considerado equilibrado se cada classe tiver uma boa representação no conjunto de dados, medido pelo número e relação de amostras. <br><br><br> As aulas desequilibradas foram detetadas nas suas inputs. Para corrigir o viés do modelo, corrija o problema de equilíbrio. Saiba mais sobre [dados desequilibrados.](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)
**Deteção de problemas de memória** |*Passado* <br><br><br><br> *Concluído* |<br> Foram analisados os valores de {horizon, lag, rolling window} e não foram detetados potenciais problemas fora da memória. Saiba mais sobre [as configurações](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) de previsão da série de tempo. <br><br><br>Os valores selecionados {horizon, lag, rolling window} foram analisados e potencialmente causarão a sua experiência a ficar sem memória. As configurações de lag ou janela sonorizadora foram desligadas.
**Deteção de frequências** |*Passado* <br><br><br><br> *Concluído* |<br> A série de tempo foi analisada e todos os pontos de dados estão alinhados com a frequência detetada. <br> <br> As séries de tempo foram analisadas e foram detetados pontos de dados que não se alinham com a frequência detetada. Estes pontos de dados foram removidos do conjunto de dados. Saiba mais sobre [a preparação de dados para a previsão de séries temporais.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)

## <a name="customize-featurization"></a>Personalizar a caracterização

Pode personalizar as definições de funcionalidade para garantir que os dados e funcionalidades utilizados para treinar o seu modelo ML resultam em previsões relevantes. 

Para personalizar as características, especifique  `"featurization": FeaturizationConfig` no seu `AutoMLConfig` objeto. Se estiver a utilizar o estúdio Azure Machine Learning para a sua experiência, veja como [.](how-to-use-automated-ml-for-ml-models.md#customize-featurization)

A personalização suportada inclui:

|Personalização|Definição|
|--|--|
|**Atualização de propósito da coluna**|Sobrepor o tipo de função para a coluna especificada.|
|**Atualização do parâmetro do transformador** |Atualizar os parâmetros para o transformador especificado. Atualmente suporta imputer (médio, mais frequente & mediano) e HashOneHotEncoder.|
|**Colunas de gotete** |Colunas a deixar de ser característica.|
|**Transformadores de blocos**| Os transformadores de blocos a utilizar no processo de caracterização.|

Crie o objeto FeaturizationConfig utilizando chamadas API:
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```
## <a name="next-steps"></a>Próximos passos

* Saiba como configurar as suas experiências automatizadas de ML,

    * Para clientes com experiência em código: [Configure experiências automatizadas de ML com o Azure Machine Learning SDK](how-to-configure-auto-train.md).
    * Para clientes com experiência de código baixo/sem código: [Crie as suas experiências automatizadas de aprendizagem automática em estúdio de Machine Learning Azure](how-to-use-automated-ml-for-ml-models.md).

* Saiba mais sobre [como e onde implementar um modelo.](how-to-deploy-and-where.md)

* Saiba mais sobre como treinar um modelo de [regressão com machine learning automatizado](tutorial-auto-train-models.md) ou como treinar utilizando machine learning automatizado num recurso [remoto](how-to-auto-train-remote.md).
