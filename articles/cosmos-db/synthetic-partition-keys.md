---
title: Crie uma chave de partição sintética em Azure Cosmos DB
description: Aprenda a usar chaves de partição sintética nos seus recipientes Azure Cosmos para distribuir os dados e a carga de trabalho uniformemente através das teclas de partição
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: e8786c2d6e93c18a5bf9856a5555d6b528f842c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75441226"
---
# <a name="create-a-synthetic-partition-key"></a>Criar uma chave de partição sintética

É a melhor prática ter uma chave de partição com muitos valores distintos, como centenas ou milhares. O objetivo é distribuir os seus dados e carga de trabalho uniformemente pelos itens associados a estes valores-chave de partição. Se tal propriedade não existir nos seus dados, pode construir uma *chave de partição sintética.* Este documento descreve várias técnicas básicas para gerar uma chave de partição sintética para o seu recipiente Cosmos.

## <a name="concatenate-multiple-properties-of-an-item"></a>Concatenate múltiplas propriedades de um item

Você pode formar uma chave de partição concatenando múltiplos valores de propriedade em uma única propriedade artificial. `partitionKey` Estas chaves são referidas como chaves sintéticas. Por exemplo, considere o seguinte documento de exemplo:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Para o documento anterior, uma opção é definir/dispositivoId ou/data como chave de partição. Utilize esta opção, se pretender dividir o seu recipiente com base no ID ou data do dispositivo. Outra opção é concatenar estes dois `partitionKey` valores numa propriedade sintética que é usada como chave de partição.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Em cenários em tempo real, pode ter milhares de itens numa base de dados. Em vez de adicionar manualmente a chave sintética, defina a lógica do lado do cliente para concatenar valores e inserir a chave sintética nos itens dos seus recipientes Cosmos.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Use uma chave de partição com um sufixo aleatório

Outra estratégia possível para distribuir a carga de trabalho de forma mais homoviada é anexar um número aleatório no final do valor-chave da partilha. Quando distribui itens desta forma, pode realizar operações de escrita paralelas através de divisórias.

Um exemplo é se uma chave de partição representa uma data. Pode escolher um número aleatório entre 1 e 400 e concatená-lo como um sufixo à data. Este método resulta em `2018-08-09.1``2018-08-09.2`valores-chave de `2018-08-09.400`partilha como, e assim por diante, através de . Como aleatoriamente a chave da partição, as operações de escrita no recipiente em cada dia são distribuídas uniformemente por várias divisórias. Este método resulta num melhor paralelismo e numa maior dose de entrada.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Utilize uma chave de partição com sufixos pré-calculados 

A estratégia de sufixo aleatório pode melhorar muito a produção de escrita, mas é difícil ler um item específico. Não sabes o valor do sufixo que foi usado quando escreveste o artigo. Para facilitar a leitura de itens individuais, utilize a estratégia de sufixos pré-calculados. Em vez de usar um número aleatório para distribuir os itens entre as divisórias, use um número que é calculado com base em algo que você quer consultar.

Considere o exemplo anterior, em que um recipiente utiliza uma data como chave de partição. Agora suponha que `Vehicle-Identification-Number` cada`VIN`item tem um ( ) atributo a que queremos aceder. Além disso, suponha que muitas vezes faça consultas para encontrar itens até `VIN`à data. Antes da sua aplicação escrever o item para o recipiente, pode calcular um sufixo de hash com base no VIN e acomodá-lo à data da chave da partição. O cálculo pode gerar um número entre 1 e 400 que é distribuído uniformemente. Este resultado é semelhante aos resultados produzidos pelo método de estratégia de sufixo aleatório. O valor-chave da divisória é então a data concatenada com o resultado calculado.

Com esta estratégia, as escritas estão uniformemente distribuídas pelos valores-chave da partilha, e através das divisórias. Pode ler facilmente um determinado item e data, pois pode `Vehicle-Identification-Number`calcular o valor-chave da partilha para um específico . O benefício deste método é que pode evitar criar uma única chave de partição quente, ou seja, uma chave de partição que leva toda a carga de trabalho. 

## <a name="next-steps"></a>Passos seguintes

Pode saber mais sobre o conceito de partição nos seguintes artigos:

* Saiba mais sobre [divisórias lógicas.](partition-data.md)
* Saiba mais sobre como fornecer a entrada em contentores e bases de [dados da Azure Cosmos.](set-throughput.md)
* Aprenda a [fornecer a entrada num recipiente Azure Cosmos.](how-to-provision-container-throughput.md)
* Aprenda a fornecer a entrada numa base de [dados Azure Cosmos.](how-to-provision-database-throughput.md)
