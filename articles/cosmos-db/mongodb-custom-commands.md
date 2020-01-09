---
title: Comandos de extensão do MongoDB para gerenciar dados na API do Azure Cosmos DB para MongoDB
description: Este artigo descreve como usar comandos de extensão do MongoDB para gerenciar dados armazenados na API do Azure Cosmos DB para o MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f57b274715eb1c8a4d517f5655c09c366574d412
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445209"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Usar comandos de extensão do MongoDB para gerenciar dados armazenados na API do Azure Cosmos DB para MongoDB 

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Você pode se comunicar com a API do Azure Cosmos DB para MongoDB usando qualquer um dos drivers de [cliente do MongoDB](https://docs.mongodb.org/ecosystem/drivers)de software livre. A API do Azure Cosmos DB para MongoDB permite o uso de drivers de cliente existentes ao aderir ao [protocolo de transmissão do MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Usando a API do Azure Cosmos DB para MongoDB, você pode aproveitar os benefícios Cosmos DB como distribuição global, fragmentação automática, alta disponibilidade, garantias de latência, automático, criptografia em repouso, backups e muito mais, enquanto preserva seus investimentos em seu aplicativo MongoDB.

## <a name="mongodb-protocol-support"></a>Suporte ao protocolo MongoDB

Por padrão, a API do Azure Cosmos DB para MongoDB é compatível com o servidor MongoDB versão 3,2, para obter mais detalhes, consulte [recursos e sintaxe com suporte](mongodb-feature-support.md). Os recursos ou operadores de consulta adicionados no MongoDB versão 3,4 estão atualmente disponíveis como uma visualização na API do Azure Cosmos DB para MongoDB. Os comandos de extensão a seguir dão suporte a Azure Cosmos DB funcionalidade específica ao executar operações CRUD nos dados armazenados na API do Azure Cosmos DB para MongoDB:

* [Criar banco de dados](#create-database)
* [Atualizar banco de dados](#update-database)
* [Obter banco de dados](#get-database)
* [Criar coleção](#create-collection)
* [Atualizar coleção](#update-collection)
* [Obter coleção](#get-collection)

## <a id="create-database"></a>Criar banco de dados

O comando criar extensão de banco de dados cria um novo banco de dados MongoDB. O nome do banco de dados é usado no contexto de bancos de dados no qual o comando é executado. O formato do comando CreateDatabase é o seguinte:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

A tabela a seguir descreve os parâmetros no comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| Ação   |  string  |   Nome do comando personalizado; ele deve ser "CreateDatabase".      |
| offerThroughput | int  | Taxa de transferência provisionada que você define no banco de dados. Este parâmetro é opcional. |

### <a name="output"></a>Saída

Retorna uma resposta de comando personalizado padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Criar uma base de dados**

Para criar um banco de dados chamado "Test", use o seguinte comando:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Criar um banco de dados com taxa de transferência**

Para criar um banco de dados chamado "teste" e uma taxa de transferência provisionada de 1000 RUs, use o seguinte comando:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a id="update-database"></a>Atualizar banco de dados

O comando Atualizar extensão do banco de dados atualiza as propriedades associadas ao banco de dados especificado. No momento, você só pode atualizar a propriedade "offerThroughput".

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

A tabela a seguir descreve os parâmetros no comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| Ação    |    string     |   Nome do comando personalizado. Deve ser "UpdateDatabase".      |
|  offerThroughput   |  int       |     Nova taxa de transferência provisionada que você deseja definir no banco de dados.    |

### <a name="output"></a>Saída

Retorna uma resposta de comando personalizado padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Atualizar a taxa de transferência provisionada associada a um banco de dados**

Para atualizar a taxa de transferência provisionada de um banco de dados com o nome "Test" para 1200 RUs, use o seguinte comando:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a id="get-database"></a>Obter banco de dados

O comando obter extensão do banco de dados retorna o objeto de banco de dados. O nome do banco de dados é usado no contexto do banco de dados no qual o comando é executado.

```
{
  customAction: "GetDatabase"
}
```

A tabela a seguir descreve os parâmetros no comando:


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  Ação   |   string      |   Nome do comando personalizado. Deve ser "GetDatabase"|
        
### <a name="output"></a>Saída

Se o comando for executado com sucesso, a resposta conterá um documento com os seguintes campos:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Status da resposta. 1 = = êxito. 0 = = falha.      |
| `database`    |    `string`        |   Nome do banco de dados.      |
|   `provisionedThroughput`  |    `int`      |    Taxa de transferência provisionada definida no banco de dados. Esse é um parâmetro de resposta opcional.     |

Se o comando falhar, uma resposta de comando personalizado padrão será retornada. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Obter o banco de dados**

Para obter o objeto de banco de dados para um banco de dados chamado "Test", use o seguinte comando:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a id="create-collection"></a>Criar coleção

O comando criar extensão da coleção cria uma nova coleção do MongoDB. O nome do banco de dados é usado no contexto de bancos de dados no qual o comando é executado. O formato do comando é o seguinte:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

A tabela a seguir descreve os parâmetros no comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| Ação    | string | Nome do comando personalizado. Deve ser ""     |
| coleção      | string | Nome da coleção                                   |
| offerThroughput | int    | Taxa de transferência provisionada a ser definida no banco de dados. É um parâmetro opcional |
| shardKey        | string | Caminho da chave de fragmento para criar uma coleção fragmentada. É um parâmetro opcional |

### <a name="output"></a>Saída

Retorna uma resposta de comando personalizado padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Criar uma coleção não fragmentada**

Para criar uma coleção não fragmentada com o nome "testcollection" e uma taxa de transferência provisionada de 1000 RUs, use o seguinte comando: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Criar uma coleção fragmentada**

Para criar uma coleção fragmentada com o nome "testcollection" e uma taxa de transferência provisionada de 1000 RUs, use o seguinte comando:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a id="update-collection"></a>Atualizar coleção

O comando Atualizar extensão da coleção atualiza as propriedades associadas à coleção especificada.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

A tabela a seguir descreve os parâmetros no comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  Ação   |   string      |   Nome do comando personalizado. Deve ser "updatecollection".      |
|  coleção   |   string      |   Nome da coleção.       |
| offerThroughput   |int|   Taxa de transferência provisionada a ser definida na coleção.|

## <a name="output"></a>Saída

Retorna uma resposta de comando personalizado padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Atualizar a taxa de transferência provisionada associada a uma coleção**

Para atualizar a taxa de transferência provisionada de uma coleção com o nome "testcollection" para 1200 RUs, use o seguinte comando:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a id="get-collection"></a>Obter coleção

O comando Get Collection personalizado retorna o objeto da coleção.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

A tabela a seguir descreve os parâmetros no comando:


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| Ação    |   string      |   Nome do comando personalizado. Deve ser "GetCollection".      |
| coleção    |    string     |    Nome da coleção.     |

### <a name="output"></a>Saída

Se o comando for executado com sucesso, a resposta conterá um documento com os campos a seguir


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status da resposta. 1 = = êxito. 0 = = falha.      |
| `database`    |    `string`     |   Nome do banco de dados.      |
| `collection`    |    `string`     |    Nome da coleção.     |
|  `shardKeyDefinition`   |   `document`      |  Documento de especificação de índice usado como chave de fragmentação. Esse é um parâmetro de resposta opcional.       |
|  `provisionedThroughput`   |   `int`      |    Taxa de transferência provisionada a ser definida na coleção. Esse é um parâmetro de resposta opcional.     |

Se o comando falhar, uma resposta de comando personalizado padrão será retornada. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Obter a coleção**

Para obter o objeto de coleção para uma coleção chamada "testcollection", use o seguinte comando:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a id="default-output"></a>Saída padrão de um comando personalizado

Se não for especificado, uma resposta personalizada conterá um documento com os seguintes campos:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status da resposta. 1 = = êxito. 0 = = falha.      |
| `code`    |   `int`      |   Retornado somente quando o comando falhou (ou seja, ok = = 0). Contém o código de erro do MongoDB. Esse é um parâmetro de resposta opcional.      |
|  `errMsg`   |  `string`      |    Retornado somente quando o comando falhou (ou seja, ok = = 0). Contém uma mensagem de erro amigável. Esse é um parâmetro de resposta opcional.      |

## <a name="next-steps"></a>Passos seguintes

Em seguida, você pode continuar a aprender os seguintes conceitos de Azure Cosmos DB: 

* [Indexação no Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expirar dados em Azure Cosmos DB automaticamente com vida útil](../cosmos-db/time-to-live.md)
