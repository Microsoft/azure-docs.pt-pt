---
title: Use a API da Azure Cosmos DB para o MongoDB para construir uma app Node.js
description: Um tutorial que cria uma base de dados online usando a API do Azure Cosmos DB para MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 28ee64f70cd281a2563a855fb1fca91f229ec7bd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "61330603"
---
# <a name="build-an-app-using-nodejs-and-azure-cosmos-dbs-api-for-mongodb"></a>Construa uma aplicação utilizando a API do Node.js e da Azure Cosmos DB para o MongoDB 
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Node.js para MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Este exemplo mostra-lhe como construir uma aplicação de consola utilizando a API de Node.js e Azure Cosmos DB para MongoDB.

Para utilizar este exemplo, tem de:

* [Crie](create-mongodb-dotnet.md#create-account) uma conta Cosmos configurada para usar a API do Azure Cosmos DB para mongoDB.
* Recupere as informações das cordas de [ligação.](connect-mongodb-account.md)

## <a name="create-the-app"></a>Criar a aplicação

1. Crie um ficheiro *app.js* e copie e cole o código abaixo.

    ```javascript
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **Opcional**: se estiver a utilizar o **controlador do MongoDB Node.js 2.2**, substitua o seguinte fragmento de código:

    Original:

    ```javascript
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    Deve ser substituído por:

    ```javascript
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. Modifique as seguintes variáveis no ficheiro *app.js* conforme as definições da sua conta (Saiba como encontrar a sua [cadeia de ligação](connect-mongodb-account.md)):

    > [!IMPORTANT]
    > O **controlador do MongoDB Node.js 3.0** precisa de carateres especiais de codificação na palavra-passe do Cosmos DB. Certifique-se de que codifica os carateres "=" como %3D
    >
    > Exemplo: A palavra-passe *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* é codificada para *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D*
    >
    > O **controlador do MongoDB Node.js 2.2** não precisa de carateres especiais de codificação na palavra-passe do Cosmos DB.
    >
    >
   
    ```javascript
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Abra o seu terminal favorito, execute **npm install mongodb --save** e, em seguida, execute a aplicação com **node app.js**

## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para MongoDB.
- Aprenda a [usar robo 3T](mongodb-robomongo.md) com API da Azure Cosmos DB para MongoDB.
- Explore [as amostras](mongodb-samples.md) de MongoDB com a API da Azure Cosmos DB para o MongoDB.
