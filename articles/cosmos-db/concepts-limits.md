---
title: Limites no Azure Cosmos DB
description: Este artigo descreve os limites no Azure Cosmos DB.
author: arramac
ms.author: arramac
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2019
ms.openlocfilehash: 0086327661df637dc0ae60208ed9424b4610ef0e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969496"
---
# <a name="limits-in-azure-cosmos-db"></a>Limites no Azure Cosmos DB

Este artigo fornece uma descrição geral dos limites no serviço do Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Armazenamento e débito

Depois de criar uma conta do Cosmos do Azure com a sua subscrição, pode gerir dados na sua conta por [criação de bases de dados, contentores e itens](databases-containers-items.md). Pode aprovisionar o débito num nível de contêiner ou um nível de base de dados em termos de [(RU/s ou RUs) de unidades de pedido](request-units.md). A tabela seguinte lista os limites de armazenamento e débito por contentor/base de dados.

| Resource | Limite predefinido |
| --- | --- |
| RUs máximos por contentor ([modo de aprovisionamento do débito dedicado](databases-containers-items.md#azure-cosmos-containers)) | 1.000.000 por padrão. Pode aumentá-la por [preencher um pedido de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) ou contactar-nos através de [pergunte o Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| RUs máximos por base de dados ([modo de aprovisionamento de taxa de transferência partilhada](databases-containers-items.md#azure-cosmos-containers)) | 1.000.000 por padrão. Pode aumentá-la por [preencher um pedido de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) ou contactar-nos através de [pergunte o Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| Máximo RU por chave de partição (lógicas) | 10,000 |
| Máximo de armazenamento em todos os itens por chave de partição (lógicas)| 10 GB |
| Número máximo de chaves de partição (lógicas) diferente | Ilimitado |
| Armazenamento máximo por contentor | Ilimitado |
| Armazenamento máximo por base de dados | Ilimitado |

> [!NOTE]
> Para melhores práticas gerir cargas de trabalho que têm chaves de partição que têm limites mais elevados de armazenamento ou débito, consulte [conceber para acesso frequente chaves de partição](synthetic-partition-keys.md)
>

Um contentor do Cosmos (ou a base de dados da taxa de transferência partilhada) tem de ter um débito mínimo de 400 RUs. À medida que aumenta o contentor, o débito suportado mínimo depende também os seguintes fatores:

* O armazenamento máximo consumido no contêiner é medido em incrementos de 40 RU por GB de armazenamento consumido. Por exemplo, se um contêiner contém 100 GB de dados, em seguida, a taxa de transferência tem de ser, pelo menos, 4000 RUs
* O débito máximo que alguma vez aprovisionado no contentor. O serviço suporta a reduzir o débito de um contentor para 10% do máximo aprovisionado. Por exemplo, se o débito foi aumentado para 10000 RU, em seguida, o débito mais baixo possível seria 1000 RUs
* O número total de contentores que já tenha criado numa base de dados partilhado débito, medido em 100 RUs por contentor. Por exemplo, se tiver criado cinco contentores dentro de uma base de dados da taxa de transferência partilhada, em seguida, a taxa de transferência tem de ser, pelo menos, 500 RUs

O débito atual e mínimo de um contentor ou uma base de dados pode ser obtido a partir do portal do Azure ou os SDKs. Para obter mais informações, consulte [débito de aprovisionar em contentores e bases de dados](set-throughput.md). Em resumo, aqui estão os limites de RU aprovisionados mínimo. 

| Resource | Limite predefinido |
| --- | --- |
| RUs mínimas por contentor ([modo de aprovisionamento do débito dedicado](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| RUs mínimas por base de dados ([modo de aprovisionamento de taxa de transferência partilhada](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| RUs mínimas por contentor dentro de uma base de dados da taxa de transferência partilhada | 100 |
| RUs mínimas por GB de armazenamento consumido | 40 |

O cosmos DB suporta o dimensionamento elástico de débito (RUs) por contentor ou a base de dados através de SDKs ou o portal. Cada contentor pode dimensionar de forma síncrona e imediatamente dentro de um intervalo de dimensionamento de 10 a 100 vezes, entre os valores mínimos e máximo. Se o valor de débito pedido está fora do intervalo, dimensionamento é executado de forma assíncrona. Dimensionamento assíncrono pode demorar horas a concluir consoante o pedido de débito e o tamanho de armazenamento de dados no contentor de minutos.  

## <a name="control-plane-operations"></a>Operações do painel de controlo

Pode [aprovisionar e gerir a sua conta do Azure Cosmos](how-to-manage-database-account.md) utilizando o portal do Azure, Azure PowerShell, CLI do Azure e modelos Azure Resource Manager. A tabela seguinte lista os limites por subscrição, a conta e o número de operações.

| Resource | Limite predefinido |
| --- | --- |
| Contas de base de dados máximo por subscrição | 50 por predefinição. Pode aumentá-la por [preencher um pedido de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) ou contactar-nos através de [pergunte o Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| Número máximo de ativações pós-falha regionais | 1/hora, por predefinição. Pode aumentá-la por [preencher um pedido de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) ou contactar-nos através de [pergunte o Cosmos DB](mailto:askcosmosdb@microsoft.com)|

> [!NOTE]
> As ativações pós-falha regionais só se aplicam a contas de escritas de região única. Contas de escrita de várias regiões não precisam nem impõe quaisquer limites sobre como alterar a região de escrita.

O cosmos DB tira automaticamente cópias de segurança dos seus dados em intervalos regulares. Para obter detalhes sobre os intervalos de retenção de cópia de segurança e do windows, consulte [restaurar a cópia de segurança Online e dados a pedido no Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-container-limits"></a>Limites de por contentor

Dependendo de qual API utiliza, um contentor do Cosmos do Azure pode representam uma coleção, uma tabela, ou criar um gráfico. Os contentores suportam configurações para [restrições de chave exclusivas](unique-keys.md), [procedimentos armazenados, acionadores e UDFs](stored-procedures-triggers-udfs.md), e [política de indexação](how-to-manage-indexing-policy.md). A tabela seguinte lista os limites específicos para as configurações de dentro de um contêiner. 

| Resource | Limite predefinido |
| --- | --- |
| Comprimento máximo do nome de base de dados ou de contentor | 255 |
| Máximos de procedimentos armazenados por contentor | 100 <sup>*</sup>|
| UDFs máximos por contentor | 25 <sup>*</sup>|
| Número máximo de caminhos na política de indexação| 100 <sup>*</sup>|
| Número máximo de chaves exclusivas por contentor|10 <sup>*</sup>|
| Número máximo de caminhos por restrição de chave exclusivo|16 <sup>*</sup>|

<sup>*</sup> Pode aumentar a qualquer um destes limites por contentor ao contactar o suporte do Azure ou contactar-nos através de [faça o Cosmos DB](mailto:askcosmosdb@microsoft.com).

## <a name="per-item-limits"></a>Limites de por item

Dependendo de qual API utiliza, um item do Cosmos do Azure pode representar a um documento numa coleção, uma linha numa tabela, ou um nó ou extremidade num gráfico. A tabela seguinte mostra os limites por item no Cosmos DB. 

| Resource | Limite predefinido |
| --- | --- |
| Tamanho máximo de um item | 2 MB (UTF-8 o comprimento de representação JSON) |
| Comprimento máximo de valor de chave de partição | 2048 bytes |
| Comprimento máximo do valor de id | 1024 bytes |
| Número máximo de propriedades por item | Sem limite prático |
| Profundidade de aninhamento máxima | Sem limite prático |
| Comprimento máximo do nome de propriedade | Sem limite prático |
| Comprimento máximo de valor da propriedade | Sem limite prático |
| Comprimento máximo do valor de propriedade de cadeia de caracteres | Sem limite prático |
| Comprimento máximo do valor de propriedade numérica | Precisão dupla IEEE754, 64 bits |

Não há restrições sobre as cargas de item, como o número de propriedades e aninhamento profundidade, exceto as restrições de comprimento de chave de partição e valores de id e geral restrição de 2 MB de tamanho. Poderá ter de configurar a política de indexação para contentores com estruturas de item de grandes ou complexos para reduzir o consumo de RU. Ver [modelagem de itens no Cosmos DB](how-to-model-partition-example.md) para um exemplo do mundo real e padrões para gerir itens de grandes dimensões.

## <a name="per-request-limits"></a>Limites de por solicitação

O cosmos DB suporta [operações CRUD e consulta](https://docs.microsoft.com/rest/api/cosmos-db/) relativamente aos recursos, como contentores, itens e as bases de dados.  

| Resource | Limite predefinido |
| --- | --- |
| Tempo de execução máximo para uma única operação (como uma execução do procedimento armazenado ou uma recuperação de página única consulta)| 5 s |
| Tamanho de máximo do pedido (procedimento armazenado, CRUD)| 2 MB |
| Tamanho máximo da resposta (por exemplo, paginado consulta) | 4 MB |

Uma vez uma operação como consulta atinge o limite de tamanho de resposta ou tempo limite da execução, ele retorna uma página de resultados e um token de continuação para o cliente para retomar a execução. Não existe nenhum limite prático na duração de que uma única consulta pode ser executado em páginas/continuações.

O cosmos DB utiliza HMAC para autorização. Pode utilizar uma chave mestra, ou uma [tokens de recurso](secure-access-to-data.md) para controlo de acesso detalhado a recursos, como contentores, de partição chaves ou itens. A tabela seguinte apresenta os limites para tokens de autorização no Cosmos DB.

| Resource | Limite predefinido |
| --- | --- |
| Tempo de expiração máximo de tokens principal | 15 min  |
| Tempo de expiração do token de recursos mínimo | 10 min  |
| Tempo de expiração do token de recursos máximos | 24 horas por predefinição. Pode aumentá-la por [preencher um pedido de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) ou contactar-nos através de [pergunte o Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| Máxima distorção para autorização de token| 15 min |

O cosmos DB suporta a execução de acionadores de gravações. O serviço suporta um máximo de um pré- acionador de e um pós-acionador de por operação de escrita. 

## <a name="sql-query-limits"></a>Limites de consulta SQL

O cosmos DB suporta a consulta de itens usando [SQL](how-to-sql-query.md). A tabela seguinte descreve as restrições nas instruções de consulta, por exemplo, em termos de número de cláusulas ou comprimento de consulta.

| Resource | Limite predefinido |
| --- | --- |
| Comprimento máximo de consulta SQL| 256 KB <sup>*</sup>|
| Associações máximas por consulta| 5 <sup>*</sup>|
| Máximos ANDs por consulta| 2000 <sup>*</sup>|
| Máximos ORs por consulta| 2000 <sup>*</sup>|
| UDFs máximos por consulta| 10 <sup>*</sup>|
| Máximo de argumentos por expressão| 6000 <sup>*</sup>|
| Pontos de máximos por polígono| 4096 <sup>*</sup>|

<sup>*</sup> Pode aumentar a qualquer um destes limites de consulta SQL ao contactar o suporte do Azure ou contactar-nos através de [faça o Cosmos DB](mailto:askcosmosdb@microsoft.com).

## <a name="mongodb-api-specific-limits"></a>Limites específicos de API do MongoDB

O cosmos DB suporta o protocolo de transmissão do MongoDB para aplicativos escritos em relação a MongoDB. Pode encontrar os comandos suportados e versões em de protocolo [funcionalidades suportadas e sintaxe](mongodb-feature-support.md).

A tabela seguinte lista os limites específicos de suporte de funcionalidades do MongoDB. Outros limites de serviço mencionados para o SQL API (principal) também se aplicam à API do MongoDB.

| Resource | Limite predefinido |
| --- | --- |
| Tamanho máximo da memória de consulta do MongoDB | 40 MB |
| Tempo de execução máximo para operações de MongoDB| 30s |

## <a name="try-cosmos-db-free-limits"></a>Tente limites gratuitos do Cosmos DB

A tabela seguinte lista os limites para o [Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) avaliação.

| Resource | Limite predefinido |
| --- | --- |
| Período de avaliação | 30 dias (pode ser renovada a qualquer número de vezes) |
| Contentores máximos por subscrição (SQL, Gremlin, API de tabela) | 1 |
| Contentores máximos por subscrição (MongoDB API) | 3 |
| Débito máximo por contentor | 5000 |
| Débito máximo por base de dados da taxa de transferência partilhada | 20000 |
| Armazenamento total máximo por conta | 10 GB |

Experimente a distribuição global do Cosmos DB suporta apenas as regiões dos E.U.A., Europa do Norte e Sudeste asiático. Não não possível criar pedidos de suporte do Azure para contas de experimentar o Azure Cosmos DB. No entanto, o suporte é fornecido para os subscritores com planos de suporte existentes.

## <a name="next-steps"></a>Passos Seguintes

Leia mais sobre o Cosmos DB principais conceitos [distribuição global](distribute-data-globally.md) e [criação de partições](partitioning-overview.md) e [débito aprovisionado](request-units.md).

Introdução ao Azure Cosmos DB com um dos nossos manuais de introdução:

* [Introdução à API SQL do Azure Cosmos DB](create-sql-api-dotnet.md)
* [Introdução à API do Azure Cosmos DB para MongoDB](create-mongodb-nodejs.md)
* [Introdução à API Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introdução à API do Gremlin do Azure Cosmos DB](create-graph-dotnet.md)
* [Introdução à API de Tabela do Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)
