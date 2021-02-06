---
title: Criação de partições e dimensionamento horizontal no Azure Cosmos DB
description: Saiba mais sobre partições, lógicas, divisórias físicas em Azure Cosmos DB, boas práticas ao escolher uma chave de partição, e como gerir divisórias lógicas
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: b91c846b5a79125c1cee9c36ce81b5c3d3229ba9
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627780"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Criação de partições e dimensionamento horizontal no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A Azure Cosmos DB utiliza divisórias para escalar contentores individuais numa base de dados para atender às necessidades de desempenho da sua aplicação. Na partilha, os itens num recipiente são divididos em subconjuntos distintos *chamados divisórias lógicas*. As divisórias lógicas são formadas com base no valor de uma chave de *partição* que está associada a cada item num recipiente. Todos os itens numa partição lógica têm o mesmo valor chave de partição.

Por exemplo, um recipiente contém itens. Cada item tem um valor único para a `UserID` propriedade. Se `UserID` servir como chave de partição para os itens no recipiente e existirem 1.000 `UserID` valores únicos, 1.000 divisórias lógicas são criadas para o recipiente.

Além de uma chave de partição que determina a partição lógica do item, cada item num recipiente tem um *ID de item* (único dentro de uma partição lógica). Combinar a chave de partição e o *ID* do item cria o *índice* do item, que identifica exclusivamente o item. [Escolher uma chave de partição](#choose-partitionkey) é uma decisão importante que irá afetar o desempenho da sua aplicação.

Este artigo explica a relação entre divisórias lógicas e físicas. Também discute as melhores práticas para a partição e dá uma visão aprofundada de como a escala horizontal funciona em Azure Cosmos DB. Não é necessário compreender estes detalhes internos para selecionar a sua chave de partição, mas cobrimo-los para que tenha clareza sobre como funciona a Azure Cosmos DB.

## <a name="logical-partitions"></a>Partições lógicas

Uma partição lógica consiste num conjunto de itens que têm a mesma chave de partição. Por exemplo, num recipiente que contém dados sobre nutrição alimentar, todos os itens contêm uma `foodGroup` propriedade. Pode utilizar `foodGroup` como chave de partição para o recipiente. Grupos de itens que têm valores `foodGroup` específicos para, por `Beef Products` `Baked Products` exemplo, e `Sausages and Luncheon Meats` formam divisórias lógicas distintas. Não tens de te preocupar em eliminar uma partição lógica quando os dados subjacentes são eliminados.

Uma divisória lógica também define o âmbito das transações de base de dados. Pode atualizar itens dentro de uma divisória lógica utilizando uma [transação com isolamento instantâneo](database-transactions-optimistic-concurrency.md). Quando novos itens são adicionados a um recipiente, novas divisórias lógicas são criadas de forma transparente pelo sistema.

Não há limite para o número de divisórias lógicas no seu recipiente. Cada divisória lógica pode armazenar até 20GB de dados. Boas escolhas chave de partição têm uma ampla gama de valores possíveis. Por exemplo, num recipiente onde todos os itens contêm uma `foodGroup` propriedade, os dados dentro da `Beef Products` partição lógica podem crescer até 20 GB. [Selecionar uma chave de partição](#choose-partitionkey) com uma ampla gama de valores possíveis garante que o recipiente é capaz de escalar.

## <a name="physical-partitions"></a>Divisórias físicas

Um recipiente é dimensionado distribuindo dados e produção através de divisórias físicas. Internamente, uma ou mais divisórias lógicas são mapeadas para uma única partição física. Normalmente, os recipientes mais pequenos têm muitas divisórias lógicas, mas só requerem uma única partição física. Ao contrário das divisórias lógicas, as divisórias físicas são uma implementação interna do sistema e são inteiramente geridas pela Azure Cosmos DB.

O número de divisórias físicas no seu recipiente depende do seguinte:

* O número de produção a provisionada (cada partição física individual pode fornecer um rendimento de até 10.000 unidades de pedido por segundo).
* O armazenamento total de dados (cada partição física individual pode armazenar até 50GB de dados).

> [!NOTE]
> As divisórias físicas são uma implementação interna do sistema e são inteiramente geridas pela Azure Cosmos DB. Ao desenvolver as suas soluções, não se concentre em divisórias físicas porque não pode controlá-las. Em vez disso, concentrem-se nas chaves da partição. Se escolher uma chave de partição que distribui uniformemente o consumo de produção através de divisórias lógicas, garantirá que o consumo de produção através de divisórias físicas seja equilibrado.

Não há limite para o número total de divisórias físicas no seu recipiente. À medida que o seu rendimento ou tamanho de dados aumenta, a Azure Cosmos DB criará automaticamente novas divisórias físicas dividindo as existentes. As divisórias físicas não afetam a disponibilidade da sua aplicação. Após a divisão física, todos os dados dentro de uma única partição lógica ainda serão armazenados na mesma partição física. Uma divisão de partição física simplesmente cria um novo mapeamento de divisórias lógicas para divisórias físicas.

A produção prevista para um recipiente é dividida uniformemente entre divisórias físicas. Um design chave de partição que não distribui pedidos uniformemente pode resultar em muitos pedidos direcionados para um pequeno subconjunto de divisórias que se tornam "quentes". As divisórias quentes conduzem a uma utilização ineficiente da produção provisitada, o que pode resultar na limitação das taxas e nos custos mais elevados.

Pode ver as divisórias físicas do seu recipiente na secção de **armazenamento** da **lâmina métrica** do portal Azure:

:::image type="content" source="./media/partitioning-overview/view-partitions-zoomed-out.png" alt-text="Número de visualização de divisórias físicas" lightbox="./media/partitioning-overview/view-partitions-zoomed-in.png" ::: 

Na imagem acima, um recipiente tem `/foodGroup` como chave de partição. Cada uma das três barras do gráfico representa uma divisória física. Na imagem, o **alcance da chave de partição** é o mesmo que uma partição física. A partição física selecionada contém as 3 divisórias lógicas de tamanho mais significativas: `Beef Products` `Vegetable and Vegetable Products` , e `Soups, Sauces, and Gravies` .

Se você providenciar uma produção de 18.000 unidades de pedido por segundo (RU/s), então cada uma das três divisórias físicas pode utilizar 1/3 do total de produção a provisionada. Dentro da partição física selecionada, as teclas de partição `Beef Products` `Vegetable and Vegetable Products` lógica, e `Soups, Sauces, and Gravies` podem, coletivamente, utilizar os 6.000 RU/s provisados da partição física. Como a produção a provisionada está igualmente dividida nas divisórias físicas do seu contentor, é importante escolher uma chave de partição que distribua uniformemente o consumo de [produção, escolhendo a chave de partição lógica certa.](#choose-partitionkey) 

## <a name="managing-logical-partitions"></a>Gestão de divisórias lógicas

A Azure Cosmos DB gere de forma transparente e automática a colocação de divisórias lógicas em divisórias físicas para satisfazer de forma eficiente as necessidades de escalabilidade e desempenho do recipiente. À medida que os requisitos de produção e armazenamento de uma aplicação aumentam, a Azure Cosmos DB move divisórias lógicas para espalhar automaticamente a carga por um maior número de divisórias físicas. Pode aprender mais sobre [divisórias físicas.](partitioning-overview.md#physical-partitions)

Azure Cosmos DB usa divisórias baseadas em haxixe para espalhar divisórias lógicas através de divisórias físicas. Azure Cosmos DB tem o valor chave da partição de um item. O resultado haxixe determina a partição física. Em seguida, Azure Cosmos DB atribui o espaço chave da chave de partição hashes uniformemente através das divisórias físicas.

As transações (em procedimentos armazenados ou gatilhos) só são permitidas contra itens numa única divisória lógica.

Você pode aprender mais sobre [como Azure Cosmos DB gere divisórias](partitioning-overview.md). (Não é necessário compreender os detalhes internos para construir ou executar as suas aplicações, mas adicionado aqui para um leitor curioso.)

## <a name="replica-sets"></a>Conjuntos de réplicas

Cada partição física consiste num conjunto de réplicas, também referidas como um [*conjunto de réplicas.*](global-dist-under-the-hood.md) Cada conjunto de réplicas acolhe uma instância do motor da base de dados. Um conjunto de réplica torna os dados armazenados dentro da partição física durável, altamente disponíveis e consistentes. Cada réplica que compõe a partição física herda a quota de armazenamento da partição. Todas as réplicas de uma partição física suportam coletivamente a produção que é atribuída à partição física. A Azure Cosmos DB gere automaticamente conjuntos de réplicas.

Normalmente, os recipientes mais pequenos só requerem uma única partição física, mas ainda terão pelo menos 4 réplicas.

A imagem a seguir mostra como as divisórias lógicas são mapeadas para divisórias físicas que são distribuídas globalmente:

:::image type="content" source="./media/partitioning-overview/logical-partitions.png" alt-text="Uma imagem que demonstra a partição de Azure Cosmos DB" border="false":::

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

O *ID do item da* propriedade do sistema existe em todos os itens do seu recipiente. Pode ter outras propriedades que representam uma identificação lógica do seu item. Em muitos casos, estas são também grandes escolhas chave de partição pelas mesmas razões que o *iD do item*.

O *ID do item* é uma ótima escolha chave de partição pelas seguintes razões:

* Existem uma vasta gama de valores possíveis (um *ID de item* único por item).
* Como existe um *ID de item* único por item, o *ID* do item faz um grande trabalho no equilíbrio uniforme do consumo de RU e armazenamento de dados.
* Pode facilmente fazer leituras de pontos eficientes, uma vez que sempre conhecerá a chave de partição de um item se souber o seu *ID do artigo.*

Algumas coisas a ter em conta ao selecionar o *ID* do item como chave de partição incluem:

* Se o *item ID* for a chave de partição, tornar-se-á um identificador único em todo o seu recipiente. Não poderá ter itens que tenham um *ID de item* duplicado.
* Se tiver um recipiente de leitura pesado que tenha [muitas divisórias físicas,](partitioning-overview.md#physical-partitions)as consultas serão mais eficientes se tiverem um filtro de igualdade com o *ID do artigo.*
* Não é possível executar procedimentos armazenados ou gatilhos através de múltiplas divisórias lógicas.

## <a name="next-steps"></a>Passos seguintes

* Conheça a [produção a provisionada em Azure Cosmos DB](request-units.md).
* Conheça a [distribuição global em Azure Cosmos DB](distribute-data-globally.md).
* Saiba como [obter a produção num recipiente Azure Cosmos.](how-to-provision-container-throughput.md)
* Saiba como [obter o rendimento numa base de dados da Azure Cosmos.](how-to-provision-database-throughput.md)
