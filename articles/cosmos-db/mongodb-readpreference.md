---
title: Utilizar a preferência de leitura do MongoDB com a API do Azure Cosmos DB para o MongoDB
description: Saiba como utilizar a preferência de leitura do MongoDB com a API do Azure Cosmos DB para o MongoDB
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 8fc66d70b840578bff086519a7b39e5f389a3de3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66479610"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Como distribuir globalmente lê com a API do Azure Cosmos DB do MongoDB

Este artigo mostra como distribuir globalmente com operações de leitura [preferência de leitura do MongoDB](https://docs.mongodb.com/manual/core/read-preference/) definições com a API do Azure Cosmos DB do MongoDB.

## <a name="prerequisites"></a>Pré-requisitos 
Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Consulte este [guia de introdução](tutorial-global-distribution-mongodb.md) artigo para obter instruções sobre como utilizar o portal do Azure para definir uma conta do Cosmos com a distribuição global de segurança e, em seguida, ligação-la.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Abra uma janela de terminal do git, tal como git bash, e `cd` para um diretório de trabalho.  

Execute os seguintes comandos para clonar o repositório de exemplo. Com base na sua plataforma de interesse, utilize um dos repositórios do exemplo seguinte:

1. [Aplicativo de exemplo .NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [Exemplo de aplicação do NodeJS]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Aplicação de exemplo do mongoose](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Aplicação de exemplo de Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [Aplicação de exemplo do SpringBoot](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Executar a aplicação

Consoante a plataforma usada, instale os pacotes necessários e iniciar a aplicação. Para instalar as dependências, siga o ficheiro README incluído no repositório da aplicação de exemplo. Por exemplo, no aplicativo de exemplo NodeJS, utilize os seguintes comandos para instalar os pacotes necessários e iniciar a aplicação.

```bash
cd mean
npm install
node index.js
```
A aplicação tenta ligar a uma origem de MongoDB e falha porque a cadeia de ligação é inválida. Siga os passos em Leiame para atualizar a cadeia de ligação `url`. Além disso, atualize o `readFromRegion` para uma região de leitura na sua conta do Cosmos. As instruções seguintes são do exemplo NodeJS:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

Depois de seguir estes passos, o aplicativo de exemplo é executado e produz a seguinte saída:

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

## <a name="read-using-read-preference-mode"></a>Leia a utilizar o modo de preferência de leitura

Protocolo de MongoDB fornece os seguintes modos de preferência de leitura para os clientes utilizarem:

1. PRIMÁRIO
2. PRIMARY_PREFERRED
3. SECUNDÁRIO
4. SECONDARY_PREFERRED
5. MAIS PRÓXIMO

Consulte para consultar os detalhes [comportamento de preferência de leitura do MongoDB](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) documentação para obter detalhes sobre o comportamento de cada um destes modos de preferência de leitura. No Cosmos DB, a principal mapeia para a região de escrita e secundário é mapeado para a região de leitura.

Com base em cenários comuns, recomendamos que utilize as seguintes definições:

1. Se **leituras de latência baixa** são necessários, utilize o **NEAREST** modo de preferência de leitura. Esta definição indica as operações de leitura para a região disponível mais próxima. Tenha em atenção que se a região mais próxima da região de escrita, em seguida, essas operações são direcionadas para essa região.
2. Se **distribuição de elevada disponibilidade e na geografia de leituras** são necessárias (latência não é uma restrição), em seguida, utilize o **preferencial SECUNDÁRIO** modo de preferência de leitura. Esta definição indica as operações de leitura para uma região de leitura disponível. Se nenhuma região de leitura estiver disponível, em seguida, pedidos são direcionados para a região de escrita.

O fragmento seguinte da aplicação de exemplo mostra como configurar a preferência de leitura mais próximo em NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Da mesma forma, o fragmento abaixo mostra como configurar a preferência de leitura SECONDARY_PREFERRED no NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

A preferência de leitura também pode ser definida passando `readPreference` como um parâmetro nas opções de URI de cadeia de caracteres de conexão:

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

Consulte os repositórios de aplicativo de exemplo correspondente para outras plataformas, tal como [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) e [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Leia a utilização de etiquetas

Além do modo de preferência de leitura, o protocolo do MongoDB permite a utilização de etiquetas para direcionar as operações de leitura. Na API do Cosmos DB para o MongoDB, o `region` marca é incluída por predefinição como parte do `isMaster` resposta:

```json
"tags": {
         "region": "West US"
      }
```

Por conseguinte, pode utilizar MongoClient o `region` Etiquetar juntamente com o nome da região para direcionar as operações de leitura para regiões específicas. Para contas do Cosmos, os nomes de região podem ser encontrados no portal do Azure à esquerda sob **definições -> globalmente os dados de réplica**. Esta definição é útil para atingir **ler isolamento** -casos em que aplicação de cliente que pretende direcionar as operações de leitura apenas numa região específica. Esta definição é ideal para não-produção/analytics escreva cenários, o que são executados em segundo plano e não são serviços críticos de produção.

O fragmento seguinte da aplicação de exemplo mostra como configurar a preferência de leitura com marcas em NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Consulte os repositórios de aplicativo de exemplo correspondente para outras plataformas, tal como [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) e [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

Neste artigo, aprendeu como distribuir globalmente com a preferência de leitura com a API do Azure Cosmos DB do MongoDB de operações de leitura.

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar a utilizar esta aplicação, elimine todos os recursos criados neste artigo no portal do Azure com os seguintes passos:

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

* [Importar dados da MongoDB para o Azure Cosmos DB](mongodb-migrate.md)
* [Configurar uma base de dados distribuída globalmente com a API do Azure Cosmos DB para MongoDB](tutorial-global-distribution-mongodb.md)
* [Desenvolver localmente com o emulador do Azure Cosmos DB](local-emulator.md)
