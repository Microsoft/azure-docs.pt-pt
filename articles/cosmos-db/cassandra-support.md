---
title: Funcionalidades e comandos do Apache Cassandra suportados pela API para Cassandra do Azure Cosmos DB
description: Saiba mais sobre o suporte de funcionalidades do Apache Cassandra na API para Cassandra do Azure Cosmos DB
services: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 5a12a65e03e7ac104eb7b09b116f7c463bbb5b98
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240720"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Funcionalidades do Apache Cassandra suportadas pela API para Cassandra do Azure Cosmos DB 

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode comunicar com a API para Cassandra do Azure Cosmos DB através de [controladores](http://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) open source de cliente do Cassandra compatíveis com o [protocolo de invocação](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) da Linguagem de Consulta do Cassandra (CQL) v4. 

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
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> –ssl 
```
**Unix/Linux/Mac:**

```bash
export SSL_VERSION=TLSv1_2 
export SSL_CERTFILE=<path to Baltimore root ca cert>
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> –ssl 
```

## <a name="cql-commands"></a>Comandos de CQL

O Azure Cosmos DB suporta os seguintes comandos de base de dados nas contas da API para Cassandra.

* CREATE KEYSPACE 
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* SELECIONAR 
* UPDATE 
* BATCH – só são suportados comandos arquivados 
* DELETE

Todas as operações CRUD, quando executadas através do SDK compatível com a CQLV4, devolverão informações adicionais sobre erros, unidades de pedido consumidas, ID de atividade. É necessário lidar com os comandos de eliminação e atualização sem descurar a governação de recursos para evitar a utilização excessiva dos recursos aprovisionados. 
* Tenha em atenção que, caso seja especificado, o valor gc_grace_seconds tem de ser zero.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            string value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

## <a name="consistency-mapping"></a>Mapeamento de consistência 

A API para Cassandra do Azure Cosmos DB permite que haja consistência em operações de leitura. Todas as operações de escrita, independentemente da consistência da conta, são sempre escritas com SLAs de desempenho de escrita.

## <a name="permission-and-role-management"></a>Gestão de permissões e funções

O Azure Cosmos DB suporta o controlo de acesso baseado em funções (RBAC) e palavras-passe/chave de leitura/escrita e só de leitura que podem ser obtidas através do [portal do Azure](https://portal.azure.com). O Azure Cosmos DB ainda não suporta utilizadores e funções para atividades de plano de dados. 

## <a name="planned-support"></a>Suporte planeado 
* Utilizar o carimbo de data/hora e o TTL em conjunto  
* O nome da região no comando create keyspace é ignorado no presente – A distribuição de dados é implementada na plataforma Cosmos DB subjacente e exposta através do portal ou do PowerShell da conta. 





## <a name="next-steps"></a>Passos seguintes

- Comece por [criar uma conta de API para Cassandra do Azure Cosmos DB, uma base de dados e uma tabela](create-cassandra-api-account-java.md) com uma aplicação Java

