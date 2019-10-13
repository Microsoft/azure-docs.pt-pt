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
ms.openlocfilehash: 0dcca2175d6ccc35a51bccb1e47f75d25cb8b11f
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299200"
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
  


## <a name="cassandra-query-language-limits"></a>Limites da Linguagem de Consulta do Cassandra

A API para Cassandra do Azure Cosmos DB não tem limites quanto ao tamanho dos dados armazenados nas tabelas. É possível armazenar centenas de terabytes ou de petabytes de dados sem desrespeitar os limites da chave de partição. Da mesma forma, as entidades ou linhas equivalentes não têm limites quanto ao número de colunas, mas o tamanho total da entidade não deve exceder os 2 MB.

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

* CRIAR keyspace (as configurações de replicação para esse comando são ignoradas, o sistema usa o [modelo de replicação de Azure Cosmos DB](global-dist-under-the-hood.md)subjacente. Se precisar de presença entre regiões de dados, você poderá habilitá-lo no nível da conta com o PowerShell, a CLI ou o portal para obter mais informações, consulte o artigo [como adicionar ou remover regiões para sua conta](how-to-manage-database-account.md#addremove-regions-from-your-database-account) .
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* SELECIONAR 
* ATUALIZAÇÃO 
* BATCH – só são suportados comandos arquivados 
* DELETE

Todas as operações CRUD quando executadas por meio do SDK compatível com CQLV4 retornarão informações adicionais sobre o erro, unidades de solicitação consumidas. Os comandos DELETE e Update precisam ser tratados com a governança de recursos em consideração, para evitar o uso correto da taxa de transferência provisionada. 
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

A API para Cassandra do Azure Cosmos DB permite que haja consistência em operações de leitura.  O mapeamento de consistência é detalhado [aqui [(https://docs.microsoft.com/azure/cosmos-db/consistency-levels-across-apis#cassandra-mapping).

## <a name="permission-and-role-management"></a>Gestão de permissões e funções

O Azure Cosmos DB oferece suporte ao controle de acesso baseado em função (RBAC) para provisionamento, chaves giratórias, métricas de exibição e senhas/chaves de leitura/gravação e somente leitura que podem ser obtidas por meio do [portal do Azure](https://portal.azure.com). Azure Cosmos DB não oferece suporte a funções de atividades CRUD. 

## <a name="keyspace-and-table-options"></a>Opções de tabela e espaço de keyspace

As opções de nome da região, classe, replication_factor, datacenter em criar comando keyspace são ignoradas no momento. O sistema usará a [distribuição global](https://docs.microsoft.com/en-us/azure/cosmos-db/global-dist-under-the-hood) do Azure Cosmos DB subjacente se você adicionar as regiões necessárias. Se precisar de presença entre regiões de dados, você poderá habilitá-lo no nível da conta com o PowerShell, a CLI ou o portal, para saber mais, consulte este documento: https://docs.microsoft.com/en-us/azure/cosmos-db/how-to-manage-database-account#addremove-regions-from-your-database-account. Durable_writes não pode ser desabilitado-como Cosmos DB garante que cada gravação seja durável. Em cada região Cosmos DB replica dados em um réplicaset composto por 4 réplicas e essa [configuração](https://docs.microsoft.com/en-us/azure/cosmos-db/global-dist-under-the-hood) de réplicaset não pode ser modificada. Todas as opções de criação de tabela são ignoradas, exceto gc_grace_seconds, que deve ser zero.
Keyspace e Table têm Option-cosmosdb_provisioned_throughput extra com o valor mínimo de 400. A taxa de transferência de keyspace permite o compartilhamento de taxa de transferência em várias tabelas e é útil para cenários quando todas as tabelas não estão utilizando a taxa de transferência. ALTER TABLE permite alterar a taxa de transferência provisionada entre as regiões. CRIAR keyspace sampleks com REPLICATION = {' class ': ' SimpleStrategy '} e cosmosdb_provisioned_throughput = 2000;  
CREATE TABLE sampleks. T1 (user_id INT PRIMARY KEY, LastName Text) com cosmosdb_provisioned_throughput = 2000; ALTER TABLE gks1. T1 com cosmosdb_provisioned_throughput = 10000;

## <a name="usage-of-cassandra-retry-connection-policy"></a>Uso da política de conexão de repetição Cassandra

Azure Cosmos DB é um sistema controlado por recursos. Isso implica que você pode fazer determinado número de operações em um determinado segundo restrito pela taxa de transferência provisionada com base nas unidades de solicitação consumidas pelas operações. Se o aplicativo exceder esse limite em uma determinada solicitação, as exceções de limitação de taxa serão geradas. O API do Cassandra do cosmos DB, traduz essas exceções para erros sobrecarregados no protocolo nativo Cassandra. Para garantir que seu aplicativo possa interceptar e fazer a nova tentativa para a limitação de taxa, é fornecido um auxiliar [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) e [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) . Se você usar outros SDKs para acessar API do Cassandra de Cosmos DB crie uma política de conexão para tentar obter essas exceções. 

## <a name="next-steps"></a>Passos seguintes

- Comece por [criar uma conta de API para Cassandra do Azure Cosmos DB, uma base de dados e uma tabela](create-cassandra-api-account-java.md) com uma aplicação Java

