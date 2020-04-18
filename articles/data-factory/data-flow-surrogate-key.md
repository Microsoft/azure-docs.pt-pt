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
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606303"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Transformação chave de substituição no fluxo de dados de mapeamento 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a transformação da chave de substituição para adicionar um valor-chave incrementante a cada linha de dados. Isto é útil ao desenhar tabelas de dimensão num modelo de dados analíticos de esquema sino de estrela. Num esquema estrela, cada membro nas tabelas de dimensão requer uma chave única que é uma chave não comercial.

## <a name="configuration"></a>Configuração

![Transformação da chave de substituição](media/data-flow/surrogate.png "Transformação da chave de substituição")

**Coluna de chaves:** O nome da coluna de chaves de substituição gerada.

**Valor de início:** O valor-chave mais baixo que será gerado.

## <a name="increment-keys-from-existing-sources"></a>Teclas incrementais de fontes existentes

Para iniciar a sua sequência a partir de um valor que existe numa fonte, utilize uma transformação de coluna derivada após a transformação da chave de substituição para adicionar os dois valores em conjunto:

![SK adicionar Max](media/data-flow/sk006.png "Transformação de chave de substituição Adicionar Max")

### <a name="increment-from-existing-maximum-value"></a>Incremento do valor máximo existente

Para semear o valor-chave com o max anterior, existem duas técnicas que pode utilizar com base no local onde estão os seus dados de origem.

#### <a name="database-sources"></a>Fontes de base de dados

Utilize uma opção de consulta SQL para selecionar MAX() da sua fonte. Por exemplo`Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![Consulta de chave de substituição](media/data-flow/sk002.png "Consulta de transformação de chaves de substituição")

#### <a name="file-sources"></a>Fontes de ficheiros

Se o seu valor máximo anterior `max()` estiver num ficheiro, utilize a função na transformação agregada para obter o valor máximo anterior:

![Ficheiro chave de substituição](media/data-flow/sk008.png "Ficheiro chave de substituição")

Em ambos os casos, deve juntar-se aos seus novos dados de entrada juntamente com a sua fonte que contenha o valor máximo anterior.

![Chave de aluguer Aderir](media/data-flow/sk004.png "Chave de aluguer Aderir")

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Exemplo

![Transformação da chave de substituição](media/data-flow/surrogate.png "Transformação da chave de substituição")

O script de fluxo de dados para a configuração da chave de substituição acima está no código abaixo.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Passos seguintes

Estes exemplos utilizam as transformações da [Coluna Desanato e Derivada.](data-flow-derived-column.md) [Join](data-flow-join.md)
