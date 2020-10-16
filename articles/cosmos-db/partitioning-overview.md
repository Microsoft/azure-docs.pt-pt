---
title: Criação de partições no Azure Cosmos DB
description: Saiba mais sobre a partição em Azure Cosmos DB, as melhores práticas ao escolher uma chave de partição, e como gerir divisórias lógicas
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: aa7d67cd6bd1bd422bd257b75ac5bde3bd534d7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481838"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Criação de partições no Azure Cosmos DB

A Azure Cosmos DB utiliza divisórias para escalar contentores individuais numa base de dados para atender às necessidades de desempenho da sua aplicação. Na partilha, os itens num recipiente são divididos em subconjuntos distintos *chamados divisórias lógicas*. As divisórias lógicas são formadas com base no valor de uma chave de *partição* que está associada a cada item num recipiente. Todos os itens numa partição lógica têm o mesmo valor de chave de partição.

Por exemplo, um recipiente contém itens. Cada item tem um valor único para a `UserID` propriedade. Se `UserID` servir como chave de partição para os itens no recipiente e existirem 1.000 `UserID` valores únicos, 1.000 divisórias lógicas são criadas para o recipiente.

Além de uma chave de partição que determina a partição lógica do item, cada item num recipiente tem um *ID de item* (único dentro de uma partição lógica). Combinar a chave de partição e o *ID* do item cria o *índice*do item, que identifica exclusivamente o item.

[Escolher uma chave de partição](partitioning-overview.md#choose-partitionkey) é uma decisão importante que irá afetar o desempenho da sua aplicação.

## <a name="managing-logical-partitions"></a>Gestão de divisórias lógicas

A Azure Cosmos DB gere de forma transparente e automática a colocação de divisórias lógicas em divisórias físicas para satisfazer de forma eficiente as necessidades de escalabilidade e desempenho do recipiente. À medida que os requisitos de produção e armazenamento de uma aplicação aumentam, a Azure Cosmos DB move divisórias lógicas para espalhar automaticamente a carga por um maior número de divisórias físicas. Pode aprender mais sobre [divisórias físicas.](partition-data.md#physical-partitions)

Azure Cosmos DB usa divisórias baseadas em haxixe para espalhar divisórias lógicas através de divisórias físicas. Azure Cosmos DB tem o valor chave da partição de um item. O resultado haxixe determina a partição física. Em seguida, Azure Cosmos DB atribui o espaço chave da chave de partição hashes uniformemente através das divisórias físicas.

As transações (em procedimentos armazenados ou gatilhos) só são permitidas contra itens numa única divisória lógica.

Você pode aprender mais sobre [como Azure Cosmos DB gere divisórias](partition-data.md). (Não é necessário compreender os detalhes internos para construir ou executar as suas aplicações, mas adicionado aqui para um leitor curioso.)

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Escolher uma chave de partição

Uma chave de partição tem dois componentes: **caminho chave de partição** e o valor chave da **partição**. Por exemplo, considere um item { "userId" : "Andrew", "worksFor": "Microsoft" } se escolher "userId" como chave de partição, os dois componentes chave de partição são os dois componentes chave de partição:

* O caminho da chave da partição (por exemplo: "/userId"). O caminho chave da partição aceita caracteres alfanuméricos e sublinhados(_). Também pode utilizar objetos aninhados utilizando a notação de trajetória padrão(/).

* O valor da chave de partição (por exemplo: "Andrew"). O valor da chave de partição pode ser de tipos de cordas ou numéricos.

Para conhecer os limites de produção, armazenamento e comprimento da chave de partição, consulte o artigo de quotas de [serviço Azure Cosmos DB.](concepts-limits.md)

Selecionar a sua chave de partição é uma escolha de design simples mas importante em Azure Cosmos DB. Uma vez selecionada a tecla de partição, não é possível alterá-la no local. Se precisar de alterar a sua tecla de partição, deve mover os seus dados para um novo recipiente com a sua nova chave de partição desejada.

Para **todos os** recipientes, a sua chave de partição deve:

* Seja um imóvel que tenha um valor que não muda. Se uma propriedade é a sua chave de partição, não pode atualizar o valor dessa propriedade.
* Ter uma cardinalidade elevada. Por outras palavras, a propriedade deve ter uma ampla gama de valores possíveis.
* Spread request unit (RU) consumo e armazenamento de dados uniformemente em todas as divisórias lógicas. Isto garante até mesmo o consumo de RU e distribuição de armazenamento através das suas divisórias físicas.

Se necessitar [de transações acid de vários itens](database-transactions-optimistic-concurrency.md#multi-item-transactions) em Azure Cosmos DB, terá de utilizar [procedimentos ou gatilhos armazenados](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures). Todos os procedimentos e gatilhos armazenados com base em JavaScript são traçados para uma única partição lógica.

## <a name="partition-keys-for-read-heavy-containers"></a>Chaves de partição para recipientes pesados de leitura

Para a maioria dos recipientes, os critérios acima são tudo o que deve considerar ao escolher uma chave de partição. No entanto, para grandes recipientes de leitura pesada, é melhor escolher uma chave de partição que apareça frequentemente como filtro nas suas consultas. As consultas podem ser [encaminhadas de forma eficiente apenas para as divisórias físicas relevantes,](how-to-query-container.md#in-partition-query) incluindo a chave de partição no predicado do filtro.

Se a maioria dos pedidos da sua carga de trabalho são consultas e a maioria das suas consultas têm um filtro de igualdade na mesma propriedade, esta propriedade pode ser uma boa escolha chave de partição. Por exemplo, se fizer frequentemente uma consulta que `UserID` filtra, selecionar `UserID` como chave de partição reduziria o número de [consultas de partição cruzadas](how-to-query-container.md#avoiding-cross-partition-queries).

No entanto, se o seu recipiente for pequeno, provavelmente não tem divisórias físicas suficientes para se preocupar com o impacto de desempenho das consultas de partição cruzada. A maioria dos pequenos contentores em Azure Cosmos DB requerem apenas uma ou duas divisórias físicas.

Se o seu recipiente pode crescer para mais do que algumas divisórias físicas, então deve escolher uma chave de partição que minimize as consultas de partição cruzadas. O seu recipiente necessitará de mais do que algumas divisórias físicas quando qualquer uma das seguintes são verdadeiras:

* O seu contentor terá mais de 30.000 RU's a provisionados
* O seu recipiente armazenará mais de 100 GB de dados

## <a name="using-item-id-as-the-partition-key"></a>Usando o ID do item como chave de partição

Se o seu recipiente tem uma propriedade que tem uma ampla gama de valores possíveis, é provável que seja uma ótima escolha chave de partição. Um possível exemplo de tal propriedade é o *ID do item.* Para pequenos recipientes pesados de leitura ou recipientes pesados de qualquer tamanho, o *iD* do item é naturalmente uma ótima escolha para a chave de partição.

O *iD do item da* propriedade do sistema é garantido existir em todos os itens do seu recipiente Cosmos. Pode ter outras propriedades que representam uma identificação lógica do seu item. Em muitos casos, estas são também grandes escolhas chave de partição pelas mesmas razões que o *iD do item*.

O *ID do item* é uma ótima escolha chave de partição pelas seguintes razões:

* Existem uma vasta gama de valores possíveis (um *ID de item* único por item).
* Como existe um *ID de item* único por item, o *ID* do item faz um grande trabalho no equilíbrio uniforme do consumo de RU e armazenamento de dados.
* Pode facilmente fazer leituras de pontos eficientes, uma vez que sempre conhecerá a chave de partição de um item se souber o seu *ID do artigo.*

Algumas coisas a ter em conta ao selecionar o *ID* do item como chave de partição incluem:

* Se o *item ID* for a chave de partição, tornar-se-á um identificador único em todo o seu recipiente. Não poderá ter itens que tenham um *ID de item*duplicado.
* Se tiver um recipiente de leitura pesado que tenha [muitas divisórias físicas,](partition-data.md#physical-partitions)as consultas serão mais eficientes se tiverem um filtro de igualdade com o *ID do artigo.*
* Não é possível executar procedimentos armazenados ou gatilhos através de múltiplas divisórias lógicas.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a partição e a escala horizontal em Azure Cosmos DB](partition-data.md).
* Conheça a [produção a provisionada em Azure Cosmos DB](request-units.md).
* Conheça a [distribuição global em Azure Cosmos DB](distribute-data-globally.md).
