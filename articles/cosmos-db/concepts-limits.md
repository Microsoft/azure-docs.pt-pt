---
title: Quotas de serviço Azure Cosmos DB
description: Quotas de serviço Azure Cosmos DB e limites de padrão em diferentes tipos de recursos.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 7ef7a55c81441077d2217ccfc41a2a9c9578eefe
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697562"
---
# <a name="azure-cosmos-db-service-quotas"></a>Quotas de serviço Azure Cosmos DB

Este artigo fornece uma descrição geral das quotas predefinidas oferecidas a diferentes recursos no Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Armazenamento e entrada

Depois de criar uma conta Azure Cosmos sob a sua subscrição, pode gerir dados na sua conta criando bases de [dados, contentores e itens.](databases-containers-items.md) Pode fornecer a entrada ao nível de um contentor ou a um nível de base de dados em termos de unidades de [pedido (RU/s ou RUs)](request-units.md). A tabela seguinte enumera os limites de armazenamento e de entrada por recipiente/base de dados.

| Recurso | Limite predefinido |
| --- | --- |
| RUs máximos por recipiente[(modo de fornecimento de entrada dedicado)](databases-containers-items.md#azure-cosmos-containers) | 1.000.000 por defeito. Você pode aumentá-lo [apresentando um bilhete](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) de apoio Azure |
| RUs máximopor base de dados[(modo de fornecimento de entrada partilhada)](databases-containers-items.md#azure-cosmos-containers) | 1.000.000 por defeito. Você pode aumentá-lo [apresentando um bilhete](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) de apoio Azure |
| RUs máximos por chave (lógica) de partição | 10,000 |
| Armazenamento máximo em todos os itens por chave de partição (lógica)| 20 GB |
| Número máximo de chaves de partição distintas (lógicas) | Ilimitado |
| Armazenamento máximo por recipiente | Ilimitado |
| Armazenamento máximo por base de dados | Ilimitado |
| Tamanho máximo do anexo por Conta (função de anexo está a ser amortizado) | 2 GB |
| RUs mínimos exigidos por 1 GB | 10 RU/s |

> [!NOTE]
> Para aprender sobre as melhores práticas para gerir cargas de trabalho que possuam chaves de partição que requerem limites mais elevados para armazenamento ou entrada, consulte Criar uma chave de [partição sintética](synthetic-partition-keys.md).
>

Um recipiente Cosmos (ou base de dados de entrada partilhada) deve ter uma entrada mínima de 400 RU/s. À medida que o recipiente cresce, o rendimento mínimo suportado também depende dos seguintes fatores:

* A entrada máxima já aprovisionada no recipiente. Por exemplo, se a sua entrada fosse aumentada para 50.000 RU/s, então a menor entrada possível seria 500 RU/s
* O armazenamento atual em GB no recipiente. Por exemplo, se o seu recipiente tiver 100 GB de armazenamento, então a menor entrada possível seria 1000 RU/s
* A entrada mínima numa base de dados de entradas partilhadas também depende do número total de contentores que já criou numa base de dados de entradas partilhadas, medida a 100 RU/s por recipiente. Por exemplo, se criou cinco contentores dentro de uma base de dados de entrada partilhada, então a entrada deve ser de pelo menos 500 RU/s

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
| Procedimentos máximos armazenados por recipiente | 100<sup>*</sup>|
| UDFs máximos por recipiente | 25<sup>*</sup>|
| Número máximo de caminhos na política de indexação| 100<sup>*</sup>|
| Número máximo de chaves únicas por recipiente|10<sup>*</sup>|
| Número máximo de caminhos por restrição de chave única|16<sup>*</sup>|

<sup>*</sup>Pode aumentar qualquer um destes limites por contentor contactando o Suporte Azure.

## <a name="per-item-limits"></a>Limites por item

Dependendo da API que você usa, um item Azure Cosmos pode representar um documento em uma coleção, uma linha em uma mesa, ou um nó ou borda em um gráfico. A tabela seguinte mostra os limites por item em Cosmos DB. 

| Recurso | Limite predefinido |
| --- | --- |
| Tamanho máximo de um item | 2 MB (comprimento UTF-8 da representação JSON) |
| Comprimento máximo do valor-chave da divisória | Bytes de 2048 |
| Comprimento máximo do valor de identificação | 1023 bytes |
| Número máximo de imóveis por item | Sem limite prático |
| Profundidade máxima de nidificação | Sem limite prático |
| Comprimento máximo do nome da propriedade | Sem limite prático |
| Comprimento máximo do valor patrimonial dos imóveis | Sem limite prático |
| Comprimento máximo do valor da propriedade de cordas | Sem limite prático |
| Comprimento máximo do valor da propriedade numérica | IEEE754 dupla precisão 64-bit |

Não existem restrições às cargas de artigos como o número de propriedades e a profundidade de nidificação, com exceção das restrições de comprimento na chave de divisória e nos valores de ID, e na restrição de tamanho global de 2 MB. Pode ter de configurar a política de indexação de contentores com estruturas de itens grandes ou complexas para reduzir o consumo de RU. Consulte artigos de [Modelação em Cosmos DB](how-to-model-partition-example.md) para um exemplo real, e padrões para gerir itens grandes.

## <a name="per-request-limits"></a>Limites por pedido

A Azure Cosmos DB apoia [operações de CRUD e consultas](https://docs.microsoft.com/rest/api/cosmos-db/) contra recursos como contentores, itens e bases de dados. Também suporta pedidos de [lote transacional](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch) contra vários itens com a mesma chave de partição num recipiente.

| Recurso | Limite predefinido |
| --- | --- |
| Tempo máximo de execução para uma única operação (como uma execução de procedimento armazenado ou uma única recolha de páginas de consulta)| 5 seg |
| Tamanho máximo do pedido (por exemplo, procedimento armazenado, CRUD)| 2 MB |
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

## <a name="limits-for-autoscale-provisioned-throughput"></a>Limites para a produção de escala automática aprovisionada

Consulte o artigo de [escala automática](provision-throughput-autoscale.md#autoscale-limits) e [faQ](autoscale-faq.md#lowering-the-max-rus) para obter uma explicação mais detalhada dos limites de produção e armazenamento com escala automática.

| Recurso | Limite predefinido |
| --- | --- |
| Ru/s máximo o sistema pode escalar para |  `Tmax`, o max RU/s de escala automática definido pelo utilizador|
| Ru/s mínimo o sistema pode escalar para | `0.1 * Tmax`|
| Ru/s atual o sistema é dimensionado para  |  `0.1*Tmax <= T <= Tmax`, com base na utilização|
| Ru/s faturados mínimos por hora| `0.1 * Tmax` <br></br>A faturação é feita por hora, onde você é cobrado para o ru/s mais alto que o sistema escalado na hora, ou `0.1*Tmax` , o que for maior. |
| Max RU/s de escala automática mínima para um recipiente  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)`arredondado para 1000 RU/s mais próximo |
| Max RU/s de escala automática mínima para uma base de dados  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`, arredondado para 1000 RU/s mais próximo. <br></br>Note que se a sua base de dados tiver mais de 25 contentores, o sistema aumenta o max RU/s de escala automática mínima por 1000 RU/s por recipiente adicional. Por exemplo, se tiver 30 contentores, o máximo ru/s de escala automática mais baixo que pode definir é 9000 RU/s (escalas entre 900 - 9000 RU/s).

## <a name="sql-query-limits"></a>Limites de consulta SQL

Cosmos DB suporta itens de consulta usando [SQL](how-to-sql-query.md). O quadro seguinte descreve restrições em declarações de consulta, por exemplo, em termos de número de cláusulas ou comprimento de consulta.

| Recurso | Limite predefinido |
| --- | --- |
| Comprimento máximo da consulta SQL| 256 KB |
| Máximo joins por consulta| 5<sup>*</sup>|
| UDFs máximos por consulta| 10<sup>*</sup>|
| Pontos máximos por polígono| 4096 |
| Caminhos máximos incluídos por contentor| 500 |
| Caminhos máximos excluídos por contentor| 500 |
| Propriedades máximas num índice composto| 8 |

<sup>*</sup>Pode aumentar estes limites de consulta SQL contactando o Suporte Azure.

## <a name="mongodb-api-specific-limits"></a>Limites específicos da API mongoDB

Cosmos DB apoia o protocolo de arame MongoDB para aplicações escritas contra mongoDB. Pode encontrar os comandos suportados e versões protocolares nas [funcionalidades suportadas do MongoDB e na sintaxe.](mongodb-feature-support.md)

A tabela seguinte enumera os limites específicos do suporte à funcionalidade MongoDB. Outros limites de serviço mencionados para a API SQL (núcleo) aplicam-se igualmente à API MongoDB.

| Recurso | Limite predefinido |
| --- | --- |
| Tamanho máximo da memória de consulta MongoDB (Esta limitação é apenas para a versão do servidor 3.2) | 40 MB |
| Tempo máximo de execução para operações da MongoDB| 30 anos |
| Tempo limite de ligação inativo para o fecho da ligação lateral do servidor* | 30 minutos |

\*Recomendamos que as aplicações do cliente estabeleçam o tempo limite de ligação inativo nas definições do controlador para 2-3 minutos, porque o [tempo limite padrão para o Azure LoadBalancer é](../load-balancer/load-balancer-tcp-idle-timeout.md#tcp-idle-timeout)de 4 minutos .  Este prazo irá garantir que as ligações inativas não sejam fechadas por um equilibrador de carga intermédio entre a máquina cliente e a Azure Cosmos DB.

## <a name="try-cosmos-db-free-limits"></a>Experimente os limites livres cosmos DB

A tabela seguinte enumera os limites para o [teste Try Azure Cosmos DB para teste gratuito.](https://azure.microsoft.com/try/cosmosdb/)

| Recurso | Limite predefinido |
| --- | --- |
| Duração do ensaio | 30 dias (um novo julgamento pode ser solicitado após expiração) <br> Após a expiração, as informações armazenadas são eliminadas. |
| Recipientes máximos por subscrição (SQL, Gremlin, Tabela API) | 1 |
| Recipientes máximos por subscrição (API MongoDB) | 3 |
| Potência máxima por recipiente | 5000 |
| O máximo de entrada por base de dados de entrada partilhada | 20 000 |
| Armazenamento total máximo por conta | 10 GB |

A Try Cosmos DB apoia a distribuição global apenas nas regiões centro dos EUA, Norte da Europa e Sudeste Asiático. Os bilhetes de apoio Azure não podem ser criados para as contas Do Try Azure Cosmos DB. No entanto, é prestado apoio aos assinantes com planos de apoio existentes.

## <a name="free-tier-account-limits"></a>Limites da conta de escalão gratuito
A tabela seguinte enumera os limites para [as contas de nível livre Azure Cosmos DB.](optimize-dev-test.md#azure-cosmos-db-free-tier)

| Recurso | Limite predefinido |
| --- | --- |
| Número de contas de nível livre por subscrição do Azure | 1 |
| Duração do desconto de nível livre | A vida útil da conta. Deve optar por participar durante a criação de conta. |
| Máximo ru/s grátis | 400 RU/s |
| Armazenamento máximo gratuitamente | 5 GB |
| Número máximo de bases de dados de entrada partilhada | 5 |
| Número máximo de contentores numa base de dados de entrada partilhada | 25 <br>Nas contas de nível livre, o mínimo de RU/s para uma base de dados de entrada partilhada com até 25 contentores é de 400 RU/s. |

  Para além do que precede, os [limites por conta aplicam-se](#per-account-limits) igualmente às contas de nível livre.

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
