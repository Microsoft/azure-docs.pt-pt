---
title: Nova transformação do ramo de fluxo de dados de mapeamento de fábrica de dados do Azure
description: Nova transformação do ramo de fluxo de dados de mapeamento de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: af2225d749283c7124f89d5a7cd735b2f6bfd121
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792621"
---
# <a name="mapping-data-flow-union-transformation"></a>Transformação union do fluxo de dados de mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

União vai juntar vários fluxos de dados em um, com o SQL Union um desses fluxos como a nova saída da transformação Union. Do esquema de cada fluxo de entrada será possível combinar tudo dentro de seu fluxo de dados, sem a necessidade de ter uma chave de junção.

Pode combinar n-número de fluxos na tabela de definições ao selecionar o ícone de "+" junto a cada linha configurada, incluindo os dados de origem, bem como fluxos de transformações existentes no seu fluxo de dados.

![Transformação Union](media/data-flow/union.png "Union")

Neste caso, pode combinar diferentes metadados de várias origens (neste exemplo, três arquivos de origem diferente) e combiná-los num único fluxo:

![Descrição geral de transformação Union](media/data-flow/union111.png "Union 1")

Para conseguir isso, adicione linhas adicionais nas definições de União, incluindo a origem de todos os que pretende adicionar. Não é necessário para uma chave de pesquisa ou uma associação comuns:

![Definições de transformação Union](media/data-flow/unionsettings.png "Union definições")

Se definir uma transformação selecione após sua União, poderá mudar o nome sobrepostos campos ou campos que não foram com o nome de origens headerless. Clique em "Inspect" para ver os metadados de combinação com 132 colunas total neste exemplo de três origens diferentes:

![Transformação Union final](media/data-flow/union333.png "Union 3")

## <a name="name-and-position"></a>Nome e a posição

Ao escolher "union por nome de", cada valor de coluna irá largar na coluna correspondente de cada origem, com um novo esquema de metadados concatenada.

Se optar por "union pela posição", cada valor de coluna irá largar na posição original de cada origem correspondente, resultando num novo combinado fluxo de dados onde os dados de cada origem são adicionados no mesmo fluxo:

![Saída Union](media/data-flow/unionoutput.png "Union de saída")

## <a name="next-steps"></a>Passos Seguintes

Explore as transformações semelhantes, incluindo [aderir](data-flow-join.md) e [Exists](data-flow-exists.md).
