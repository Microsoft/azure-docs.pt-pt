---
title: 'Remover Linhas Duplicadas: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Remove Duplicate Rows em Azure Machine Learning para remover potenciais duplicados de um conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: bf35d08128aa8a3e8f545ed7184866694219f2cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90905223"
---
# <a name="remove-duplicate-rows-module"></a>Remover módulo de Linhas Duplicadas

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para remover potenciais duplicados de um conjunto de dados.

Por exemplo, assuma que os seus dados se parecem com os seguintes, e representa vários registos para pacientes. 

| PatientID | Iniciais| Sexo|Idade|Admitido|
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

Claramente, este exemplo tem várias colunas com dados potencialmente duplicados. Se são realmente duplicados depende do seu conhecimento dos dados. 

+ Por exemplo, pode saber que muitos pacientes têm o mesmo nome. Não eliminaria duplicados usando colunas de nome, apenas a coluna **de identificação.** Desta forma, apenas as linhas com valores de ID duplicados são filtradas, independentemente de os pacientes terem ou não o mesmo nome.

+ Em alternativa, pode decidir permitir duplicados no campo ID e utilizar outra combinação de ficheiros para encontrar registos únicos, como o primeiro nome, apelido, idade e género.  

Para definir os critérios para se uma linha é duplicada ou não, especifique uma única coluna ou um conjunto de colunas para utilizar como **teclas**. Duas linhas são consideradas duplicadas apenas quando os valores em **todas as** colunas-chave são iguais. Se alguma linha tiver valor em falta para **as teclas,** não serão consideradas linhas duplicadas. Por exemplo, se o Sexo e a Idade forem definidos como Chaves acima da tabela, as linhas 6 e 7 não são linhas duplicadas dado que têm valor em falta na Idade.

Quando executam o módulo, cria um conjunto de dados de candidatos e devolve um conjunto de linhas que não têm duplicados em todo o conjunto de colunas especificadas.

> [!IMPORTANT]
> O conjunto de dados de origem não é alterado; este módulo cria um novo conjunto de dados que é filtrado para excluir duplicados, com base nos critérios especificados.

## <a name="how-to-use-remove-duplicate-rows"></a>Como utilizar remover linhas duplicadas

1. Adicione o módulo ao seu oleoduto. Pode encontrar o módulo **Remover Linhas Duplicadas** em **Transformação de Dados,** **Manipulação**.  

2. Ligue o conjunto de dados que pretende verificar se há linhas duplicadas.

3. No painel **Propriedades,** sob **a expressão do filtro de seleção de colunas Chave,** clique no seletor de **colunas de lançamento,** para escolher colunas a utilizar na identificação de duplicados.

    Neste contexto, **Key** não significa um identificador único. Todas as colunas que selecionar utilizando o Seletor de Colunas são designadas como **colunas-chave**. Todas as colunas não selecionadas são consideradas colunas não-chave. A combinação de colunas que seleciona como teclas determina a singularidade dos registos. (Pense nisso como uma declaração SQL que usa múltiplas igualdades.)

    Exemplos:

    + "Quero garantir que os IDs são únicos": Escolha apenas a coluna ID.
    + "Quero garantir que a combinação de nome próprio, apelido e ID é única": Selecione as três colunas.

4. Utilize a primeira caixa de verificação **de linha duplicada** para indicar qual a linha a devolver quando forem encontradas duplicações:

    + Se selecionada, a primeira linha é devolvida e outras descartadas. 
    + Se desmarcar esta opção, a última linha duplicada é mantida nos resultados e outras são descartadas. 

5. Envie o oleoduto.

6. Para rever os resultados, clique com o botão direito no módulo e **selecione Visualize**. 

> [!TIP]
> Se os resultados forem difíceis de entender, ou se pretender excluir algumas colunas de consideração, pode remover colunas utilizando o módulo [Select Columns in Dataset.](./select-columns-in-dataset.md)

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 