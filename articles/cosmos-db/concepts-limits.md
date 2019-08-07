---
title: Azure Cosmos DB cotas de serviço
description: Azure Cosmos DB cotas de serviço e limites padrão em diferentes tipos de recursos.
author: arramac
ms.author: arramac
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: d3d09d466e05c97de215542c66987aa6b723afce
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827983"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB cotas de serviço

Este artigo fornece uma visão geral das cotas padrão oferecidas a diferentes recursos no Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Armazenamento e taxa de transferência

Depois de criar uma conta do Azure Cosmos em sua assinatura, você pode gerenciar os dados em sua conta [Criando bancos de dados, contêineres e itens](databases-containers-items.md). Você pode provisionar a taxa de transferência em um nível de contêiner ou em um nível de banco de dados em termos de [unidades de solicitação (ru/s ou RUs)](request-units.md). A tabela a seguir lista os limites de armazenamento e taxa de transferência por contêiner/banco de dados.

| Resource | Limite predefinido |
| --- | --- |
| RUs máxima por contêiner ([modo de taxa de transferência dedicada](databases-containers-items.md#azure-cosmos-containers)) | 1 milhão por padrão. Você pode aumentá-lo ao [arquivar um tíquete de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) |
| RUs máxima por banco de dados (modo provisionado de[taxa de transferência compartilhada](databases-containers-items.md#azure-cosmos-containers)) | 1 milhão por padrão. Você pode aumentá-lo ao [arquivar um tíquete de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) |
| RUs máxima por chave de partição (lógica) | 10,000 |
| Armazenamento máximo em todos os itens por chave de partição (lógica)| 10 GB |
| Número máximo de chaves de partição (lógica) distintas | Ilimitado |
| Armazenamento máximo por contêiner | Ilimitado |
| Armazenamento máximo por banco de dados | Ilimitado |

> [!NOTE]
> Para obter as práticas recomendadas para gerenciar cargas de trabalho com chaves de partição que precisam de limites mais altos para armazenamento ou taxa de transferência, consulte [criando chaves de partição ativa](synthetic-partition-keys.md)
>

Um contêiner Cosmos (ou banco de dados de produtividade compartilhado) deve ter uma taxa de transferência mínima de 400 RUs. À medida que o contêiner cresce, a taxa de transferência mínima suportada também depende dos seguintes fatores:

* A taxa de transferência mínima que você pode definir em um contêiner depende da taxa de transferência máxima já provisionada no contêiner. O serviço oferece suporte à redução da taxa de transferência de um contêiner para 10% do máximo provisionado. Por exemplo, se a taxa de transferência foi aumentada para 10000 RUs, a menor taxa de transferência possível provisionada seria 1000 RUs
* A taxa de transferência mínima em um banco de dados de produtividade compartilhada também depende do número total de contêineres que você já criou em um banco de dados de produtividade compartilhado, medido em 100 RUs por contêiner. Por exemplo, se você tiver criado cinco contêineres em um banco de dados de produtividade compartilhado, a taxa de transferência deverá ser pelo menos 500 RUs

A taxa de transferência atual e mínima de um contêiner ou de um banco de dados pode ser recuperada do portal do Azure ou dos SDKs. Para obter mais informações, consulte provisionar [taxa de transferência em contêineres e bancos de dados](set-throughput.md). 

> [!NOTE]
> Em alguns casos, talvez seja possível reduzir a taxa de transferência para menos de 10%. Use a API para obter o máximo de RUs exato por contêiner.
>

Em resumo, aqui estão os limites mínimos de RU provisionados. 

| Resource | Limite predefinido |
| --- | --- |
| RUs mínima por contêiner ([modo de taxa de transferência dedicada](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| RUs mínima por banco de dados (modo provisionado de[taxa de transferência compartilhada](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| RUs mínima por contêiner em um banco de dados de produtividade compartilhado | 100 |

O Cosmos DB dá suporte ao ajuste elástico de taxa de transferência (RUs) por contêiner ou banco de dados por meio de SDKs ou Portal. Cada contêiner pode ser dimensionado de forma síncrona e imediatamente dentro de um intervalo de escala de 10 a 100 vezes, entre os valores mínimo e máximo. Se o valor de taxa de transferência solicitado estiver fora do intervalo, o dimensionamento será executado de forma assíncrona. O dimensionamento assíncrono pode levar de minutos a horas para ser concluído, dependendo da taxa de transferência e do tamanho de armazenamento de dados solicitados no contêiner.  

## <a name="control-plane-operations"></a>Operações de plano de controle

Você pode [provisionar e gerenciar sua conta do Azure Cosmos](how-to-manage-database-account.md) usando os modelos portal do Azure, Azure PowerShell, CLI do Azure e Azure Resource Manager. A tabela a seguir lista os limites por assinatura, conta e número de operações.

| Resource | Limite predefinido |
| --- | --- |
| Máximo de contas de banco de dados por assinatura | 50 por padrão. Você pode aumentá-lo ao [arquivar um tíquete de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)|
| Número máximo de failovers regionais | 1/hora por padrão. Você pode aumentá-lo ao [arquivar um tíquete de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)|

> [!NOTE]
> Failovers regionais só se aplicam a contas de gravações de região única. As contas de gravação de várias regiões não exigem ou têm limites para alterar a região de gravação.

Cosmos DB automaticamente faz backups de seus dados em intervalos regulares. Para obter detalhes sobre intervalos de retenção e janelas de backup, consulte [backup online e restauração de dados sob demanda em Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Limites por conta

| Resource | Limite predefinido |
| --- | --- |
| Número máximo de bancos de dados | Ilimitado |
| Número máximo de contêineres por banco de dados (ou conta) | Ilimitado |
| Número máximo de regiões | Sem limite (todas as regiões do Azure) |

## <a name="per-container-limits"></a>Limites por contêiner

Dependendo de qual API você usa, um contêiner Cosmos do Azure pode representar uma coleção, uma tabela ou um grafo. Os contêineres dão suporte a configurações para [restrições de chave exclusiva](unique-keys.md), [procedimentos armazenados, gatilhos e UDFs](stored-procedures-triggers-udfs.md)e [política](how-to-manage-indexing-policy.md)de indexação. A tabela a seguir lista os limites específicos para as configurações dentro de um contêiner. 

| Resource | Limite predefinido |
| --- | --- |
| Comprimento máximo do banco de dados ou nome do contêiner | 255 |
| Máximo de procedimentos armazenados por contêiner | 100 <sup>*</sup>|
| Máximo de UDFs por contêiner | 25 <sup>*</sup>|
| Número máximo de caminhos na política de indexação| 100 <sup>*</sup>|
| Número máximo de chaves exclusivas por contêiner|10 <sup>*</sup>|
| Número máximo de caminhos por restrição de chave exclusiva|16 <sup>*</sup>|

<sup>*</sup>Você pode aumentar qualquer um desses limites por contêiner entrando em contato com o suporte do Azure.

## <a name="per-item-limits"></a>Limites por item

Dependendo de qual API você usa, um item Cosmos do Azure pode representar um documento em uma coleção, uma linha em uma tabela ou um nó ou borda em um grafo. A tabela a seguir mostra os limites por item em Cosmos DB. 

| Resource | Limite predefinido |
| --- | --- |
| Tamanho máximo de um item | 2 MB (comprimento UTF-8 da representação JSON) |
| Comprimento máximo do valor de chave de partição | 2048 bytes |
| Comprimento máximo do valor da ID | 1024 bytes |
| Número máximo de propriedades por item | Sem limite prático |
| Profundidade máxima de aninhamento | Sem limite prático |
| Comprimento máximo do nome da propriedade | Sem limite prático |
| Comprimento máximo do valor da propriedade | Sem limite prático |
| Comprimento máximo do valor da propriedade da cadeia de caracteres | Sem limite prático |
| Comprimento máximo do valor da propriedade numérica | IEEE754 de precisão dupla de 64 bits |

Não há restrições sobre as cargas de item, como o número de propriedades e a profundidade de aninhamento, exceto as restrições de comprimento nos valores de chave de partição e ID e a restrição de tamanho geral de 2 MB. Talvez seja necessário configurar a política de indexação para contêineres com estruturas de item grandes ou complexas para reduzir o consumo de RU. Consulte [modelando itens em Cosmos DB](how-to-model-partition-example.md) para obter um exemplo real e padrões para gerenciar itens grandes.

## <a name="per-request-limits"></a>Limites por solicitação

O Cosmos DB dá suporte a [operações CRUD e de consulta](https://docs.microsoft.com/rest/api/cosmos-db/) em recursos como contêineres, itens e bancos de dados.  

| Resource | Limite predefinido |
| --- | --- |
| Tempo máximo de execução para uma única operação (como uma execução de procedimento armazenado ou uma única recuperação de página de consulta)| 5 s |
| Tamanho máximo da solicitação (procedimento armazenado, CRUD)| 2 MB |
| Tamanho máximo da resposta (por exemplo, consulta paginada) | 4 MB |

Quando uma operação como consulta atinge o tempo limite de execução ou o limite de tamanho de resposta, ela retorna uma página de resultados e um token de continuação para o cliente para retomar a execução. Não há nenhum limite prático na duração de uma única consulta pode ser executada em páginas/continuações.

Cosmos DB usa HMAC para autorização. Você pode usar uma chave mestra ou tokens de [recurso](secure-access-to-data.md) para controle de acesso refinado a recursos como contêineres, chaves de partição ou itens. A tabela a seguir lista os limites para tokens de autorização no Cosmos DB.

| Resource | Limite predefinido |
| --- | --- |
| Tempo máximo de expiração do token mestre | 15 min  |
| Tempo de expiração do token de recurso mínimo | 10 min  |
| Tempo máximo de expiração do token de recurso | 24 h por padrão. Você pode aumentá-lo ao [arquivar um tíquete de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)|
| Distorção máxima de relógio para autorização de token| 15 min |

Cosmos DB dá suporte à execução de gatilhos durante as gravações. O serviço dá suporte a um máximo de um pré-gatilho e um pós-gatilho por operação de gravação. 

## <a name="sql-query-limits"></a>Limites de consulta SQL

Cosmos DB dá suporte à consulta de itens usando [SQL](how-to-sql-query.md). A tabela a seguir descreve as restrições nas instruções de consulta, por exemplo, em termos de número de cláusulas ou comprimento de consulta.

| Resource | Limite predefinido |
| --- | --- |
| Comprimento máximo da consulta SQL| 256 KB <sup>*</sup>|
| Máximo de junções por consulta| 5 <sup>*</sup>|
| Máximo de ANDs por consulta| 2000 <sup>*</sup>|
| Máximo de ORs por consulta| 2000 <sup>*</sup>|
| Máximo de UDFs por consulta| 10 <sup>*</sup>|
| Máximo de argumentos por expressão| 6000 <sup>*</sup>|
| Máximo de pontos por polígono| 4096 <sup>*</sup>|

<sup>*</sup>Você pode aumentar qualquer um desses limites de consulta SQL entrando em contato com o suporte do Azure.

## <a name="mongodb-api-specific-limits"></a>Limites específicos da API do MongoDB

O Cosmos DB dá suporte ao protocolo de transmissão do MongoDB para aplicativos escritos no MongoDB. Você pode encontrar os comandos e as versões de protocolo com suporte em [sintaxe e recursos do MongoDB com suporte](mongodb-feature-support.md).

A tabela a seguir lista os limites específicos para o suporte a recursos do MongoDB. Outros limites de serviço mencionados para a API do SQL (núcleo) também se aplicam à API do MongoDB.

| Resource | Limite predefinido |
| --- | --- |
| Tamanho máximo de memória de consulta do MongoDB | 40 MB |
| Tempo máximo de execução para operações do MongoDB| 30s |

## <a name="try-cosmos-db-free-limits"></a>Experimente Cosmos DB limites gratuitos

A tabela a seguir lista os limites para o [Azure Cosmos DB de teste para avaliação gratuita](https://azure.microsoft.com/try/cosmosdb/) .

| Resource | Limite predefinido |
| --- | --- |
| Duração da avaliação | 30 dias (pode ser renovado várias vezes) |
| Máximo de contêineres por assinatura (SQL, Gremlin, API de Tabela) | 1 |
| Máximo de contêineres por assinatura (API do MongoDB) | 3 |
| Taxa de transferência máxima por contêiner | 5000 |
| Taxa de transferência máxima por banco de dados de taxa de transferência compartilhada | 20000 |
| Máximo de armazenamento total por conta | 10 GB |

Tente Cosmos DB dá suporte à distribuição global somente nas regiões EUA Central, Europa Setentrional e sudeste asiático. Tíquetes de suporte do Azure não podem ser criados para contas try Azure Cosmos DB. No entanto, o suporte é fornecido para assinantes com planos de suporte existentes.

## <a name="next-steps"></a>Passos Seguintes

Leia mais sobre os conceitos principais do Cosmos DB a [distribuição global](distribute-data-globally.md) e o [particionamento](partitioning-overview.md) e a [taxa de transferência](request-units.md)provisionada.

Introdução ao Azure Cosmos DB com um dos nossos manuais de introdução:

* [Introdução à API SQL do Azure Cosmos DB](create-sql-api-dotnet.md)
* [Introdução à API do Azure Cosmos DB para MongoDB](create-mongodb-nodejs.md)
* [Introdução à API Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introdução à API do Gremlin do Azure Cosmos DB](create-graph-dotnet.md)
* [Introdução à API de Tabela do Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)
