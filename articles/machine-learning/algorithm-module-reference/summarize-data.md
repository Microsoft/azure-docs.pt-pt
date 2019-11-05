---
title: Resumir Dados
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo resumir dados no serviço Azure Machine Learning para gerar um relatório de estatísticas descritivas básico para as colunas em um conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 503eb533b83cbeedcc16b73a9c1dbe821a4d4d94
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492575"
---
# <a name="summarize-data"></a>Resumir Dados

Este artigo descreve um módulo do designer de Azure Machine Learning (versão prévia).

Use o módulo resumir dados para criar um conjunto de medidas estatísticas padrão que descrevem cada coluna na tabela de entrada.

As estatísticas de resumo são úteis quando você deseja entender as características do conjunto de todos. Por exemplo, talvez você precise saber:

- Quantos valores ausentes existem em cada coluna?
- Quantos valores exclusivos existem em uma coluna de recurso?
- Qual é a média e o desvio padrão de cada coluna?

O módulo calcula as pontuações importantes para cada coluna e retorna uma linha de estatísticas de resumo para cada variável (coluna de dados) fornecida como entrada.

## <a name="how-to-configure-summarize-data"></a>Como configurar dados de resumo  

1. Adicione o módulo **resumir dados** ao seu pipeline. Você pode encontrar esse módulo na categoria **funções estatísticas** no designer.

1. Conecte o conjunto de um para o qual você deseja gerar um relatório.

    Se você quiser relatar apenas algumas colunas, use o módulo [selecionar colunas no conjunto de DataSet](select-columns-in-dataset.md) para projetar um subconjunto de colunas com o qual trabalhar.

1. Nenhum parâmetro adicional é necessário. Por padrão, o módulo analisa todas as colunas fornecidas como entrada e, dependendo do tipo de valores nas colunas, gera um conjunto de estatísticas relevante, conforme descrito na seção [resultados](#results) .

1. Execute o pipeline ou clique com o botão direito do mouse no módulo e selecione **executar selecionado**.

## <a name="results"></a>Resultados

O relatório do módulo pode incluir as estatísticas a seguir. 

|Nome da coluna|Descrição|
|------|------|  
|**Funcionalidade**|Nome da coluna|
|**Contar**|Contagem de todas as linhas|
|**Contagem de valor exclusivo**|Número de valores exclusivos na coluna|
|**Contagem de valor ausente**|Número de valores exclusivos na coluna|
|**Min**|Valor mais baixo na coluna|  
|**Maximizar**|Valor mais alto na coluna|
|**Significa**|Média de todos os valores de coluna|
|**Desvio médio**|Desvio médio de valores de coluna|
|**Primeiro quartil**|Valor no primeiro quartil|
|**Cuja**|Valor da coluna mediana|
|**terceiro quartil**|Valor no terceiro quartil|
|**Moda**|Modo de valores de coluna|
|**Intervalo**|Inteiro que representa o número de valores entre os valores máximo e mínimo|
|**Variação de amostra**|Variação para a coluna; consulte a observação|
|**Desvio padrão de exemplo**|Desvio padrão para a coluna; consulte a observação|
|**Distorção de exemplo**|Distorção para a coluna; consulte a observação|
|**Curtose de amostra**|Curtose da coluna; consulte a observação|
|**P 0,5**|0,5% percentil|
|**P1**|1% percentil|
|**P5**|5% percentil|
|**P95**|95% percentil|
|**P 99,5**|99,5% percentil |

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço.  
