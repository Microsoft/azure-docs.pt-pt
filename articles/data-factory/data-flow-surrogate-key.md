---
title: Transformação chave de substituição no fluxo de dados de mapeamento
description: Como utilizar o fluxo de dados de mapeamento da Azure Data Factory Surrogate Key Transformation para gerar valores-chave sequenciais
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: ade2fd6011bbcdaed4ce31ce70bfb4235429bb0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81606303"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Transformação chave de substituição no fluxo de dados de mapeamento 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a transformação da chave de substituição para adicionar um valor chave de incremento a cada linha de dados. Isto é útil ao desenhar tabelas de dimensão num modelo de dados analíticos de esquema estelar. Num esquema estelar, cada membro nas suas tabelas de dimensão requer uma chave única que é uma chave não-negócio.

## <a name="configuration"></a>Configuração

![Transformação de chave de substituição](media/data-flow/surrogate.png "Transformação de chave de substituição")

**Coluna-chave:** O nome da coluna-chave de substituição gerada.

**Valor inicial:** O valor-chave mais baixo que será gerado.

## <a name="increment-keys-from-existing-sources"></a>Chaves incrementais de fontes existentes

Para iniciar a sua sequência a partir de um valor que existe numa fonte, use uma transformação de coluna derivada após a sua transformação chave de substituição para adicionar os dois valores juntos:

![SK adicionar Max](media/data-flow/sk006.png "Transformação da chave de substituição Adicionar Max")

### <a name="increment-from-existing-maximum-value"></a>Incremento do valor máximo existente

Para semear o valor chave com o max anterior, existem duas técnicas que pode usar com base no local onde estão os seus dados de origem.

#### <a name="database-sources"></a>Fontes de base de dados

Utilize uma opção de consulta SQL para selecionar MAX() da sua fonte. Por exemplo `Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![Consulta de chave de substituição](media/data-flow/sk002.png "Consulta de transformação de chave de substituição")

#### <a name="file-sources"></a>Fontes de arquivo

Se o seu valor máximo anterior estiver num ficheiro, utilize a `max()` função na transformação agregada para obter o valor máximo anterior:

![Ficheiro de chave de substituição](media/data-flow/sk008.png "Ficheiro de chave de substituição")

Em ambos os casos, deve juntar-se aos seus novos dados de entrada juntamente com a sua fonte que contém o valor máximo anterior.

![Chave de substituição Aderir](media/data-flow/sk004.png "Chave de substituição Aderir")

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Syntax

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Exemplo

![Transformação de chave de substituição](media/data-flow/surrogate.png "Transformação de chave de substituição")

O script de fluxo de dados para a configuração da chave de substituição acima está no corte de código abaixo.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Passos seguintes

Estes exemplos utilizam as transformações [da Coluna Unida](data-flow-join.md) e [Derivada.](data-flow-derived-column.md)
