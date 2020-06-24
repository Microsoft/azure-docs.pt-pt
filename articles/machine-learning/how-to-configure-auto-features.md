---
title: Caracterização em experiências AutoML
titleSuffix: Azure Machine Learning
description: Saiba quais as configurações de ações que o Azure Machine Learning oferece e como a engenharia de recursos é suportada em experiências automatizadas de ML.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 05/28/2020
ms.custom: seodec18
ms.openlocfilehash: aa348728cd4e9ac0ce5d70cb293ac850cc549666
ms.sourcegitcommit: 1383842d1ea4044e1e90bd3ca8a7dc9f1b439a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/16/2020
ms.locfileid: "84817136"
---
# <a name="featurization-in-automated-machine-learning"></a>Caracterização na aprendizagem automática de máquinas

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste guia, aprenderá:

- O que as definições de ações Azure Machine Learning oferecem.
- Como personalizar essas funcionalidades para as suas [experiências automatizadas de aprendizagem automática de máquinas.](concept-automated-ml.md)

*A engenharia* de recursos é o processo de utilização do conhecimento de domínio dos dados para criar funcionalidades que ajudam a aprendizagem automática (ML) algoritmos para aprender melhor. No Azure Machine Learning, as técnicas de dimensionamento de dados e normalização são aplicadas para facilitar a engenharia de recursos. Coletivamente, estas técnicas e esta engenharia de recursos são chamadas *de caracterização* em machine learning automatizado, ou *AutoML,* experiências.

Este artigo assume que já sabe configurar uma experiência AutoML. Para obter informações sobre a configuração, consulte os seguintes artigos:

- Para uma experiência de código-primeiro: [Configure experiências automatizadas de ML utilizando o Azure Machine Learning SDK para Python](how-to-configure-auto-train.md).
- Para uma experiência de baixo código ou sem código: [Crie, reveja e implemente modelos automatizados de aprendizagem automática de máquinas utilizando o estúdio Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md).

## <a name="configure-featurization"></a>Conigure aconsendo a exibição

Em todas as experiências automatizadas de aprendizagem automática de máquinas, [as técnicas automáticas de escala e normalização](#featurization) são aplicadas aos seus dados por padrão. Estas técnicas são tipos de caracterização que ajudam *certos* algoritmos que são sensíveis a características em escalas diferentes. No entanto, também pode permitir a acoplamento adicional, como *a imputação de valores em falta,* *codificação*e *transformação.*

> [!NOTE]
> Os passos para a caracterização automatizada de aprendizagem automática (como a normalização de recursos, o manuseamento de dados em falta ou a conversão de texto em numérico) tornam-se parte do modelo subjacente. Quando utiliza o modelo para previsões, aplicam-se automaticamente os mesmos passos de apresentação que são aplicados durante o treino.

Para experiências que configura com o Python SDK, pode ativar ou desativar a definição de aconstrução e especificar ainda mais os passos de aconteseção a utilizar para a sua experiência. Se estiver a utilizar o estúdio Azure Machine Learning, consulte os [passos para permitir a exibição](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

A tabela seguinte mostra as definições aceites para `featurization` a [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig):

|Configuração de exibição | Description|
------------- | ------------- |
|`"featurization": 'auto'`| Especifica que, como parte do pré-processamento, [os guarda-dados e as etapas de exibição](#featurization) devem ser feitos automaticamente. Esta é a predefinição.|
|`"featurization": 'off'`| Especifica que os passos de caracterização não devem ser feitos automaticamente.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Especifica que devem ser utilizados passos de aposição personalizados. [Saiba como personalizar a caracterização.](#customize-featurization)|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Caracterização automática

A tabela seguinte resume técnicas que são automaticamente aplicadas aos seus dados. Estas técnicas são aplicadas para experiências que são configuradas usando o SDK ou o estúdio. Para desativar este comportamento, coloque `"featurization": 'off'` no seu `AutoMLConfig` objeto.

> [!NOTE]
> Se pretende exportar os seus modelos criados pela AutoML para um [modelo ONNX,](concept-onnx.md)apenas as opções de exibição indicadas com um asterisco ("*") são suportadas no formato ONNX. Saiba mais sobre [a conversão de modelos para ONNX](concept-automated-ml.md#use-with-onnx).

|Etapas de exibição &nbsp;| Description |
| ------------- | ------------- |
|**Largar altas características de cardinalidade ou nenhuma variação*** |Largue estas funcionalidades dos conjuntos de treino e validação. Aplica-se a características com todos os valores em falta, com o mesmo valor em todas as linhas, ou com elevado cardinalício (por exemplo, hashes, IDs ou GUIDs).|
|**Imputar valores em falta*** |Para características numéricas, imputar com a média de valores na coluna.<br/><br/>Para características categóricas, imputar com o valor mais frequente.|
|**Gerar funcionalidades adicionais*** |Para as características datetime: Ano, mês, dia, dia da semana, dia do ano, quarto, semana do ano, Hora, Minuto, Segundo.<br/><br/>Para funcionalidades de texto: Frequência de prazo baseada em unigramas, bigrams e trigramas.|
|**Transformar e codificar***|Transforme características numéricas que têm poucos valores únicos em características categóricas.<br/><br/>A codificação de um só calor é usada para características categóricas de baixa cardinalidade. A codificação de haxixe é usada para características categóricas de alto cardeal.|
|**Incorporações de palavras**|Um text featurizer converte vetores de fichas de texto em vetores de frase usando um modelo pré-treinado. O vetor incorporado de cada palavra em um documento é agregado com o resto para produzir um vetor de recursos documentais.|
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
**Imputação de valores de recurso em falta** |Passado <br><br><br> Concluído| Não foram detetados valores de recurso em falta nos seus dados de treino. Saiba mais sobre [a imputação de valor em falta.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Os valores de recurso em falta foram detetados nos seus dados de treino e foram imputados.
**Alto tratamento de recurso cardinalício** |Passado <br><br><br> Concluído| As suas entradas foram analisadas, e não foram detetadas características de alto cardeal. Saiba mais sobre [a deteção de recursos de alta cardinalidade.](#automatic-featurization) <br><br> As características de alta cardinalidade foram detetadas nas suas entradas e foram tratadas.
**Tratamento dividido de validação** |Concluído| A configuração de validação foi definida `'auto'` e os dados de treino continham *menos de 20.000 linhas*. <br> Cada iteração do modelo treinado foi validada através da validação cruzada. Saiba mais sobre [dados de validação.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data) <br><br> A configuração de validação foi definida para `'auto'` , e os dados de treino continham *mais de 20.000 linhas*. <br> Os dados de entrada foram divididos num conjunto de dados de formação e num conjunto de dados de validação para validação do modelo.
**Deteção de equilíbrio de classes** |Passado <br><br><br><br><br> Alertado | As suas entradas foram analisadas e todas as aulas são equilibradas nos seus dados de treino. Um conjunto de dados é considerado equilibrado se cada classe tiver uma boa representação no conjunto de dados, medido pelo número e relação de amostras. <br><br><br> As aulas desequilibradas foram detetadas nas suas entradas. Para corrigir o viés do modelo, corrija o problema de equilíbrio. Saiba mais sobre [dados desequilibrados.](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)
**Deteção de problemas de memória** |Passado <br><br><br><br> Concluído |<br> Os valores selecionados (horizonte, lag, janela rolante) foram analisados e não foram detetados potenciais problemas fora da memória. Saiba mais sobre [as configurações de previsão de séries temporq.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) <br><br><br>Os valores selecionados (horizonte, lag, janela rolante) foram analisados e potencialmente farão com que a sua experiência fique sem memória. As configurações de lag ou janela de rolamento foram desligadas.
**Deteção de frequências** |Passado <br><br><br><br> Concluído |<br> As séries horárias foram analisadas e todos os pontos de dados estão alinhados com a frequência detetada. <br> <br> As séries horárias foram analisadas e foram detetados pontos de dados que não se alinham com a frequência detetada. Estes pontos de dados foram removidos do conjunto de dados. Saiba mais sobre [a preparação de dados para a previsão de séries de tempo](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data).

## <a name="customize-featurization"></a>Personalizar a exibição

Pode personalizar as definições de exibição para garantir que os dados e funcionalidades utilizados para treinar o seu modelo ML resultem em previsões relevantes.

Para personalizar as ações, especifique  `"featurization": FeaturizationConfig` no seu `AutoMLConfig` objeto. Se estiver a usar o estúdio Azure Machine Learning para a sua experiência, consulte o [artigo de como fazer.](how-to-use-automated-ml-for-ml-models.md#customize-featurization)

As personalizações suportadas incluem:

|Personalização|Definição|
|--|--|
|**Atualização do propósito da coluna**|Substitua o tipo de recurso para a coluna especificada.|
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

## <a name="next-steps"></a>Passos seguintes

* Saiba como configurar as suas experiências automatizadas de ML:

    * Para uma experiência de código-primeiro: [Configure experiências automatizadas de ML utilizando o Azure Machine Learning SDK](how-to-configure-auto-train.md).
    * Para uma experiência de baixo código ou sem código: [Crie as suas experiências automatizadas de ML no estúdio Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md).

* Saiba mais sobre [como e onde implementar um modelo.](how-to-deploy-and-where.md)

* Saiba mais sobre [como treinar um modelo de regressão utilizando machine learning automatizado](tutorial-auto-train-models.md) ou como treinar utilizando machine learning automatizado num recurso [remoto.](how-to-auto-train-remote.md)
