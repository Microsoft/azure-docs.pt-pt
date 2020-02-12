---
title: 'Remover linhas duplicadas: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo remover linhas duplicadas no Azure Machine Learning para remover duplicatas potenciais de um conjunto de informações.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 429ddd62cccb8657aa18ec844968cc12df778f55
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153796"
---
# <a name="remove-duplicate-rows-module"></a>Remover o módulo de linhas duplicadas

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para remover duplicatas potenciais de um conjunto de uma.

Por exemplo, suponha que seus dados sejam semelhantes ao seguinte e representem vários registros para pacientes. 

| PatientID | Iniciais| Género|Idade|Verdade|
|----|----|----|----|----|
|1|F.M.| M| 53| Jan|
|2| F.A.M.| M| 53| Jan|
|3| F.A.M.| M| 24| Jan|
|3| F.M.| M| 24| Fev|
|4| F.M.| M| 23| Fev|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Claramente, este exemplo tem várias colunas com dados potencialmente duplicados. Se eles são, na verdade, duplicatas, depende de seu conhecimento dos dados. 

+ Por exemplo, você pode saber que muitos pacientes têm o mesmo nome. Não eliminaria duplicados usando colunas de nome, apenas a coluna de **identificação.** Dessa forma, somente as linhas com valores de ID duplicados são filtradas, independentemente de os pacientes terem o mesmo nome ou não.

+ Como alternativa, você pode optar por permitir duplicatas no campo ID e usar alguma outra combinação de arquivos para localizar registros exclusivos, como nome, sobrenome, idade e sexo.  

Para definir os critérios para se uma linha é ou não duplicada, especifice uma única coluna ou um conjunto de colunas para utilizar como **teclas**. Duas linhas só são consideradas duplicadas quando os valores em **todas as** colunas-chave são iguais. Se alguma linha tiver valor em falta para **as teclas,** não serão consideradas linhas duplicadas. Por exemplo, se sexo e idade forem definidos como chaves na tabela acima, a linha 6 e 7 não serão linhas duplicadas, dado que elas têm valor ausente na idade.

Quando você executa o módulo, ele cria um conjunto de registros candidato e retorna um conjunto de linhas que não possuem duplicatas no conjunto de colunas especificado.

> [!IMPORTANT]
> O conjunto de fonte de origem não é alterado; Esse módulo cria um novo conjunto de um que é filtrado para excluir duplicatas, com base nos critérios que você especificar.

## <a name="how-to-use-remove-duplicate-rows"></a>Como usar remover linhas duplicadas

1. Adicione o módulo ao seu pipeline. Pode encontrar o módulo **Remover Linhas Duplicadas** em transformação de **dados,** **manipulação**.  

2. Conecte o conjunto de registros que você deseja verificar em busca de linhas duplicadas.

3. No painel **Propriedades,** sob a expressão do filtro de **seleção da coluna Chave,** clique no seletor de colunas de **lançamento,** para escolher colunas para utilizar na identificação de duplicados.

    Neste contexto, **Key** não significa um identificador único. Todas as colunas selecionadas utilizando o Seletor de Colunas são designadas como colunas de **teclas**. Todas as colunas não selecionadas são consideradas colunas não chave. A combinação de colunas que você seleciona como chaves determina a exclusividade dos registros. (Imagine-o como uma instrução SQL que usa várias junções equalities.)

    Exemplos:

    + "Quero garantir que as IDs sejam exclusivas": escolha apenas a coluna ID.
    + "Desejo garantir que a combinação de nome, sobrenome e ID seja exclusiva": selecione todas as três colunas.

4. Utilize a primeira caixa de verificação **duplicada** para indicar qual a linha a devolver quando forem encontrados duplicados:

    + Se selecionado, a primeira linha será retornada e outras descartadas. 
    + Se você desmarcar essa opção, a última linha duplicada será mantida nos resultados e outras serão descartadas. 

5. Executar o pipeline.

6. Para rever os resultados, clique no módulo e **selecione Visualize**. 

> [!TIP]
> Se os resultados forem difíceis de entender, ou se pretender excluir algumas colunas de consideração, pode remover colunas utilizando as Colunas Select no módulo [Dataset.](./select-columns-in-dataset.md)

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 