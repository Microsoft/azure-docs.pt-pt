---
title: A exibição com aprendizagem automática de máquinas
titleSuffix: Azure Machine Learning
description: Aprenda as definições de exibição de dados no Azure Machine Learning e como personalizar essas funcionalidades para as suas experiências automatizadas de ML.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to,automl,contperf-fy21q2
ms.date: 12/18/2020
ms.openlocfilehash: 526afe758063ce6c5f6bd86f8192f56d5f844a85
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/19/2020
ms.locfileid: "97694005"
---
# <a name="data-featurization-in-automated-machine-learning"></a>A participação de dados na aprendizagem automática de máquinas



Conheça as definições de exibição de dados no Azure Machine Learning e como personalizar essas funcionalidades para [experiências automatizadas de ML](concept-automated-ml.md).

## <a name="feature-engineering-and-featurization"></a>Engenharia de recursos e a presença

*A engenharia* de recursos é o processo de utilização do conhecimento de domínio dos dados para criar funcionalidades que ajudam a aprendizagem automática (ML) algoritmos para aprender melhor. No Azure Machine Learning, as técnicas de dimensionamento de dados e normalização são aplicadas para facilitar a engenharia de recursos. Coletivamente, estas técnicas e esta engenharia de recursos são chamadas *de caracterização* em machine learning automatizado, ou *autoML,* experiências.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que já sabe configurar uma experiência AutoML. Para obter informações sobre a configuração, consulte os seguintes artigos:

- Para uma experiência de código-primeiro: [Configure experiências automatizadas de ML utilizando o Azure Machine Learning SDK para Python](how-to-configure-auto-train.md).
- Para uma experiência de baixo código ou sem código: [Crie, reveja e implemente modelos automatizados de aprendizagem automática de máquinas utilizando o estúdio Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md).

## <a name="configure-featurization"></a>Conigure aconsendo a exibição

Em todas as experiências automatizadas de aprendizagem automática de máquinas, [as técnicas automáticas de escala e normalização](#featurization) são aplicadas aos seus dados por padrão. Estas técnicas são tipos de caracterização que ajudam *certos* algoritmos que são sensíveis a características em escalas diferentes. Pode ativar mais aloções, tais como *imputação de valores em falta,* *codificação* e *transformação.*

> [!NOTE]
> Os passos para a caracterização automatizada de aprendizagem automática (como a normalização de recursos, o manuseamento de dados em falta ou a conversão de texto em numérico) tornam-se parte do modelo subjacente. Quando utiliza o modelo para previsões, aplicam-se automaticamente os mesmos passos de apresentação que são aplicados durante o treino.

Para experiências que configura com o Python SDK, pode ativar ou desativar a definição de aconstrução e especificar ainda mais os passos de aconteseção a utilizar para a sua experiência. Se estiver a utilizar o estúdio Azure Machine Learning, consulte os [passos para permitir a exibição](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

A tabela seguinte mostra as definições aceites para `featurization` a [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig):

|Configuração de exibição | Description|
------------- | ------------- |
|`"featurization": 'auto'`| Especifica que, como parte do pré-processamento, [os guarda-dados](#data-guardrails) e [as etapas de exibição](#featurization) devem ser feitos automaticamente. Esta é a predefinição.|
|`"featurization": 'off'`| Especifica que os passos de caracterização não devem ser feitos automaticamente.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Especifica que devem ser utilizados passos de aposição personalizados. [Saiba como personalizar a caracterização.](#customize-featurization)|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Caracterização automática

A tabela seguinte resume técnicas que são automaticamente aplicadas aos seus dados. Estas técnicas são aplicadas para experiências que são configuradas usando o SDK ou o estúdio. Para desativar este comportamento, coloque `"featurization": 'off'` no seu `AutoMLConfig` objeto.

> [!NOTE]
> Se pretende exportar os seus modelos criados pela AutoML para um [modelo ONNX,](concept-onnx.md)apenas as opções de exibição indicadas com um asterisco ("*") são suportadas no formato ONNX. Saiba mais sobre [a conversão de modelos para ONNX](concept-automated-ml.md#use-with-onnx).

|Etapas de exibição &nbsp;| Description |
| ------------- | ------------- |
|**Largar altas características de cardinalidade ou nenhuma variação** _ |Largue estas funcionalidades dos conjuntos de treino e validação. Aplica-se a características com todos os valores em falta, com o mesmo valor em todas as linhas, ou com elevado cardinalício (por exemplo, hashes, IDs ou GUIDs).|
|_*Imputar valores em falta**_ |Para características numéricas, imputar com a média de valores na coluna.<br/><br/>Para características categóricas, imputar com o valor mais frequente.|
|_*Gerar mais funcionalidades**_ |Para as características datetime: Ano, mês, dia, dia da semana, dia do ano, quarto, semana do ano, Hora, Minuto, Segundo.<br><br> _For tarefas de previsão,* estas funcionalidades adicionais do DateTime são criadas: ISO ano, meio - semestre, mês civil como string, Semana, Dia da semana como string, Dia do trimestre, Dia do ano, AM/PM (0 se a hora é antes do meio-dia (12 horas), 1 de outra forma), AM/PM como corda, Hora do Dia (12 horas por dia)<br/><br/>Para funcionalidades de texto: Frequência de prazo baseada em unigramas, bigrams e trigramas. Saiba mais sobre [como isto é feito com o BERT.](#bert-integration)|
|**Transformar e codificar** _|Transforme características numéricas que têm poucos valores únicos em características categóricas.<br/><br/>A codificação de um só calor é usada para características categóricas de baixa cardinalidade. A codificação de haxixe é usada para características categóricas de alto cardeal.|
|_ *Incorporações de palavras**|Um text featurizer converte vetores de fichas de texto em vetores de frase usando um modelo pré-treinado. O vetor incorporado de cada palavra em um documento é agregado com o resto para produzir um vetor de recursos documentais.|
|**Codificações de alvos**|Para características categóricas, este passo mapeia cada categoria com um valor-alvo médio para problemas de regressão, e para a probabilidade de classe para cada classe para problemas de classificação. A ponderação baseada em frequências e a validação cruzada k-fold são aplicadas para reduzir a sobremontagem do mapeamento e do ruído causados por categorias de dados escassas.|
|**Codificação do alvo de texto**|Para a entrada de texto, um modelo linear empilhado com saco de palavras é usado para gerar a probabilidade de cada classe.|
|**Peso da evidência (Ai)**|Calcula o Ai como uma medida de correlação das colunas categóricas para a coluna-alvo. O Ai é calculado como o registo da relação entre probabilidades de classe vs. fora de classe. Este passo produz uma coluna de recurso numérico por classe e elimina a necessidade de imputar explicitamente os valores em falta e o tratamento mais estranho.|
|**Distância cluster**|Treina um modelo de agrupamento de meios K em todas as colunas numéricas. Produz *k* novas funcionalidades (uma nova característica numérica por cluster) que contêm a distância de cada amostra ao centroíid de cada cluster.|

## <a name="data-guardrails"></a>Guarda-dados

*Os guardrails de dados* ajudam-no a identificar potenciais problemas com os seus dados (por exemplo, valores em falta ou [desequilíbrio de classe).](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data) Também ajudam a tomar medidas corretivas para melhorar os resultados.

São aplicados guarda-costas de dados:

- **Para experiências com SDK:** Quando os parâmetros `"featurization": 'auto'` ou `validation=auto` estiverem especificados no seu `AutoMLConfig` objeto.
- **Para experiências em estúdio**: Quando a apresentação automática estiver ativada.

Pode rever os guarda-dados para a sua experiência:

- Definindo `show_output=True` quando submete uma experiência utilizando o SDK.

- No estúdio, no separador **Data guardrails** da sua execução automática de ML.

### <a name="data-guardrail-states"></a>Estados de guarda de dados

Os guarda-dados exibem um dos três estados:

|Estado| Descrição |
|----|---- |
|**Passado**| Não foram detetados problemas de dados e não é necessária qualquer ação por si. |
|**Concluído**| Foram aplicadas alterações aos seus dados. Encorajamo-lo a rever as ações corretivas que a AutoML tomou, para garantir que as alterações se alinham com os resultados esperados. |
|**Alertado**| Um problema de dados foi detetado, mas não foi possível ser corrigido. Encorajamo-lo a rever e corrigir o problema.|

### <a name="supported-data-guardrails"></a>Suportes de dados suportados

A tabela a seguir descreve os guarda-dados que são suportados atualmente e os estados associados que poderá ver quando submeter a sua experiência:

Guarda-costas|Estado|Condição &nbsp; para &nbsp; o gatilho
---|---|---
**Imputação de valores de recurso em falta** |Passado <br><br><br> Concluído| Não foram detetados valores de recurso em falta nos seus dados de treino. Saiba mais sobre [a imputação de valor em falta.](./how-to-use-automated-ml-for-ml-models.md#customize-featurization) <br><br> Os valores de recurso em falta foram detetados nos seus dados de treino e foram imputados.
**Alto tratamento de recurso cardinalício** |Passado <br><br><br> Concluído| As suas entradas foram analisadas, e não foram detetadas características de alto cardeal. <br><br> As características de alta cardinalidade foram detetadas nas suas entradas e foram tratadas.
**Tratamento dividido de validação** |Concluído| A configuração de validação foi definida `'auto'` e os dados de treino continham *menos de 20.000 linhas*. <br> Cada iteração do modelo treinado foi validada através da validação cruzada. Saiba mais sobre [dados de validação.](./how-to-configure-auto-train.md#training-validation-and-test-data) <br><br> A configuração de validação foi definida para `'auto'` , e os dados de treino continham *mais de 20.000 linhas*. <br> Os dados de entrada foram divididos num conjunto de dados de formação e num conjunto de dados de validação para validação do modelo.
**Deteção de equilíbrio de classes** |Passado <br><br><br><br>Alertado <br><br><br>Concluído | As suas entradas foram analisadas e todas as aulas são equilibradas nos seus dados de treino. Um conjunto de dados é considerado equilibrado se cada classe tiver uma boa representação no conjunto de dados, medido pelo número e relação de amostras. <br><br> As aulas desequilibradas foram detetadas nas suas entradas. Para corrigir o viés do modelo, corrija o problema de equilíbrio. Saiba mais sobre [dados desequilibrados.](./concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)<br><br> As classes desequilibradas foram detetadas nas suas entradas e a lógica abrangente determinou aplicar o equilíbrio.
**Deteção de problemas de memória** |Passado <br><br><br><br> Concluído |<br> Os valores selecionados (horizonte, lag, janela rolante) foram analisados e não foram detetados potenciais problemas fora da memória. Saiba mais sobre [as configurações de previsão de séries temporq.](./how-to-auto-train-forecast.md#configuration-settings) <br><br><br>Os valores selecionados (horizonte, lag, janela rolante) foram analisados e potencialmente farão com que a sua experiência fique sem memória. As configurações de lag ou janela de rolamento foram desligadas.
**Deteção de frequências** |Passado <br><br><br><br> Concluído |<br> As séries horárias foram analisadas e todos os pontos de dados estão alinhados com a frequência detetada. <br> <br> As séries horárias foram analisadas e foram detetados pontos de dados que não se alinham com a frequência detetada. Estes pontos de dados foram removidos do conjunto de dados. Saiba mais sobre [a preparação de dados para a previsão de séries de tempo](./how-to-auto-train-forecast.md#preparing-data).

## <a name="customize-featurization"></a>Personalizar a exibição

Pode personalizar as definições de exibição para garantir que os dados e funcionalidades utilizados para treinar o seu modelo ML resultem em previsões relevantes.

Para personalizar as ações, especifique `"featurization": FeaturizationConfig` no seu `AutoMLConfig` objeto. Se estiver a usar o estúdio Azure Machine Learning para a sua experiência, consulte o [artigo de como fazer.](how-to-use-automated-ml-for-ml-models.md#customize-featurization) Para personalizar os tipos de tarefas de previsão, consulte o [como fazer a previsão](how-to-auto-train-forecast.md#customize-featurization).

As personalizações suportadas incluem:

|Personalização|Definição|
|--|--|
|**Atualização do propósito da coluna**|Sobrepor o tipo de função detetada automaticamente para a coluna especificada.|
|**Atualização do parâmetro do transformador** |Atualize os parâmetros para o transformador especificado. Atualmente suporta *o Imputer* (médio, mais frequente e mediano) e *o HashOneHotEncoder*.|
|**Colunas de queda** |Especifica as colunas a deixar de serem apresentando.|
|**Transformadores de blocos**| Especifica os transformadores de blocos a utilizar no processo de aposição.|

Crie o `FeaturizationConfig` objeto utilizando chamadas API:

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

## <a name="featurization-transparency"></a>Transparência da participação

Todos os modelos AutoML têm a caracterização aplicada automaticamente.  A caracterização inclui engenharia de recursos automatizados `"featurization": 'auto'` (quando) e escala e normalização, que depois impacta o algoritmo selecionado e os seus valores de hiperparímetro. O AutoML suporta diferentes métodos para garantir que tem visibilidade no que foi aplicado ao seu modelo.

Considere este exemplo de previsão:

+ Existem quatro características de entrada: A (Numérico), B (Numérico), C (Numérico), D (DataTime).
+ A função numérica C é largada porque é uma coluna de ID com todos os valores únicos.
+ As características numéricas A e B têm valores em falta e, portanto, são imputadas pela média.
+ A função DateTime D apresenta 11 funcionalidades diferentes.

Para obter esta informação, utilize a `fitted_model` saída da sua experiência automatizada de ML.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```
### <a name="automated-feature-engineering"></a>Engenharia de recursos automatizados 
Devolve `get_engineered_feature_names()` uma lista de nomes de recursos projetados.

  >[!Note]
  >Utilize o "timeseriestransformer" para a tarefa='previsão', então utilize o "adaptador de dados" para a tarefa de "regressão" ou "classificação".

  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

Esta lista inclui todos os nomes de recursos projetados. 

  ```
  ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

`get_featurization_summary()`Obtém um resumo de todas as funcionalidades de entrada.

  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

Saída

  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   |Saída|Definição|
   |----|--------|
   |RawFeatureName|Denominação/nome da coluna a partir do conjunto de dados fornecido.|
   |TipoDetectado|Tipo de dados detetado da função de entrada.|
   |Caiu|Indica se a função de entrada foi largada ou utilizada.|
   |EngenhariaFeatureCount|Número de funcionalidades geradas através de transformações automatizadas de engenharia de recursos.|
   |Transformações|Lista de transformações aplicadas às funcionalidades de entrada para gerar funcionalidades de engenharia.|

### <a name="scaling-and-normalization"></a>Escala e normalização

Para compreender a escala/normalização e o algoritmo selecionado com os seus valores de hiperparímetro, utilize `fitted_model.steps` . 

A seguinte saída da amostra é de correr `fitted_model.steps` para uma corrida escolhida:

```
[('RobustScaler', 
  RobustScaler(copy=True, 
  quantile_range=[10, 90], 
  with_centering=True, 
  with_scaling=True)), 

  ('LogisticRegression', 
  LogisticRegression(C=0.18420699693267145, class_weight='balanced', 
  dual=False, 
  fit_intercept=True, 
  intercept_scaling=1, 
  max_iter=100, 
  multi_class='multinomial', 
  n_jobs=1, penalty='l2', 
  random_state=None, 
  solver='newton-cg', 
  tol=0.0001, 
  verbose=0, 
  warm_start=False))
```

Para obter mais detalhes, utilize esta função de ajudante: 

```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()

print_model(model)
```

Esta função de ajudante devolve a seguinte saída para uma determinada execução utilizando `LogisticRegression with RobustScalar` como algoritmo específico.

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>Prever probabilidade de classe

Os modelos produzidos com ML automatizado têm todos objetos de invólucro que espelham a funcionalidade da sua classe de origem aberta. A maioria dos objetos de invólucro de modelo de classificação devolvidos por ML automatizado implementam a `predict_proba()` função, que aceita uma amostra de dados matrizes semelhantes a matrizes ou escassas das suas funcionalidades (valores X), e devolve uma matriz n-dimensional de cada amostra e sua respetiva probabilidade de classe.

Assumindo que recuperou o melhor modelo de execução e equipado utilizando as mesmas chamadas de cima, pode ligar `predict_proba()` diretamente do modelo equipado, fornecendo uma `X_test` amostra no formato adequado, dependendo do tipo de modelo.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Se o modelo subjacente não suportar a `predict_proba()` função ou o formato estiver incorreto, será lançada uma exceção específica da classe do modelo. Consulte os docs de referência [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) e [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) para exemplos de como esta função é implementada para diferentes tipos de modelos.

<a name="bert-integration"></a>

## <a name="bert-integration-in-automated-ml"></a>Integração BERT em ML automatizado

[BERT](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657) é utilizado na camada de caracterização de AutoML. Nesta camada, se uma coluna contiver texto livre ou outros tipos de dados como timetamps ou números simples, então a caracterização é aplicada em conformidade.

Para o BERT, o modelo é afinado e treinado utilizando as etiquetas fornecidas pelo utilizador. A partir daqui, as incorporações de documentos são saídas como funcionalidades ao lado de outras, como funcionalidades baseadas em timetamp, dia da semana. 


### <a name="steps-to-invoke-bert"></a>Passos para invocar o BERT

Para invocar o BERT, inseeta-se  `enable_dnn: True` no seu automl_settings e use um computo `vm_size = "STANDARD_NC6"` GPU (ou uma GPU superior). Se for utilizado um cálculo CPU, em vez de BERT, o AutoML permite o aditador BiLSTM DNN.

A AutoML dá os seguintes passos para o BERT. 

1. **Pré-processamento e tokenização de todas as colunas de texto**. Por exemplo, o transformador "StringCast" pode ser encontrado no resumo da caracterização do modelo final. Um exemplo de como produzir o resumo da caracterização do modelo pode ser encontrado [neste caderno](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b).

2. **Concatenate todas as colunas de texto numa única coluna de texto,** daí `StringConcatTransformer` o modelo final. 

    A nossa implementação do BERT limita o comprimento total do texto de uma amostra de formação a 128 fichas. Isto significa que todas as colunas de texto quando concatenadas, devem ser idealmente no máximo 128 fichas de comprimento. Se várias colunas estiverem presentes, cada coluna deve ser podada de modo que esta condição seja satisfeita. Caso contrário, para colunas de comprimento concatenated >128 tokens a camada de tokens BERT trunca esta entrada para 128 fichas.

3. **Como parte da varredura de funcionalidades, a AutoML compara o BERT com a linha de base (características do saco de palavras) numa amostra dos dados.** Esta comparação determina se a BERT daria melhorias de precisão. Se o BERT tiver um desempenho melhor do que a linha de base, a AutoML utiliza então o BERT para caracterização de texto para todos os dados. Nesse caso, verá o `PretrainedTextDNNTransformer` no modelo final.

O BERT geralmente corre mais tempo do que outros afetantes. Para um melhor desempenho, recomendamos a utilização de "STANDARD_NC24r" ou "STANDARD_NC24rs_V3" para as suas capacidades de RDMA. 

O AutoML distribuirá o treino BERT por vários nosdes se estiverem disponíveis (até um máximo de oito nóns). Isto pode ser feito no seu `AutoMLConfig` objeto definindo o `max_concurrent_iterations` parâmetro para mais de 1. 

## <a name="supported-languages-for-bert-in-automl"></a>Línguas apoiadas para BERT em autoML 

A AutoML suporta atualmente cerca de 100 idiomas e, dependendo do idioma do conjunto de dados, o autoML escolhe o modelo BERT apropriado. Para os dados alemães, utilizamos o modelo BERT alemão. Para inglês, usamos o modelo BERT inglês. Para todas as outras línguas, usamos o modelo BERT multilíngue.

No código seguinte, o modelo BERT alemão é acionado, uma vez que a língua de conjunto de dados é especificada para `deu` , o código linguístico de três letras para alemão de acordo com a [classificação ISO](https://iso639-3.sil.org/code/deu):

```python
from azureml.automl.core.featurization import FeaturizationConfig

featurization_config = FeaturizationConfig(dataset_language='deu')

automl_settings = {
    "experiment_timeout_minutes": 120,
    "primary_metric": 'accuracy', 
# All other settings you want to use 
    "featurization": featurization_config,
    
  "enable_dnn": True, # This enables BERT DNN featurizer
    "enable_voting_ensemble": False,
    "enable_stack_ensemble": False
}
```

## <a name="next-steps"></a>Passos seguintes

* Saiba como configurar as suas experiências automatizadas de ML:

    * Para uma experiência de código-primeiro: [Configure experiências automatizadas de ML utilizando o Azure Machine Learning SDK](how-to-configure-auto-train.md).
    * Para uma experiência de baixo código ou sem código: [Crie as suas experiências automatizadas de ML no estúdio Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md).

* Saiba mais sobre [como e onde implementar um modelo.](how-to-deploy-and-where.md)

* Saiba mais sobre [como treinar um modelo de regressão utilizando machine learning automatizado](tutorial-auto-train-models.md) ou como treinar utilizando machine learning automatizado num recurso [remoto.](how-to-auto-train-remote.md)
