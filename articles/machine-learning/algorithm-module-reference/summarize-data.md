---
title: Resumir Dados
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo De dados de resumo em Aprendizagem automática Azure para gerar um relatório básico de estatísticas descritivas para as colunas num conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: b0def12582dd3795e1b17334406e28d77c3c5656
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477447"
---
# <a name="summarize-data"></a>Resumir Dados

Este artigo descreve um módulo de designer de Machine Learning Azure (pré-visualização).

Utilize o módulo Resumo dados para criar um conjunto de medidas estatísticas padrão que descrevem cada coluna na tabela de entrada.

As estatísticas sumárias são úteis quando se pretende compreender as características do conjunto completo de dados. Por exemplo, talvez precise de saber:

- Quantos valores faltam em cada coluna?
- Quantos valores únicos existem numa coluna de recurso?
- Qual é a média e o desvio padrão para cada coluna?

O módulo calcula as pontuações importantes para cada coluna e devolve uma linha de estatísticas sumárias para cada variável (coluna de dados) fornecida como entrada.

## <a name="how-to-configure-summarize-data"></a>Como configurar dados de resumo  

1. Adicione o módulo **Dados Resumo** ao seu pipeline. Pode encontrar este módulo na categoria **Funções Estatísticas** no designer.

1. Ligue o conjunto de dados para o qual pretende gerar um relatório.

    Se pretender reportar apenas algumas colunas, utilize as [Colunas Select no](select-columns-in-dataset.md) módulo Dataset para projetar um subconjunto de colunas para trabalhar.

1. Não são necessários parâmetros adicionais. Por padrão, o módulo analisa todas as colunas fornecidas como entrada, e dependendo do tipo de valores nas colunas, produz um conjunto de estatísticas relevante, conforme descrito na secção [Resultados.](#results)

1. Submeta o oleoduto.

## <a name="results"></a>Resultados

O relatório do módulo pode incluir as seguintes estatísticas. 

|Nome da coluna|Descrição|
|------|------|  
|**Funcionalidade**|Nome da coluna|
|**Contagem**|Conde de todas as linhas|
|**Contagem de valor único**|Número de valores únicos na coluna|
|**Contagem de valor em falta**|Número de valores únicos na coluna|
|**Min**|Valor mais baixo na coluna|  
|**Máximo**|Valor mais elevado na coluna|
|**Média**|Média de todos os valores da coluna|
|**Desvio médio**|Desvio médio dos valores da coluna|
|**1º Quartil**|Valor no primeiro quartil|
|**Mediano**|Valor mediano da coluna|
|**3º Quartil**|Valor no terceiro quartil|
|**Modo**|Modo de valores de coluna|
|**Alcance**|Inteiro representando o número de valores entre os valores máximo e mínimo|
|**Variação da amostra**|Variação para coluna; ver Nota|
|**Desvio padrão da amostra**|Desvio padrão para coluna; ver Nota|
|**Skewness da amostra**|Skewness para coluna; ver Nota|
|**Amostra kurtose**|Kurtose para coluna; ver Nota|
|**P0.5**|Percentil de 0,5%|
|**P1**|1% percentil|
|**P5**|Percentil de 5%|
|**P95**|95% percentil|
|**P99.5**|99,5% percentil |

## <a name="technical-notes"></a>Notas técnicas

- Para colunas não numéricas, apenas os valores para Conde, contagem de valor único e contagem de valor em falta são calculados. Para outras estatísticas, um valor nulo é devolvido.

- As colunas que contêm valores booleanos são processadas usando estas regras:

    - Ao calcular Min, um e lógico e é aplicado.
    
    - Ao calcular Max, um OR lógico é aplicado
    
    - Ao calcular Range, o módulo verifica primeiro se o número de valores únicos na coluna é igual a 2.
    
    - Ao calcular qualquer estatística que exija cálculos de pontoflutuante, os valores do True são tratados como 1.0, e os valores de Falso são tratados como 0.0.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning.  
