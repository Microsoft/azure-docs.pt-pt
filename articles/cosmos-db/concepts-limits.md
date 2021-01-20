---
title: Quotas de serviço DB da Azure Cosmos
description: Quotas de serviço DB da Azure Cosmos e limites padrão em diferentes tipos de recursos.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 007bf845bab6f493fae91debefde27a4929d9f95
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611037"
---
# <a name="azure-cosmos-db-service-quotas"></a>Quotas de serviço DB da Azure Cosmos

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Este artigo fornece uma descrição geral das quotas predefinidas oferecidas a diferentes recursos no Azure Cosmos DB.

## <a name="storage-and-database-operations"></a>Operações de armazenamento e base de dados

Depois de criar uma conta Azure Cosmos sob a sua subscrição, pode gerir dados na sua conta [criando bases de dados, contentores e itens.](account-databases-containers-items.md)

### <a name="provisioned-throughput"></a>Débito aprovisionado

Pode provistirá a produção a nível de contentores ou a nível de base de dados em termos de unidades de [pedido (RU/s ou RUs)](request-units.md). O quadro que se segue enumera os limites de armazenagem e produção por contentor/base de dados.

| Recurso | Limite predefinido |
| --- | --- |
| RUs máximos por contentor[(modo dedicado a produção)](account-databases-containers-items.md#azure-cosmos-containers) | 1.000.000 por defeito. Você pode aumentá-lo [ao arquivar um bilhete de apoio Azure](create-support-request-quota-increase.md) |
| RUs máximo por base de dados[(modo de produção partilhado)](account-databases-containers-items.md#azure-cosmos-containers) | 1.000.000 por defeito. Você pode aumentá-lo [ao arquivar um bilhete de apoio Azure](create-support-request-quota-increase.md) |
| RUs máximo por partição (lógica) | 10,000 |
| Armazenamento máximo em todos os itens por partição (lógica) | 20 GB |
| Número máximo de chaves de partição distintas (lógicas) | Ilimitado |
| Armazenamento máximo por contentor | Ilimitado |
| Armazenamento máximo por base de dados | Ilimitado |
| Tamanho máximo do anexo por Conta (a função de anexo está a ser depreciada) | 2 GB |
| RU/s mínimos exigidos por 1 GB | 10 RU/s<br>**Nota:** este mínimo pode ser reduzido se a sua conta for elegível para o nosso [programa de "alto armazenamento/baixo rendimento"](set-throughput.md#high-storage-low-throughput-program) |

> [!NOTE]
> Para conhecer as melhores práticas de gestão de cargas de trabalho que tenham chaves de partição que requerem limites mais elevados para armazenamento ou produção, consulte [Criar uma chave de partição sintética](synthetic-partition-keys.md).

### <a name="minimum-throughput-limits"></a>Limites mínimos de produção

Um recipiente Cosmos (ou base de dados de produção partilhada) deve ter um rendimento mínimo de 400 RU/s. À medida que o recipiente cresce, a Cosmos DB requer uma produção mínima para garantir que a base de dados ou o contentor têm recursos suficientes para as suas operações.

A produção atual e mínima de um contentor ou de uma base de dados pode ser recuperada a partir do portal Azure ou dos SDKs. Para obter mais informações, consulte [a produção de provisão em contentores e bases de dados.](set-throughput.md) 

O ru/s mínimo real pode variar dependendo da configuração da sua conta. Pode utilizar [as métricas do Azure Monitor](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) para visualizar o histórico de produção provisitada (RU/s) e armazenamento num recurso. 

#### <a name="minimum-throughput-on-container"></a>Produção mínima no contentor 

Para estimar a produção mínima exigida a um recipiente com produção manual, encontre o máximo de:

* 400 RU/s 
* Armazenamento atual em GB * 10 RU/s
* RU/s mais elevado a provisionado no contentor / 100

Exemplo: Suponha que tenha um recipiente a provisionado com 400 RU/s e 0 GB de armazenamento. Aumenta-se a produção para 50.000 RU/s e importa 20 GB de dados. O RU/s mínimo é agora `MAX(400, 20 * 10 RU/s per GB, 50,000 RU/s / 100)` = 500 RU/s. Com o tempo, o armazenamento aumenta para 200 GB. O RU/s mínimo é agora `MAX(400, 200 * 10 RU/s per GB, 50,000 / 100)` = 2000 RU/s. 

**Nota:** a produção mínima de 10 RU/s por GB de armazenamento pode ser reduzida se a sua conta for elegível para o nosso [programa de "alto armazenamento/baixa produção".](set-throughput.md#high-storage-low-throughput-program)

#### <a name="minimum-throughput-on-shared-throughput-database"></a>Produção mínima na base de dados de produção partilhada 
Para estimar o rendimento mínimo exigido de uma base de dados de produção partilhada com produção manual, encontre o máximo de:

* 400 RU/s 
* Armazenamento atual em GB * 10 RU/s
* RU/s mais elevado a provisionado na base de dados / 100
* 400 + MAX (Contagem de contentores - 25,0) * 100 RU/s

Exemplo: Suponha que tenha uma base de dados a provisionada com 400 RU/s, 15 GB de armazenamento e 10 contentores. O RU/s mínimo é `MAX(400, 15 * 10 RU/s per GB, 400 / 100, 400 + 0 )` = 400 RU/s. Se houvesse 30 contentores na base de dados, o RU/s mínimo seria `400 + MAX(30 - 25, 0) * 100 RU/s` = 900 RU/s. 

**Nota:** a produção mínima de 10 RU/s por GB de armazenamento pode ser reduzida se a sua conta for elegível para o nosso [programa de "alto armazenamento/baixa produção".](set-throughput.md#high-storage-low-throughput-program)

Em resumo, aqui estão os limites mínimos previstos para a RU. 

| Recurso | Limite predefinido |
| --- | --- |
| RUs mínimos por contentor[(modo dedicado a produção)](./account-databases-containers-items.md#azure-cosmos-containers) | 400 |
| RUs mínimos por base de dados[(modo de produção partilhado)](./account-databases-containers-items.md#azure-cosmos-containers) | 400 RU/s para os primeiros 25 contentores. Mais 100 RU/s para cada recipiente posteriormente. |

Cosmos DB suporta a escala programática de produção (RU/s) por contentor ou base de dados através dos SDKs ou portal.    

Dependendo das atuais configurações de R/S aprovisionadas e de recursos, cada recurso pode escalar sincronizadamente e imediatamente entre o RU/s mínimo até 100x o RU/s mínimo. Se o valor de produção solicitado estiver fora do alcance, o dimensionamento é realizado de forma assíncronea. A escala assíncrona pode demorar minutos a horas a ser concluída, dependendo do rendimento solicitado e do tamanho do armazenamento de dados no recipiente.  

### <a name="serverless"></a>Sem servidor

[O Serverless](serverless.md) permite-lhe utilizar os seus recursos DB Azure Cosmos de forma baseada no consumo. O quadro que se segue enumera os limites de armazenamento e de rebendação por contentor/base de dados.

| Recurso | Limite |
| --- | --- |
| Roção máxima de RU/s por (lógica) | 5000 |
| Armazenamento máximo em todos os itens por partição (lógica) | 20 GB |
| Número máximo de chaves de partição distintas (lógicas) | Ilimitado |
| Armazenamento máximo por contentor | 50 GB |

## <a name="control-plane-operations"></a>Operações de avião de controlo

Pode [providenciar e gerir a sua conta Azure Cosmos](how-to-manage-database-account.md) utilizando os modelos Azure PowerShell, Azure CLI e Azure Resource Manager. O quadro que se segue lista os limites por subscrição, conta e número de operações.

| Recurso | Limite predefinido |
| --- | --- |
| Contas de base de dados máximas por subscrição | 50 por defeito. Você pode aumentá-lo [ao arquivar um bilhete de apoio Azure](create-support-request-quota-increase.md)|
| Número máximo de caderias regionais | 1/hora por defeito. Você pode aumentá-lo [ao arquivar um bilhete de apoio Azure](create-support-request-quota-increase.md)|

> [!NOTE]
> Os induções regionais aplicam-se apenas às contas de uma única região. As contas de escrita multi-regiões não requerem nem têm limites para alterar a região de escrita.

Cosmos DB retira automaticamente os backups dos seus dados em intervalos regulares. Para obter detalhes sobre intervalos e janelas de retenção de backup, consulte [a cópia de segurança online e a restauração de dados on-demand em Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Limites por conta

### <a name="provisioned-throughput"></a>Débito aprovisionado

| Recurso | Limite predefinido |
| --- | --- |
| Número máximo de bases de dados | Ilimitado |
| Número máximo de contentores por base de dados com produção partilhada |25 |
| Número máximo de contentores por base de dados ou conta com produção dedicada  |ilimitado |
| Número máximo de regiões | Sem limite (todas as regiões Azure) |

### <a name="serverless"></a>Sem servidor

| Recurso | Limite |
| --- | --- |
| Número máximo de bases de dados | Ilimitado |
| Número máximo de contentores por conta  | 100 |
| Número máximo de regiões | 1 (Qualquer região azul) |

## <a name="per-container-limits"></a>Limites por contentor

Dependendo da API que utilizar, um recipiente Azure Cosmos pode representar uma coleção, uma tabela ou um gráfico. Os contentores suportam configurações para [restrições únicas,](unique-keys.md) [procedimentos armazenados, gatilhos e UDFs,](stored-procedures-triggers-udfs.md)e [política de indexação](how-to-manage-indexing-policy.md). A tabela a seguir lista os limites específicos às configurações dentro de um contentor. 

| Recurso | Limite predefinido |
| --- | --- |
| Comprimento máximo da base de dados ou nome do contentor | 255 |
| Procedimentos máximos armazenados por contentor | 100 <sup>*</sup>|
| UDFs máximos por contentor | 25 <sup>*</sup>|
| Número máximo de caminhos na política de indexação| 100 <sup>*</sup>|
| Número máximo de chaves únicas por recipiente|10 <sup>*</sup>|
| Número máximo de caminhos por restrição única da chave|16 <sup>*</sup>|
| Valor máximo de TTL |2147483647|

<sup>*</sup> Pode aumentar qualquer um destes limites por contentor criando um [pedido de Suporte Azure](create-support-request-quota-increase.md).

## <a name="per-item-limits"></a>Limites por artigo

Dependendo da API que utiliza, um item Azure Cosmos pode representar um documento numa coleção, uma linha numa mesa ou um nó ou borda num gráfico. A tabela a seguir mostra os limites por item em Cosmos DB. 

| Recurso | Limite predefinido |
| --- | --- |
| Tamanho máximo de um item | 2 MB (comprimento UTF-8 da representação JSON) |
| Comprimento máximo do valor da chave de partição | Bytes de 2048 |
| Comprimento máximo do valor de ID | 1023 bytes |
| Número máximo de propriedades por item | Sem limite prático |
| Comprimento máximo do nome da propriedade | Sem limite prático |
| Comprimento máximo do valor da propriedade | Sem limite prático |
| Comprimento máximo do valor da propriedade da corda | Sem limite prático |
| Comprimento máximo do valor da propriedade numérica | IEEE754 de dupla precisão 64 bits |
| Nível máximo de nidificação para objetos/matrizes incorporados | 128 |
| Valor máximo de TTL |2147483647|

Não existem restrições às cargas de produtos como o número de propriedades e a profundidade de nidificação, exceto as restrições de comprimento na chave de partição e valores de ID, e a restrição global de tamanho de 2 MB. Pode ter de configurar a política de indexação de recipientes com estruturas de item grandes ou complexas para reduzir o consumo de RU. Veja [itens de modelação em Cosmos DB](how-to-model-partition-example.md) para um exemplo do mundo real, e padrões para gerir itens grandes.

## <a name="per-request-limits"></a>Limites por pedido

A Azure Cosmos DB suporta [a CRUD e opera operações de consulta](/rest/api/cosmos-db/) contra recursos como contentores, itens e bases de dados. Também suporta [pedidos de lote transacional](/dotnet/api/microsoft.azure.cosmos.transactionalbatch) contra vários itens com a mesma chave de partição num recipiente.

| Recurso | Limite predefinido |
| --- | --- |
| Tempo máximo de execução para uma única operação (como uma execução de procedimento armazenado ou uma única recuperação de página de consulta)| 5 seg |
| Tamanho máximo do pedido (por exemplo, procedimento armazenado, CRUD)| 2 MB |
| Tamanho máximo de resposta (por exemplo, consulta paginada) | 4 MB |
| Número máximo de operações num lote transacional | 100 |

Uma vez que uma operação como a consulta atinge o prazo de execução ou o limite de tamanho de resposta, devolve uma página de resultados e um sinal de continuação ao cliente para retomar a execução. Não existe um limite prático na duração de uma única consulta que possa correr através de páginas/continuações.

Cosmos DB usa HMAC para autorização. Você pode usar uma chave primária, ou um tokens de recursos para controlo de acesso de [grãos finos](secure-access-to-data.md) a recursos como recipientes, chaves de partição ou itens. A tabela que se segue lista limites para fichas de autorização em Cosmos DB.

| Recurso | Limite predefinido |
| --- | --- |
| Prazo máximo de validade do token primário | 15 min  |
| Prazo mínimo de validade do recurso simbólico | 10 min  |
| Prazo máximo de validade do recurso simbólico | 24 h por defeito. Você pode aumentá-lo [ao arquivar um bilhete de apoio Azure](create-support-request-quota-increase.md)|
| Distorção máxima do relógio para autorização simbólica| 15 min |

Cosmos DB suporta a execução de gatilhos durante as escritas. O serviço suporta um máximo de um pré-gatilho e um pós-gatilho por operação de escrita.

## <a name="metadata-request-limits"></a>Limites de pedido de metadados

A Azure Cosmos DB mantém metadados do sistema para cada conta. Estes metadados permitem enumerar recolhas, bases de dados, outros recursos DB da Azure Cosmos e as suas configurações gratuitamente.

| Recurso | Limite predefinido |
| --- | --- |
|A taxa máxima de recolha cria uma taxa por minuto|    100|
|Base de dados máxima criar taxa por minuto|    100|
|Taxa máxima de atualização de produção prevista por minuto|    5|

## <a name="limits-for-autoscale-provisioned-throughput"></a>Limites para a produção de autoescala prevista

Consulte o artigo [de Autoscale](provision-throughput-autoscale.md#autoscale-limits) e [as FAQ](autoscale-faq.md#lowering-the-max-rus) para obter uma explicação mais detalhada dos limites de produção e armazenamento com autoescala.

| Recurso | Limite predefinido |
| --- | --- |
| MÁXIMO RU/s o sistema pode escalar para |  `Tmax`, o autoscale max RU/s definido pelo utilizador|
| RU/s mínimos que o sistema pode escalar para | `0.1 * Tmax`|
| RU/s corrente o sistema é dimensionado para  |  `0.1*Tmax <= T <= Tmax`, com base na utilização|
| RU/s de faturação mínima por hora| `0.1 * Tmax` <br></br>A faturação é feita por hora, onde você é cobrado para o mais alto RU/s o sistema escalado em hora, ou `0.1*Tmax` , o que for maior. |
| Mínimo autoscale max RU/s para um recipiente  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)` arredondado para 1000 RU/s mais próximo |
| Mínimo de autoescala max RU/s para uma base de dados  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`, arredondado para 1000 RU/s mais próximo. <br></br>Note se a sua base de dados tem mais de 25 contentores, o sistema aumenta o máximo de autoescala mínima RU/s por 1000 RU/s por recipiente adicional. Por exemplo, se tiver 30 contentores, o máximo de autoescala mais baixo que pode definir é de 9000 RU/s (balanças entre 900 - 9000 RU/s).

## <a name="sql-query-limits"></a>Limites de consulta SQL

Cosmos DB suporta consulta de itens usando [SQL](./sql-query-getting-started.md). O quadro que se segue descreve restrições em declarações de consulta, por exemplo em termos de número de cláusulas ou comprimento de consulta.

| Recurso | Limite predefinido |
| --- | --- |
| Comprimento máximo de consulta SQL| 256 KB |
| JoiNs máximos por consulta| 5 <sup>*</sup>|
| UDFs máximos por consulta| 10 <sup>*</sup>|
| Pontos máximos por polígono| 4096 |
| Caminhos máximos incluídos por contentor| 500 |
| Caminhos máximos excluídos por contentor| 500 |
| Propriedades máximas num índice composto| 8 |

<sup>*</sup>Pode aumentar qualquer um destes limites de consulta SQL criando um [pedido de Suporte Azure.](create-support-request-quota-increase.md)

## <a name="mongodb-api-specific-limits"></a>Limites específicos da API de MongoDB

Cosmos DB suporta o protocolo de arame MongoDB para aplicações escritas contra o MongoDB. Pode encontrar os comandos e versões de protocolo suportados nas [funcionalidades e sintaxe do MongoDB suportados.](mongodb-feature-support.md)

A tabela que se segue lista os limites específicos do suporte de recurso MongoDB. Outros limites de serviço mencionados para a API SQL (core) também se aplicam à API mongodb.

| Recurso | Limite predefinido |
| --- | --- |
| Tamanho máximo da memória da consulta mongoDB (Esta limitação é apenas para a versão do servidor 3.2) | 40 MB |
|Tempo máximo de execução para operações mongoDB (para a versão do servidor 3.2)| 15 segundos|
|Tempo máximo de execução para operações mongoDB (para a versão do servidor 3.6)| 60 segundos|
| Intervalo de ligação inativo para o fecho da ligação lateral do servidor* | 30 minutos |

\* Recomendamos que as aplicações do cliente ajustem o tempo limite de ligação inativo nas definições do controlador para 2-3 minutos, porque o [tempo limite padrão para O Azure LoadBalancer é de 4 minutos](../load-balancer/load-balancer-tcp-idle-timeout.md).  Este timeout garantirá que as ligações ociosas não sejam fechadas por um equilibrador de carga intermédio entre a máquina do cliente e a Azure Cosmos DB.

## <a name="try-cosmos-db-free-limits"></a>Experimente cosmos DB Limites livres

A tabela que se segue lista os limites para o [Try Azure Cosmos DB para](https://azure.microsoft.com/try/cosmosdb/) teste gratuito.

| Recurso | Limite predefinido |
| --- | --- |
| Duração do julgamento | 30 dias (um novo julgamento pode ser solicitado após o termo) <br> Após a expiração, a informação armazenada é eliminada. |
| Recipientes máximos por subscrição (SQL, Gremlin, Table API) | 1 |
| Contentores máximos por subscrição (MongoDB API) | 3 |
| Produção máxima por contentor | 5000 |
| Produção máxima por base de dados de produção partilhada | 20 000 |
| Armazenamento total máximo por conta | 10 GB |

A Try Cosmos DB apoia a distribuição global apenas nas regiões central dos EUA, Norte da Europa e Sudeste Asiático. Os bilhetes de apoio Azure não podem ser criados para contas de Try Azure Cosmos DB. No entanto, é prestado apoio aos assinantes com planos de apoio existentes.

## <a name="azure-cosmos-db-free-tier-account-limits"></a>Limites de conta de nível livre Azure Cosmos DB

A tabela que se segue lista os limites para as [contas de nível livre do Azure Cosmos.](optimize-dev-test.md#azure-cosmos-db-free-tier)

| Recurso | Limite predefinido |
| --- | --- |
| Número de contas de nível livre por subscrição do Azure | 1 |
| Duração do desconto de nível livre | Vida inteira da conta. Deve optar durante a criação de conta. |
| MÁXIMO RU/s grátis | 400 RU/s |
| Armazenamento máximo gratuito | 5 GB |
| Número máximo de bases de dados de produção partilhadas | 5 |
| Número máximo de contentores numa base de dados de produção partilhada | 25 <br>Nas contas de nível livre, o RU/s mínimo para uma base de dados de produção partilhada com até 25 contentores é de 400 RU/s. |

Para além do acima referido, os [limites da conta Per aplicam-se](#per-account-limits) também às contas de nível livre.

> [!NOTE]
> O nível livre de Azure Cosmos DB é diferente da conta livre do Azure. A conta gratuita Azure oferece créditos e recursos Azure gratuitamente por tempo limitado. Ao utilizar o Azure Cosmos DB como parte desta conta gratuita, obtém-se um armazenamento de 25 GB e 400 RU/s de produção provisitada durante 12 meses.

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre os conceitos fundamentais da Cosmos DB, [distribuição global](distribute-data-globally.md) e [partição](partitioning-overview.md) e [produção a provisionada.](request-units.md)

Introdução ao Azure Cosmos DB com um dos nossos manuais de introdução:

* [Introdução à API SQL do Azure Cosmos DB](create-sql-api-dotnet.md)
* [Começa com a API da Azure Cosmos para a MongoDB](create-mongodb-nodejs.md)
* [Introdução à API Cassandra do Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introdução à API do Gremlin do Azure Cosmos DB](create-graph-dotnet.md)
* [Introdução à API de Tabela do Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/)
