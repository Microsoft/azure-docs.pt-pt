---
title: Use a preferência de Leitura com a API do Azure Cosmos DB para MongoDB
description: Saiba como usar a Preferência de Leitura de MongoDB com a API do Azure Cosmos DB para MongoDB
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 579767a0d535605a2316c35bd413a75474b5a3de
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410006"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Como distribuir globalmente as leituras usando a API da Azure Cosmos DB para mongoDB

Este artigo mostra como distribuir globalmente as operações de leitura com as definições de preferência de [Leitura MongoDB](https://docs.mongodb.com/manual/core/read-preference/) usando a API do Azure Cosmos DB para mongoDB.

## <a name="prerequisites"></a>Pré-requisitos 
Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Consulte este artigo da [Quickstart](tutorial-global-distribution-mongodb.md) para obter instruções sobre a utilização do portal Azure para criar uma conta Cosmos com distribuição global e, em seguida, ligar-se a ele.

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Abra uma janela de terminal do git, tal como git bash, e `cd` para um diretório de trabalho.  

Execute os seguintes comandos para clonar o repositório de exemplo. Com base na sua plataforma de interesse, utilize um dos seguintes repositórios de amostra:

1. [.NET aplicação da amostra](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [Aplicação da amostra nodeJS]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Aplicação da amostra de Mongoose](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Aplicação da amostra java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [Aplicação da amostra SpringBoot](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Executar a aplicação

Dependendo da plataforma utilizada, instale os pacotes necessários e inicie a aplicação. Para instalar dependências, siga a README incluída no repositório de aplicação da amostra. Por exemplo, na aplicação da amostra NodeJS, utilize os seguintes comandos para instalar as embalagens necessárias e iniciar a aplicação.

```bash
cd mean
npm install
node index.js
```
A aplicação tenta ligar-se a uma fonte MongoDB e falha porque a cadeia de ligação é inválida. Siga os passos na README `url`para atualizar a cadeia de ligação . Além disso, atualize a `readFromRegion` região de leitura na sua conta Cosmos. As seguintes instruções são da amostra NodeJS:

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

## <a name="read-using-read-preference-mode"></a>Ler usando o modo ler preferência

O protocolo MongoDB fornece os seguintes modos de preferência de leitura para os clientes utilizarem:

1. PRIMÁRIO
2. PRIMARY_PREFERRED
3. SECUNDÁRIO
4. SECONDARY_PREFERRED
5. MAIS PRÓXIMO

Consulte a documentação detalhada do comportamento da [MongoDB Read Preferencial](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) para obter detalhes sobre o comportamento de cada um destes modos de preferência de leitura. Em Cosmos DB, mapas primários para escrever região e mapas secundários para a região de READ.

Com base em cenários comuns, recomendamos a utilização das seguintes definições:

1. Se forem necessárias leituras de **latência baixas,** utilize o modo de preferência de leitura **mais PRÓXIMO.** Esta definição direciona as operações de leitura para a região disponível mais próxima. Note-se que se a região mais próxima for a região WRITE, então estas operações são direcionadas para aquela região.
2. Se for em prevaricação **a elevada disponibilidade e a distribuição geo-geo-qualidade das leituras** (a latência não é um constrangimento), utilize o modo de preferência de leitura PREFERIDO **PRIMÁRIO** ou **SECUNDÁRIO.** Esta definição direciona as operações de leitura para uma região disponível de WRITE ou READ, respectivamente. Se a região não estiver disponível, os pedidos são direcionados para a próxima região disponível de acordo com o comportamento de preferência de leitura.

O seguinte corte da aplicação da amostra mostra como configurar a preferência de leitura mais próxima no NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Da mesma forma, o corte abaixo mostra como configurar a preferência de leitura SECONDARY_PREFERRED no NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

A Preferência de Leitura também `readPreference` pode ser definida passando como parâmetro nas opções URI de linha de ligação:

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

Além do modo Read Preference, o protocolo MongoDB permite a utilização de tags para operações de leitura direta. Na API da Cosmos DB para MongoDB, a `region` etiqueta é `isMaster` incluída por padrão como parte da resposta:

```json
"tags": {
         "region": "West US"
      }
```

Assim, o MongoClient `region` pode usar a etiqueta juntamente com o nome da região para direcionar as operações de leitura para regiões específicas. Para as contas da Cosmos, os nomes da região podem ser encontrados no portal Azure à esquerda sob os dados de **Replica de Definições->a nível global.** Este cenário é útil para alcançar **o isolamento da leitura** - casos em que a aplicação do cliente quer direcionar as operações de leitura para uma região específica apenas. Esta configuração é ideal para cenários de tipo não-produção/analítico, que funcionam em segundo plano e não são serviços críticos de produção.

O seguinte corte da aplicação da amostra mostra como configurar a Preferência de Leitura com etiquetas no NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Consulte os repos de aplicação da amostra correspondentes para outras plataformas, tais como [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) e [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

Neste artigo, aprendeu a distribuir globalmente as operações de leitura usando a Read Preference com a API da Azure Cosmos DB para o MongoDB.

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a utilizar esta app, elimine todos os recursos criados por este artigo no portal Azure com os seguintes passos:

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou. 
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

* [Import MongoDB data into Azure Cosmos DB](mongodb-migrate.md) (Importar dados do MongoDB para o Azure Cosmos DB)
* [Criar uma base de dados globalmente distribuída com a API da Azure Cosmos DB para o MongoDB](tutorial-global-distribution-mongodb.md)
* [Desenvolver localmente com o emulador Azure Cosmos DB](local-emulator.md)
