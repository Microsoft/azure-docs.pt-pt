---
title: Criação de partições no Azure Cosmos DB
description: Aprenda sobre a partilha em Azure Cosmos DB, as melhores práticas ao escolher uma chave de partição, e como gerir divisórias lógicas
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 1a760b4cedad5e43a2ef9f186162675aaf6d5ea5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234184"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Criação de partições no Azure Cosmos DB

A Azure Cosmos DB utiliza a partilha para escalar recipientes individuais numa base de dados para atender às necessidades de desempenho da sua aplicação. Na partilha, os itens de um recipiente são divididos em subconjuntos distintos *chamados divisórias lógicas*. As divisórias lógicas são formadas com base no valor de uma chave de *partição* que está associada a cada item num recipiente. Todos os itens numa divisória lógica têm o mesmo valor-chave de partição.

Por exemplo, um recipiente contém itens. Cada item tem um `UserID` valor único para a propriedade. Se `UserID` servir como chave de partição para os itens no recipiente `UserID` e existem 1.000 valores únicos, 1.000 divisórias lógicas são criadas para o recipiente.

Além de uma chave de partição que determina a partição lógica do item, cada item num recipiente tem um ID de *item* (único dentro de uma divisória lógica). Combinar a chave de partição e o ID do *item* cria o *índice*do item, que identifica exclusivamente o item.

[Escolher uma chave de partição](partitioning-overview.md#choose-partitionkey) é uma decisão importante que irá afetar o desempenho da sua aplicação.

## <a name="managing-logical-partitions"></a>Gestão de divisórias lógicas

A Azure Cosmos DB gere de forma transparente e automática a colocação de divisórias lógicas em divisórias físicas para satisfazer eficientemente as necessidades de escalabilidade e desempenho do recipiente. À medida que os requisitos de entrada e armazenamento de uma aplicação aumentam, o Azure Cosmos DB move divisórias lógicas para espalhar automaticamente a carga através de um maior número de divisórias físicas. Pode aprender mais sobre [divisórias físicas.](partition-data.md#physical-partitions)

O Azure Cosmos DB usa divisórias baseadas em hash para espalhar divisórias lógicas através de divisórias físicas. Azure Cosmos DB hashes o valor chave da partição de um item. O resultado hashed determina a partição física. Em seguida, o Azure Cosmos DB atribui o espaço chave da chave de partição hashes uniformemente através das divisórias físicas.

As transações (em procedimentos ou gatilhos armazenados) só são permitidas contra itens numa única partição lógica.

Você pode aprender mais sobre [como o Azure Cosmos DB gere divisórias.](partition-data.md) (Não é necessário compreender os detalhes internos para construir ou executar as suas aplicações, mas adicionado aqui para um leitor curioso.)

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Escolher uma chave de partição

Selecionar a sua chave de partição é uma escolha de design simples mas importante em Azure Cosmos DB. Uma vez selecionada a sua chave de partição, não é possível troque-la no local. Se precisar de alterar a chave de partição, deverá mover os seus dados para um novo recipiente com a sua nova chave de partição desejada.

Para **todos os** recipientes, a sua chave de partição deve:

* Seja um imóvel que tenha um valor que não muda. Se uma propriedade é a sua chave de partição, você não pode atualizar o valor da propriedade.
* Tenha uma alta cardeal. Por outras palavras, a propriedade deve ter uma ampla gama de valores possíveis.
* Espalhe o consumo e armazenamento de dados da unidade de pedido (RU) uniformemente em todas as divisórias lógicas. Isto garante até mesmo o consumo de RU e distribuição de armazenamento em todas as suas divisórias físicas.

Se necessitar de [transações de ÁCIDO multi-item](database-transactions-optimistic-concurrency.md#multi-item-transactions) em Azure Cosmos DB, terá de utilizar [procedimentos ou gatilhos armazenados](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures). Todos os procedimentos e gatilhos armazenados baseados em JavaScript são reparados numa única partição lógica.

## <a name="partition-keys-for-read-heavy-containers"></a>Chaves de partição para recipientes pesados de leitura

Para a maioria dos recipientes, os critérios acima referidos são tudo o que você precisa considerar ao escolher uma chave de partição. No entanto, para grandes recipientes de leitura pesadas, é possível escolher uma chave de partição que apareça frequentemente como filtro nas suas consultas. As consultas podem ser [eficientemente encaminhadas para apenas as divisórias físicas relevantes,](how-to-query-container.md#in-partition-query) incluindo a chave de partição no predicado do filtro.

Se a maioria dos pedidos da sua carga de trabalho são consultas e a maioria das suas consultas têm um filtro de igualdade na mesma propriedade, esta propriedade pode ser uma boa escolha chave de divisória. Por exemplo, se executar frequentemente uma consulta `UserID`que se `UserID` filtra, então selecionar como chave de partição reduziria o número de [consultas de divisória cruzada](how-to-query-container.md#avoiding-cross-partition-queries).

No entanto, se o seu recipiente é pequeno, provavelmente não tem divisórias físicas suficientes para se preocupar com o impacto de desempenho das consultas de partição cruzada. A maioria dos pequenos contentores em Azure Cosmos DB só requerem uma ou duas divisórias físicas.

Se o seu recipiente pode crescer para mais do que algumas divisórias físicas, então deve certificar-se de que escolhe uma chave de partição que minimize as consultas de divisóriacruzada. O seu recipiente necessitará de mais do que algumas divisórias físicas quando qualquer uma das seguintes partes for verdadeira:

* O seu contentor terá mais de 30.000 RU's aprovisionados
* O seu contentor irá armazenar mais de 100 GB de dados

## <a name="using-item-id-as-the-partition-key"></a>Usando o ID do item como chave de partição

Se o seu contentor tem uma propriedade que tem uma ampla gama de valores possíveis, é provavelmente uma ótima escolha chave de divisórias. Um exemplo possível de tal propriedade é o id do *artigo.* Para pequenos recipientes de leitura pesadaou recipientes pesados de qualquer tamanho, o ID do *artigo* é naturalmente uma ótima escolha para a chave de partição.

O *ID* de propriedade do sistema é garantido existir em todos os itens do seu recipiente Cosmos. Pode ter outras propriedades que representam uma identificação lógica do seu artigo. Em muitos casos, estas são também grandes opções-chave de partição pelas mesmas razões que o *id item*.

O ID do *item* é uma ótima escolha chave de divisória pelas seguintes razões:

* Existem uma ampla gama de valores possíveis (um item id único por *item).*
* Como existe um *item único por item,* o *ITEM* ID faz um grande trabalho ao equilibrar uniformemente o consumo de RU e armazenamento de dados.
* Você pode facilmente fazer leituras de pontos eficientes, uma vez que você sempre saberá a chave de partição de um item se você souber o seu *ITEm ID*.

Algumas coisas a considerar ao selecionar o ID do *item* como a chave de partição incluem:

* Se o ID do *artigo* for a chave de partição, tornar-se-á um identificador único em todo o seu recipiente. Não poderá ter itens que tenham uma identificação duplicada do *artigo.*
* Se tiver um recipiente pesado de leitura que tenha [muitas divisórias físicas,](partition-data.md#physical-partitions)as consultas serão mais eficientes se tiverem um filtro de igualdade com o ID do *artigo*.
* Não se pode executar procedimentos armazenados ou gatilhos em várias divisórias lógicas.

## <a name="next-steps"></a>Passos seguintes

* Aprenda sobre [divisórias e escalas horizontais em Azure Cosmos DB](partition-data.md).
* Saiba mais sobre [a provisão disponibilizada em Azure Cosmos DB](request-units.md).
* Conheça a [distribuição global em Azure Cosmos DB.](distribute-data-globally.md)
