---
title: 'Remover linhas duplicadas: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Remover Linhas Duplicadas em Aprendizagem automática Azure para remover potenciais duplicados de um conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 490d3305abcbcd906a0f727d736db8cab7e4287e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79456026"
---
# <a name="remove-duplicate-rows-module"></a>Remover módulo De Linhas Duplicadas

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para remover potenciais duplicados de um conjunto de dados.

Por exemplo, assuma que os seus dados se parecem com os seguintes, e representa vários registos para os pacientes. 

| PatientID | Iniciais| Género|Idade|Admitido|
|----|----|----|----|----|
|1|F.M.| M| 53| Jan|
|2| F.A.M.| M| 53| Jan|
|3| F.A.M.| M| 24| Jan|
|3| F.M.| M| 24| Feb|
|4| F.M.| M| 23| Feb|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Claramente, este exemplo tem múltiplas colunas com dados potencialmente duplicados. Se são realmente duplicados depende do seu conhecimento dos dados. 

+ Por exemplo, pode saber que muitos pacientes têm o mesmo nome. Não eliminaria duplicados usando colunas de nome, apenas a coluna de **identificação.** Desta forma, apenas as filas com valores de IDENTIFICAção duplicados são filtradas, independentemente de os pacientes terem ou não o mesmo nome.

+ Em alternativa, pode decidir permitir duplicados no campo de IDENTIFICAção e utilizar alguma outra combinação de ficheiros para encontrar registos únicos, como primeiro nome, apelido, idade e género.  

Para definir os critérios para se uma linha é ou não duplicada, especifice uma única coluna ou um conjunto de colunas para utilizar como **teclas**. Duas linhas só são consideradas duplicadas quando os valores em **todas as** colunas-chave são iguais. Se alguma linha tiver valor em falta para **as teclas,** não serão consideradas linhas duplicadas. Por exemplo, se o género e a idade forem definidos como Chaves na tabela acima, as linhas 6 e 7 não são linhas duplicadas dado que têm valor em falta na Idade.

Quando executa o módulo, cria um conjunto de dados de candidatos e devolve um conjunto de linhas que não têm duplicados através do conjunto de colunas que especificou.

> [!IMPORTANT]
> O conjunto de dados de origem não é alterado; este módulo cria um novo conjunto de dados que é filtrado para excluir duplicados, com base nos critérios que especifica.

## <a name="how-to-use-remove-duplicate-rows"></a>Como utilizar remover linhas duplicadas

1. Adicione o módulo ao seu oleoduto. Pode encontrar o módulo **Remover Linhas Duplicadas** em transformação de **dados,** **manipulação**.  

2. Ligue o conjunto de dados que pretende verificar para as linhas duplicadas.

3. No painel **Propriedades,** sob a expressão do filtro de **seleção da coluna Chave,** clique no seletor de colunas de **lançamento,** para escolher colunas para utilizar na identificação de duplicados.

    Neste contexto, **Key** não significa um identificador único. Todas as colunas selecionadas utilizando o Seletor de Colunas são designadas como colunas de **teclas**. Todas as colunas não selecionadas são consideradas colunas não-chave. A combinação de colunas que seleciona como teclas determina a singularidade dos registos. (Pense nisso como uma declaração SQL que usa múltiplas igualdades.)

    Exemplos:

    + "Quero garantir que as identidades são únicas": Escolha apenas a coluna de IDENTIFICAÇÃO.
    + "Quero garantir que a combinação de primeiro nome, apelido e ID é única": Selecione as três colunas.

4. Utilize a primeira caixa de verificação **duplicada** para indicar qual a linha a devolver quando forem encontrados duplicados:

    + Se selecionado, a primeira linha é devolvida e outras descartadas. 
    + Se desverificar esta opção, a última linha duplicada é mantida nos resultados e outras são descartadas. 

5. Submeta o oleoduto.

6. Para rever os resultados, clique no módulo e **selecione Visualize**. 

> [!TIP]
> Se os resultados forem difíceis de entender, ou se pretender excluir algumas colunas de consideração, pode remover colunas utilizando as Colunas Select no módulo [Dataset.](./select-columns-in-dataset.md)

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 