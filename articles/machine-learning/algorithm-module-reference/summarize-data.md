---
title: Resumir Dados
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de Dados Sumize em Azure Machine Learning para gerar um relatório básico de estatísticas descritivas para as colunas num conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: 5206565b85d1551e5e551f1dfe75d28c93bc53f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898215"
---
# <a name="summarize-data"></a>Resumir Dados

Este artigo descreve um módulo de Azure Machine Learning designer.

Utilize o módulo De Dados Sumário para criar um conjunto de medidas estatísticas padrão que descrevem cada coluna na tabela de entrada.

As estatísticas resumidas são úteis quando se pretende compreender as características do conjunto completo de dados. Por exemplo, talvez precise de saber:

- Quantos valores em falta existem em cada coluna?
- Quantos valores únicos existem numa coluna de recursos?
- Qual é o desvio médio e padrão para cada coluna?

O módulo calcula as pontuações importantes para cada coluna e devolve uma linha de estatísticas sumárias para cada variável (coluna de dados) fornecida como entrada.

## <a name="how-to-configure-summarize-data"></a>Como configurar Resumos Dados  

1. Adicione o módulo **de Dados Sumário** ao seu pipeline. Pode encontrar este módulo na categoria **Funções Estatísticas** no designer.

1. Ligue o conjunto de dados para o qual pretende gerar um relatório.

    Se pretender reportar apenas algumas colunas, utilize o módulo [Colunas Select no](select-columns-in-dataset.md) conjunto de dados para projetar um subconjunto de colunas para trabalhar.

1. Não são necessários parâmetros adicionais. Por predefinição, o módulo analisa todas as colunas que são fornecidas como entrada, e dependendo do tipo de valores nas colunas, produz um conjunto relevante de estatísticas conforme descrito na secção [Resultados.](#results)

1. Envie o oleoduto.

## <a name="results"></a>Results

O relatório do módulo pode incluir as seguintes estatísticas. 

|Nome da coluna|Descrição|
|------|------|  
|**Funcionalidade**|Nome da coluna|
|**Contagem**|Contagem de todas as linhas|
|**Contagem de valor única**|Número de valores únicos na coluna|
|**Contagem de valor em falta**|Número de valores únicos na coluna|
|**Rio Min**|Valor mais baixo na coluna|  
|**Max**|Valor mais alto na coluna|
|**Média**|Média de todos os valores da coluna|
|**Desvio médio**|Desvio médio dos valores das colunas|
|**1º Quartil**|Valor no primeiro quartil|
|**Mediana**|Valor mediano da coluna|
|**3º Quartil**|Valor no terceiro quartil|
|**Modo**|Modo de valores de coluna|
|**Intervalo**|Inteiro representando o número de valores entre os valores máximo e mínimo|
|**Variação da amostra**|Variação para coluna; ver Nota|
|**Desvio padrão da amostra**|Desvio padrão para colunas; ver Nota|
|**Distorção da amostra**|Skewness para a coluna; ver Nota|
|**Amostra de Kurtose**|Kurtose para coluna; ver Nota|
|**P0.5**|Percentil de 0,5%|
|**P1**|1% percentil|
|**P5**|Percentil de 5%|
|**P95**|Percentil de 95%|
|**P99.5**|Percentil de 99,5% |

## <a name="technical-notes"></a>Notas técnicas

- Para colunas não numéricas, apenas são calculados os valores para Conde, contagem de valor único e contagem de valor em falta. Para outras estatísticas, é devolvido um valor nulo.

- As colunas que contêm valores Boolean são processadas utilizando estas regras:

    - Ao calcular Min, é aplicado um E lógico.
    
    - Ao calcular Max, um OR lógico é aplicado
    
    - Ao calcular Range, o módulo verifica primeiro se o número de valores únicos na coluna é igual a 2.
    
    - Ao calcular qualquer estatística que exija cálculos de pontos flutuantes, os valores de True são tratados como 1.0, e os valores de False são tratados como 0.0.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning.  
