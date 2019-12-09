---
title: Mapeando a transformação de chave substituta do fluxo de dados
description: Como usar a transformação de chave substituta do fluxo de dados de mapeamento de Azure Data Factory para gerar valores de chave sequencial
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: bab48aa9079c1b8020bb828a6bb91bd244a78cf1
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930207"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Mapeando a transformação de chave substituta do fluxo de dados



Use a transformação chave substituta para adicionar um valor de chave arbitrária de não comercial ao seu conjunto de linhas de fluxo de dados. Isso é útil ao criar tabelas de dimensões em um modelo de dados analíticos de esquema em estrela, em que cada membro em suas tabelas de dimensões precisa ter uma chave exclusiva que seja uma chave que não seja de negócios, parte da metodologia Kimball DW.

![Transformação de chave substituta](media/data-flow/surrogate.png "Transformação de chave substituta")

"Coluna de chave" é o nome que você dará à sua nova coluna de chave substituta.

"Valor inicial" é o ponto inicial do valor incremental.

## <a name="increment-keys-from-existing-sources"></a>Incrementar chaves de fontes existentes

Se você quiser iniciar sua sequência a partir de um valor que existe em uma fonte, poderá usar uma transformação de coluna derivada imediatamente após a transformação de chave substituta e adicionar os dois valores juntos:

![SK Add Max](media/data-flow/sk006.png "Adição máxima da transformação de chave substituta")

Para propagar o valor de chave com o máximo anterior, há duas técnicas que você pode usar:

### <a name="database-sources"></a>Fontes de banco de dados

Use a opção "consulta" para selecionar MAX () de sua origem usando a transformação origem:

![Consulta de chave substituta](media/data-flow/sk002.png "Consulta de transformação chave substituta")

### <a name="file-sources"></a>Fontes de arquivo

Se o valor máximo anterior estiver em um arquivo, você poderá usar sua transformação de origem junto com uma transformação agregação e usar a função de expressão MAX () para obter o valor máximo anterior:

![Arquivo de chave substituta](media/data-flow/sk008.png "Arquivo de chave substituta")

Em ambos os casos, você deve unir seus novos dados de entrada junto com sua fonte que contém o valor máximo anterior:

![Junção de chave substituta](media/data-flow/sk004.png "Junção de chave substituta")

## <a name="next-steps"></a>Passos seguintes

Esses exemplos usam as transformações de [junção](data-flow-join.md) e [coluna derivada](data-flow-derived-column.md) .
