---
title: Produção de provisão em contentores e bases de dados da Azure Cosmos
description: Saiba como definir a produção prevista para os seus contentores e bases de dados Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 81a31448a588849a410b37868cf579fbb0a9ceb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777794"
---
# <a name="introduction-to-provisioned-throughput-in-azure-cosmos-db"></a>Introdução à produção prevista na Azure Cosmos DB

O Azure Cosmos DB permite-lhe definir o rendimento previsto nas suas bases de dados e contentores. Existem dois tipos de produção a provisionada, standard (manual) ou autoescala. Estes artigos dão uma visão geral de como funciona o rendimento previsto. 

Uma base de dados do Azure Cosmos é uma unidade de gestão para um conjunto de contentores. Uma base de dados é constituída por um conjunto de contentores independentes de esquemas. Um contentor do Azure Cosmos é a unidade de escalabilidade para o débito e para o armazenamento. Um contentor é particionado horizontalmente num conjunto de computadores numa região do Azure e é distribuído por todas as regiões do Azure associadas à conta do Azure Cosmos.

Com a Azure Cosmos DB, você pode provisões para dois granulares:
 
- Contentores do Azure Cosmos
- Bases de dados do Azure Cosmos

## <a name="set-throughput-on-a-container"></a>Produção definida num recipiente  

A produção a forjada num contentor Azure Cosmos é exclusivamente reservada a esse contentor. O contentor recebe sempre a produção a provisionada. A produção a provisionada num contentor é apoiada financeiramente pelas AEA. Para aprender a configurar a produção padrão (manual) num recipiente, consulte [a produção de provisão num recipiente Azure Cosmos](how-to-provision-container-throughput.md). Para aprender a configurar a produção de autoescalação num recipiente, consulte [a produção de escala automática .](how-to-provision-autoscale-throughput.md)

A definição de produção prevista num recipiente é a opção mais utilizada. Pode dimensionar elasticamente a produção de um recipiente, fornecendo qualquer quantidade de produção através da utilização de [Unidades de Pedido (RUs)](request-units.md). 

A produção prevista para um recipiente é distribuída uniformemente entre as suas divisórias físicas, e assumindo uma boa chave de partição que distribui uniformemente as divisórias lógicas entre as divisórias físicas, a produção também é distribuída uniformemente por todas as divisórias lógicas do recipiente. Não é possível especificar seletivamente a produção de divisórias lógicas. Uma ou mais divisórias lógicas de um recipiente são acolhidas por uma partição física, as divisórias físicas pertencem exclusivamente ao recipiente e suportam a produção prevista no recipiente. 

Se a carga de trabalho em execução numa partição lógica consumir mais do que a produção que foi atribuída à partição física subjacente, é possível que as suas operações sejam limitadas. O que é conhecido como uma _partição quente_ ocorre quando uma partição lógica tem desproporcionalmente mais pedidos do que outros valores-chave de partição.

Quando ocorre uma limitação da taxa, pode aumentar a produção prevista para todo o contentor ou voltar a tentar as operações. Também deve certificar-se de que escolhe uma chave de partição que distribui uniformemente o armazenamento e o volume de pedidos. Para obter mais informações sobre a partição, consulte [a partição e a escala horizontal em Azure Cosmos DB](partition-data.md).

Recomendamos que configuure a produção na granularidade do recipiente quando desejar um desempenho garantido para o recipiente.

A imagem a seguir mostra como uma partição física acolhe uma ou mais divisórias lógicas de um recipiente:

:::image type="content" source="./media/set-throughput/resource-partition.png" alt-text="Partição física que acolhe uma ou mais divisórias lógicas de um recipiente" border="false":::

## <a name="set-throughput-on-a-database"></a>Definir saída numa base de dados

> [!NOTE]
> Atualmente, não é possível o fornecimento de produção numa base de dados da Azure Cosmos nas contas onde [as chaves geridas pelo cliente](how-to-setup-cmk.md) estão ativadas.

Quando fornece o rendimento numa base de dados Azure Cosmos, o resultado é partilhado em todos os contentores (chamados contentores de base de dados partilhados) na base de dados. A única exceção ocorre se tiver especificado um débito aprovisionado em contentores específicos na base de dados. A partilha da produção de nível de base de dados entre os seus contentores é análoga à hospedagem de uma base de dados num conjunto de máquinas. Como todos os contentores dentro de uma base de dados partilham os recursos disponíveis numa máquina, naturalmente não obtém um desempenho previsível em nenhum recipiente específico. Para aprender a configurar a produção prevista numa base de dados, consulte a [produção de Configure prevista numa base de dados do Azure Cosmos.](how-to-provision-database-throughput.md) Para aprender a configurar a produção de autoescalação numa base de dados, consulte [a produção de escala automática da Provision](how-to-provision-autoscale-throughput.md).

A definição de produção numa base de dados Azure Cosmos garante que recebe sempre o resultado previsto para essa base de dados. Como todos os contentores dentro da base de dados partilham a produção a forjada, a Azure Cosmos DB não fornece quaisquer garantias de produção previsíveis para um determinado contentor nessa base de dados. A parte da produção que um recipiente específico pode receber depende de:

* O número de contentores.
* A escolha das chaves de partição para vários recipientes.
* A distribuição da carga de trabalho através de várias divisórias lógicas dos contentores. 

Recomendamos que configuure a produção numa base de dados quando pretender partilhar a produção em vários contentores, mas não quer dedicar a produção a nenhum recipiente em particular. 

Os seguintes exemplos demonstram onde é preferido a produção de provisão ao nível da base de dados:

* A partilha da produção de uma base de dados através de um conjunto de contentores é útil para uma aplicação multitenante. Cada utilizador pode ser representado por um recipiente Azure Cosmos distinto.

* Partilhar a produção de uma base de dados através de um conjunto de contentores é útil quando migra uma base de dados NoSQL, como o MongoDB ou a Cassandra, hospedado num conjunto de VMs ou de servidores físicos no local para a Azure Cosmos DB. Pense na produção a provisionada configurada na sua base de dados Azure Cosmos como um equivalente lógico, mas mais rentável e elástico, à capacidade de computação do seu cluster MongoDB ou Cassandra.  

Todos os recipientes criados dentro de uma base de dados com produção a forerada devem ser criados com uma [chave de partição](partition-data.md). A qualquer momento, a produção atribuída a um contentor dentro de uma base de dados é distribuída por todas as divisórias lógicas desse contentor. Quando se tem contentores que partilham a produção configurada numa base de dados, não se pode aplicar seletivamente a produção a um recipiente específico ou a uma divisória lógica. 

Se a carga de trabalho numa partição lógica consumir mais do que a produção atribuída a uma partição lógica específica, as suas operações são limitadas à taxa. Quando ocorre a limitação da taxa, pode aumentar a produção de toda a base de dados ou voltar a tentar as operações. Para obter mais informações sobre partição, consulte [as divisórias lógicas.](partition-data.md)

Os contentores numa base de dados de débito partilhado partilham o débito (RU/s) alocado a essa base de dados. Pode ter até quatro contentores com um mínimo de 400 RU/s na base de dados. Com a produção normalizada (manual), cada novo recipiente após os quatro primeiros exigirá um mínimo adicional de 100 RU/s. Por exemplo, se tiver uma base de dados de débito partilhado com oito contentores, o mínimo de RU/s na base de dados será de 800 RU/s. Com a produção de autoescala a provisionada, pode ter até 25 contentores numa base de dados com max 4000 RU/s (balanças entre 400 - 4000 RU/s).

> [!NOTE]
> Em fevereiro de 2020, introduzimos uma alteração que permite ter um máximo de 25 contentores numa base de dados de produção partilhada, o que permite melhor a partilha de produção através dos contentores. Após os primeiros 25 contentores, só pode adicionar mais contentores à base de dados se forem [a provisionados com produção dedicada](#set-throughput-on-a-database-and-a-container), que é separada da produção partilhada da base de dados.<br>
Se a sua conta DB Azure Cosmos já tiver uma base de dados de produção partilhada com >=25 contentores, a conta e todas as outras contas na mesma subscrição do Azure estão isentas desta alteração. Contacte [o suporte do produto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se tiver feedback ou dúvidas. 

Se as suas cargas de trabalho envolverem a eliminação e recriação de todas as coleções numa base de dados, recomenda-se que deixe cair a base de dados vazia e recrie uma nova base de dados antes da criação da recolha. A imagem a seguir mostra como uma partição física pode acolher uma ou mais divisórias lógicas que pertencem a diferentes recipientes dentro de uma base de dados:

:::image type="content" source="./media/set-throughput/resource-partition2.png" alt-text="Partição física que acolhe uma ou mais divisórias lógicas de um recipiente" border="false":::

## <a name="set-throughput-on-a-database-and-a-container"></a>Colocar a produção numa base de dados e num contentor

Pode combinar os dois modelos. É permitido o fornecimento de produção na base de dados e no contentor. O exemplo a seguir mostra como prever a produção normalizada (manual) prevista numa base de dados da Azure Cosmos e num contentor:

* Pode criar uma base de dados Azure Cosmos chamada *Z* com produção padrão (manual) de *RUs "K".* 
* Em seguida, crie cinco recipientes chamados *A,* *B,* *C,* *D*e *E* dentro da base de dados. Ao criar o contentor B, certifique-se de que permite a **produção dedicada a esta** opção de contentor e configure explicitamente as RUs *"P"* de produção prevista neste recipiente. Note que só pode configurar a produção partilhada e dedicada ao criar a base de dados e o contentor. 

   :::image type="content" source="./media/set-throughput/coll-level-throughput.png" alt-text="Partição física que acolhe uma ou mais divisórias lógicas de um recipiente":::

* A produção de RUs *"K"* é partilhada nos quatro contentores *A,* *C,* *D*e *E*. A quantidade exata de produção disponível para *A,* *C,* *D*ou *E* varia. Não há SLAs para a produção de cada contentor.
* O contentor chamado *B* tem a garantia de obter sempre a produção de RUs *"P".* É apoiado por SLAs.

> [!NOTE]
> Um contentor com produção a forerada não pode ser convertido para recipiente de base de dados partilhado. Inversamente, um recipiente de base de dados partilhado não pode ser convertido para ter uma produção dedicada.

## <a name="update-throughput-on-a-database-or-a-container"></a>Atualizar o rendimento numa base de dados ou num contentor

Depois de criar um recipiente Azure Cosmos ou uma base de dados, pode atualizar o rendimento previsto. Não existe limite para a produção máxima prevista que possa configurar na base de dados ou no contentor. 

Para estimar o [rendimento mínimo previsto](concepts-limits.md#storage-and-database-operations) de uma base de dados ou de um contentor, encontrará o máximo de:

* 400 RU/s 
* Armazenamento atual em GB * 10 RU/s
* RU/s mais elevados a provisionados na base de dados ou no contentor / 100
* Contagem de contentores * 100 RU/s (apenas base de dados de produção partilhada)

O ru/s mínimo real pode variar dependendo da configuração da sua conta. Pode utilizar [as métricas do Azure Monitor](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) para visualizar o histórico de produção provisitada (RU/s) e armazenamento num recurso.

Pode recuperar o rendimento mínimo de um contentor ou de uma base de dados programáticamente utilizando os SDKs ou visualizar o valor no portal Azure. Ao utilizar o .NET SDK, o [recipiente. Substituir o método DeputeseAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet&preserve-view=true) permite-lhe escalar o valor de produção previsto. Ao utilizar o SDK Java, o método [CosmosContainer.replaceProvisionedThroughput](sql-api-java-sdk-samples.md) permite-lhe escalar o valor de produção previsto.

Ao utilizar o método .NET SDK, o método [Container.ReadThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.readthroughputasync?view=azure-dotnet&preserve-view=true) permite-lhe recuperar o rendimento mínimo de um contentor ou de uma base de dados. 

Pode escalar o rendimento previsto de um contentor ou de uma base de dados a qualquer momento. Quando uma operação de escala é realizada para aumentar a produção, pode demorar mais tempo devido às tarefas do sistema para obter os recursos necessários. Pode verificar o estado da operação de escala no portal Azure ou utilizar programáticamente os SDKs. Ao utilizar o .NET SDK, pode obter o estado da operação da balança utilizando o `Container.ReadThroughputAsync` método.

## <a name="comparison-of-models"></a>Comparação de modelos
Este quadro mostra uma comparação entre a produção padrão de provisionamento (manual) numa base de dados vs. num contentor. 

|**Parâmetro**  |**Produção padrão (manual) numa base de dados**  |**Produção padrão (manual) num recipiente**|**Produção de escala automática numa base de dados** | **Produção de escala automática num contentor**|
|---------|---------|---------|---------|---------|
|Ponto de entrada (ru/s mínimo) |400 RU/s. Após os quatro primeiros contentores, cada recipiente adicional requer um mínimo de 100 RU/s</li> |400| Autoescala entre 400 - 4000 RU/s. Pode ter até 25 contentores sem RU/s mínimo por recipiente</li> | Autoescala entre 400 - 4000 RU/s.|
|RU/s mínimo por contentor|100|400|--|Autoescala entre 400 - 4000 RU/s|
|RUs máximos|Ilimitado, na base de dados.|Ilimitado, no contentor.|Ilimitado, na base de dados.|Ilimitado, no contentor.
|RUs atribuídos ou disponíveis para um recipiente específico|Sem garantias. As RUs atribuídas a um determinado contentor dependem das propriedades. As propriedades podem ser a escolha das chaves de partição dos contentores que partilham a produção, a distribuição da carga de trabalho e o número de contentores. |Todas as RUs configuradas no recipiente são exclusivamente reservadas ao recipiente.|Sem garantias. As RUs atribuídas a um determinado contentor dependem das propriedades. As propriedades podem ser a escolha das chaves de partição dos contentores que partilham a produção, a distribuição da carga de trabalho e o número de contentores. |Todas as RUs configuradas no recipiente são exclusivamente reservadas ao recipiente.|
|Armazenamento máximo para um recipiente|Ilimitado.|Ilimitado|Ilimitado|Ilimitado|
|Produção máxima por partição lógica de um recipiente|10K RU/s|10K RU/s|10K RU/s|10K RU/s|
|Armazenamento máximo (dados + índice) por partição lógica de um recipiente|20 GB|20 GB|20 GB|20 GB|

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [divisórias lógicas.](partition-data.md)
* Saiba como providenciar o [padrão (manual) num recipiente Azure Cosmos](how-to-provision-container-throughput.md).
* Saiba como providenciar a [produção padrão (manual) numa base de dados da Azure Cosmos](how-to-provision-database-throughput.md).
* Saiba como providenciar a [produção de autoescalação numa base de dados ou contentor Azure Cosmos](how-to-provision-autoscale-throughput.md).
