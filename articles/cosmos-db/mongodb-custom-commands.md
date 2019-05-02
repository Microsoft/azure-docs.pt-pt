---
title: Comandos de extensão do MongoDB para gerir os dados armazenados na API do Azure Cosmos DB para o MongoDB
description: Este artigo descreve como utilizar comandos de extensão do MongoDB para gerir os dados armazenados na API do Azure Cosmos DB para o MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: 94b1048befc8716caf5f7f51adb1f95d047d4077
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925660"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Utilizar os comandos de extensão do MongoDB para gerir os dados armazenados na API do Azure Cosmos DB para o MongoDB 

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode comunicar com a API do Azure Cosmos DB para o MongoDB por meio de um código-fonte aberto [controladores de cliente da MongoDB](https://docs.mongodb.org/ecosystem/drivers). API do Azure Cosmos DB do MongoDB permite a utilização de controladores existentes do cliente através do respetivo a [protocolo de MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Ao utilizar a API do Azure Cosmos DB para o MongoDB, pode desfrutar dos benefícios do Cosmos DB, tais como distribuição global, fragmentação automática, elevada disponibilidade, garantias de latência, automática, encriptação em repouso, cópias de segurança, e muitos mais, embora preservar seus investimentos na sua aplicação MongoDB.

## <a name="mongodb-protocol-support"></a>Suporte de protocolo do MongoDB

Por predefinição, o Azure Cosmos DB API do MongoDB é compatível com o MongoDB a versão de servidor 3.2, para obter mais detalhes, consulte [funcionalidades e sintaxe suportados](mongodb-feature-support.md). Os recursos ou operadores de consulta, adicionados no MongoDB versão 3.4 estão atualmente disponíveis como pré-visualização na API do Azure Cosmos DB para o MongoDB. Os seguintes comandos de extensão suportam a funcionalidade específica do Azure Cosmos DB ao realizar operações de CRUD em dados armazenados na API do Azure Cosmos DB para o MongoDB:

* [Criar base de dados](#create-database)
* [Atualizar base de dados](#update-database)
* [Obter a base de dados](#get-database)
* [Criar coleção](#create-collection)
* [Atualizar a coleção](#update-collection)
* [Obter coleção](#get-collection)

## <a id="create-database"></a> Criar base de dados

O comando de extensão de base de dados create Cria uma nova base de dados do MongoDB. O nome de base de dados é utilizado a partir do contexto de bases de dados em relação ao qual o comando for executado. O formato do comando CreateDatabase é o seguinte:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

A tabela seguinte descreve os parâmetros no comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| customAction   |  string  |   Nome do comando personalizado, tem de ser "CreateDatabase".      |
| offerThroughput | int  | Débito aprovisionado que definiu na base de dados. Este parâmetro é opcional. |

### <a name="output"></a>Saída

Devolve uma resposta de comando personalizado predefinido. Consulte a [predefinido saída](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Criar uma base de dados**

Para criar uma base de dados com o nome "teste", utilize o seguinte comando:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Criar uma base de dados com débito**

Para criar uma base de dados com o nome "teste" e o débito aprovisionado de 1000 RUs, utilize o seguinte comando:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a id="update-database"></a> Atualizar base de dados

O comando de extensão de base de dados de atualização atualiza as propriedades associadas a base de dados especificada. Atualmente, só é possível atualizar a propriedade de "offerThroughput".

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

A tabela seguinte descreve os parâmetros no comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| customAction    |    string     |   Nome do comando personalizado. Tem de ser "UpdateDatabase".      |
|  offerThroughput   |  int       |     Débito aprovisionado novos que queira definir na base de dados.    |

### <a name="output"></a>Saída

Devolve uma resposta de comando personalizado predefinido. Consulte a [predefinido saída](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Atualizar o débito aprovisionado associado a uma base de dados**

Para atualizar o débito aprovisionado das bases de dados com o nome "teste" para 1200 RU, utilize o seguinte comando:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a id="get-database"></a> Obter a base de dados

O comando de extensão de base de dados de get retorna o objeto de base de dados. O nome de base de dados é utilizado a partir do contexto de base de dados em relação ao qual o comando for executado.

```
{
  customAction: "GetDatabase"
}
```

A tabela seguinte descreve os parâmetros no comando:


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  customAction   |   string      |   Nome do comando personalizado. Tem de ser "GetDatabase"|
        
### <a name="output"></a>Saída

Se o comando for bem-sucedida, a resposta contém um documento com os seguintes campos:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Estado da resposta. 1 = = êxito. 0 = = falha.      |
| `database`    |    `string`        |   Nome da base de dados.      |
|   `provisionedThroughput`  |    `int`      |    Débito aprovisionado é definido na base de dados. Este é um parâmetro opcional de resposta.     |

Se o comando falhar, é devolvida uma resposta de comando personalizado predefinido. Consulte a [predefinido saída](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Obter a base de dados**

Para obter o objeto de base de dados para uma base de dados com o nome "teste", utilize o seguinte comando:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a id="create-collection"></a> Criar coleção

O comando de extensão de coleção de criar cria uma nova coleção MongoDB. O nome de base de dados é utilizado a partir do contexto de bases de dados em relação ao qual o comando for executado. O formato do comando CreateCollection é o seguinte:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

A tabela seguinte descreve os parâmetros no comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| customAction    | string | Nome do comando personalizado. Tem de ser "CreateCollection"     |
| coleção      | string | Nome da coleção                                   |
| offerThroughput | int    | Débito aprovisionado para definir na base de dados. É um parâmetro opcional |
| shardKey        | string | Caminho da chave de partição horizontal para criar uma coleção em partição horizontal. É um parâmetro opcional |

### <a name="output"></a>Saída

Devolve uma resposta de comando personalizado predefinido. Consulte a [predefinido saída](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Criar uma coleção de unsharded**

Para criar uma coleção de unsharded com o nome "testCollection" e o débito aprovisionado de 1000 RUs, utilize o seguinte comando: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Criar uma coleção em partição horizontal**

Para criar uma coleção em partição horizontal com o nome "testCollection" e o débito aprovisionado de 1000 RUs, utilize o seguinte comando:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a id="update-collection"></a> Atualizar a coleção

O comando de extensão de coleção de atualização atualiza as propriedades associadas a coleção especificada.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

A tabela seguinte descreve os parâmetros no comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  customAction   |   string      |   Nome do comando personalizado. Tem de ser "UpdateCollection".      |
|  coleção   |   string      |   Nome da coleção.       |
| offerThroughput   |int|   Débito aprovisionado para definir na coleção.|

## <a name="output"></a>Saída

Devolve uma resposta de comando personalizado predefinido. Consulte a [predefinido saída](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Atualizar o débito aprovisionado associado a uma coleção**

Para atualizar o débito aprovisionado de uma coleção com o nome "testCollection" para 1200 RU, utilize o seguinte comando:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a id="get-collection"></a> Obter coleção

O comando personalizado da coleção de get retorna o objeto de coleção.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

A tabela seguinte descreve os parâmetros no comando:


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| customAction    |   string      |   Nome do comando personalizado. Tem de ser "GetCollection".      |
| coleção    |    string     |    Nome da coleção.     |

### <a name="output"></a>Saída

Se o comando for bem-sucedida, a resposta contém um documento com os seguintes campos


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Estado da resposta. 1 = = êxito. 0 = = falha.      |
| `database`    |    `string`     |   Nome da base de dados.      |
| `collection`    |    `string`     |    Nome da coleção.     |
|  `shardKeyDefinition`   |   `document`      |  Documento de especificação de um índice utilizado como uma chave de partição horizontal. Este é um parâmetro opcional de resposta.       |
|  `provisionedThroughput`   |   `int`      |    Débito aprovisionado para definir na coleção. Este é um parâmetro opcional de resposta.     |

Se o comando falhar, é devolvida uma resposta de comando personalizado predefinido. Consulte a [predefinido saída](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Obter a coleção**

Para obter o objeto de coleção para uma coleção designada "testCollection", utilize o seguinte comando:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a id="default-output"></a> Saída padrão de um comando personalizado

Se não for especificado, uma resposta personalizada contém um documento com os seguintes campos:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Estado da resposta. 1 = = êxito. 0 = = falha.      |
| `code`    |   `int`      |   Só devolvido quando a falha do comando (ou seja, ok = = 0). Contém o código de erro do MongoDB. Este é um parâmetro opcional de resposta.      |
|  `errMsg`   |  `string`      |    Só devolvido quando a falha do comando (ou seja, ok = = 0). Contém uma mensagem de erro amigável de utilizador. Este é um parâmetro opcional de resposta.      |

## <a name="next-steps"></a>Passos Seguintes

Em seguida, pode avançar para saber os seguintes conceitos do Azure Cosmos DB: 

* [Indexação no Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expirar dados no Azure Cosmos DB automaticamente com o tempo de duração](../cosmos-db/time-to-live.md)
