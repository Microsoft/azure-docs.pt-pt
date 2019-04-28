---
title: Mapeamento de transformação chave de substituição do fluxo de dados de fábrica de dados do Azure
description: Como utilizar o mapeamento de fluxo de substitutos chave de transformação Azure Data Factory de dados para gerar valores de chave sequenciais
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: eaa1c577f7e208400d3430222b006e0dbbd7956a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61350622"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Mapeamento de transformação chave de substituição do fluxo de dados

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilize a transformação de chave de substituição para adicionar um valor chave arbitrário não comerciais incrementação para o seu conjunto de linhas de fluxo de dados. Isto é útil ao criar tabelas de dimensões de um modelo de dados analíticos de esquema em estrela onde cada membro das tabelas de dimensão tem de ter uma chave exclusiva que é parte fundamental, não comerciais da metodologia Kimball armazém de dados.

![Transformação de chave de substituição](media/data-flow/surrogate.png "transformação de chave de substituição")

"Coluna de chave" é o nome que vai dar para a nova coluna de chave de substituição.

"Começar o valor" é o ponto de início do valor incremental.

## <a name="increment-keys-from-existing-sources"></a>Chaves de incremento de fontes existentes

Se quiser iniciar a sequência de um valor de que existe numa origem, pode usar uma transformação Derived Column imediatamente a seguir a sua transformação de chave de substituição e juntar os dois valores:

![Máx. de adicionar SK](media/data-flow/sk006.png "Max adicionar transformação chave substituta")

Para efetuar o seeding o valor da chave com o máximo anterior, existem duas técnicas que pode utilizar:

### <a name="database-sources"></a>Origens de base de dados

Utilize a opção de "Consulta" selecionar MAX() da sua origem usando a transformação de origem:

![Consulta de chave de substituição](media/data-flow/sk002.png "consulta de transformação de chave de substituição")

### <a name="file-sources"></a>Origens de ficheiros

Se o valor máximo anterior está num arquivo, pode utilizar a sua transformação de origem, juntamente com uma transformação de agregação e usar a função de expressão de MAX() para obter o valor máximo anterior:

![Ficheiro de chave de substituição](media/data-flow/sk008.png "ficheiro de chave de substituição")

Em ambos os casos, tem de associar o seu recebidos novos dados, juntamente com a sua origem que contém o valor máximo anterior:

![Associação de chave de substituição](media/data-flow/sk004.png "associação de chave de substituição")

## <a name="next-steps"></a>Passos Seguintes

Estes exemplos utilizam o [aderir](data-flow-join.md) e [Derived Column](data-flow-derived-column.md) transformações.
