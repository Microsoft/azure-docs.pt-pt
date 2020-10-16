---
title: Como preservar a privacidade dos dados utilizando os pacotes WhiteNoise (pré-visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a aplicar as melhores práticas de privacidade diferenciais aos modelos Azure Machine Learning utilizando os pacotes WhiteNoise.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: slbird
author: slbird
ms.reviewer: luquinta
ms.date: 07/09/2020
ms.openlocfilehash: 355d96fe5a617effab89fbd038f7f1785215f88f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90897695"
---
# <a name="use-differential-privacy-in-azure-machine-learning-preview"></a>Use privacidade diferencial em Azure Machine Learning (pré-visualização)



Aprenda a aplicar as melhores práticas de privacidade diferenciais aos modelos Azure Machine Learning utilizando os pacotes WhiteNoise Python.

Privacidade diferencial é a definição padrão de privacidade. Os sistemas que aderem a esta definição de privacidade fornecem fortes garantias contra uma vasta gama de ataques de reconstrução de dados e reidentificação, incluindo ataques de adversários que possuem informações auxiliares. Saiba mais sobre como [funciona a privacidade diferencial.](./concept-differential-privacy.md)

> [!NOTE]
> Por favor, note que estamos renomeando o kit de ferramentas e apresentaremos o novo nome nas próximas semanas. 

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)
- [Python 3](https://www.python.org/downloads/)

## <a name="install-whitenoise-packages"></a>Instalar pacotes WhiteNoise

### <a name="standalone-installation"></a>Instalação autónoma

As bibliotecas são projetadas para funcionar a partir de clusters de faíscas distribuídos, e podem ser instaladas como qualquer outro pacote.

As instruções abaixo assumem que os seus `python` `pip` comandos estão mapeados para `python3` e `pip3` .

Utilize pip para instalar os [pacotes WhiteNoise Python](https://pypi.org/project/opendp-whitenoise/).

`pip install opendp-whitenoise`

Para verificar se as embalagens estão instaladas, lance uma solicitação de pitão e escreva:

```python
import opendp.whitenoise.core
import opendp.whitenoise.sql
```

Se as importações forem bem sucedidas, as bibliotecas estão instaladas e prontas a ser utilizadas.

### <a name="docker-image"></a>Imagem do Docker

Também pode usar pacotes WhiteNoise com Docker.

Puxe a `opendp/whitenoise` imagem para usar as bibliotecas dentro de um recipiente Docker que inclui Spark, Jupyter e código de amostra.

```sh
docker pull opendp/whitenoise:privacy
```

Assim que tiver tirado a imagem, lance o servidor Jupyter:

```sh
docker run --rm -p 8989:8989 --name whitenoise-run opendp/whitenoise:privacy
```

Isto inicia um servidor Jupyter na porta `8989` na sua , com senha `localhost` `pass@word99` . Assumindo que usou a linha de comando acima para iniciar o contentor com o `whitenoise-privacy` nome, pode abrir um terminal de bash no servidor Jupyter executando:

```sh
docker exec -it whitenoise-run bash
```

A instância do Docker limpa todo o estado no encerramento, por isso vais perder quaisquer cadernos que cries no caso de execução. Para remediar isto, pode ligar uma pasta local ao recipiente quando a lançar:

```sh
docker run --rm -p 8989:8989 --name whitenoise-run --mount type=bind,source=/Users/your_name/my-notebooks,target=/home/privacy/my-notebooks opendp/whitenoise:privacy
```

Quaisquer cadernos que cries sob a pasta *dos meus cadernos* serão armazenados no teu sistema de ficheiros local.

## <a name="perform-data-analysis"></a>Realizar análise de dados

Para preparar uma versão diferenciadamente privada, é necessário escolher uma fonte de dados, uma estatística e alguns parâmetros de privacidade, indicando o nível de proteção da privacidade.  

Esta amostra refere-se ao California Public Use Microdata (PUMS), representando registos anonimizados de demografia cidadã:

```python
import os
import sys
import numpy as np
import opendp.whitenoise.core as wn

data_path = os.path.join('.', 'data', 'PUMS_california_demographics_1000', 'data.csv')
var_names = ["age", "sex", "educ", "race", "income", "married", "pid"]
```

Neste exemplo, calculamos a média e a variação da idade.  Usamos um total `epsilon` de 1.0 (epsilon é o nosso parâmetro de privacidade, espalhando o nosso orçamento de privacidade através das duas quantidades que queremos calcular. Saiba mais sobre [as métricas de privacidade.](concept-differential-privacy.md#differential-privacy-metrics)

```python
with wn.Analysis() as analysis:
    # load data
    data = wn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = wn.dp_mean(data = wn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
    # get variance of age
    age_var = wn.dp_variance(data = wn.cast(data['age'], type="FLOAT"),
                             privacy_usage = {'epsilon': .35},
                             data_lower = 0.,
                             data_upper = 100.,
                             data_n = 1000
                            )
analysis.release()

print("DP mean of age: {0}".format(age_mean.value))
print("DP variance of age: {0}".format(age_var.value))
print("Privacy usage: {0}".format(analysis.privacy_usage))
```

Os resultados são parecidos com os de baixo:

```text
DP mean of age: 44.55598845931517
DP variance of age: 231.79044646429134
Privacy usage: approximate {
  epsilon: 1.0
}
```

Há algumas coisas importantes a notar sobre este exemplo.  Primeiro, o `Analysis` objeto representa um gráfico de processamento de dados.  Neste exemplo, a média e a variação são calculadas a partir do mesmo nó de origem.  No entanto, pode incluir expressões mais complexas que combinam entradas com saídas de forma arbitrária.

O gráfico de análise inclui `data_upper` e `data_lower` metadados, especificando os limites inferior e superior para idades.  Estes valores são utilizados para calibrar com precisão o ruído para garantir a privacidade diferencial.  Estes valores também são utilizados em alguns manuseamento de valores forasteiros ou em falta.

Finalmente, o gráfico de análise acompanha o orçamento total de privacidade gasto.

Pode utilizar a biblioteca para compor gráficos de análise mais complexos, com vários mecanismos, estatísticas e funções de utilidade:

| Estatísticas    | Mecanismos | Utilitários  |
| ------------- |------------|------------|
| Contagem         | Gaussiano   | Elenco       |
| Histograma     | Geométrico  | Aperto   |
| Média          | Rio Laplace    | Digitalizar   |
| Quantis     |            | Filtrar     |
| Soma           |            | Imputation |
| Variação/Covariância |      | Transformação  |

Consulte o caderno de [análise de dados](https://github.com/opendifferentialprivacy/whitenoise-samples/blob/master/analysis/basic_data_analysis.ipynb) para obter mais detalhes.

## <a name="approximate-utility-of-differentially-private-releases"></a>Utilidade aproximada de lançamentos diferencialmente privados

Uma vez que a privacidade diferencial funciona calibrando o ruído, a utilidade das versões pode variar dependendo do risco de privacidade.  Geralmente, o ruído necessário para proteger cada indivíduo torna-se insignificante à medida que os tamanhos da amostra crescem grandes, mas sobrecarregam o resultado para libertações que visam um único indivíduo.  Os analistas podem rever a informação de precisão para uma versão para determinar o quão útil é a libertação:

```python
with wn.Analysis() as analysis:
    # load data
    data = wn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = wn.dp_mean(data = wn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
analysis.release()

print("Age accuracy is: {0}".format(age_mean.get_accuracy(0.05)))
```

O resultado dessa operação deve ser semelhante ao seguinte:

```text
Age accuracy is: 0.2995732273553991
```

Este exemplo calcula a média como acima, e usa a `get_accuracy` função para solicitar precisão em `alpha` 0,05. Um `alpha` de 0,05 representa um intervalo de 95%, nesse valor libertado ficará dentro dos limites de precisão reportados cerca de 95% das vezes.  Neste exemplo, a precisão reportada é de 0,3, o que significa que o valor libertado será dentro de um intervalo de largura 0,6, cerca de 95% do tempo.  Não é correto pensar neste valor como uma barra de erro, uma vez que o valor lançado ficará fora do intervalo de precisão reportado à taxa especificada por `alpha` , e os valores fora do alcance podem estar fora de qualquer direção.

Os analistas podem consultar `get_accuracy` diferentes valores de obter intervalos de `alpha` confiança mais estreitos ou mais amplos, sem incorrer em custos adicionais de privacidade.

## <a name="generate-a-histogram"></a>Gerar um histograma

A função incorporada `dp_histogram` cria histogramas diferencialmente privados sobre qualquer um dos seguintes tipos de dados:

- Uma variável contínua, onde o conjunto de números tem de ser dividido em caixotes
- Uma variável booleana ou dicotomia, que só pode assumir dois valores
- Uma variável categórica, onde há categorias distintas enumeradas como cordas

Aqui está um exemplo de um `Analysis` caixote de lixo para um histograma variável contínua:

```python
income_edges = list(range(0, 100000, 10000))

with wn.Analysis() as analysis:
    data = wn.Dataset(path = data_path, column_names = var_names)

    income_histogram = wn.dp_histogram(
            wn.cast(data['income'], type='int', lower=0, upper=100),
            edges = income_edges,
            upper = 1000,
            null_value = 150,
            privacy_usage = {'epsilon': 0.5}
        )
```

Como os indivíduos são desarticulamente divididos entre os caixotes do histograma, o custo de privacidade é incorrido apenas uma vez por histograma, mesmo que o histograma inclua muitos caixotes.

Para mais informações sobre histogramas, consulte o [caderno de histogramas.](https://github.com/opendifferentialprivacy/whitenoise-samples/blob/master/analysis/histograms.ipynb)

## <a name="generate-a-covariance-matrix"></a>Gerar uma matriz de covariância

A WhiteNoise oferece três funcionalidades diferentes com a sua `dp_covariance` função:

- Covariância entre dois vetores
- Matriz de covariância de uma matriz
- Matriz de covariância cruzada de um par de matrizes

Aqui está um exemplo de computação de uma covariância escalar:

```python
with wn.Analysis() as analysis:
    wn_data = wn.Dataset(path = data_path, column_names = var_names)

    age_income_cov_scalar = wn.dp_covariance(
      left = wn.cast(wn_data['age'], 
      type = "FLOAT"), 
      right = wn.cast(wn_data['income'], 
      type = "FLOAT"), 
      privacy_usage = {'epsilon': 1.0},
      left_lower = 0., 
      left_upper = 100., 
      left_n = 1000, 
      right_lower = 0., 
      right_upper = 500_000.,
      right_n = 1000)
```

Para mais informações, consulte o [caderno de covariance](
https://github.com/opendifferentialprivacy/whitenoise-samples/blob/master/analysis/covariance.ipynb)

## <a name="next-steps"></a>Passos Seguintes

- Explore [os cadernos de amostras whiteNoise.](https://github.com/opendifferentialprivacy/whitenoise-samples/tree/master/analysis)