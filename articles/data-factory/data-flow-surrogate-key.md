---
title: Mapeamento fluxo de dados Surrogate Key Transformation
description: Como utilizar o fluxo de dados de mapeamento da Azure Data Factory Surrogate Key Transformation para gerar valores-chave sequenciais
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: bab48aa9079c1b8020bb828a6bb91bd244a78cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930207"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Mapeamento fluxo de dados Surrogate Key Transformation



Utilize a Transformação da Chave de Substituição para adicionar um valor-chave arbitrário não empresarial incrementante ao conjunto de linhas de fluxo de dados. Isto é útil ao desenhar tabelas de dimensão num modelo de dados analíticos de esquemas de estrela, onde cada membro nas tabelas de dimensão precisa de ter uma chave única que é uma chave não-empresarial, parte da metodologia Kimball DW.

![Transformação da chave de substituição](media/data-flow/surrogate.png "Transformação da chave de substituição")

"Coluna chave" é o nome que dará à sua nova coluna de chaves de substituição.

"Valor de Início" é o ponto de partida do valor incremental.

## <a name="increment-keys-from-existing-sources"></a>Teclas incrementais de fontes existentes

Se quiser iniciar a sua sequência a partir de um valor que existe numa Fonte, pode utilizar uma transformação da Coluna Derivada imediatamente após a transformação da chave de substituição e adicionar os dois valores em conjunto:

![SK adicionar Max](media/data-flow/sk006.png "Transformação de chave de substituição Adicionar Max")

Para semear o valor-chave com o max anterior, existem duas técnicas que pode utilizar:

### <a name="database-sources"></a>Fontes de base de dados

Utilize a opção "Consulta" para selecionar MAX() da sua fonte utilizando a transformação fonte:

![Consulta de chave de substituição](media/data-flow/sk002.png "Consulta de transformação de chaves de substituição")

### <a name="file-sources"></a>Fontes de ficheiros

Se o seu valor máximo anterior estiver num ficheiro, pode utilizar a sua transformação Source juntamente com uma transformação agregada e utilizar a função de expressão MAX() para obter o valor máximo anterior:

![Ficheiro chave de substituição](media/data-flow/sk008.png "Ficheiro chave de substituição")

Em ambos os casos, deve juntar-se aos seus novos dados de entrada juntamente com a sua fonte que contenha o valor máximo anterior:

![Chave de aluguer Aderir](media/data-flow/sk004.png "Chave de aluguer Aderir")

## <a name="next-steps"></a>Passos seguintes

Estes exemplos utilizam as transformações da [Coluna Desanato e Derivada.](data-flow-derived-column.md) [Join](data-flow-join.md)
