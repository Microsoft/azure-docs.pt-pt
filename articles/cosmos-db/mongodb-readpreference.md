---
title: Use a preferência de Ler com a API da Azure Cosmos DB para a MongoDB
description: Saiba como usar a Preferência de Leitura do MongoDB com a API da Azure Cosmos para a MongoDB
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 02/26/2019
ms.custom: devx-track-js
ms.openlocfilehash: 3c78ad6605e927015d35df12cadf0347dd0337cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349049"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Como distribuir globalmente leituras usando AZure Cosmos DB's API para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este artigo mostra como distribuir globalmente as operações de leitura com as definições [de Preferência de Leitura mongoDB](https://docs.mongodb.com/manual/core/read-preference/) usando a API da Azure Cosmos DB para a MongoDB.

## <a name="prerequisites"></a>Pré-requisitos 
Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Consulte este artigo [do Quickstart](tutorial-global-distribution-mongodb.md) para obter instruções sobre a utilização do portal Azure para criar uma conta Cosmos com distribuição global e, em seguida, conectá-la.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Abra uma janela de terminal do git, tal como git bash, e `cd` para um diretório de trabalho.  

Execute os seguintes comandos para clonar o repositório de exemplo. Com base na sua plataforma de interesse, utilize um dos seguintes repositórios de amostra:

1. [.Net pedido de amostra](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [Aplicação da amostra NodeJS]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Aplicação da amostra de Mongoose](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Aplicação da amostra de Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [Aplicação da amostra SpringBoot](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Executar a aplicação

Dependendo da plataforma utilizada, instale as embalagens necessárias e inicie a aplicação. Para instalar dependências, siga a README incluída no repositório de aplicação da amostra. Por exemplo, na aplicação de amostra NodeJS, utilize os seguintes comandos para instalar as embalagens necessárias e iniciar a aplicação.

```bash
cd mean
npm install
node index.js
```
A aplicação tenta ligar-se a uma fonte mongoDB e falha porque a cadeia de ligação é inválida. Siga os passos na README para atualizar a cadeia de ligação `url` . Além disso, atualize `readFromRegion` a região de leitura na sua conta cosmos. As seguintes instruções são da amostra NodeJS:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

Após seguir estes passos, a aplicação da amostra executa e produz a seguinte saída:

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

## <a name="read-using-read-preference-mode"></a>Leia usando o modo de Preferência de Leitura

O protocolo MongoDB fornece os seguintes modos de Preferência de Leitura para os clientes utilizarem:

1. PRIMÁRIO
2. PRIMARY_PREFERRED
3. SECUNDÁRIO
4. SECONDARY_PREFERRED
5. MAIS PRÓXIMO

Consulte a documentação detalhada do [comportamento da MongoDB Read Preference](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) para obter detalhes sobre o comportamento de cada um destes modos de preferência de leitura. Em Cosmos DB, mapas primários para a região WRITE e mapas secundários para a região de READ.

Com base em cenários comuns, recomendamos a utilização das seguintes definições:

1. Se forem necessárias **leituras de baixa latência,** utilize o modo de preferência de leitura **MAIS PRÓXIMO.** Este ajuste direciona as operações de leitura para a região disponível mais próxima. Note-se que se a região mais próxima for a região WRITE, então estas operações são direcionadas para aquela região.
2. Se for necessária **uma elevada disponibilidade e distribuição geofísmica de leituras** (a latência não é uma restrição), utilize o modo de preferência de leitura PREFERENCIAL **ou** PREFERENCIAL **DE PREFERÊNCIAS.** Este ajuste direciona as operações de leitura para uma região disponível de WRITE ou READ, respectivamente. Se a região não estiver disponível, os pedidos são direcionados para a próxima região disponível de acordo com o comportamento de preferência de leitura.

O seguinte corte da aplicação da amostra mostra como configurar a preferência de leitura mais próxima em NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Da mesma forma, o corte abaixo mostra como configurar a preferência de leitura de SECONDARY_PREFERRED em NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

A Preferência de Leitura também pode ser definida passando `readPreference` como um parâmetro nas opções URI de cadeia de ligação:

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

Consulte os repos de aplicação da amostra correspondentes para outras plataformas, tais como [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) e [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Ler usando tags

Além do modo Leitura Preferência, o protocolo MongoDB permite a utilização de tags para operações de leitura direta. Na API da Cosmos DB para a MongoDB, a `region` etiqueta é incluída por padrão como parte da `isMaster` resposta:

```json
"tags": {
         "region": "West US"
      }
```

Assim, a MongoClient pode usar a `region` etiqueta juntamente com o nome da região para direcionar as operações de leitura para regiões específicas. Para as contas cosmos, os nomes da região podem ser encontrados no portal Azure à esquerda sob **os dados de Replica De Definições->a nível global**. Esta configuração é útil para alcançar **o isolamento da leitura** - casos em que a aplicação do cliente quer dirigir operações de leitura apenas para uma região específica. Esta configuração é ideal para cenários de tipo não-produção/analítico, que funcionam em segundo plano e não são serviços críticos de produção.

O seguinte corte da aplicação da amostra mostra como configurar a Preferência de Leitura com etiquetas em NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Consulte os repos de aplicação da amostra correspondentes para outras plataformas, tais como [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) e [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

Neste artigo, aprendeu a distribuir globalmente operações de leitura usando a Read Preference com a API da Azure Cosmos DB para a MongoDB.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não vai continuar a utilizar esta aplicação, elimine todos os recursos criados por este artigo no portal Azure com os seguintes passos:

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

* [Import MongoDB data into Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) (Importar dados do MongoDB para o Azure Cosmos DB)
* [Configurar uma base de dados distribuída globalmente com a API da Azure Cosmos para a MongoDB](tutorial-global-distribution-mongodb.md)
* [Desenvolva-se localmente com o emulador Azure Cosmos DB](local-emulator.md)