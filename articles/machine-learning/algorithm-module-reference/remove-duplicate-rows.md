---
title: 'Remova linhas duplicadas: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de remover linhas duplicadas no serviço Azure Machine Learning para remover duplicatas potenciais de um conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: dce90d911085c1f7330a2e0952bb9576c1d765fa
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029299"
---
# <a name="remove-duplicate-rows-module"></a>Remover o módulo de duplicar linhas

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para remover duplicatas potenciais de um conjunto de dados.

Por exemplo, suponha seus dados é semelhante ao seguinte e representa vários registos dos doentes. 

| PatientID | Iniciais| Género|Idade|Admitiram|
|----|----|----|----|----|
|1|F.M.| M| 53| Jan.|
|2| F.A.M.| M| 53| Jan.|
|3| F.A.M.| M| 24| Jan.|
|3| F.M.| M| 24| Fev.|
|4| F.M.| M| 23| Fev.|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Claramente, este exemplo tem várias colunas com dados potencialmente duplicados. Se eles são duplicados, na verdade, depende do seu conhecimento dos dados. 

+ Por exemplo, pode saber que muitas pacientes têm o mesmo nome. Não elimina duplicados com quaisquer colunas de nome, apenas os **ID** coluna. Dessa forma, apenas as linhas com valores duplicados de ID são filtradas, independentemente se os pacientes têm o mesmo nome ou não.

+ Em alternativa, pode optar por permitir duplicatas no campo de ID e utilizar outras combinações de ficheiros para encontrar registros exclusivos, como o nome próprio, último nome, idade e género.  

Para definir os critérios para uma linha é ou não um duplicado, tem de especificar uma única coluna ou um conjunto de colunas para utilizar como **chaves**. Duas linhas são consideradas duplicados apenas quando os valores na **todos os** colunas de chave são iguais. Se qualquer linha tem um valor em falta para **chaves**, eles não serão considerados linhas duplicadas. Por exemplo, se a idade e género são definidas como chaves na tabela, linha 6 e 7 acima não são linhas duplicadas tendo em conta têm valor em falta na idade.

Ao executar o módulo, ele cria um conjunto de dados do Release candidate e devolve um conjunto de linhas que têm não contém duplicados entre o conjunto de colunas especificado.

> [!IMPORTANT]
> O conjunto de dados de origem não é alterado; Este módulo cria um novo conjunto de dados, que é filtrado para excluir duplicatas, com base nos critérios que especificar.

## <a name="how-to-use-remove-duplicate-rows"></a>Como utilizar o remover linhas duplicado

1. Adicione o módulo à sua experimentação. Pode encontrar os **remover linhas de duplicar** módulo a ser **transformação de dados**, **manipulação**.  

2. Ligue-se o conjunto de dados que pretende verificar as linhas duplicadas.

3. Na **propriedades** painel, em **expressão de filtro de seleção de coluna da chave**, clique em **iniciar Seletor de colunas**, para escolher colunas para utilizar na identificação de duplicatas.

    Neste contexto, **chave** não significa um identificador exclusivo. Todas as colunas que selecione usando o Seletor de colunas são designadas como **colunas de chave**. Todas as colunas não selecionadas são consideradas colunas sem chave. A combinação de colunas que selecionou como chaves determina a exclusividade dos registos. (Considerá-lo como uma instrução SQL que usa vários associações de equalities.)

    Exemplos:

    + "Quero garantir que as IDs são exclusivos": Escolha apenas a coluna de ID.
    + "Quero garantir que a combinação do nome próprio, apelido e ID é o única": Selecione todas as três colunas.

4. Utilize o **manter a primeira linha duplicada** caixa de verificação para indicar que a linha a devolver quando forem encontradas duplicatas:

    + Se selecionado, a primeira linha é retornada e outras pessoas é rejeitada. 
    + Se desmarcar esta opção, a última linha duplicada é mantida nos resultados e outros são eliminados. 

5. Execute a experimentação.

6. Para rever os resultados, o módulo com o botão direito, selecione **conjunto de dados de resultados**e clique em **Visualize**. 

> [!TIP]
> Se os resultados são difíceis de compreender ou se pretende excluir algumas colunas de consideração, pode remover colunas, utilizando o [Select Columns in Dataset](./select-columns-in-dataset.md) módulo.

## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 