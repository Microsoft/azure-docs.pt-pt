---
title: Caracterização em experiências AutoML
titleSuffix: Azure Machine Learning
description: Saiba quais as configurações de ações que o Azure Machine Learning oferece e como a engenharia de recursos é suportada em experiências automatizadas do ml.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 05/28/2020
ms.custom: seodec18
ms.openlocfilehash: c81e0a71b23e865ca2938f5fbf3c73cdb5c3aeb1
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434300"
---
# <a name="featurization-with-automated-machine-learning"></a>A exibição com aprendizagem automática de máquinas

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste guia, saiba quais são as definições de exibição e como personalizá-las para as suas [experiências automatizadas de aprendizagem automática.](concept-automated-ml.md)

A engenharia de recursos é o processo de utilização do conhecimento de domínio dos dados para criar funcionalidades que ajudam os algoritmos ML a aprender melhor. No Azure Machine Learning, aplicam-se técnicas de dimensionamento e normalização de dados para facilitar a engenharia de recursos. Coletivamente, estas técnicas e engenharia de recursos são referidas como caracterização em machine learning automatizado, AutoML, experiências.

Este artigo pressupõe que já está familiarizado com a forma de configurar uma experiência AutoML. Consulte os seguintes artigos para mais detalhes,

* Para uma primeira experiência de código: [Configuure experiências automatizadas de ML com o Python SDK](how-to-configure-auto-train.md).
* Para uma experiência de código baixo/sem código: [Criar, rever e implementar modelos automatizados de aprendizagem automática de máquinas com o estúdio Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md)

## <a name="configure-featurization"></a>Conigure aconsendo a exibição

Em todas as experiências automatizadas de aprendizagem automática de máquinas, [as técnicas automáticas de escala e normalização](#featurization) são aplicadas aos seus dados por padrão. Estas técnicas de escala e normalização são tipos de caracterização que ajudam *certos* algoritmos sensíveis às características em escalas diferentes. No entanto, também pode permitir a acoplamento adicional, como **a imputação de valores em falta, codificação** e **transformação.**

> [!NOTE]
> As etapas automatizadas de aprendizagem automática (normalização de recurso, manuseamento de dados em falta, conversão de texto em numérico, etc.) tornam-se parte do modelo subjacente. Ao utilizar o modelo para previsões, aplicam-se automaticamente os mesmos passos de apresentação aplicados durante o treino.

Para experiências configuradas com o SDK, pode ativar/desativar a definição `featurization` e especificar ainda mais os passos de aposição que devem ser utilizados para a sua experiência. Se estiver a utilizar o estúdio Azure Machine Learning, veja como ativar a participação [com estes passos.](how-to-use-automated-ml-for-ml-models.md#customize-featurization)

A tabela seguinte mostra as definições aceites `featurization` na [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Configuração de exibição | Description|
------------- | ------------- |
|**`"featurization": 'auto'`**| Indica que, como parte do pré-processamento, [os guarda-dados e as etapas de exibição](#featurization) são executados automaticamente. **Definição padrão**.|
|**`"featurization": 'off'`**| Indica que os passos de caracterização não devem ser feitos automaticamente.|
|**`"featurization":`&nbsp;`'FeaturizationConfig'`**| Indica que deve ser utilizado um passo de aposição personalizado. [Saiba como personalizar a caracterização.](#customize-featurization)|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Caracterização automática

A tabela seguinte resume técnicas automaticamente aplicadas aos seus dados. Isto acontece para experiências configuradas através do SDK ou do estúdio. Para desativar este comportamento, coloque `"featurization": 'off'` no seu `AutoMLConfig` objeto.

> [!NOTE]
> Se planeia exportar os seus modelos auto ML criados para um [modelo ONNX,](concept-onnx.md)apenas as opções de exibição indicadas com um * são suportadas no formato ONNX. Saiba mais sobre [a conversão de modelos para ONNX](concept-automated-ml.md#use-with-onnx). 

|Etapas de exibição &nbsp;| Description |
| ------------- | ------------- |
|**Largar altas características de cardinalidade ou nenhuma variação*** |Largue-os de conjuntos de treino e validação, incluindo características com todos os valores em falta, o mesmo valor em todas as linhas ou com alta cardinalidade (por exemplo, hashes, IDs ou GUIDs).|
|**Imputar valores em falta*** |Para características numéricas, imputar com a média de valores na coluna.<br/><br/>Para características categóricas, imputar com o valor mais frequente.|
|**Gerar funcionalidades adicionais*** |Para as características datetime: Ano, mês, dia, dia da semana, dia do ano, quarto, semana do ano, Hora, Minuto, Segundo.<br/><br/>Para as características de texto: Frequência de prazo baseada em unigramas, bi-gramas e tri-caracteres-gramas.|
|**Transformar e codificar***|Características numéricas com poucos valores únicos são transformadas em características categóricas.<br/><br/>A codificação de um só calor é realizada para baixa cardinalidade categórica; para alta cardinalidade, codificação de haxixe quente.|
|**Incorporações de palavras**|O text featurizer que converte vetores de fichas de texto em vetores de frase usando um modelo pré-treinado. O vetor incorporado de cada palavra num documento é agregado em conjunto para produzir um vetor de recursos documentais.|
|**Codificações de alvos**|Para características categóricas, mapeia cada categoria com valor de alvo médio para problemas de regressão, e para a probabilidade de classe para cada classe para problemas de classificação. A ponderação baseada em frequências e a validação cruzada k-fold são aplicadas para reduzir a adaptação do mapeamento e ruído causados por categorias de dados escassas.|
|**Codificação do alvo de texto**|Para a entrada de texto, um modelo linear empilhado com saco de palavras é usado para gerar a probabilidade de cada classe.|
|**Peso da evidência (Ai)**|Calcula o Ai como uma medida de correlação das colunas categóricas para a coluna-alvo. É calculado como o registo da relação entre probabilidades de classe vs fora de classe. Este passo produz uma coluna de característica numérica por classe e elimina a necessidade de imputar explicitamente os valores em falta e o tratamento mais estranho.|
|**Distância cluster**|Treina um modelo de agrupamento k-significa em todas as colunas numéricas.  Saídas k novas funcionalidades, uma nova característica numérica por cluster, contendo a distância de cada amostra ao centroid de cada cluster.|

## <a name="data-guardrails"></a>Guarda-dados

Os guardrails de dados ajudam-no a identificar potenciais problemas com os seus dados (por exemplo, valores em falta, [desequilíbrio de classe)](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)e ajudam a tomar medidas corretivas para melhorar os resultados. 

São aplicados guarda-costas de dados 

* **Para experiências SDK**, quando os parâmetros `"featurization": 'auto'` ou são `validation=auto` especificados no seu `AutoMLConfig` objeto.
* **Para experiências em estúdio,** quando a *apresentação automática* estiver ativada.  

Pode rever os guarda-dados relativos à sua experiência

* Ao definir `show_output=True` ao submeter uma experiência com o Python SDK.

* No estúdio no separador **Data guardrails** da sua execução automática de ML.

### <a name="data-guardrail-states"></a>Estados de guarda de dados

Os guarda-dados mostrarão um dos três estados: **Passado,** **Feito**ou **Alertado.**

|Estado| Descrição |
|----|---- |
|**Passado**| Não foram detetados problemas de dados e não é necessária qualquer ação do utilizador. |
|**Concluído**| Foram aplicadas alterações aos seus dados. Encorajamos os utilizadores a rever as ações corretivas que a ML automatizada tomou para garantir que as alterações se alinham com os resultados esperados. |
|**Alertado**| Foi detetado um problema de dados que não podia ser corrigido. Encorajamos os utilizadores a rever e corrigir o problema.| 

A tabela seguinte descreve os guarda-dados atualmente suportados e os estados associados que os utilizadores podem encontrar ao submeter a sua experiência.

Guarda-costas|Estado|Condição &nbsp; para &nbsp; o gatilho
---|---|---
**Imputação de valores de recurso em falta** |*Passado* <br><br><br> *Concluído*| Não foram detetados valores de recurso em falta nos seus dados de treino. Saiba mais sobre [a imputação de valor em falta.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Os valores de recurso em falta foram detetados nos seus dados de treino e imputados.
**Alto tratamento de recurso cardinalício** |*Passado* <br><br><br> *Concluído*| As suas entradas foram analisadas, e não foram detetadas características de cardinalício. Saiba mais sobre [a deteção de recursos cardinalícios elevados.](#automatic-featurization) <br><br> As altas características do cardinalício foram detetadas nas suas entradas e foram tratadas.
**Tratamento dividido de validação** |*Concluído*| A configuração de validação foi definida como 'auto' e os dados de formação continham **menos de 20.000 linhas**. <br> Cada iteração do modelo treinado foi validada através de validação cruzada. Saiba mais sobre [dados de validação.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data) <br><br> A configuração de validação foi definida como 'auto' e os dados de formação continham **mais de 20.000 linhas**. <br> Os dados de entrada foram divididos num conjunto de dados de formação e num conjunto de dados de validação para validação do modelo.
**Deteção de equilíbrio de classes** |*Passado* <br><br><br><br><br> *Alertado* | As suas entradas foram analisadas e todas as aulas são equilibradas nos seus dados de treino. Um conjunto de dados é considerado equilibrado se cada classe tiver uma boa representação no conjunto de dados, medido pelo número e relação de amostras. <br><br><br> As aulas desequilibradas foram detetadas nas suas entradas. Para corrigir o viés do modelo, corrija o problema de equilíbrio. Saiba mais sobre [dados desequilibrados.](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)
**Deteção de problemas de memória** |*Passado* <br><br><br><br> *Concluído* |<br> Foram analisados os valores de {horizonte, lag, janela rolante} e não foram detetados potenciais problemas fora de memória. Saiba mais sobre [as configurações de previsão de séries temporq.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) <br><br><br>Os valores de {horizonte, lag, janela rolante) foram analisados e potencialmente farão com que a sua experiência fique sem memória. As configurações da janela de lag ou de rolamento foram desligadas.
**Deteção de frequências** |*Passado* <br><br><br><br> *Concluído* |<br> As séries horárias foram analisadas e todos os pontos de dados estão alinhados com a frequência detetada. <br> <br> As séries horárias foram analisadas e foram detetados pontos de dados que não se alinham com a frequência detetada. Estes pontos de dados foram removidos do conjunto de dados. Saiba mais sobre [a preparação de dados para a previsão de séries de tempo.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)

## <a name="customize-featurization"></a>Personalizar a exibição

Pode personalizar as definições de exibição para garantir que os dados e funcionalidades utilizados para treinar o seu modelo ML resultem em previsões relevantes. 

Para personalizar as ações, especifique  `"featurization": FeaturizationConfig` no seu `AutoMLConfig` objeto. Se estiver a utilizar o estúdio Azure Machine Learning para a sua experiência, consulte o [como fazer](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

A personalização suportada inclui:

|Personalização|Definição|
|--|--|
|**Atualização do propósito da coluna**|Tipo de função de substituição para a coluna especificada.|
|**Atualização do parâmetro do transformador** |Atualizar parâmetros para o transformador especificado. Atualmente suporta o Imputer (médio, mais frequente & mediano) e HashOneHotEncoder.|
|**Colunas de queda** |Colunas a cair de ser acossadas.|
|**Transformadores de blocos**| Bloquear transformadores para serem utilizados no processo de caracterização.|

Crie o objetoConfig de Exibição utilizando chamadas API:
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

* Saiba como configurar as suas experiências automatizadas de ML,

    * Para clientes com experiência de código: [Configurar experiências automatizadas de ML com o Azure Machine Learning SDK](how-to-configure-auto-train.md).
    * Para clientes com experiência de baixo/código: [Crie as suas experiências automatizadas de aprendizagem automática de máquinas no estúdio Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md).

* Saiba mais sobre [como e onde implementar um modelo.](how-to-deploy-and-where.md)

* Saiba mais sobre [como treinar um modelo de regressão com machine learning automatizado](tutorial-auto-train-models.md) ou como treinar utilizando [aprendizagem automática de máquinas num recurso remoto.](how-to-auto-train-remote.md)
