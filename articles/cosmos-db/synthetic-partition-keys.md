---
title: Crie uma chave de partição sintética em Azure Cosmos DB para distribuir os dados e a carga de trabalho uniformemente.
description: Saiba como usar chaves de partição sintéticas em seus contêineres de Cosmos do Azure para distribuir os dados e a carga de trabalho uniformemente entre as chaves de partição
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 093610777b150c90ad55f1ce18337f1de8b17219
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870501"
---
# <a name="create-a-synthetic-partition-key"></a>Criar uma chave de partição sintética

É a melhor prática ter uma chave de partição com muitos valores distintos, como centenas ou milhares. O objetivo é distribuir seus dados e a carga de trabalho uniformemente entre os itens associados a esses valores de chave de partição. Se essa propriedade não existir em seus dados, você poderá construir uma *chave de partição sintética*. Este documento descreve várias técnicas básicas para gerar uma chave de partição sintética para seu contêiner Cosmos.

## <a name="concatenate-multiple-properties-of-an-item"></a>Concatenar várias propriedades de um item

Você pode formar uma chave de partição concatenando vários valores de propriedade em uma única propriedade de `partitionKey` artificial. Essas chaves são chamadas de chaves sintéticas. Por exemplo, considere o seguinte documento de exemplo:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Para o documento anterior, uma opção é definir/deviceId ou/Date como a chave de partição. Use essa opção se você quiser particionar seu contêiner com base na ID ou na data do dispositivo. Outra opção é concatenar esses dois valores em uma propriedade de `partitionKey` sintética que é usada como a chave de partição.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Em cenários em tempo real, você pode ter milhares de itens em um banco de dados. Em vez de adicionar a chave sintética manualmente, defina a lógica do cliente para concatenar valores e inserir a chave sintética nos itens em seus contêineres do cosmos.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Usar uma chave de partição com um sufixo aleatório

Outra estratégia possível para distribuir a carga de trabalho mais uniformemente é acrescentar um número aleatório no final do valor da chave de partição. Ao distribuir itens dessa forma, você pode executar operações de gravação paralelas entre partições.

Um exemplo é se uma chave de partição representa uma data. Você pode escolher um número aleatório entre 1 e 400 e concatenar-o como um sufixo para a data. Esse método resulta em valores de chave de partição como `2018-08-09.1`,`2018-08-09.2`e assim por diante, por meio de `2018-08-09.400`. Como você randomiza a chave de partição, as operações de gravação no contêiner em cada dia são distribuídas uniformemente entre várias partições. Esse método resulta em um paralelismo melhor e na taxa de transferência geral mais alta.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Usar uma chave de partição com sufixos previamente calculados 

A estratégia de sufixo aleatório pode melhorar muito a taxa de transferência de gravação, mas é difícil ler um item específico. Você não sabe o valor do sufixo que foi usado quando escreveu o item. Para facilitar a leitura de itens individuais, use a estratégia de sufixos previamente calculados. Em vez de usar um número aleatório para distribuir os itens entre as partições, use um número que seja calculado com base em algo que você deseja consultar.

Considere o exemplo anterior, em que um contêiner usa uma data como a chave de partição. Agora suponha que cada item tenha um atributo `Vehicle-Identification-Number` (`VIN`) que desejamos acessar. Além disso, suponha que você geralmente execute consultas para localizar itens pelo `VIN`, além de data. Antes que seu aplicativo grave o item no contêiner, ele pode calcular um sufixo de hash com base no VIN e acrescentá-lo à data da chave da partição. O cálculo pode gerar um número entre 1 e 400 que é distribuído uniformemente. Esse resultado é semelhante aos resultados produzidos pelo método de estratégia de sufixo aleatório. O valor da chave de partição é, então, a data concatenada com o resultado calculado.

Com essa estratégia, as gravações são espalhadas uniformemente entre os valores de chave de partição e entre as partições. Você pode ler facilmente um item e uma data específicos, pois você pode calcular o valor da chave de partição para um `Vehicle-Identification-Number`específico. O benefício desse método é que você pode evitar a criação de uma única chave de partição ativa, ou seja, uma chave de partição que usa toda a carga de trabalho. 

## <a name="next-steps"></a>Passos seguintes

Você pode saber mais sobre o conceito de particionamento nos seguintes artigos:

* Saiba mais sobre [partições lógicas](partition-data.md).
* Saiba mais sobre como [provisionar a taxa de transferência em bancos de dados e contêineres do Azure Cosmos](set-throughput.md).
* Saiba como [provisionar a taxa de transferência em um contêiner Cosmos do Azure](how-to-provision-container-throughput.md).
* Saiba como [provisionar a taxa de transferência em um banco de dados Cosmos do Azure](how-to-provision-database-throughput.md).
