---
title: MongoDB comandos de extensão para gerir dados na API da Azure Cosmos DB para mongoDB
description: Este artigo descreve como usar comandos de extensão MongoDB para gerir dados armazenados na API da Azure Cosmos DB para mongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f99c4d096bcbe1fbdc42cac80a491d6017266cb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80583573"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Utilize comandos de extensão MongoDB para gerir dados armazenados na API da Azure Cosmos DB para o MongoDB 

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode comunicar com a API da Azure Cosmos DB para a MongoDB utilizando qualquer um dos condutores de [clientes MongoDB](https://docs.mongodb.org/ecosystem/drivers)de código aberto. A API da Azure Cosmos DB para a MongoDB permite a utilização de condutores de clientes existentes, aderindo ao [protocolo de arame MongoDB.](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)

Ao utilizar a API do Azure Cosmos DB para mongoDB, pode usufruir dos benefícios cosmos DB tais como distribuição global, sharding automático, alta disponibilidade, garantias de latência, automática, encriptação em repouso, backups e muito mais, preservando os seus investimentos na sua app MongoDB.

## <a name="mongodb-protocol-support"></a>Apoio ao protocolo MongoDB

Por padrão, a API do Azure Cosmos DB para MongoDB é compatível com a versão 3.2 do servidor MongoDB, para mais detalhes, ver [funcionalidades suportadas e sintaxe.](mongodb-feature-support.md) As funcionalidades ou operadores de consulta adicionados na versão 3.4 do MongoDB estão atualmente disponíveis como pré-visualização na API do Azure Cosmos DB para mongoDB. Os comandos de extensão seguinte suportam a funcionalidade específica do Azure Cosmos DB ao executar operações CRUD nos dados armazenados na API da Azure Cosmos DB para mongoDB:

* [Criar base de dados](#create-database)
* [Base de dados de atualizações](#update-database)
* [Obter base de dados](#get-database)
* [Criar coleção](#create-collection)
* [Recolha de atualização](#update-collection)
* [Obter coleção](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>Criar base de dados

O comando de extensão de base de dados cria uma nova base de dados MongoDB. O nome da base de dados é utilizado a partir do contexto das bases de dados contra o qual o comando é executado. O formato do comando CreateDatabase é o seguinte:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

A tabela seguinte descreve os parâmetros dentro do comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| customAction   |  string  |   Nome do comando personalizado, deve ser "CreateDatabase".      |
| ofertaThroughput | int  | Aprovisionada entrada que definiu na base de dados. Este parâmetro é opcional. |

### <a name="output"></a>Saída

Devolve uma resposta de comando personalizada padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros da saída.

### <a name="examples"></a>Exemplos

**Criar uma base de dados**

Para criar uma base de dados denominada "teste", utilize o seguinte comando:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Criar uma base de dados com entrada**

Para criar uma base de dados denominada "teste" e aprovisionada de 1000 RUs, utilize o seguinte comando:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a>Base de dados de atualizações

O comando de extensão da base de dados atualiza as propriedades associadas à base de dados especificada. Atualmente, só pode atualizar a propriedade "offerThroughput".

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

A tabela seguinte descreve os parâmetros dentro do comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| customAction    |    string     |   Nome do comando personalizado. Deve ser "UpdateDatabase".      |
|  ofertaThroughput   |  int       |     Nova entrada disponibilizada que pretende definir na base de dados.    |

### <a name="output"></a>Saída

Devolve uma resposta de comando personalizada padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros da saída.

### <a name="examples"></a>Exemplos

**Atualizar a entrada disponibilizada associada a uma base de dados**

Para atualizar a entrada prevista de uma base de dados com o nome "teste" a 1200 RUs, utilize o seguinte comando:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a>Obter base de dados

O comando de extensão da base de dados de obter devolve o objeto da base de dados. O nome da base de dados é utilizado a partir do contexto da base de dados contra o qual o comando é executado.

```
{
  customAction: "GetDatabase"
}
```

A tabela seguinte descreve os parâmetros dentro do comando:


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  customAction   |   string      |   Nome do comando personalizado. Deve ser "GetDatabase"|
        
### <a name="output"></a>Saída

Se o comando for bem sucedido, a resposta contém um documento com os seguintes campos:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Estado de resposta. 1 == sucesso. 0 == falha.      |
| `database`    |    `string`        |   Nome da base de dados.      |
|   `provisionedThroughput`  |    `int`      |    Entrada aprovisionada que está definida na base de dados. Este é um parâmetro de resposta opcional.     |

Se o comando falhar, uma resposta de comando personalizada padrão é devolvida. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros da saída.

### <a name="examples"></a>Exemplos

**Obter a base de dados**

Para obter o objeto de base de dados para uma base de dados chamada "teste", utilize o seguinte comando:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a>Criar coleção

O comando de extensão de coleção cria uma nova coleção MongoDB. O nome da base de dados é utilizado a partir do contexto das bases de dados contra o qual o comando é executado. O formato do comando CreateCollection é o seguinte:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

A tabela seguinte descreve os parâmetros dentro do comando:

| **Campo** | **Tipo** | **Necessário** | **Descrição** |
|---------|---------|---------|---------|
| customAction | string | Necessário | Nome do comando personalizado. Deve ser "CreateCollection".|
| coleção | string | Necessário | Nome da coleção. Não são permitidos personagens especiais.|
| ofertaThroughput | int | Opcional* | Entrada aprovisionada para definir na base de dados. Se este parâmetro não for fornecido, irá desempor-se ao mínimo, 400 RU/s. * Para especificar a entrada para além de `shardKey` 10.000 RU/s, é necessário o parâmetro.|
| fragmentoChave | string | Opcional* | O caminho para a Chave do Fragmento para a coleção espumosa. Este parâmetro é necessário se colocar mais de 10.000 RU/s em `offerThroughput`.  Se for especificado, todos os documentos inseridos exigirão este valor. |

### <a name="output"></a>Saída

Devolve uma resposta de comando personalizada padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros da saída.

### <a name="examples"></a>Exemplos

**Criar uma coleção sem duras**

Para criar uma coleção não dura com o nome "testCollection" e a sua provisão de 1000 RUs, utilize o seguinte comando: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Criar uma coleção escaced**

Para criar uma coleção esfarvidada com o nome "testCollection" e a provisão de 1000 RUs, e uma propriedade shardkey "a.b", utilize o seguinte comando:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a>Recolha de atualização

O comando de extensão de recolha de atualizações atualiza as propriedades associadas à recolha especificada.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

A tabela seguinte descreve os parâmetros dentro do comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  customAction   |   string      |   Nome do comando personalizado. Deve ser "UpdateCollection".      |
|  coleção   |   string      |   Nome da coleção.       |
| ofertaThroughput   |int|   Entrada prevista para definir na coleção.|

## <a name="output"></a>Saída

Devolve uma resposta de comando personalizada padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros da saída.

### <a name="examples"></a>Exemplos

**Atualizar a entrada prevista associada a uma coleção**

Para atualizar a entrada prevista de uma coleção com o nome "testCollection" a 1200 RUs, utilize o seguinte comando:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>Obter coleção

O comando personalizado da coleção de recolha devolve o objeto de recolha.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

A tabela seguinte descreve os parâmetros dentro do comando:


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| customAction    |   string      |   Nome do comando personalizado. Deve ser "GetCollection".      |
| coleção    |    string     |    Nome da coleção.     |

### <a name="output"></a>Saída

Se o comando for bem sucedido, a resposta contém um documento com os seguintes campos


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Estado de resposta. 1 == sucesso. 0 == falha.      |
| `database`    |    `string`     |   Nome da base de dados.      |
| `collection`    |    `string`     |    Nome da coleção.     |
|  `shardKeyDefinition`   |   `document`      |  Documento de especificação de índice usado como chave de fragmentos. Este é um parâmetro de resposta opcional.       |
|  `provisionedThroughput`   |   `int`      |    Entrada provisionada para definir na coleção. Este é um parâmetro de resposta opcional.     |

Se o comando falhar, uma resposta de comando personalizada padrão é devolvida. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros da saída.

### <a name="examples"></a>Exemplos

**Pegue a coleção**

Para obter o objeto de recolha para uma coleção chamada "TestCollection", utilize o seguinte comando:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>Saída padrão de um comando personalizado

Se não especificada, uma resposta personalizada contém um documento com os seguintes campos:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Estado de resposta. 1 == sucesso. 0 == falha.      |
| `code`    |   `int`      |   Só voltou quando o comando falhou (isto é, ok == 0). Contém o código de erro MongoDB. Este é um parâmetro de resposta opcional.      |
|  `errMsg`   |  `string`      |    Só voltou quando o comando falhou (isto é, ok == 0). Contém uma mensagem de erro fácil de utilizar. Este é um parâmetro de resposta opcional.      |

## <a name="next-steps"></a>Passos seguintes

Em seguida, você pode começar a aprender os seguintes conceitos Azure Cosmos DB: 

* [Indexação no Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expirar dados em Azure Cosmos DB automaticamente com tempo para viver](../cosmos-db/time-to-live.md)
