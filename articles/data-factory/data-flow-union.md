---
title: Mapeamento de processamento de fluxo de dados da união
description: Dados de mapeamento da Fábrica de Dados azure fluem transformação de novo ramo
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: adba1eb61676dbebcb356490b14b279ebe69c644
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930152"
---
# <a name="azure-data-factory-mapping-data-flow-union-transformation"></a>Azure Data Factory mapeando a transformação da união de dados

A União combinará múltiplos fluxos de dados num só, com a União SQL desses fluxos como a nova produção da transformação da União. Todo o esquema de cada fluxo de entrada será combinado dentro do fluxo de dados, sem necessidade de ter uma chave de adesão.

Pode combinar n-number de streams na tabela de definições selecionando o ícone "+" ao lado de cada linha configurada, incluindo tanto os dados de origem como os fluxos de transformações existentes no fluxo de dados.

![Transformação da União](media/data-flow/union.png "União")

Neste caso, pode combinar metadados díspares de várias fontes (neste exemplo, três ficheiros de origem diferentes) e combiná-los num único fluxo:

![Visão geral da transformação da união](media/data-flow/union111.png "União 1")

Para tal, adicione linhas adicionais nas Definições da União, incluindo todas as fontes que pretende adicionar. Não há necessidade de uma procura comum ou chave de adesão:

![Configurações de transformação da união](media/data-flow/unionsettings.png "Definições sindicais")

Se definir uma transformação Select após a sua União, poderá renomear campos ou campos sobrepostos que não foram nomeados a partir de fontes sem cabeça. Clique em "Inspecionar" para ver os metadados combinados com 132 colunas totais neste exemplo de três fontes diferentes:

![Final da transformação da União](media/data-flow/union333.png "União 3")

## <a name="name-and-position"></a>Nome e posição

Quando escolher "união por nome", cada valor da coluna cairá na coluna correspondente de cada fonte, com um novo esquema de metadados concatenados.

Se escolher "união por posição", cada valor da coluna cairá na posição original de cada fonte correspondente, resultando num novo fluxo combinado de dados onde os dados de cada fonte são adicionados ao mesmo fluxo:

![Produção sindical](media/data-flow/unionoutput.png "Saída da União")

## <a name="next-steps"></a>Passos seguintes

Explore transformações semelhantes, incluindo [Join](data-flow-join.md) and [Exists.](data-flow-exists.md)
