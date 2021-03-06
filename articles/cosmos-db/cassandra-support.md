---
title: Funcionalidades do Apache Cassandra suportadas pela API para Cassandra do Azure Cosmos DB
description: Saiba mais sobre o suporte de funcionalidades do Apache Cassandra na API para Cassandra do Azure Cosmos DB
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: f48935edd5746b0f3f3440e249024cbe2901317b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374376"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Funcionalidades do Apache Cassandra suportadas pela API para Cassandra do Azure Cosmos DB 
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode comunicar com a Azure Cosmos DB Cassandra API através do Protocolo Binário V4 do [Protocolo](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) Binário v4, [conforme os motoristas](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver)de clientes cassandra de código aberto. 

Ao utilizar a API para Cassandra do Azure Cosmos DB, pode desfrutar dos benefícios das APIs para Apache Cassandra, bem como das funcionalidades proporcionadas pelo Azure Cosmos DB. As funcionalidades empresariais incluem [distribuição global](distribute-data-globally.md), [criação automática de partições de aumento horizontal](cassandra-partitioning.md), garantias de disponibilidade e latência, encriptação de dados inativos, cópias de segurança e mais.

## <a name="cassandra-protocol"></a>Protocolo do Cassandra 

O Azure Cosmos DB Cassandra API é compatível com a Cassandra Query Language (CQL) v3.11 API (retrocompatível com a versão 2.x). Os comandos, as ferramentas, as limitações e as exceções de CQL suportados encontram-se listados abaixo. Qualquer controlador de cliente que entenda estes protocolos deverá conseguir ligar à API para Cassandra do Azure Cosmos DB.

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

|Tipo  |Suportado |
|---------|---------|
| ascii  | Yes |
| bigint  | Yes |
| blob  | Yes |
| boolean  | Yes |
| counter  | Yes |
| data  | Yes |
| decimal  | Yes |
| double  | Yes |
| float  | Yes |
| frozen  | Yes |
| inet  | Yes |
| int  | Yes |
| lista  | Yes |
| set  | Yes |
| smallint  | Yes |
| texto  | Yes |
| hora  | Yes |
| carimbo de data/hora  | Yes |
| timeuuid  | Yes |
| tinyint  | Yes |
| tuple  | Yes |
| uuid  | Yes |
| varchar  | Yes |
| varint  | Yes |
| tuples | Yes | 
| udts  | Yes |
| mapa | Yes |

A estática é suportada para a declaração do tipo de dados.

## <a name="cql-functions"></a>Funções de CQL

A API para Cassandra do Azure Cosmos DB suporta as seguintes funções de CQL:

|Comando  |Suportado |
|---------|---------|
| Token * | Yes |
| ttl *** | Yes |
| tempo de escrita *** | Yes |
| elenco ** | Yes |

> [!NOTE] 
> \* A API cassandra suporta o token como uma projeção/seletor, e só permite token(pk) no lado esquerdo de uma cláusula onde. Por exemplo, `WHERE token(pk) > 1024` é apoiado, mas `WHERE token(pk) > token(100)` **não** é apoiado.  
> \*\* A `cast()` função não é nesteble na API de Cassandra. Por exemplo, `SELECT cast(count as double) FROM myTable` é apoiado, mas `SELECT avg(cast(count as double)) FROM myTable` **não** é apoiado.    
> \*\*\* Os tempos e TTL personalizados especificados com a `USING` opção são aplicados a um nível de linha (e não por célula).



Funções agregadas:

|Comando  |Suportado |
|---------|---------|
| avg | Yes |
| count | Yes |
| min | Yes |
| max | Yes |
| soma | Yes |

> [!NOTE]
> As funções agregadas funcionam em colunas regulares, mas os agregados em colunas de agrupamento **não** são suportados.


Funções de conversão blob:
 
|Comando  |Suportado |
|---------|---------|
| typeAsBlob(valor)   | Yes |
| blobAsType(valor) | Yes |


UUID e funções timeuuid:
 
|Comando  |Suportado |
|---------|---------|
| dateOf()  | Yes |
| now()  | Yes |
| minTimeuuid()  | Yes |
| unixTimestampOf()  | Yes |
| toDate(timeuuid)  | Yes |
| toTimestamp(timeuuid)  | Yes |
| toUnixTimestamp(timeuuid)  | Yes |
| toDate(carimbo de data/hora)  | Yes |
| toUnixTimestamp(carimbo de data/hora)  | Yes |
| toTimestamp(data)  | Yes |
| toUnixTimestamp(data) | Yes |


  
## <a name="cql-commands"></a>Comandos de CQL

O Azure Cosmos DB suporta os seguintes comandos de base de dados nas contas da API para Cassandra.

|Comando  |Suportado |
|---------|---------|
| PERMITIR A FILTRAGEM | Yes |
| ALTERAR O ESPAÇO DE CHAVES | N/A (serviço PaaS, replicação gerida internamente)|
| ALTERAR VISTA MATERIALIZADA | No |
| ALTERAR PAPEL | No |
| ALTER TABLE | Yes |
| TIPO ALTER | No |
| UTILIZADOR ALTER | No |
| LOTE | Sim (apenas lote não enólogo)|
| ARMAZENAMENTO COMPACTO | N/A (serviço PaaS) |
| CRIAR AGREGADO | No | 
| CRIAR ÍNDICE PERSONALIZADO (SASI) | No |
| CREATE INDEX | Sim (sem [especificar o nome do índice](cassandra-secondary-index.md), e índices em chaves de agrupamento ou recolha completa de FROZEN não suportada) |
| CREATE FUNCTION | No |
| CRIAR KEYSPACE (definições de replicação ignoradas) | Yes |
| CRIAR VISTA MATERIALIZADA | No |
| CREATE TABLE | Yes |
| CRIAR GATILHO | No |
| CRIAR TIPO | Yes |
| CRIAR PAPEL | No |
| CREATE USER (Deprecado em Apache Cassandra nativo) | No |
| DELETE | Yes |
| DISTINCT | No |
| AGREGADO DE DROP | No |
| DROP FUNCTION | No |
| DROP INDEX | Yes |
| DROP KEYSPACE | Yes |
| VISTA MATERIALIZADA GOTA | No |
| PAPEL DE DROP | No |
| DROP TABLE | Yes |
| DETONADOR DE QUEDA | No | 
| TIPO DE GOTA | Yes |
| UTILIZADOR DROP (Deprecado em Apache Cassandra nativo) | No |
| GRANT | No |
| INSERT | Yes |
| PERMISSÕES DE LISTA | No |
| FUNÇÕES DE LISTA | No |
| UTILIZADORES LISTA (Deprecado em Apache Cassandra nativo) | No |
| REVOKE | No |
| SELECIONAR | Yes |
| UPDATE | Yes |
| TRUNCATO | No |
| USE | Yes |

## <a name="lightweight-transactions-lwt"></a>Transações leves (LWT)

| Componente  |Suportado |
|---------|---------|
| APAGAR SE EXISTE | Yes |
| Excluir condições | No |
| INSIRA SE NÃO EXISTE | Yes |
| ATUALIZAR SE EXISTE | Yes |
| ATUALIZAÇÃO SE NÃO EXISTE | Yes |
| Condições de ATUALIZAÇÃO | No |

## <a name="cql-shell-commands"></a>Comandos CQL Shell

O Azure Cosmos DB suporta os seguintes comandos de base de dados nas contas da API para Cassandra.

|Comando  |Suportado |
|---------|---------|
| CAPTURA | Yes |
| CLARO | Yes |
| CONSISTÊNCIA * | N/D |
| CÓPIA | No |
| DESCREVER | Yes |
| cqlshExpand | No |
| SAÍDA | Yes |
| LOGIN | N/A (a função CQL `USER` não é suportada, portanto `LOGIN` é redundante) |
| PAGING | Yes |
| CONSISTÊNCIA EM SÉRIE * | N/D |
| programa | Yes |
| ORIGEM | Yes |
| RASTREIO | N/A (Cassandra API é apoiada pela Azure Cosmos DB - use [o registo de diagnóstico](cosmosdb-monitor-resource-logs.md) para resolução de problemas) |

> [!NOTE] 
> \* A consistência funciona de forma diferente na Azure Cosmos DB, consulte [aqui](cassandra-consistency.md) para mais informações.  


## <a name="json-support"></a>Suporte JSON
|Comando  |Suportado |
|---------|---------|
| SELECIONE JSON | Yes |
| INSERIR JSON | Yes |
| fromJson() | No |
| toJson() | No |


## <a name="cassandra-api-limits"></a>Limites da API para Cassandra

A API para Cassandra do Azure Cosmos DB não tem limites quanto ao tamanho dos dados armazenados nas tabelas. É possível armazenar centenas de terabytes ou de petabytes de dados sem desrespeitar os limites da chave de partição. Da mesma forma, todas as entidades ou equivalentes de linha não têm limites no número de colunas. No entanto, o tamanho total da entidade não deve exceder 2 MB. Os dados por chave de partição não podem exceder 20 GB como em todas as outras APIs.

## <a name="tools"></a>Ferramentas 

A API para Cassandra do Azure Cosmos DB é uma plataforma de serviço gerida. Não necessita de gestão nem de utilitários como o Recoletor de Lixo, a Máquina Virtual de Java (JVM) e o nodetool para gerir o cluster. Esta API suporta ferramentas como o cqlsh, que utiliza a compatibilidade com CQLv4 Binária. 

* O explorador de dados, métricas, diagnósticos de registo, PowerShell e CLI do portal Azure são outros mecanismos suportados para gerir a conta.

## <a name="hosted-cql-shell-preview"></a>Concha CQL hospedada (pré-visualização)

Pode abrir uma concha nativa hospedada de Cassandra (CQLSH v5.0.1) diretamente do Data Explorer no [portal Azure](data-explorer.md) ou do [Azure Cosmos DB Explorer](https://cosmos.azure.com/). Antes de ativar a concha CQL, tem de ativar a função [Desatado](enable-notebooks.md) na sua conta (se ainda não estiver ativada, será solicitada ao clicar em `Open Cassandra Shell` ). Veja o artigo [Ative os cadernos para Azure Cosmos DB contas](enable-notebooks.md#supported-regions) para regiões de Azure suportadas.

:::image type="content" source="./media/cassandra-support/cqlsh.png" alt-text="Open CQLSH":::

Também pode ligar-se à API Cassandra em Azure Cosmos DB utilizando o CQLSH instalado numa máquina local. Vem com Apache Cassandra 3.1.1 e funciona fora da caixa definindo as variáveis ambientais. As seguintes secções incluem as instruções para instalar, configurar e ligar à Cassandra API em Azure Cosmos DB, no Windows ou Linux utilizando o CQLSH.

> [!NOTE]
> As ligações à Azure Cosmos DB Cassandra API não funcionarão com as versões da DataStax Enterprise (DSE) do CQLSH. Certifique-se de que utiliza apenas as versões Apache Cassandra de código aberto do CQLSH ao ligar-se à API cassandra. 

**Janelas:**

Se utilizar janelas, recomendamos que ative o [sistema de ficheiros Windows para o Linux](/windows/wsl/install-win10#install-the-windows-subsystem-for-linux). Em seguida, pode seguir os comandos linux abaixo.

**Unix/Linux/Mac:**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

Todas as operações CRUD que são executadas através de um SDK compatível com CQL v4 irão devolver informações extra sobre unidades de erro e pedido consumidas. Os comandos DELETE e UPDATE devem ser tratados com a governação dos recursos tomados em consideração, a fim de garantir a utilização mais eficiente da produção prevista.

* Tenha em atenção que, caso seja especificado, o valor gc_grace_seconds tem de ser zero.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($"CustomPayload:  {key}: {value}"); 
        } 
```

## <a name="consistency-mapping"></a>Mapeamento de consistência 

A API para Cassandra do Azure Cosmos DB permite que haja consistência em operações de leitura.  O mapeamento de consistência é detalhado [aqui.](./cassandra-consistency.md#mapping-consistency-levels)

## <a name="permission-and-role-management"></a>Gestão de permissões e funções

A Azure Cosmos DB suporta o controlo de acesso baseado em funções (Azure RBAC) para provisões, chaves rotativas, métricas de visualização e palavras-passe/chaves só de leitura que podem ser obtidas através do [portal Azure.](https://portal.azure.com) A Azure Cosmos DB não apoia papéis para atividades da CRUD.

## <a name="keyspace-and-table-options"></a>Opções de keyspace e tabela

As opções para nome da região, classe, replication_fator e datacenter no comando "Create Keyspace" são ignoradas atualmente. O sistema utiliza o método de replicação global de [distribuição do](global-dist-under-the-hood.md) Azure Cosmos DB para adicionar as regiões. Se precisar da presença de dados entre regiões, pode ative-lo ao nível da conta com PowerShell, CLI ou portal, para saber mais, ver como adicionar artigo [de regiões.](how-to-manage-database-account.md#addremove-regions-from-your-database-account) Durable_writes não pode ser desativado porque a Azure Cosmos DB garante que cada escrita é duradoura. Em todas as regiões, a Azure Cosmos DB replica os dados através do conjunto de réplicas que é composto por quatro réplicas e esta [configuração](global-dist-under-the-hood.md) de conjunto de réplicas não pode ser modificada.
 
Todas as opções são ignoradas ao criar a tabela, com exceção gc_grace_seconds, que deve ser definida para zero.
O Keyspace e a mesa têm uma opção extra chamada "cosmosdb_provisioned_throughput" com um valor mínimo de 400 RU/s. A produção keyspace permite a partilha de produção em várias tabelas e é útil para cenários quando todas as tabelas não estão usando o rendimento previsto. O comando Alter Table permite alterar a produção de provisões em todas as regiões. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```
## <a name="secondary-index"></a>Índice Secundário
A API Cassandra suporta índices secundários em todos os tipos de dados, exceto tipos de recolha congelada, tipos decimal e variantes. 

## <a name="usage-of-cassandra-retry-connection-policy"></a>Utilização da política de ligação de repetição Cassandra

Azure Cosmos DB é um sistema governado por recursos. Isto significa que pode fazer um certo número de operações num dado segundo com base nas unidades de pedido consumidas pelas operações. Se um pedido exceder esse limite num dado segundo, os pedidos são limitados pela taxa e serão lançadas exceções. A API Cassandra em Azure Cosmos DB traduz estas exceções a erros sobrecarregados no protocolo nativo de Cassandra. Para garantir que a sua aplicação pode intercetar e revalinha pedidos em caso de limitação de taxa, a [faísca](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) e as extensões [java](https://github.com/Azure/azure-cosmos-cassandra-extensions) são fornecidas. Consulte também as amostras de código java para a [versão 3](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) e a [versão 4](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4) Datastax drivers, ao ligar-se à Cassandra API em Azure Cosmos DB. Se utilizar outros SDKs para aceder à Cassandra API em Azure Cosmos DB, crie uma política de conexão para voltar a tentar nestas exceções.

## <a name="next-steps"></a>Passos seguintes

- Começar com a [criação de uma conta API cassandra, base de dados e uma tabela](create-cassandra-api-account-java.md) usando uma aplicação Java
