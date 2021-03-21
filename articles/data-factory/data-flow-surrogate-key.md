---
title: Transformação chave de substituição no fluxo de dados de mapeamento
description: Como utilizar o fluxo de dados de mapeamento da Azure Data Factory Surrogate Key Transformation para gerar valores-chave sequenciais
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/30/2020
ms.openlocfilehash: d1f8993b1adc297b1bfadba114df76a66e59afa2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93147189"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Transformação chave de substituição no fluxo de dados de mapeamento 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a transformação da chave de substituição para adicionar um valor chave de incremento a cada linha de dados. Isto é útil ao desenhar tabelas de dimensão num modelo de dados analíticos de esquema estelar. Num esquema estelar, cada membro nas suas tabelas de dimensão requer uma chave única que é uma chave não-negócio.

## <a name="configuration"></a>Configuração

![Transformação de chave de substituição](media/data-flow/surrogate.png "Transformação de chave de substituição")

**Coluna-chave:** O nome da coluna-chave de substituição gerada.

**Valor inicial:** O valor-chave mais baixo que será gerado.

## <a name="increment-keys-from-existing-sources"></a>Chaves incrementais de fontes existentes

Para iniciar a sua sequência a partir de um valor que existe numa fonte, recomendamos usar um lavatório de cache para poupar esse valor e usar uma transformação de coluna derivada para adicionar os dois valores juntos. Utilize uma procura em cache para obter a saída e apdicê-la à chave gerada. Para mais informações, saiba sobre [lavatórios](data-flow-sink.md#cache-sink) de cache e [pesquisas em cache.](concepts-data-flow-expression-builder.md#cached-lookup)

![Lookup de chave de substituição](media/data-flow/cached-lookup-example.png "Lookup de chave de substituição")

### <a name="increment-from-existing-maximum-value"></a>Incremento do valor máximo existente

Para semear o valor chave com o max anterior, existem duas técnicas que pode usar com base no local onde estão os seus dados de origem.

#### <a name="database-sources"></a>Fontes de base de dados

Utilize uma opção de consulta SQL para selecionar MAX() da sua fonte. Por exemplo, `Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`.

![Consulta de chave de substituição](media/data-flow/surrogate-key-max-database.png "Consulta de transformação de chave de substituição")

#### <a name="file-sources"></a>Fontes de arquivo

Se o seu valor máximo anterior estiver num ficheiro, utilize a `max()` função na transformação agregada para obter o valor máximo anterior:

![Ficheiro de chave de substituição](media/data-flow/surrogate-key-max-file.png "Ficheiro de chave de substituição")

Em ambos os casos, terá de escrever para uma pia de cache e procurar o valor. 


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
