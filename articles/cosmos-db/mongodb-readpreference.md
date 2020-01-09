---
title: Usar a preferência de leitura com a API do Azure Cosmos DB para MongoDB
description: Saiba como usar a preferência de leitura do MongoDB com a API do Azure Cosmos DB para MongoDB
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 10e6ed556abe8f8c438e5436fbb93c1b70b85d2b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445166"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Como distribuir globalmente as leituras usando a API do Azure Cosmos DB para MongoDB

Este artigo mostra como distribuir globalmente as operações de leitura com as configurações de [preferência de leitura do MongoDB](https://docs.mongodb.com/manual/core/read-preference/) usando a API do Azure Cosmos DB para o MongoDB.

## <a name="prerequisites"></a>Pré-requisitos 
Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Consulte este artigo de [início rápido](tutorial-global-distribution-mongodb.md) para obter instruções sobre como usar o portal do Azure para configurar uma conta do cosmos com distribuição global e, em seguida, conectar-se a ela.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Abra uma janela de terminal do git, tal como git bash, e `cd` para um diretório de trabalho.  

Execute os seguintes comandos para clonar o repositório de exemplo. Com base na sua plataforma de interesse, use um dos seguintes repositórios de exemplo:

1. [Aplicativo de exemplo .NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [Aplicativo de exemplo NodeJS]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Aplicativo de exemplo Mongoose](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Aplicativo de exemplo Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [Aplicativo de exemplo SpringBoot](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Executar a aplicação

Dependendo da plataforma usada, instale os pacotes necessários e inicie o aplicativo. Para instalar as dependências, siga o arquivo LEIAme incluído no repositório de aplicativos de exemplo. Por exemplo, no aplicativo de exemplo NodeJS, use os comandos a seguir para instalar os pacotes necessários e iniciar o aplicativo.

```bash
cd mean
npm install
node index.js
```
O aplicativo tenta se conectar a uma origem do MongoDB e falha porque a cadeia de conexão é inválida. Siga as etapas no LEIAme para atualizar a cadeia de conexão `url`. Além disso, atualize o `readFromRegion` para uma região de leitura em sua conta do cosmos. As instruções a seguir são do exemplo NodeJS:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

Depois de seguir essas etapas, o aplicativo de exemplo é executado e produz a seguinte saída:

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>Ler usando o modo de preferência de leitura

O protocolo MongoDB fornece os seguintes modos de preferência de leitura para os clientes usarem:

1. PRIMARY
2. PRIMARY_PREFERRED
3. SECUNDÁRIO
4. SECONDARY_PREFERRED
5. MAIS próximo

Consulte a documentação detalhada de [comportamento de preferência de leitura do MongoDB](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) para obter detalhes sobre o comportamento de cada um desses modos de preferência de leitura. Em Cosmos DB, o primário mapeia para a região de gravação e mapas secundários para a região de leitura.

Com base em cenários comuns, é recomendável usar as seguintes configurações:

1. Se forem necessárias **leituras de baixa latência** , use o modo de preferência de leitura **mais próximo** . Essa configuração direciona as operações de leitura para a região mais próxima disponível. Observe que, se a região mais próxima for a região de gravação, essas operações serão direcionadas para essa região.
2. Se a **alta disponibilidade e a distribuição geográfica de leituras** forem necessárias (a latência não é uma restrição), use o modo de preferência de leitura **preferencial secundário** . Essa configuração direciona as operações de leitura para uma região de leitura disponível. Se nenhuma região de leitura estiver disponível, as solicitações serão direcionadas para a região de gravação.

O trecho a seguir do aplicativo de exemplo mostra como configurar a preferência de leitura mais próxima no NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Da mesma forma, o trecho a seguir mostra como configurar a preferência de leitura de SECONDARY_PREFERRED no NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

A preferência de leitura também pode ser definida passando `readPreference` como um parâmetro nas opções de URI da cadeia de conexão:

```javascript
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017?ssl=true&replicaSet=globaldb&readPreference=nearest';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the Server
MongoClient.connect(url, function(err, client) {
  console.log("Connected correctly to server");

  const db = client.db(dbName);

  client.close();
});
```

Consulte o aplicativo de exemplo correspondente repositórios para outras plataformas, como [.net](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) e [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Ler usando marcas

Além do modo de preferência de leitura, o protocolo MongoDB permite o uso de marcas para direcionar operações de leitura. Na API do Cosmos DB para MongoDB, a marca de `region` é incluída por padrão como parte da resposta `isMaster`:

```json
"tags": {
         "region": "West US"
      }
```

Portanto, MongoClient pode usar a marca `region` junto com o nome da região para direcionar operações de leitura para regiões específicas. Para contas do cosmos, os nomes de região podem ser encontrados em portal do Azure à esquerda em **Configurações-> dados da réplica globalmente**. Essa configuração é útil para obter **isolamento de leitura** – casos em que o aplicativo cliente deseja direcionar operações de leitura apenas para uma região específica. Essa configuração é ideal para cenários de tipo não de produção/análise, que são executados em segundo plano e não são serviços críticos de produção.

O trecho de código a seguir do aplicativo de exemplo mostra como configurar a preferência de leitura com marcas no NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Consulte o aplicativo de exemplo correspondente repositórios para outras plataformas, como [.net](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) e [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

Neste artigo, você aprendeu como distribuir globalmente operações de leitura usando a preferência de leitura com a API de Azure Cosmos DB para MongoDB.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não for continuar a usar este aplicativo, exclua todos os recursos criados por este artigo na portal do Azure com as seguintes etapas:

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

* [Import MongoDB data into Azure Cosmos DB](mongodb-migrate.md) (Importar dados do MongoDB para o Azure Cosmos DB)
* [Configurar um banco de dados distribuído globalmente com a API do Azure Cosmos DB para MongoDB](tutorial-global-distribution-mongodb.md)
* [Desenvolver localmente com o emulador de Azure Cosmos DB](local-emulator.md)
