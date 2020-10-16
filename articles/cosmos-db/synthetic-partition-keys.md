---
title: Crie uma chave de partição sintética em Azure Cosmos DB
description: Saiba como usar chaves de partição sintéticas nos seus recipientes Azure Cosmos para distribuir os dados e a carga de trabalho uniformemente através das teclas de partição
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: e8786c2d6e93c18a5bf9856a5555d6b528f842c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75441226"
---
# <a name="create-a-synthetic-partition-key"></a>Criar uma chave de partição sintética

É a melhor prática ter uma chave de partição com muitos valores distintos, como centenas ou milhares. O objetivo é distribuir os seus dados e carga de trabalho uniformemente através dos itens associados a estes valores-chave de partição. Se tal propriedade não existir nos seus dados, pode construir uma *chave de partição sintética.* Este documento descreve várias técnicas básicas para gerar uma chave de partição sintética para o seu recipiente Cosmos.

## <a name="concatenate-multiple-properties-of-an-item"></a>Concatenate várias propriedades de um item

Você pode formar uma chave de partição, concatenando vários valores de propriedade em uma única `partitionKey` propriedade artificial. Estas chaves são referidas como chaves sintéticas. Por exemplo, considere o seguinte exemplo:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Para o documento anterior, uma opção é definir /deviceId ou /data como a chave de partição. Utilize esta opção, se pretender dividir o seu recipiente com base no ID ou na data do dispositivo. Outra opção é concatenar estes dois valores numa propriedade sintética `partitionKey` que é usada como chave de partição.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Em cenários em tempo real, pode ter milhares de itens numa base de dados. Em vez de adicionar a chave sintética manualmente, defina a lógica do lado do cliente para valores concatenados e insira a chave sintética nos itens nos seus recipientes Cosmos.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Use uma chave de partição com um sufixo aleatório

Outra estratégia possível para distribuir a carga de trabalho de forma mais uniforme é anexar um número aleatório no final do valor chave da partição. Quando distribuir itens desta forma, pode efetuar operações de escrita paralelas através de divisórias.

Um exemplo é se uma chave de partição representa uma data. Pode escolher um número aleatório entre 1 e 400 e concatená-lo como um sufixo até à data. Este método resulta em valores-chave de partição como  `2018-08-09.1` , e assim por `2018-08-09.2` diante, através de  `2018-08-09.400` . Como aleatoriamente a chave de partição, as operações de escrita no recipiente em cada dia são distribuídas uniformemente por várias divisórias. Este método resulta num melhor paralelismo e maior produção geral.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Use uma chave de partição com sufixos pré-calculados 

A estratégia de sufixo aleatório pode melhorar muito a produção de escrita, mas é difícil ler um item específico. Não sabes o valor do sufixo que foi usado quando escreveste o artigo. Para facilitar a leitura de itens individuais, utilize a estratégia de sufixos pré-calculados. Em vez de utilizar um número aleatório para distribuir os itens entre as divisórias, utilize um número que seja calculado com base em algo que pretende consultar.

Considere o exemplo anterior, onde um recipiente utiliza uma data como chave de partição. Agora suponha que cada item tem um  `Vehicle-Identification-Number` `VIN` atributo que queremos aceder. Além disso, suponha que muitas vezes faça consultas para encontrar itens até `VIN` à data, além de. Antes da sua aplicação escrever o item no recipiente, pode calcular um sufixo de haxixe com base no VIN e apdicê-lo à data da chave de partição. O cálculo pode gerar um número entre 1 e 400 que é distribuído uniformemente. Este resultado é semelhante aos resultados produzidos pelo método de estratégia de sufixo aleatório. O valor chave da partição é então a data concatenada com o resultado calculado.

Com esta estratégia, as escritas são distribuídas uniformemente pelos valores-chave da partição, e através das divisórias. Pode ler facilmente um determinado item e data, pois pode calcular o valor da chave de partição para um específico `Vehicle-Identification-Number` . O benefício deste método é que pode evitar criar uma única chave de partição quente, ou seja, uma chave de partição que leva toda a carga de trabalho. 

## <a name="next-steps"></a>Passos seguintes

Pode saber mais sobre o conceito de partição nos seguintes artigos:

* Saiba mais sobre [divisórias lógicas.](partition-data.md)
* Saiba mais sobre como [obter produção em contentores e bases de dados da Azure Cosmos.](set-throughput.md)
* Saiba como [obter a produção num recipiente Azure Cosmos.](how-to-provision-container-throughput.md)
* Saiba como [obter o rendimento numa base de dados da Azure Cosmos.](how-to-provision-database-throughput.md)
