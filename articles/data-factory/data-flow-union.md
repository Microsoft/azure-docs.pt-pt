---
title: Transformação da União no fluxo de dados de mapeamento
description: Azure Data Factory mapeamento dados fluxo nova transformação de ramo
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/27/2020
ms.openlocfilehash: e10418c18e6f8e0089a893e9d33ee1081f3131f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91823642"
---
# <a name="union-transformation-in-mapping-data-flow"></a>Transformação da União no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A União combinará vários fluxos de dados num só, com a União SQL desses fluxos como a nova produção da transformação da União. Todo o esquema de cada fluxo de entrada será combinado dentro do seu fluxo de dados, sem necessidade de ter uma chave de união.

Pode combinar n-número de streams na tabela de definições selecionando o ícone "+" ao lado de cada linha configurada, incluindo tanto dados de origem como fluxos de transformações existentes no fluxo de dados.

Aqui está um pequeno vídeo de passagem da transformação sindical no fluxo de dados de mapeamento da ADF:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vngz]

![Transformação da União](media/data-flow/union.png "União")

Neste caso, pode combinar metadados díspares de múltiplas fontes (neste exemplo, três ficheiros de origem diferentes) e combiná-los num único fluxo:

![Visão geral da transformação da União](media/data-flow/union111.png "União 1")

Para tal, adicione linhas adicionais nas Definições da União, incluindo todas as fontes que pretende adicionar. Não há necessidade de uma procura comum ou de uma chave de junção:

![Configurações de transformação da União](media/data-flow/unionsettings.png "Configurações da União")

Se definir uma transformação Select após a sua União, poderá renomear campos ou campos sobrepostos que não foram nomeados de fontes sem cabeça. Clique em "Inspecionar" para ver os metadados combinados com 132 colunas totais neste exemplo a partir de três fontes diferentes:

![Final de transformação da União](media/data-flow/union333.png "União 3")

## <a name="name-and-position"></a>Nome e posição

Quando escolher "união por nome", cada valor de coluna cairá na coluna correspondente a partir de cada fonte, com um novo esquema de metadados concatenados.

Se escolher "união por posição", cada valor de coluna cairá na posição original de cada fonte correspondente, resultando num novo fluxo combinado de dados onde os dados de cada fonte são adicionados ao mesmo fluxo:

![Produção sindical](media/data-flow/unionoutput.png "Produção da União")

## <a name="next-steps"></a>Passos seguintes

Explore transformações semelhantes, incluindo [Join](data-flow-join.md) and [Exists.](data-flow-exists.md)
