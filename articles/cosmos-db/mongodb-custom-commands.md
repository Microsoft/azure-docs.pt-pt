---
title: Extensão mongoDB ordena a gestão de dados na API da Azure Cosmos para a MongoDB
description: Este artigo descreve como usar comandos de extensão MongoDB para gerir dados armazenados na API da Azure Cosmos DB para o MongoDB.
author: jasonwhowell
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/28/2020
ms.author: jasonh
ms.custom: devx-track-js
ms.openlocfilehash: a40be5212fb1335482ec5011d24c8eaf5f3d9a00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91409685"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Utilize comandos de extensão MongoDB para gerir os dados armazenados na API da Azure Cosmos para o MongoDB 

O documento que se segue contém os comandos de ação personalizados específicos da API da Azure Cosmos DB para o MongoDB. Estes comandos podem ser utilizados para criar e obter recursos de base de dados específicos do [modelo de capacidade DB do Azure Cosmos.](databases-containers-items.md)

Ao utilizar a API da Azure Cosmos para a MongoDB, poderá usufruir dos benefícios da Cosmos DB, tais como distribuição global, fragmentos automáticos, alta disponibilidade, garantias de latência, automática, encriptação em repouso, backups e muito mais, preservando os seus investimentos na sua app MongoDB. Você pode comunicar com a API da Azure Cosmos DB para a MongoDB usando qualquer um dos controladores clientes de código aberto [MongoDB](https://docs.mongodb.org/ecosystem/drivers). A API da Azure Cosmos DB para a MongoDB permite a utilização de condutores clientes existentes, aderindo ao [protocolo de fio MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

## <a name="mongodb-protocol-support"></a>Apoio ao protocolo mongoDB

A API da Azure Cosmos DB para o MongoDB é compatível com a versão 3.2 e 3.6 do servidor MongoDB. Consulte [funcionalidades suportadas e sintaxe](mongodb-feature-support.md) para mais detalhes. 

Os seguintes comandos de extensão fornecem a capacidade de criar e modificar recursos específicos da Azure Cosmos através de pedidos de base de dados:

* [Criar base de dados](#create-database)
* [Base de dados de atualização](#update-database)
* [Obtenha base de dados](#get-database)
* [Criar coleção](#create-collection)
* [Coleção de atualização](#update-collection)
* [Obter coleção](#get-collection)

## <a name="create-database"></a><a id="create-database"></a> Criar base de dados

O comando de extensão de base de dados cria uma nova base de dados MongoDB. O nome da base de dados pode ser utilizado a partir do contexto da base de dados definido pelo `use database` comando. A tabela a seguir descreve os parâmetros dentro do comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| `customAction`   |  `string`  |   Nome do comando personalizado, deve ser "CreateDatabase".      |
| `offerThroughput` | `int`  | Provisão que definiu na base de dados. Este parâmetro é opcional. |
| `autoScaleSettings` | `Object` | Requerido para [o modo autoescala](provision-throughput-autoscale.md). Este objeto contém as definições associadas ao modo de capacidade de autoescalação. Pode configurar o `maxThroughput` valor, que descreve a maior quantidade de Unidades de Pedido que a coleção será aumentada de forma dinâmica. |

### <a name="output"></a>Saída

Se o comando for bem sucedido, retornará a seguinte resposta:

```javascript
{ "ok" : 1 }
```

Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

#### <a name="create-a-database"></a>Criar uma base de dados

Para criar uma base de dados com o nome `"test"` de todos os valores predefinidos, utilize o seguinte comando:

```javascript
use test
db.runCommand({customAction: "CreateDatabase"});
```

Este comando criará uma base de dados sem o nível de base de dados. Isto significa que as coleções dentro desta base de dados terão de especificar a quantidade de produção que precisa de utilizar.

#### <a name="create-a-database-with-throughput"></a>Criar uma base de dados com produção

Para criar uma base de dados com o nome `"test"` e especificar um [nível de base de dados](set-throughput.md#set-throughput-on-a-database) provisive o produção de 1000 RUs, utilize o seguinte comando:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

Isto criará uma base de dados e definirá uma produção para ela. Todas as coleções dentro desta base de dados partilharão o resultado definido, a menos que as coleções sejam criadas com [um nível de produção específico.](set-throughput.md#set-throughput-on-a-database-and-a-container)

#### <a name="create-a-database-with-autoscale-throughput"></a>Criar uma base de dados com produção de autoestações

Para criar uma base de dados com o nome `"test"` e especificar um rendimento máximo de autoescala de 20.000 RU/s ao nível da [base de dados,](set-throughput.md#set-throughput-on-a-database)utilize o seguinte comando:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```

## <a name="update-database"></a><a id="update-database"></a> Base de dados de atualização

O comando de extensão da base de dados de atualização atualiza as propriedades associadas à base de dados especificada. A tabela a seguir descreve os parâmetros dentro do comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| `customAction`    |    `string`     |   Nome do comando personalizado. Deve ser "UpdateDatabase".      |
|  `offerThroughput`   |  `int`       |     Nova produção provisida que pretende definir na base de dados se a base de dados utilizar o [nível de base de dados](set-throughput.md#set-throughput-on-a-database)  |
| `autoScaleSettings` | `Object` | Requerido para [o modo autoescala](provision-throughput-autoscale.md). Este objeto contém as definições associadas ao modo de capacidade de autoescalação. Pode configurar o `maxThroughput` valor, que descreve a maior quantidade de Unidades de Pedido que a base de dados será aumentada de forma dinâmica. |

Este comando utiliza a base de dados especificada no contexto da sessão. Esta é a base de dados que usou no `use <database>` comando. De momento, o nome da base de dados não pode ser alterado usando este comando.

### <a name="output"></a>Saída

Se o comando for bem sucedido, retornará a seguinte resposta:

```javascript
{ "ok" : 1 }
```

Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

#### <a name="update-the-provisioned-throughput-associated-with-a-database"></a>Atualizar a produção prevista associada a uma base de dados

Para atualizar o resultado previsto de uma base de dados com o nome `"test"` de 1200 RUs, utilize o seguinte comando:

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

#### <a name="update-the-autoscale-throughput-associated-with-a-database"></a>Atualizar a produção de escala automática associada a uma base de dados

Para atualizar o resultado previsto de uma base de dados com nome `"test"` para 20.000 RUs, ou transformá-lo num [nível de produção de escala automática,](provision-throughput-autoscale.md)utilize o seguinte comando:

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```


## <a name="get-database"></a><a id="get-database"></a> Obtenha base de dados

O comando de extensão da base de dados devolve o objeto da base de dados. O nome da base de dados é utilizado a partir do contexto da base de dados com o qual o comando é executado.

```javascript
{
  customAction: "GetDatabase"
}
```

A tabela a seguir descreve os parâmetros dentro do comando:


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   Nome do comando personalizado. Deve ser "GetDatabase"|
        
### <a name="output"></a>Saída

Se o comando for bem sucedido, a resposta contém um documento com os seguintes campos:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Estado de resposta. 1 == sucesso. 0 == falha.      |
| `database`    |    `string`        |   O nome da base de dados.      |
|   `provisionedThroughput`  |    `int`      |    Produção provisida que é definida na base de dados se a base de dados estiver a utilizar o  [nível de base de dados manual](set-throughput.md#set-throughput-on-a-database)     |
| `autoScaleSettings` | `Object` | Este objeto contém os parâmetros de capacidade associados à base de dados se estiver a utilizar o [modo De escala automática](provision-throughput-autoscale.md). O `maxThroughput` valor descreve a maior quantidade de Unidades de Pedido que a base de dados será aumentada de forma dinâmica. |

Se o comando falhar, uma resposta de comando personalizada por defeito é devolvida. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

#### <a name="get-the-database"></a>Obtenha a base de dados

Para obter o objeto de base de dados de uma base de dados `"test"` chamada, utilize o seguinte comando:

```javascript
use test
db.runCommand({customAction: "GetDatabase"});
```

Se a base de dados não tiver uma produção associada, a saída será:

```javascript
{ "database" : "test", "ok" : 1 }
```

Se a base de dados tiver uma [produção manual de nível de base](set-throughput.md#set-throughput-on-a-database) associada, a saída mostrará os `provisionedThroughput` valores:

```javascript
{ "database" : "test", "provisionedThroughput" : 20000, "ok" : 1 }
```

Se a base de dados tiver uma [produção autoesta pública](provision-throughput-autoscale.md) de nível de base associada, a saída mostraria o , que descreve o `provisionedThroughput` RU/s mínimo para a base de dados, e o `autoScaleSettings` objeto, incluindo `maxThroughput` o , que descreve o máximo RU/s para a base de dados.

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
                "maxThroughput" : 20000
        },
        "ok" : 1
}
```

## <a name="create-collection"></a><a id="create-collection"></a> Criar coleção

O comando de extensão de coleção criar uma nova coleção MongoDB. O nome da base de dados é utilizado a partir do contexto de bases de dados definido pelo `use database` comando. O formato do comando CreateCollection é o seguinte:

```javascript
{
  customAction: "CreateCollection",
  collection: "<Collection Name>",
  shardKey: "<Shard key path>",
  offerThroughput: (int), // Amount of throughput allocated to a specific collection

}
```

A tabela a seguir descreve os parâmetros dentro do comando:

| **Campo** | **Tipo** | **Necessário** | **Descrição** |
|---------|---------|---------|---------|
| `customAction` | `string` | Necessário | Nome do comando personalizado. Deve ser "CreateCollection".|
| `collection` | `string` | Necessário | O nome da coleção. Não são permitidos caracteres ou espaços especiais.|
| `offerThroughput` | `int` | Opcional | Provisão para definir na base de dados. Se este parâmetro não for fornecido, irá incumprimento ao mínimo, 400 RU/s. * Para especificar a potência para além de 10.000 RU/s, `shardKey` o parâmetro é necessário.|
| `shardKey` | `string` | Necessário para coleções com grande produção | O caminho para a Chave de Fragmentos para a coleção de fragmentos. Este parâmetro é necessário se definir mais de 10.000 RU/s em `offerThroughput` .  Se for especificado, todos os documentos inseridos exigirão esta chave e valor. |
| `autoScaleSettings` | `Object` | Necessário para [o modo autoescala](provision-throughput-autoscale.md) | Este objeto contém as definições associadas ao modo de capacidade de autoescalação. Pode configurar o `maxThroughput` valor, que descreve a maior quantidade de Unidades de Pedido que a coleção será aumentada de forma dinâmica. |

### <a name="output"></a>Saída

Devolve uma resposta de comando personalizada padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

#### <a name="create-a-collection-with-the-minimum-configuration"></a>Criar uma coleção com a configuração mínima

Para criar uma nova coleção com nome `"testCollection"` e os valores predefinidos, utilize o seguinte comando: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection"});
```

Isto resultará numa nova coleção fixa, sem fragmentos, com 400RU/s e um índice no `_id` campo automaticamente criado. Este tipo de configuração também será aplicado ao criar novas coleções através da `insert()` função. Por exemplo: 

```javascript
use test
db.newCollection.insert({});
```

#### <a name="create-a-unsharded-collection"></a>Criar uma coleção não-cacada

Para criar uma coleção nãohardificada com nome `"testCollection"` e produção prevista de 1000 RUs, utilize o seguinte comando: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

Pode criar uma coleção com até 10.000 RU/s como sem `offerThroughput` necessidade de especificar uma chave de fragmentos. Para coleções com maior produção, confira a secção seguinte.

#### <a name="create-a-sharded-collection"></a>Criar uma coleção de fragmentos

Para criar uma coleção de fragmentos com nome `"testCollection"` e produção prevista de 11.000 RUs, e uma `shardkey` propriedade "a.b", utilize o seguinte comando:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 11000, shardKey: "a.b" });
```

Este comando requer agora o `shardKey` parâmetro, uma vez que mais de 10.000 RU/s especificados no `offerThroughput` .

#### <a name="create-an-unsharded-autoscale-collection"></a>Criar uma coleção autoescalada sem fragmentos

Para criar uma coleção nãohardificada com o nome `'testCollection'` que utiliza capacidade de [produção automática](provision-throughput-autoscale.md) definida para 4.000 RU/s, utilize o seguinte comando:

```javascript
use test
db.runCommand({ 
    customAction: "CreateCollection", collection: "testCollection", 
    autoScaleSettings:{
      maxThroughput: 4000
    } 
});
```

Pelo `autoScaleSettings.maxThroughput` valor pode especificar um intervalo de 4.000 RU/s a 10.000 RU/s sem uma chave de fragmentos. Para uma produção de escala automática mais elevada, é necessário especificar o `shardKey` parâmetro.

#### <a name="create-a-sharded-autoscale-collection"></a>Criar uma coleção de autoescala de aço

Para criar uma coleção de fragmentos com o nome `'testCollection'` de uma chave de fragmentos chamada , e que utiliza capacidade de `'a.b'` [produção autoscalífusia](provision-throughput-autoscale.md) definida para 20.000 RU/s, utilize o seguinte comando:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", shardKey: "a.b", autoScaleSettings: { maxThroughput: 20000 }});
```

## <a name="update-collection"></a><a id="update-collection"></a> Coleção de atualização

O comando de extensão de recolha de atualização atualiza as propriedades associadas à recolha especificada.

```javascript
{
  customAction: "UpdateCollection",
  collection: "<Name of the collection that you want to update>",
  offerThroughput: (int) // New throughput that will be set to the collection
}
```

A tabela a seguir descreve os parâmetros dentro do comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   Nome do comando personalizado. Deve ser "UpdateCollection".      |
|  `collection`   |   `string`      |   O nome da coleção.       |
| `offerThroughput` | `int` |   Produção provisida para definir a coleção.|
| `autoScaleSettings` | `Object` | Requerido para [o modo autoescala](provision-throughput-autoscale.md). Este objeto contém as definições associadas ao modo de capacidade de autoescalação. O `maxThroughput` valor descreve a maior quantidade de Unidades de Pedido que a coleção será aumentada para dinamicamente. |

## <a name="output"></a>Saída

Devolve uma resposta de comando personalizada padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

#### <a name="update-the-provisioned-throughput-associated-with-a-collection"></a>Atualizar a produção a provisionada associada a uma coleção

Para atualizar o rendimento previsto de uma coleção com nome `"testCollection"` para 1200 RUs, utilize o seguinte comando:

```javascript
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a> Obter coleção

O comando personalizado de recolha de recolha devolve o objeto de coleção.

```javascript
{
  customAction: "GetCollection",
  collection: "<Name of the collection>"
}
```

A tabela a seguir descreve os parâmetros dentro do comando:


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| `customAction`    |   `string`      |   Nome do comando personalizado. Deve ser "GetCollection".      |
| `collection`    |    `string`     |    O nome da coleção.     |

### <a name="output"></a>Saída

Se o comando for bem sucedido, a resposta contém um documento com os seguintes campos


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Estado de resposta. 1 == sucesso. 0 == falha.      |
| `database`    |    `string`     |   O nome da base de dados.      |
| `collection`    |    `string`     |    O nome da coleção.     |
|  `shardKeyDefinition`   |   `document`      |  Documento de especificação de índice usado como chave de fragmento. Este é um parâmetro de resposta opcional.       |
|  `provisionedThroughput`   |   `int`      |    Provisioned Throughput para definir na coleção. Este é um parâmetro de resposta opcional.     |
| `autoScaleSettings` | `Object` | Este objeto contém os parâmetros de capacidade associados à base de dados se estiver a utilizar o [modo De escala automática](provision-throughput-autoscale.md). O `maxThroughput` valor descreve a maior quantidade de Unidades de Pedido que a coleção será aumentada para dinamicamente. |

Se o comando falhar, uma resposta de comando personalizada por defeito é devolvida. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

#### <a name="get-the-collection"></a>Pegue a coleção

Para obter o objeto de coleção para uma coleção `"testCollection"` chamada, use o seguinte comando:

```javascript
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

Se a coleção tiver uma capacidade de produção associada, incluirá o `provisionedThroughput` valor, e a saída será:

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 400,
        "ok" : 1
}
```

Se a coleção tiver uma produção autoestacionada associada, incluirá o `autoScaleSettings` objeto com o `maxThroughput` parâmetro, que define o rendimento máximo que a coleção irá aumentar para dinamicamente. Além disso, incluirá também o `provisionedThroughput` valor, que define o rendimento mínimo que esta coleção reduzirá se não houver pedidos na coleção: 

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 1000,
        "autoScaleSettings" : {
            "maxThroughput" : 10000
        },
        "ok" : 1
}
```

Se a recolha estiver a partilhar a [produção de nível de base de dados](set-throughput.md#set-throughput-on-a-database), seja no modo de autoestamos ou manual, a saída seria:

```javascript
{ "database" : "test", "collection" : "testCollection", "ok" : 1 }
```

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
            "maxThroughput" : 20000
        },
        "ok" : 1
}
```


## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a> Saída padrão de um comando personalizado

Se não for especificada, uma resposta personalizada contém um documento com os seguintes campos:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Estado de resposta. 1 == sucesso. 0 == falha.      |
| `code`    |   `int`      |   Só regressou quando o comando falhou (isto é, ok == 0). Contém o código de erro MongoDB. Este é um parâmetro de resposta opcional.      |
|  `errMsg`   |  `string`      |    Só regressou quando o comando falhou (isto é, ok == 0). Contém uma mensagem de erro fácil de utilizar. Este é um parâmetro de resposta opcional.      |

Por exemplo:

```javascript
{ "ok" : 1 }
```

## <a name="next-steps"></a>Passos seguintes

Em seguida, você pode proceder para aprender os seguintes conceitos Azure Cosmos DB: 

* [Indexação no Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expire os dados em Azure Cosmos DB automaticamente com tempo de vida](../cosmos-db/time-to-live.md)
