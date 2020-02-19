---
title: Quotas de serviço Azure Cosmos DB
description: Quotas de serviço Azure Cosmos DB e limites de padrão em diferentes tipos de recursos.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 940ac7b0a2720283b669fc7ea5a9dbc163ddbe64
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77460216"
---
# <a name="azure-cosmos-db-service-quotas"></a>Quotas de serviço Azure Cosmos DB

Este artigo fornece uma descrição geral das quotas predefinidas oferecidas a diferentes recursos no Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Armazenamento e entrada

Depois de criar uma conta Azure Cosmos sob a sua subscrição, pode gerir dados na sua conta criando bases de [dados, contentores e itens.](databases-containers-items.md) Pode fornecer a entrada ao nível de um contentor ou a um nível de base de dados em termos de unidades de [pedido (RU/s ou RUs)](request-units.md). A tabela seguinte enumera os limites de armazenamento e de entrada por recipiente/base de dados.

| Recurso | Limite predefinido |
| --- | --- |
| RUs máximos por recipiente[(modo de fornecimento de entrada dedicado)](databases-containers-items.md#azure-cosmos-containers) | 1\.000.000 por defeito. Você pode aumentá-lo [apresentando um bilhete](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) de apoio Azure |
| RUs máximopor base de dados[(modo de fornecimento de entrada partilhada)](databases-containers-items.md#azure-cosmos-containers) | 1\.000.000 por defeito. Você pode aumentá-lo [apresentando um bilhete](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) de apoio Azure |
| RUs máximos por chave (lógica) de partição | 10,000 |
| Armazenamento máximo em todos os itens por chave de partição (lógica)| 10 GB |
| Número máximo de chaves de partição distintas (lógicas) | Ilimitado |
| Armazenamento máximo por recipiente | Ilimitado |
| Armazenamento máximo por base de dados | Ilimitado |
| Tamanho máximo do anexo por Conta (função de anexo está a ser amortizado) | 2GB |
| RUs mínimos exigidos por 1 GB | 10 RU/s |

> [!NOTE]
> Para aprender sobre as melhores práticas para gerir cargas de trabalho que possuam chaves de partição que requerem limites mais elevados para armazenamento ou entrada, consulte Criar uma chave de [partição sintética](synthetic-partition-keys.md).
>

Um recipiente Cosmos (ou base de dados de entrada partilhada) deve ter uma entrada mínima de 400 RUs. À medida que o recipiente cresce, o rendimento mínimo suportado também depende dos seguintes fatores:

* A entrada mínima que pode fixar num recipiente depende da potência máxima já disponibilizada no recipiente. Por exemplo, se a sua entrada fosse aumentada para 10000 RUs, então a menor entrada possível seria 1000 RUs
* A entrada mínima numa base de dados de entrada partilhada também depende do número total de contentores que já criou numa base de dados de produção partilhada, medida em 100 RUs por recipiente. Por exemplo, se criou cinco contentores dentro de uma base de dados de entrada partilhada, então a entrada deve ser de pelo menos 500 RUs

A entrada corrente e mínima de um recipiente ou de uma base de dados pode ser recuperada a partir do portal Azure ou dos SDKs. Para mais informações, consulte a [entrada de provisões em contentores e bases](set-throughput.md)de dados . 

> [!NOTE]
> Em alguns casos, pode ser capaz de baixar a entrada para menos de 10%. Utilize a API para obter as RUs mínimas exatas por recipiente.
>

Em resumo, aqui estão os limites mínimos previstos para a RU. 

| Recurso | Limite predefinido |
| --- | --- |
| Mínimo suscpor recipiente[(modo de fornecimento de entrada dedicado)](databases-containers-items.md#azure-cosmos-containers) | 400 |
| RUs mínimos por base de dados[(modo de fornecimento](databases-containers-items.md#azure-cosmos-containers)de rendimento partilhado) | 400 |
| RUs mínimos por recipiente dentro de uma base de dados de entrada partilhada | 100 |

Cosmos DB suporta a escala elástica de entrada (RUs) por recipiente ou base de dados através dos SDKs ou portal. Cada recipiente pode escalar sincronizadamente e imediatamente dentro de uma escala de 10 a 100 vezes, entre valores mínimos e máximos. Se o valor de entrada solicitado estiver fora do alcance, a escala é executada sincronicamente. A escala assíncrona pode demorar minutos a horas a ser concluída, dependendo do tamanho de entrada e armazenamento de dados solicitado no recipiente.  

## <a name="control-plane-operations"></a>Controlar as operações dos aviões

Pode [fornecer e gerir a sua conta Azure Cosmos](how-to-manage-database-account.md) utilizando os modelos do portal Azure, Azure PowerShell, Azure CLI e Azure Resource Manager. A tabela seguinte enumera os limites por subscrição, conta e número de operações.

| Recurso | Limite predefinido |
| --- | --- |
| Contas máximas de base de dados por subscrição | 50 por defeito. Você pode aumentá-lo [apresentando um bilhete](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) de apoio Azure|
| Número máximo de falhas regionais | 1/hora por defeito. Você pode aumentá-lo [apresentando um bilhete](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) de apoio Azure|

> [!NOTE]
> As falhas regionais aplicam-se apenas a uma única região, escreve contas. As contas escritas multi-regiões não requerem ou têm quaisquer limites para a alteração da região de escrita.

Cosmos DB recebe automaticamente cópias de segurança dos seus dados em intervalos regulares. Para mais detalhes sobre intervalos de retenção de backup e janelas, consulte a cópia de segurança online e os dados a [pedido restaurados em Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Limites por conta

| Recurso | Limite predefinido |
| --- | --- |
| Número máximo de bases de dados | Ilimitado |
| Número máximo de contentores por base de dados com entrada partilhada |25 |
| Número máximo de contentores por base de dados ou conta com entrada dedicada  |ilimitado |
| Número máximo de regiões | Sem limite (todas as regiões de Azure) |

## <a name="per-container-limits"></a>Limites por contentor

Dependendo da API que você usa, um recipiente Azure Cosmos pode representar uma coleção, uma tabela ou gráfico. Os recipientes suportam configurações para [restrições de chave únicas,](unique-keys.md) [procedimentos armazenados, gatilhos e UDFs,](stored-procedures-triggers-udfs.md)e [política de indexação.](how-to-manage-indexing-policy.md) A tabela seguinte enumera os limites específicos das configurações dentro de um recipiente. 

| Recurso | Limite predefinido |
| --- | --- |
| Comprimento máximo da base de dados ou nome do contentor | 255 |
| Procedimentos máximos armazenados por recipiente | 100 <sup>*</sup>|
| UDFs máximos por recipiente | 25 <sup>*</sup>|
| Número máximo de caminhos na política de indexação| 100 <sup>*</sup>|
| Número máximo de chaves únicas por recipiente|10 <sup>*</sup>|
| Número máximo de caminhos por restrição de chave única|16 <sup>*</sup>|

<sup>*</sup> Pode aumentar qualquer um destes limites por contentor contactando o Suporte Azure.

## <a name="per-item-limits"></a>Limites por item

Dependendo da API que você usa, um item Azure Cosmos pode representar um documento em uma coleção, uma linha em uma mesa, ou um nó ou borda em um gráfico. A tabela seguinte mostra os limites por item em Cosmos DB. 

| Recurso | Limite predefinido |
| --- | --- |
| Tamanho máximo de um item | 2 MB (comprimento UTF-8 da representação JSON) |
| Comprimento máximo do valor-chave da divisória | Bytes de 2048 |
| Comprimento máximo do valor da identificação | 1023 bytes |
| Número máximo de imóveis por item | Sem limite prático |
| Profundidade máxima de nidificação | Sem limite prático |
| Comprimento máximo do nome da propriedade | Sem limite prático |
| Comprimento máximo do valor patrimonial dos imóveis | Sem limite prático |
| Comprimento máximo do valor da propriedade de cordas | Sem limite prático |
| Comprimento máximo do valor da propriedade numérica | IEEE754 dupla precisão 64-bit |

Não existem restrições às cargas de artigos como o número de propriedades e a profundidade de nidificação, com exceção das restrições de comprimento na chave de divisória e valores id, e na restrição de tamanho global de 2 MB. Pode ter de configurar a política de indexação de contentores com estruturas de itens grandes ou complexas para reduzir o consumo de RU. Consulte artigos de [Modelação em Cosmos DB](how-to-model-partition-example.md) para um exemplo real, e padrões para gerir itens grandes.

## <a name="per-request-limits"></a>Limites por pedido

A Azure Cosmos DB apoia [operações de CRUD e consultas](https://docs.microsoft.com/rest/api/cosmos-db/) contra recursos como contentores, itens e bases de dados. Também suporta pedidos de [lote transacional](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch) contra vários itens com a mesma chave de partição num recipiente.

| Recurso | Limite predefinido |
| --- | --- |
| Tempo máximo de execução para uma única operação (como uma execução de procedimento armazenado ou uma única recolha de páginas de consulta)| 5 seg |
| Tamanho máximo do pedido (por exemplo, procedimento armazenado, CRUD)| 2 MB |
| Tamanho máximo da resposta (por exemplo, consulta paginada) | 4 MB |
| Número máximo de operações num lote transacional | 100 |

Uma vez que uma operação como a consulta atinge o prazo de execução ou o limite de tamanho da resposta, devolve uma página de resultados e um sinal de continuação ao cliente para retomar a execução. Não existe um limite prático para a duração que uma única consulta pode passar por páginas/continuações.

Cosmos DB usa HMAC para autorização. Pode utilizar uma chave principal, ou um [recurso](secure-access-to-data.md) para o controlo de acesso de grãos finos a recursos como recipientes, chaves de partição ou itens. A tabela seguinte enumera os limites para fichas de autorização em Cosmos DB.

| Recurso | Limite predefinido |
| --- | --- |
| Tempo máximo de validade do símbolo principal | 15 min  |
| Tempo mínimo de validade do recurso | 10 min  |
| Tempo máximo de validade do recurso | 24 h por defeito. Você pode aumentá-lo [apresentando um bilhete](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) de apoio Azure|
| Limite máximo do relógio para autorização simbólica| 15 min |

Cosmos DB apoia a execução de gatilhos durante os escritos. O serviço suporta um máximo de um pré-gatilho e um pós-gatilho por operação de escrita. 

## <a name="autopilot-mode-limits"></a>Limites do modo piloto automático

Consulte o artigo [autopiloto](provision-throughput-autopilot.md#autopilot-limits) para obter os limites de entrada e armazenamento no modo piloto automático.

## <a name="sql-query-limits"></a>Limites de consulta SQL

Cosmos DB suporta itens de consulta usando [SQL](how-to-sql-query.md). O quadro seguinte descreve restrições em declarações de consulta, por exemplo, em termos de número de cláusulas ou comprimento de consulta.

| Recurso | Limite predefinido |
| --- | --- |
| Comprimento máximo da consulta SQL| 256 <sup>KB*</sup>|
| Máximo joins por consulta| 5 <sup>*</sup>|
| AnDs máximos por consulta| 2000 <sup>*</sup>|
| ORs máximos por consulta| 2000 <sup>*</sup>|
| UDFs máximos por consulta| 10 <sup>*</sup>|
| Argumentos máximos por expressão IN| 6000 <sup>*</sup>|
| Pontos máximos por polígono| 4096 <sup>*</sup>|

<sup>*</sup> Pode aumentar qualquer um destes limites de consulta SQL contactando o Suporte Azure.

## <a name="mongodb-api-specific-limits"></a>Limites específicos da API mongoDB

Cosmos DB apoia o protocolo de arame MongoDB para aplicações escritas contra mongoDB. Pode encontrar os comandos suportados e versões protocolares nas [funcionalidades suportadas do MongoDB e na sintaxe.](mongodb-feature-support.md)

A tabela seguinte enumera os limites específicos do suporte à funcionalidade MongoDB. Outros limites de serviço mencionados para a API SQL (núcleo) aplicam-se igualmente à API MongoDB.

| Recurso | Limite predefinido |
| --- | --- |
| Tamanho máximo da memória da consulta MongoDB | 40 MB |
| Tempo máximo de execução para operações da MongoDB| 30 anos |

## <a name="try-cosmos-db-free-limits"></a>Experimente os limites livres cosmos DB

A tabela seguinte enumera os limites para o [teste Try Azure Cosmos DB para teste gratuito.](https://azure.microsoft.com/try/cosmosdb/)

| Recurso | Limite predefinido |
| --- | --- |
| Duração do ensaio | 30 dias (pode ser renovado várias vezes) |
| Recipientes máximos por subscrição (SQL, Gremlin, Tabela API) | 1 |
| Recipientes máximos por subscrição (API MongoDB) | 3 |
| Potência máxima por recipiente | 5000 |
| O máximo de entrada por base de dados de entrada partilhada | 20000 |
| Armazenamento total máximo por conta | 10 GB |

A Try Cosmos DB apoia a distribuição global apenas nas regiões centro dos EUA, Norte da Europa e Sudeste Asiático. Os bilhetes de apoio Azure não podem ser criados para as contas Do Try Azure Cosmos DB. No entanto, é prestado apoio aos assinantes com planos de apoio existentes.

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre os conceitos fundamentais da Cosmos DB distribuição e [partição](partitioning-overview.md) [global](distribute-data-globally.md) e [produção aprovisionada.](request-units.md)

Introdução ao Azure Cosmos DB com um dos nossos manuais de introdução:

* [Introdução à API SQL do Azure Cosmos DB](create-sql-api-dotnet.md)
* [Inicie-se com a API da Azure Cosmos DB para o MongoDB](create-mongodb-nodejs.md)
* [Introdução à API Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introdução à API do Gremlin do Azure Cosmos DB](create-graph-dotnet.md)
* [Introdução à API de Tabela do Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)
