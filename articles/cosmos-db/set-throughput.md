---
title: Débito de aprovisionar em contentores de Cosmos do Azure e bases de dados
description: Saiba como definir a taxa de transferência provisionada para seus contêineres e bancos de dados do Azure Cosmos.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.openlocfilehash: 2bcd428e2de90251d4d64111b1c3e6b6f812ac4c
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467611"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Aprovisionar débito em contentores e bases de dados

Uma base de dados do Cosmos do Azure é uma unidade de gestão para um conjunto de contentores. Uma base de dados consiste num conjunto de contentores de esquema desconhecido. Um contentor do Cosmos do Azure é a unidade de escalabilidade para o débito e armazenamento. Um contentor é particionado horizontalmente através de um conjunto de máquinas dentro de uma região do Azure e é distribuído por todas as regiões do Azure à sua conta do Cosmos do Azure.

Com Azure Cosmos DB, você pode provisionar a taxa de transferência em duas granularidades:
 
- Contentores do Azure Cosmos
- Bases de dados do Cosmos do Azure

## <a name="set-throughput-on-a-container"></a>Definir a taxa de transferência em um contêiner  

A taxa de transferência provisionada em um contêiner Cosmos do Azure é reservada exclusivamente para esse contêiner. O contentor recebe o débito aprovisionado o tempo todo. O débito aprovisionado num contentor com suporte financeiro suporte dos SLAs. Para saber como configurar a taxa de transferência em um contêiner, confira provisionar [taxa de transferência em um contêiner Cosmos do Azure](how-to-provision-container-throughput.md).

Definir a taxa de transferência provisionada em um contêiner é a opção usada com mais frequência. Você pode dimensionar de forma elástica a taxa de transferência para um contêiner Provisionando qualquer quantidade de taxa de transferência usando as [unidades de solicitação (RUs)](request-units.md). 

A taxa de transferência provisionada em um contêiner Cosmos do Azure é distribuída uniformemente entre todas as partições lógicas do contêiner. Não é possível especificar seletivamente a taxa de transferência para partições lógicas. Como uma ou mais partições lógicas de um contêiner são hospedadas por uma partição física, as partições físicas pertencem exclusivamente ao contêiner e dão suporte à taxa de transferência provisionada no contêiner. 

Se a carga de trabalho em execução em uma partição lógica consumir mais do que a taxa de transferência alocada para essa partição lógica, suas operações obterão uma taxa limitada. Quando a limitação de taxa ocorre, você pode aumentar a taxa de transferência provisionada para todo o contêiner ou tentar novamente as operações. Para obter mais informações sobre a criação de partições, consulte [partições lógicas](partition-data.md).

Recomendamos que você configure a taxa de transferência na granularidade do contêiner quando desejar o desempenho garantido para o contêiner.

A imagem a seguir mostra como uma partição física hospeda uma ou mais partições lógicas de um contêiner:

![Partição física](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Definir a taxa de transferência em um banco de dados

Quando você provisiona a taxa de transferência em um banco de dados Cosmos do Azure, a taxa de transferência é compartilhada entre todos os contêineres no banco de dados. Uma exceção é se você especificou uma taxa de transferência provisionada em contêineres específicos no banco de dados. O compartilhamento da taxa de transferência provisionada no nível de banco de dados entre seus contêineres é análogo à Hospedagem de um banco de dados em um cluster de máquinas. Como todos os contêineres em um banco de dados compartilham os recursos disponíveis em um computador, naturalmente, você não obtém um desempenho previsível em nenhum contêiner específico. Para saber como configurar a taxa de transferência provisionada em um banco de dados, consulte [Configurar a taxa de transferência provisionada em um banco de dados Cosmos do Azure](how-to-provision-database-throughput.md).

Definir a taxa de transferência em um banco de dados Cosmos do Azure garante que você receba a taxa de transferência provisionada para esse banco de dados o tempo todo. Como todos os contêineres no banco de dados compartilham a taxa de transferência provisionada, Azure Cosmos DB não fornece nenhuma garantia de taxa de transferência previsível para um contêiner específico nesse banco de dados. A parte da taxa de transferência que pode receber um contentor específico está dependente:

* O número de contêineres.
* A opção de chaves de partição para vários contêineres.
* A distribuição da carga de trabalho em várias partições lógicas dos contentores. 

Recomendamos que você configure a taxa de transferência em um banco de dados quando desejar compartilhar a taxa de transferência em vários contêineres, mas não quiser dedicar a taxa de transferência a nenhum contêiner específico. 

Os exemplos a seguir demonstram onde é preferível provisionar a taxa de transferência no nível do banco de dados:

* O compartilhamento de uma taxa de transferência provisionada de um banco de dados em um conjunto de contêineres é útil para um aplicativo multilocatário. Cada utilizador pode ser representado por um contentor do Azure Cosmos distinto.

* O compartilhamento de uma taxa de transferência provisionada de um banco de dados em um conjunto de contêineres é útil quando você migra um banco de dados NoSQL, como MongoDB ou Cassandra, hospedado em um cluster de VMs ou em servidores físicos locais para Azure Cosmos DB. Considere a taxa de transferência provisionada configurada no banco de dados Cosmos do Azure como um equivalente lógico, mas mais econômica e elástica, para aquela da capacidade de computação do cluster MongoDB ou Cassandra.  

Todos os contêineres criados dentro de um banco de dados com taxa de transferência provisionada devem ser criados com uma [chave de partição](partition-data.md). Num determinado período de tempo, o débito alocado a um contentor numa base de dados é distribuído por todas as partições lógicas nesse contentor. Quando você tem contêineres que compartilham a taxa de transferência provisionada configurada em um banco de dados, não é possível aplicar seletivamente a taxa de transferência a um contêiner específico ou a uma partição lógica. 

Se a carga de trabalho em uma partição lógica consumir mais do que a taxa de transferência alocada para uma partição lógica específica, suas operações serão limitadas por taxa. Quando a limitação de taxa ocorre, você pode aumentar a taxa de transferência do banco de dados inteiro ou tentar novamente as operações. Para obter mais informações sobre a criação de partições, consulte [partições lógicas](partition-data.md).

Várias partições lógicas que pertencem a contêineres diferentes que compartilham a taxa de transferência provisionada para um banco de dados podem ser hospedadas em uma única partição física. Embora uma única partição lógica de um contêiner sempre esteja no escopo de uma partição física, *"L"* partições lógicas entre os contêineres *"C"* que compartilham a taxa de transferência provisionada de um banco de dados podem ser mapeadas e hospedadas em *"R"* físicas Elas. 

A imagem a seguir mostra como uma partição física pode hospedar uma ou mais partições lógicas que pertencem a contêineres diferentes em um banco de dados:

![Partição física](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Definir a taxa de transferência em um banco de dados e um contêiner

Você pode combinar os dois modelos. O provisionamento de taxa de transferência no banco de dados e no contêiner é permitido. O exemplo seguinte mostra como aprovisionar o débito numa base de dados do Cosmos do Azure e um contentor:

* Você pode criar um banco de dados Cosmos do Azure chamado *Z* com taxa de transferência provisionada de *"K"* RUs. 
* Em seguida, crie cinco contêineres chamados *a*, *B*, *C*, *D*e *e no banco* de dados. Ao criar o contêiner B, certifique-se de habilitar provisionar **taxa de transferência dedicada para essa** opção de contêiner e configurar explicitamente *"P"* RUs de taxa de transferência provisionada neste contêiner. Observe que você pode configurar a taxa de transferência compartilhada e dedicada somente ao criar o banco de dados e o contêiner. 

   ![Definindo a taxa de transferência no nível do contêiner](./media/set-throughput/coll-level-throughput.png)

* A taxa de transferência de RUs *"K"* é compartilhada entre os quatro contêineres *a*, *C*, *D*e *e*. A quantidade exata de taxa de transferência disponível para *a*, *C*, *D*ou *E E* varia. Não há SLAs para a taxa de transferência de cada contêiner individual.
* O contêiner chamado *B* tem a garantia de obter a taxa de transferência de RUs *"P"* o tempo todo. Ele é apoiado por SLAs.

## <a name="update-throughput-on-a-database-or-a-container"></a>Atualizar a taxa de transferência em um banco de dados ou em um contêiner

Depois de criar um contêiner Cosmos do Azure ou um banco de dados, você pode atualizar a taxa de transferência provisionada. Não há limite para a taxa de transferência máxima provisionada que você pode configurar no banco de dados ou no contêiner. A taxa de transferência mínima provisionada depende dos seguintes fatores: 

* O tamanho máximo de dados que você já armazenou no contêiner
* A taxa de transferência máxima que você já provisiona no contêiner
* O número máximo de contêineres de Cosmos do Azure que você já criou em um banco de dados com taxa de transferência compartilhada. 

Você pode recuperar a taxa de transferência mínima de um contêiner ou de um banco de dados programaticamente usando os SDKs ou exibir o valor no portal do Azure. Ao usar o SDK do .NET, o método [DocumentClient. ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) permite que você dimensione o valor da taxa de transferência provisionada. Ao usar o SDK do Java, o método requestoptions [. setOfferThroughput](sql-api-java-samples.md#offer-examples) permite que você dimensione o valor de taxa de transferência provisionado. 

Ao usar o SDK do .NET, o método [DocumentClient. ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) permite que você recupere a taxa de transferência mínima de um contêiner ou de um banco de dados. 

Você pode dimensionar a taxa de transferência provisionada de um contêiner ou de um banco de dados a qualquer momento. Quando uma operação de dimensionamento é executada para aumentar a taxa de transferência, ela pode levar mais tempo devido às tarefas do sistema para provisionar os recursos necessários. Você pode verificar o status da operação de dimensionamento em portal do Azure ou programaticamente usando os SDKs. Ao usar o SDK do .net, você pode obter o status da operação de dimensionamento usando `DocumentClient.ReadOfferAsync` o método.

## <a name="comparison-of-models"></a>Comparação dos modelos

|**Parâmetro**  |**Débito aprovisionado num banco de dados**  |**Débito aprovisionado num contentor**|
|---------|---------|---------|
|RUs mínimo |400 (após os quatro primeiros contêineres, cada contêiner adicional requer um mínimo de 100 RUs por segundo.) |400|
|RUs mínimas por contentor|100|400|
|RUs máximos|Ilimitado, no banco de dados.|Ilimitado, no contêiner.|
|RUs atribuído ou disponível para um contêiner específico|Não existem garantias. O RUs atribuído a um determinado contêiner depende das propriedades. As propriedades podem ser a opção de chaves de partição de contêineres que compartilham a taxa de transferência, a distribuição da carga de trabalho e o número de contêineres. |Todos os RUs configurados no contentor exclusivamente estão reservados para o contentor.|
|Armazenamento máximo para um contentor|Inúmeros.|Inúmeros.|
|Débito máximo por partição lógica de um contentor|10 mil RUs|10 mil RUs|
|Armazenamento máximo (dados + índice) por partição lógica de um contentor|10 GB|10 GB|

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [partições lógicas](partition-data.md).
* Saiba como provisionar a [taxa de transferência em um contêiner Cosmos do Azure](how-to-provision-container-throughput.md).
* Saiba como provisionar a [taxa de transferência em um banco de dados Cosmos do Azure](how-to-provision-database-throughput.md).

