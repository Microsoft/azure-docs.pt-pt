---
title: Funcionalidades e comandos do Apache Cassandra suportados pela API para Cassandra do Azure Cosmos DB
description: Saiba mais sobre o suporte de funcionalidades do Apache Cassandra na API para Cassandra do Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 7f2a8ead8195328552a138b71e304c5f5a0857e2
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064055"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Funcionalidades do Apache Cassandra suportadas pela API para Cassandra do Azure Cosmos DB 

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode comunicar com a API para Cassandra do Azure Cosmos DB através de [controladores](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) open source de cliente do Cassandra compatíveis com o [protocolo de invocação](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) da Linguagem de Consulta do Cassandra (CQL) v4. 

Ao utilizar a API para Cassandra do Azure Cosmos DB, pode desfrutar dos benefícios das APIs para Apache Cassandra, bem como das funcionalidades proporcionadas pelo Azure Cosmos DB. As funcionalidades empresariais incluem [distribuição global](distribute-data-globally.md), [criação automática de partições de aumento horizontal](partition-data.md), garantias de disponibilidade e latência, encriptação de dados inativos, cópias de segurança e mais.

## <a name="cassandra-protocol"></a>Protocolo do Cassandra 

A API para Cassandra do Azure Cosmos DB é compatível com a versão **v4** do CQL. Os comandos, as ferramentas, as limitações e as exceções de CQL suportados encontram-se listados abaixo. Qualquer controlador de cliente que entenda estes protocolos deverá conseguir ligar à API para Cassandra do Azure Cosmos DB.

## <a name="cassandra-driver"></a>Controlador do Cassandra

As seguintes versões de controladores do Cassandra são suportadas pela API para Cassandra do Azure Cosmos DB:

* [Java 3.5 e posterior](https://github.com/datastax/java-driver)  
* [C# 3.5 e posterior](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5 e posterior](https://github.com/datastax/nodejs-driver)  
* [Python 3.15 e posterior](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 
## <a name="cql-data-types"></a>Tipos de dados de CQL 

A API para Cassandra do Azure Cosmos DB suporta os seguintes tipos de dados de CQL:

* ascii  
* bigint  
* blob  
* boolean  
* counter  
* date  
* decimal  
* double  
* float  
* frozen  
* inet  
* int  
* list  
* set  
* smallint  
* texto  
* hora  
* carimbo de data/hora  
* timeuuid  
* tinyint  
* tuple  
* uuid  
* varchar  
* varint  
* tuples  
* udts  
* map  

## <a name="cql-functions"></a>Funções de CQL

A API para Cassandra do Azure Cosmos DB suporta as seguintes funções de CQL:

* Certificado de  
* Funções de agregação
  * mín., máx., média, contagem
* Funções de conversão de blobs 
  * typeAsBlob(valor)  
  * blobAsType(valor)
* Funções UUID e timeuuid 
  * dateOf()  
  * now()  
  * minTimeuuid()  
  * unixTimestampOf()  
  * toDate(timeuuid)  
  * toTimestamp(timeuuid)  
  * toUnixTimestamp(timeuuid)  
  * toDate(carimbo de data/hora)  
  * toUnixTimestamp(carimbo de data/hora)  
  * toTimestamp(data)  
  * toUnixTimestamp(data) 
  


## <a name="cassandra-api-limits"></a>Limites de API do Cassandra

A API para Cassandra do Azure Cosmos DB não tem limites quanto ao tamanho dos dados armazenados nas tabelas. É possível armazenar centenas de terabytes ou de petabytes de dados sem desrespeitar os limites da chave de partição. Da mesma forma, todas as entidades ou linhas equivalentes não têm nenhum limite no número de colunas, no entanto, o tamanho total da entidade não deve exceder 2 MB. Os dados por chave de partição não podem exceder 10 GB como em todas as outras APIs.

## <a name="tools"></a>Ferramentas 

A API para Cassandra do Azure Cosmos DB é uma plataforma de serviço gerida. Não necessita de gestão nem de utilitários como o Recoletor de Lixo, a Máquina Virtual de Java (JVM) e o nodetool para gerir o cluster. Esta API suporta ferramentas como o cqlsh, que utiliza a compatibilidade com CQLv4 Binária. 

* O explorador de dados, as métricas, o diagnóstico de registos, o PowerShell e a CLI do portal do Azure são outros mecanismos suportados que permitem gerir a conta.

## <a name="cql-shell"></a>Shell de CQL  

O utilitário de linha de comandos da CQLSH é disponibilizado com o Apache Cassandra 3.1.1 e funciona imediatamente com as seguintes variáveis de ambiente ativadas:

Antes de executar os comandos que se seguem, [adicione um certificado de raiz Baltimore ao arquivo de certificados de AC](https://docs.microsoft.com/java/azure/java-sdk-add-certificate-ca-store?view=azure-java-stable#to-add-a-root-certificate-to-the-cacerts-store). 

**Windows:** 

```bash
set SSL_VERSION=TLSv1_2 
SSL_CERTIFICATE=<path to Baltimore root ca cert>
set CQLSH_PORT=10350 
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```
**Unix/Linux/Mac:**

```bash
export SSL_VERSION=TLSv1_2 
export SSL_CERTFILE=<path to Baltimore root ca cert>
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```

## <a name="cql-commands"></a>Comandos de CQL

O Azure Cosmos DB suporta os seguintes comandos de base de dados nas contas da API para Cassandra.

* CRIAR keyspace (as configurações de replicação para este comando são ignoradas)
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* SELECIONAR 
* ATUALIZAÇÃO 
* BATCH – só são suportados comandos arquivados 
* DELETE

Todas as operações CRUD quando executadas por meio do SDK compatível com CQLV4 retornarão informações adicionais sobre o erro, unidades de solicitação consumidas. Os comandos DELETE e Update precisam ser tratados com a governança de recursos em consideração para garantir o uso correto da taxa de transferência provisionada. 
* Tenha em atenção que, caso seja especificado, o valor gc_grace_seconds tem de ser zero.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

## <a name="consistency-mapping"></a>Mapeamento de consistência 

A API para Cassandra do Azure Cosmos DB permite que haja consistência em operações de leitura.  O mapeamento de consistência é detalhado [aqui](consistency-levels-across-apis.md#cassandra-mapping).

## <a name="permission-and-role-management"></a>Gestão de permissões e funções

O Azure Cosmos DB oferece suporte ao controle de acesso baseado em função (RBAC) para provisionamento, chaves giratórias, métricas de exibição e senhas/chaves de leitura/gravação e somente leitura que podem ser obtidas por meio do [portal do Azure](https://portal.azure.com). Azure Cosmos DB não oferece suporte a funções de atividades CRUD.

## <a name="keyspace-and-table-options"></a>Opções de tabela e espaço de keyspace

As opções para nome da região, classe, replication_factor e datacenter no comando "criar keyspace" são ignoradas no momento. O sistema usa o método de replicação de [distribuição global](global-dist-under-the-hood.md) da Azure Cosmos DB subjacente para adicionar as regiões. Se você precisar da presença de dados entre regiões, poderá habilitá-lo no nível da conta com o PowerShell, a CLI ou o portal, para saber mais, consulte o artigo [como adicionar regiões](how-to-manage-database-account.md#addremove-regions-from-your-database-account) . Durable_writes não pode ser desabilitado porque Azure Cosmos DB garante que cada gravação seja durável. Em todas as regiões, Azure Cosmos DB replica os dados no conjunto de réplicas composto por 4 réplicas e essa [configuração](global-dist-under-the-hood.md) de conjunto de réplicas não pode ser modificada.
 
Todas as opções são ignoradas ao criar a tabela, exceto gc_grace_seconds, que deve ser definido como zero.
O keyspace e a tabela têm uma opção extra chamada "cosmosdb_provisioned_throughput" com um valor mínimo de 400 RU/s. A taxa de transferência de keyspace permite o compartilhamento de taxa de transferência em várias tabelas e é útil para cenários quando todas as tabelas não estão utilizando a taxa de transferência provisionada. O comando ALTER TABLE permite alterar a taxa de transferência provisionada entre as regiões. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```


## <a name="usage-of-cassandra-retry-connection-policy"></a>Uso da política de conexão de repetição Cassandra

Azure Cosmos DB é um sistema controlado por recursos. Isso significa que você pode fazer um determinado número de operações em um determinado segundo com base nas unidades de solicitação consumidas pelas operações. Se um aplicativo exceder esse limite em um determinado segundo, as solicitações serão limitadas por taxa e as exceções serão geradas. O API do Cassandra no Azure Cosmos DB converte essas exceções em erros sobrecarregados no protocolo nativo Cassandra. Para garantir que seu aplicativo possa interceptar e repetir solicitações na limitação da taxa de caso, as extensões [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) e [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) são fornecidas. Se você usar outros SDKs para acessar API do Cassandra no Azure Cosmos DB, crie uma política de conexão para tentar novamente essas exceções.

## <a name="next-steps"></a>Passos seguintes

- Comece por [criar uma conta de API para Cassandra do Azure Cosmos DB, uma base de dados e uma tabela](create-cassandra-api-account-java.md) com uma aplicação Java

