---
title: Débito de aprovisionar em contentores de Cosmos do Azure e bases de dados
description: Saiba como configurar o débito aprovisionado para contentores do Cosmos do Azure e bases de dados.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: andrl
ms.openlocfilehash: 8335a235de708227136400f3af8fa7b4d0a52e29
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520909"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Aprovisionar débito em contentores e bases de dados

Uma base de dados do Cosmos do Azure é uma unidade de gestão para um conjunto de contentores. Uma base de dados consiste num conjunto de contentores de esquema desconhecido. Um contentor do Cosmos do Azure é a unidade de escalabilidade para o débito e armazenamento. Um contentor é particionado horizontalmente através de um conjunto de máquinas dentro de uma região do Azure e é distribuído por todas as regiões do Azure à sua conta do Cosmos do Azure.

Com o Azure Cosmos DB, pode configurar o débito em duas granularidades:
 
- Contentores do Azure Cosmos
- Bases de dados do Cosmos do Azure

## <a name="set-throughput-on-a-container"></a>Definir débito num contentor  

O débito aprovisionado num contentor do Cosmos do Azure é reservado exclusivamente para o contentor. O contentor recebe o débito aprovisionado o tempo todo. O débito aprovisionado num contentor com suporte financeiro suporte dos SLAs. Para configurar o débito num contentor, consulte [débito de aprovisionar num contentor do Azure Cosmos](how-to-provision-container-throughput.md).

A definição de débito aprovisionado num contentor é a opção amplamente usada. Pode dimensionar o débito para um contentor ao aprovisionar qualquer quantidade de débito ao utilizar unidades de pedido (RUs). Mas não é possível especificar seletivamente a taxa de transferência para partições lógicas. 

Se a carga de trabalho em execução numa partição lógica consome mais do que o débito que foi alocado para a partição lógica específica, as operações de obtém taxa limitado. Quando ocorre a limitação de velocidade, pode aumentar o débito para o contêiner inteiro ou repita as operações. Para obter mais informações sobre a criação de partições, consulte [partições lógicas](partition-data.md).

Recomendamos que configure o débito com a granularidade de contentor quando pretender que o desempenho garantido para o contentor.

Débito aprovisionado num contentor do Cosmos do Azure é distribuído uniformemente por todas as partições lógicas do contentor. Uma vez que um ou mais partições lógicas de um contentor são alojadas por uma partição física, as partições físicas pertencem exclusivamente para o contentor e suportam o débito aprovisionado no contentor. 

A imagem seguinte mostra como uma partição física hospeda um ou mais partições lógicas de um contentor:

![Partição física](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Definir débito numa base de dados

Quando aprovisionar o débito numa base de dados do Cosmos do Azure, o débito é compartilhado entre todos os contentores na base de dados. Uma exceção é se tiver especificado um débito aprovisionado em contentores específicos. O débito de base de dados entre seus contêineres de partilha é análogo ao que aloja uma base de dados num cluster de máquinas. Uma vez que todos os contentores dentro de uma base de dados partilham os recursos disponíveis numa máquina, naturalmente não obtém um desempenho previsível em qualquer contentor específico. Para configurar o débito numa base de dados, consulte [configurar o débito aprovisionado numa base de dados do Azure Cosmos](how-to-provision-database-throughput.md).

Débito de definição numa base de dados do Cosmos do Azure garante que recebe o débito aprovisionado o tempo todo. Uma vez que todos os contentores na base de dados partilham o débito aprovisionado, o Azure Cosmos DB não fornece que garantias de qualquer débito previsível para um determinado contêiner nessa base de dados. A parte da taxa de transferência que pode receber um contentor específico está dependente:

* O número de contentores.
* A escolha das chaves de partição para vários contentores.
* A distribuição da carga de trabalho em várias partições lógicas dos contentores. 

Recomendamos que configure o débito numa base de dados quando pretende partilhar a taxa de transferência em vários contentores, mas não quer dedicar a taxa de transferência para qualquer determinado contentor. 

Os exemplos seguintes demonstram onde preferível aprovisionar o débito ao nível da base de dados:

* A partilha de débito aprovisionado de uma base de dados num conjunto de contentores é útil para uma aplicação multi-inquilino. Cada utilizador pode ser representado por um contentor do Azure Cosmos distinto.

* Partilhar o débito aprovisionado de uma base de dados num conjunto de contentores é útil ao migrar uma base de dados NoSQL, como o MongoDB ou Cassandra, alojadas num cluster de VMs ou a partir de servidores físicos no local ao Azure Cosmos DB. Considere o débito aprovisionado configurado na sua base de dados do Cosmos do Azure como um equivalente lógico, mas mais econômico e elástica, para que a capacidade de computação do seu cluster do MongoDB ou o Cassandra.  

Todos os contentores criados dentro de uma base de dados com débito aprovisionado tem de ser criados com uma chave de partição. Num determinado período de tempo, o débito alocado a um contentor numa base de dados é distribuído por todas as partições lógicas nesse contentor. Quando tiver de contentores que partilhem o débito aprovisionado num banco de dados, não pode aplicar seletivamente a taxa de transferência para um contentor específico ou de uma partição lógica. 

Se a carga de trabalho numa partição lógica consome mais do que a taxa de transferência que é atribuída a uma partição lógica específica, suas operações são taxa limitado. Quando ocorre a limitação de velocidade, pode aumentar o débito para o contêiner inteiro ou repita as operações. Para obter mais informações sobre a criação de partições, consulte [partições lógicas](partition-data.md).

Várias partições lógicas que partilham o débito aprovisionado para um banco de dados podem ser hospedadas numa única partição física. Enquanto uma única partição lógica de um contentor sempre tem um âmbito dentro de uma partição física, as partições lógicas do "L" em "C" contentores que partilhem o débito aprovisionado das bases de dados podem ser mapeadas e alojadas em partições físicas de "R". 

A imagem seguinte mostra como uma partição física pode alojar uma ou mais partições lógicas que pertencem a contentores diferentes dentro de uma base de dados:

![Partição física](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Definir débito num banco de dados e um contentor

Pode combinar os dois modelos. Aprovisionamento de débito no banco de dados e o contentor é permitido. O exemplo seguinte mostra como aprovisionar o débito numa base de dados do Cosmos do Azure e um contentor:

* Pode criar uma base de dados do Cosmos do Azure com o nome Z com débito aprovisionado de RUs "K". 
* Em seguida, cria cinco contentores com o nome A, B, C, D e E na base de dados.
* Pode configurar explicitamente "P" RUs do débito aprovisionado no contentor com o nome B.
* O débito de RUs "K" é compartilhado entre os contentores de quatro, C, D e E. A quantidade exata de débito disponível para A, C, D ou E varia. Não há nenhum SLA para a taxa de transferência de cada contentor individuais.
* O contentor com o nome B é garantido para obter o débito de "P" RUs o tempo todo. Ele é suportado por SLAs.

## <a name="update-throughput-on-a-database-or-a-container"></a>Débito de atualização num banco de dados ou um contentor

Depois de criar um contentor do Cosmos do Azure ou uma base de dados, pode atualizar o débito aprovisionado. Não existe nenhum limite no débito aprovisionado máximo que pode configurar a base de dados ou o contentor. O débito aprovisionado mínimo depende dos seguintes fatores: 

* O tamanho máximo dos dados que alguma vez armazena no contentor
* O débito máximo que Aprovisiona nunca no contentor
* O número máximo de contentores do Azure Cosmos que nunca de criar uma base de dados com a taxa de transferência partilhada. 

Pode obter o débito mínimo de um contentor ou uma base de dados por meio de programação através de SDKs ou ver o valor no portal do Azure. Ao utilizar o SDK de .NET, o [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) método permite-lhe ajustar o valor do débito aprovisionado. Ao utilizar o SDK de Java, o [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples) método permite-lhe ajustar o valor do débito aprovisionado. 

Ao utilizar o SDK de .NET, o [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) método permite-lhe obter o débito mínimo de um contentor ou uma base de dados. 

Pode dimensionar o débito aprovisionado de um contentor ou uma base de dados em qualquer altura. Pode executar a operação de redução vertical após o período de inatividade de 4 horas. O período de inatividade é definido como o tempo de período quando não ocorreu nenhuma oferta operações de substituição (que inclui a ampliação e redução verticais) num contentor ou uma base de dados. 

## <a name="comparison-of-models"></a>Comparação dos modelos

|**Quota**  |**Débito aprovisionado num banco de dados**  |**Débito aprovisionado num contentor**|
|---------|---------|---------|
|RUs mínimo |400 (após os primeiros quatro contentores, cada contentor adicional requer um mínimo de 100 RU por segundo.) |400|
|RUs mínimas por contentor|100|400|
|RUs mínimas necessária para consumir a 1 GB de armazenamento|40|40|
|RUs máximos|Ilimitado, na base de dados.|Ilimitado, no contentor.|
|RUs atribuídos ou disponíveis para um contentor específico|Não existem garantias. RUs atribuídos a um determinado contêiner dependem as propriedades. As propriedades podem ser a escolha de chaves de partição de contentores que partilhem o débito, a distribuição de carga de trabalho e o número de contentores. |Todos os RUs configurados no contentor exclusivamente estão reservados para o contentor.|
|Armazenamento máximo para um contentor|Ilimitado.|Ilimitado.|
|Débito máximo por partição lógica de um contentor|10 mil RUs|10 mil RUs|
|Armazenamento máximo (dados + índice) por partição lógica de um contentor|10 GB|10 GB|

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [partições lógicas](partition-data.md).
* Saiba como [débito de aprovisionar num contentor do Azure Cosmos](how-to-provision-container-throughput.md).
* Saiba como [débito de aprovisionar uma base de dados do Azure Cosmos](how-to-provision-database-throughput.md).

