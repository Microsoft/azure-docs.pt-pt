---
title: Nova transformação do ramo de fluxo de dados de mapeamento de fábrica de dados do Azure
description: Nova transformação do ramo de fluxo de dados de mapeamento de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 1eebc879ad56ba4f35e6a8a1b857ae877a6a2f01
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726265"
---
# <a name="mapping-data-flow-union-transformation"></a>Transformação union do fluxo de dados de mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

União vai juntar vários fluxos de dados em um, com o SQL Union um desses fluxos como a nova saída da transformação Union. Do esquema de cada fluxo de entrada será possível combinar tudo dentro de seu fluxo de dados, sem a necessidade de ter uma chave de junção.

Pode combinar n-número de fluxos na tabela de definições ao selecionar o ícone de "+" junto a cada linha configurado.

![Transformação Union](media/data-flow/union.png "Union")

Neste caso, pode combinar diferentes metadados de várias origens (neste exemplo, três arquivos de origem diferente) e combiná-los num único fluxo:

![Descrição geral de transformação Union](media/data-flow/union111.png "Union 1")

Para conseguir isso, adicione linhas adicionais nas definições de União, incluindo a origem de todos os que pretende adicionar. Não é necessário para uma chave de pesquisa ou uma associação comuns:

![Definições de transformação Union](media/data-flow/unionsettings.png "Union definições")

Se definir uma transformação selecione após sua União, poderá mudar o nome sobrepostos campos ou campos que não foram com o nome de origens headerless. Clique em "Inspect" para ver os metadados de combinação com 132 colunas total neste exemplo de três origens diferentes:

![Transformação Union final](media/data-flow/union333.png "Union 3")
