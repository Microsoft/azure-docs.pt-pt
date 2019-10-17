---
title: Transformação de Azure Data Factory mapeamento de fluxo de dados novo Branch
description: Transformação de Azure Data Factory mapeamento de fluxo de dados novo Branch
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 4832cd2036f615d1e90d5e7a21c1a9832c2fa837
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387138"
---
# <a name="mapping-data-flow-union-transformation"></a>Mapeando a transformação de União de fluxo de dados



Union combinará vários fluxos de dados em um, com a União SQL desses fluxos como a nova saída da transformação Union. Todo o esquema de cada fluxo de entrada será combinado dentro do fluxo de dados, sem a necessidade de ter uma chave de junção.

Você pode combinar n-Number de fluxos na tabela de configurações selecionando o ícone "+" ao lado de cada linha configurada, incluindo dados de origem, bem como fluxos de transformações existentes em seu fluxo de dados.

![Transformação Union](media/data-flow/union.png "União")

Nesse caso, você pode combinar metadados distintos de várias fontes (neste exemplo, três arquivos de origem diferentes) e combiná-los em um único fluxo:

![Visão geral da transformação Union](media/data-flow/union111.png "União 1")

Para conseguir isso, adicione linhas adicionais nas configurações de União, incluindo todas as fontes que você deseja adicionar. Não há necessidade de uma pesquisa comum ou chave de junção:

![Configurações de transformação de União](media/data-flow/unionsettings.png "Configurações de União")

Se você definir uma transformação selecionar após sua União, você poderá renomear campos ou campos sobrepostos que não foram nomeados de fontes sem cabeçalho. Clique em "inspecionar" para ver a combinação de metadados com o total de 132 colunas neste exemplo de três fontes diferentes:

![Transformação Union final](media/data-flow/union333.png "União 3")

## <a name="name-and-position"></a>Nome e posição

Quando você escolhe "Union by Name", cada valor de coluna será inserido na coluna correspondente de cada fonte, com um novo esquema de metadados concatenado.

Se você escolher "Union por posição", cada valor de coluna será inserido na posição original de cada fonte correspondente, resultando em um novo fluxo combinado de dados em que os dados de cada fonte são adicionados ao mesmo fluxo:

![Saída de União](media/data-flow/unionoutput.png "Saída de União")

## <a name="next-steps"></a>Passos seguintes

Explore transformações semelhantes, incluindo [Join](data-flow-join.md) e [Exists](data-flow-exists.md).
