---
title: Passos de pré-migração para migrações de dados do MongoDB à API do Azure Cosmos DB para o MongoDB
description: Este documento fornece uma descrição geral dos pré-requisitos para uma migração de dados do MongoDB para o Cosmos DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 476a143555323bbb5058541000a5b1a26d23b71a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014417"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Passos de pré-migração para migrações de dados do MongoDB à API do Azure Cosmos DB para o MongoDB

Antes de migrar os dados do MongoDB (no local ou na nuvem (IaaS)) para a API do Azure Cosmos DB para o MongoDB, deve:

1. [Criar uma conta do Azure Cosmos DB](#create-account)
2. [Estimar o débito necessário para as cargas de trabalho](#estimate-throughput)
3. [Escolher uma chave de partição ideal para os seus dados](#partitioning)
4. [Compreender a política de indexação que podem ser definidos nos seus dados](#indexing)

Se já tiver concluído os pré-requisitos acima, para a migração, consulte a [MongoDB migrar dados para a API do Azure Cosmos DB para o MongoDB](../dms/tutorial-mongodb-cosmos-db.md) para as instruções de migração de dados reais. Caso contrário, este documento fornece instruções para lidar com esses pré-requisitos. 

## <a id="create-account"></a> Criar uma conta do Azure Cosmos DB 

Antes de iniciar a migração, terá [criar uma conta do Cosmos do Azure com a API do Azure Cosmos DB do MongoDB](create-mongodb-dotnet.md). 

A criação de conta, pode escolher definições para [distribuir globalmente](distribute-data-globally.md) seus dados. Tem também a opção para ativar várias regiões escritas (ou configuração de vários mestre), que permite que cada uma das suas regiões para ser tanto uma gravação e a região de leitura.

![Criação da conta](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a> Estimar a necessidade de débito de suas cargas de trabalho

Antes de iniciar a migração, utilizando o [Database Migration Service (DMS)](../dms/dms-overview.md), deve determinar a quantidade de débito para aprovisionar para as suas bases de dados do Cosmos do Azure e coleções.

Débito pode ser provisionado em qualquer um:

- Coleção

- Base de Dados

> [!NOTE]
> Também pode ter uma combinação das respostas anteriores, onde algumas coleções numa base de dados podem ter dedicados aprovisionado o débito e outras pessoas podem partilhar o débito. Para obter detalhes, consulte a [definir débito num banco de dados e um contentor](set-throughput.md) página.
>

Primeiro deve decidir se pretende aprovisionar a base de dados ou o débito de nível de coleção ou uma combinação de ambos. Em geral, recomenda-se para configurar um débito dedicado ao nível da coleção. Aprovisionamento de débito ao nível da base de dados permite que as coleções na base de dados para partilhar o débito aprovisionado. Com a taxa de transferência partilhada, no entanto, não é garantido um débito específicos em cada coleção individual e não obtém um desempenho previsível em qualquer coleção específica.

Se não tiver a certeza sobre que débito deve ser dedicado a cada coleção individual, pode escolher o débito de nível de base de dados. Pode pensar o débito aprovisionado configurado na sua base de dados do Cosmos do Azure como um equivalente lógico para que a capacidade de computação de uma VM de MongoDB ou um servidor físico, mas mais económico com a capacidade de dimensionar de forma elástica. Para obter mais informações, consulte [débito de aprovisionar em bancos de dados e contentores do Azure Cosmos](set-throughput.md).

Se aprovisionar o débito ao nível da base de dados, todas as coleções criadas dentro dessa base de dados tem de ser criadas com uma chave de partição/partição horizontal. Para obter mais informações sobre a criação de partições, consulte [criação de partições e dimensionamento horizontal no Azure Cosmos DB](partition-data.md). Se não especificar uma chave de partição/partição horizontal durante a migração, o serviço de migração de base de dados do Azure preenche automaticamente o campo de chave de partição horizontal com um *ID* atributo que é gerado automaticamente para cada documento.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>Número ideal de unidades de pedido (RUs) para aprovisionamento

No Azure Cosmos DB, o débito é aprovisionado com antecedência e é medido em unidades de pedido (RU) por segundo. Se tiver cargas de trabalho que executam o MongoDB numa VM ou no local, considere do RU como uma abstração simple para recursos físicos, tal como para o tamanho de uma VM ou de servidor no local – uma e os recursos que eles possuem, por exemplo, memória, CPU, de IOPs. 

Ao contrário das VMs ou servidores no local, o RUs são fáceis de aumentar vertical ou horizontalmente em qualquer altura. Pode alterar o número de RUs aprovisionadas em segundos, e é faturado apenas para o número máximo de RUs que for aprovisionado para um determinado período de uma hora. Para obter mais informações, consulte [unidades de pedido no Azure Cosmos DB](request-units.md).

Seguem-se os principais fatores que afetam o número de RUs necessários:
- **Tamanho do item (ou seja, o documento)**: À medida que aumenta o tamanho de um documento/item, o número de RUs consumidas para ler ou escrever o item/documento também aumenta.
- **Contagem de propriedade do item**: Supondo que o [indexação predefinida](index-overview.md) em todas as propriedades, o número de RUs consumidos para escrever um item aumenta à medida que os aumentos de contagem de propriedade do item. Pode reduzir o consumo de unidades de pedido para operações de escrita por [limitar o número de propriedades indexadas](index-policy.md).
- **Operações simultâneas**: Solicitar unidades consumidas depende também a frequência com que diferentes operações de CRUD (como escritas, leituras, atualizações, eliminações) e consultas mais complexas são executadas. Pode usar [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) para as necessidades de simultaneidade dos seus dados do MongoDB atuais de saída.
- **Padrões de consulta**: A complexidade de uma consulta afeta quantas unidades de pedido são consumidas pela consulta.

Se exportar ficheiros JSON usando [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) e compreender quantos escritas, leituras, atualiza e elimina nesse local take por segundo, pode usar o [Planeador de capacidade do Azure Cosmos DB](https://www.documentdb.com/capacityplanner) para estimar o número inicial de RUs para aprovisionar. O planeador de capacidade não avaliar o custo de consultas mais complexas. Então, se tiver consultas complexas nos seus dados, serão consumidas RUs adicionais. A Calculadora também parte do princípio que todos os campos são indexados e consistência da sessão é utilizada. A melhor maneira de compreender o custo de consultas é migrar seus dados (ou dados de exemplo) para o Azure Cosmos DB, [ligue-se ao ponto final do Cosmos DB](connect-mongodb-account.md) e executar uma consulta de exemplo a partir da Shell de MongoDB usando o `getLastRequestStastistics` comando para obter o encargos de pedidos, o número de RUs consumidos de saída:

`db.runCommand({getLastRequestStatistics: 1})`

Este comando vai gerar um documento JSON semelhante ao seguinte:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Depois de compreender o número de RUs consumidos por uma consulta e a simultaneidade precisa para essa consulta, pode ajustar o número de RUs aprovisionadas. Otimizar o RUs não é um evento único - continuamente deve otimizar ou aumentar verticalmente o RUs aprovisionado, dependendo se estiver não era esperado um tráfego intenso, em oposição a uma pesada carga de trabalho ou a importação de dados.

## <a id="partitioning"></a>Escolha a sua chave de partição
Criação de partições é um ponto importante consideração antes de migrar para uma base de dados globalmente distribuído, como o Azure Cosmos DB. O Azure Cosmos DB utiliza a criação de partições para dimensionar contentores individuais num banco de dados para satisfazer as necessidades de desempenho e escalabilidade do seu aplicativo. Na criação de partições, os itens num contentor estão divididos em subconjuntos distintos chamados partições lógicas. Para obter detalhes e recomendações sobre como escolher a chave de partição correta para os seus dados, consulte a [escolhendo uma seção de chave de partição](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Os dados de índice
Por predefinição, o Azure Cosmos DB indexa todos os seus campos de dados após a ingestão. Pode modificar os [política de indexação](index-policy.md) no Azure Cosmos DB em qualquer altura. Na verdade, muitas vezes, é recomendável desativar indexação ao migrar dados e, em seguida, voltar a ativá-lo quando os dados já estão no Cosmos DB. Para obter mais detalhes sobre indexação, pode ler mais sobre isso na [indexação no Azure Cosmos DB](index-overview.md) secção. 

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) migra automaticamente as coleções do MongoDB com índices exclusivos. No entanto, os índices exclusivos devem ser criados antes da migração. O Azure Cosmos DB não suporta a criação de índices exclusivos, se já existir dados em suas coleções. Para obter mais informações, consulte [chaves exclusivas no Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Passos Seguintes
* [Migre os dados da MongoDB para o Cosmos DB com o serviço de migração de base de dados.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Débito de aprovisionar em contentores de Cosmos do Azure e bases de dados](set-throughput.md)
* [Criação de partições no Azure Cosmos DB](partition-data.md)
* [Distribuição global no Azure Cosmos DB](distribute-data-globally.md)
* [Indexação no Azure Cosmos DB](index-overview.md)
* [Unidades de pedido no Azure Cosmos DB](request-units.md)
