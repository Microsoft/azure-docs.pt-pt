---
title: Débito de aprovisionar em contentores de Cosmos do Azure e bases de dados
description: Saiba como definir a entrada disponibilizada para os seus contentores e bases de dados Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: 31ad7a9d1108adc9071812454419252a813cb93e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364535"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Aprovisionar débito em contentores e bases de dados

Uma base de dados do Cosmos do Azure é uma unidade de gestão para um conjunto de contentores. Uma base de dados consiste num conjunto de contentores de esquema desconhecido. Um contentor do Cosmos do Azure é a unidade de escalabilidade para o débito e armazenamento. Um contentor é particionado horizontalmente através de um conjunto de máquinas dentro de uma região do Azure e é distribuído por todas as regiões do Azure à sua conta do Cosmos do Azure.

Com a Azure Cosmos DB, pode fornecer a sua provisão em duas granularidades:
 
- Contentores do Azure Cosmos
- Bases de dados do Azure Cosmos

## <a name="set-throughput-on-a-container"></a>Coloque a entrada num recipiente  

A entrada disponibilizada num contentor Azure Cosmos é exclusivamente reservada para esse contentor. O contentor recebe o débito aprovisionado o tempo todo. O débito aprovisionado num contentor com suporte financeiro suporte dos SLAs. Para aprender a configurar a entrada num recipiente, consulte a [entrada de provisionamento num recipiente Azure Cosmos](how-to-provision-container-throughput.md).

A regulação da entrada aprovisionada num recipiente é a opção mais utilizada. Pode escalar elástico a entrada de um recipiente, aprovisionando qualquer quantidade de entrada utilizando Unidades de [Pedido (RUs)](request-units.md). 

A entrada prevista para um recipiente é distribuída uniformemente entre as suas divisórias físicas, e assumindo uma boa chave de partição que distribui as divisórias lógicas uniformemente entre as divisórias físicas, a entrada também é distribuída uniformemente em todos os as divisórias lógicas do recipiente. Não é possível especificar seletivamente a entrada para divisórias lógicas. Uma vez que uma ou mais divisórias lógicas de um recipiente são alojadas por uma divisória física, as divisórias físicas pertencem exclusivamente ao recipiente e suportam a entrada aprovisionada no recipiente. 

Se a carga de trabalho em funcionamento numa divisória lógica consumir mais do que a entrada que foi atribuída a essa partição lógica, as suas operações ficam limitadas. Quando ocorrer a limitação da taxa, pode aumentar a entrada prevista para todo o contentor ou voltar a tentar as operações. Para obter mais informações sobre a partilha, consulte [divisórias lógicas.](partition-data.md)

Recomendamos que configure a entrada na granularidade do recipiente quando pretender um desempenho garantido para o recipiente.

A imagem seguinte mostra como uma divisória física acolhe uma ou mais divisórias lógicas de um recipiente:

![Partição física](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Definir a entrada numa base de dados

Quando você disponibiliza a entrada numa base de dados Azure Cosmos, a entrada é partilhada em todos os recipientes (chamados recipientes de base de dados partilhadas) na base de dados. Uma exceção é se especificou uma entrada aprovisionada em recipientes específicos na base de dados. A partilha da produção de nível de base de dados aprovisionada entre os seus contentores é análoga ao alojamento de uma base de dados sobre um conjunto de máquinas. Como todos os contentores dentro de uma base de dados partilham os recursos disponíveis numa máquina, naturalmente não obtém um desempenho previsível em nenhum recipiente específico. Para aprender a configurar a entrada disponibilizada numa base de dados, consulte o [Configure aprovisionado numa base de dados Azure Cosmos](how-to-provision-database-throughput.md).

A definição de entrada numa base de dados Azure Cosmos garante que recebe o fornecimento de entrada para essa base de dados a toda a hora. Como todos os contentores dentro da base de dados partilham o fornecimento de entrada, o Azure Cosmos DB não fornece quaisquer garantias previsíveis de entrada para um determinado recipiente nessa base de dados. A parte da taxa de transferência que pode receber um contentor específico está dependente:

* O número de contentores.
* A escolha das chaves de partição para vários contentores.
* A distribuição da carga de trabalho em várias partições lógicas dos contentores. 

Recomendamos que configure a entrada numa base de dados quando pretender partilhar a entrada em vários recipientes, mas não quer dedicar a entrada a nenhum recipiente em particular. 

Os seguintes exemplos demonstram onde é preferível fornecer entrada ao nível da base de dados:

* A partilha da entrada de uma base de dados disponibilizada através de um conjunto de contentores é útil para uma aplicação multiarrendatária. Cada utilizador pode ser representado por um contentor do Azure Cosmos distinto.

* Partilhar a entrada de uma base de dados aprovisionada através de um conjunto de contentores é útil quando se migra uma base de dados NoSQL, como o MongoDB ou a Cassandra, hospedada num conjunto de VMs ou de servidores físicos no local para o Azure Cosmos DB. Pense na produção disponibilizada configurada na sua base de dados Azure Cosmos como um equivalente lógico, mas mais rentável e elástico, à capacidade computacional do seu cluster MongoDB ou Cassandra.  

Todos os recipientes criados dentro de uma base de dados com entrada aprovisionada devem ser criados com uma [chave de partição](partition-data.md). Em qualquer momento, a entrada atribuída a um recipiente dentro de uma base de dados é distribuída por todas as divisórias lógicas desse recipiente. Quando tem recipientes que partilham a entrada configurada numa base de dados, não é possível aplicar seletivamente a entrada a um recipiente específico ou a uma partição lógica. 

Se a carga de trabalho numa divisória lógica consumir mais do que a entrada atribuída a uma divisória lógica específica, as suas operações são limitadas à taxa. Quando ocorre a limitação da taxa, pode aumentar o misto para toda a base de dados ou voltar a tentar as operações. Para obter mais informações sobre a partilha, consulte [divisórias lógicas.](partition-data.md)

Os contentores numa base de dados de entradas partilhadas partilham o resultado (RU/s) atribuído a essa base de dados. Pode ter até quatro contentores com um mínimo de 400 RU/s na base de dados. Cada recipiente novo após os quatro primeiros exigirá um mínimo adicional de 100 RU/s. Por exemplo, se tiver uma base de dados de entrada partilhada com oito contentores, o mínimo de RU/s na base de dados será de 800 RU/s.

> [!NOTE]
> Em fevereiro de 2020, introduzimos uma alteração que lhe permite ter um máximo de 25 contentores numa base de dados de entrada partilhada, o que permite melhor a partilha de entrada em todos os contentores. Após os primeiros 25 contentores, só pode adicionar mais contentores à base de dados se forem [aprovisionados com uma entrada dedicada](#set-throughput-on-a-database-and-a-container), que é separada da entrada partilhada da base de dados.<br>
Se a sua conta Azure Cosmos DB já contiver uma base de dados de entrada partilhada com recipientes >=25, a conta e todas as outras contas na mesma subscrição do Azure estão isentas desta alteração. Contacte [o suporte do produto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se tiver feedback ou dúvidas. 

Se as suas cargas de trabalho implicarem a eliminar e recriar todas as coleções numa base de dados, recomenda-se que deixe cair a base de dados vazia e recrie uma nova base de dados antes da criação da recolha. A imagem que se segue mostra como uma divisória física pode alojar uma ou mais divisórias lógicas que pertencem a diferentes contentores dentro de uma base de dados:

![Partição física](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Coloque a entrada numa base de dados e num recipiente

Pode combinar os dois modelos. É permitido o fornecimento de entrada na base de dados e no recipiente. O exemplo seguinte mostra como aprovisionar o débito numa base de dados do Cosmos do Azure e um contentor:

* Pode criar uma base de dados Azure Cosmos chamada *Z* com aprodo de *"K"* RUs. 
* Em seguida, crie cinco recipientes denominados *A,* *B,* *C,* *D*e *E* dentro da base de dados. Ao criar o recipiente B, certifique-se de que ativa a **provisão dedicada para esta** opção de contentor e configura explicitamente as RUs *"P"* de entrada aprovisionada neste recipiente. Tenha em anote que só pode configurar a entrada partilhada e dedicada ao criar a base de dados e o recipiente. 

   ![Definição da entrada ao nível do contentor](./media/set-throughput/coll-level-throughput.png)

* A entrada *"K"* rUs é partilhada nos quatro recipientes *A,* *C,* *D*e *E*. A quantidade exata de entrada disponível para *A,* *C,* *D*ou *E* varia. Não existem SLAs para a entrada de cada recipiente.
* O recipiente chamado *B* é garantido para obter a entrada de RUs *"P"* a toda a hora. É apoiado por SLAs.

> [!NOTE]
> Um recipiente com entrada aprovisionada não pode ser convertido em recipiente de base de dados partilhado. Inversamente, um recipiente de base de dados partilhado não pode ser convertido para ter uma entrada dedicada.

## <a name="update-throughput-on-a-database-or-a-container"></a>Atualizar a entrada numa base de dados ou num contentor

Depois de criar um recipiente Azure Cosmos ou uma base de dados, pode atualizar a entrada prevista. Não existe um limite para a entrada máxima prevista que possa configurar na base de dados ou no recipiente. O rendimento mínimo previsto depende dos seguintes fatores: 

* O tamanho máximo de dados que já armazena no recipiente
* A entrada máxima que alguma vez fornecer no recipiente
* O número máximo de contentores Azure Cosmos que alguma vez cria numa base de dados com entrada partilhada. 

Pode recuperar a entrada mínima de um recipiente ou de uma base de dados programáticamente utilizando os SDKs ou visualizar o valor no portal Azure. Ao utilizar o .NET SDK, o [método DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) permite-lhe escalar o valor de execução provisionado. Ao utilizar o Java SDK, o método [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples) permite-lhe escalar o valor de entrada provisionado. 

Ao utilizar o .NET SDK, o método [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) permite-lhe recuperar a entrada mínima de um recipiente ou de uma base de dados. 

Pode escalar a entrada prevista de um recipiente ou de uma base de dados a qualquer momento. Quando uma operação de escala é executada para aumentar a entrada, pode demorar mais tempo devido às tarefas do sistema para fornecer os recursos necessários. Pode verificar o estado da operação de escala no portal Azure ou utilizar programaticamente os SDKs. Ao utilizar o SDK .Net, pode obter o estado do funcionamento da escala utilizando o método `DocumentClient.ReadOfferAsync`.

## <a name="comparison-of-models"></a>Comparação dos modelos

|**Parâmetro**  |**Entrada disponibilizada numa base de dados**  |**Entrada aprovisionada num recipiente**|
|---------|---------|---------|
|RUs mínimo |400 (Após os primeiros quatro contentores, cada recipiente adicional requer um mínimo de 100 RUs por segundo.) |400|
|RUs mínimas por contentor|100|400|
|RUs máximos|Ilimitado, na base de dados.|Ilimitado, no contentor.|
|RUs atribuídos ou disponíveis para um recipiente específico|Não existem garantias. As RUs atribuídas a um determinado contentor dependem das propriedades. As propriedades podem ser a escolha de chaves de divisória de recipientes que partilham a entrada, a distribuição da carga de trabalho e o número de contentores. |Todos os RUs configurados no contentor exclusivamente estão reservados para o contentor.|
|Armazenamento máximo para um contentor|Ilimitado.|Ilimitado.|
|Débito máximo por partição lógica de um contentor|10 mil RUs|10 mil RUs|
|Armazenamento máximo (dados + índice) por partição lógica de um contentor|20 GB|20 GB|

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [divisórias lógicas.](partition-data.md)
* Aprenda a [fornecer a entrada num recipiente Azure Cosmos.](how-to-provision-container-throughput.md)
* Aprenda a fornecer a entrada numa base de [dados Azure Cosmos.](how-to-provision-database-throughput.md)

