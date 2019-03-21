---
title: Ligar uma aplicação Mongoose node. js ao Azure Cosmos DB
description: Saiba como utilizar a arquitetura Mongoose para armazenar e gerir dados no Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: 23275bc639b445b55cafb72c929514541ba00660
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105952"
---
# <a name="connect-a-nodejs-mongoose-application-to-azure-cosmos-db"></a>Ligar uma aplicação Mongoose node. js ao Azure Cosmos DB

Este tutorial demonstra como utilizar o [arquitetura Mongoose](https://mongoosejs.com/) ao armazenar dados no Cosmos DB. Podemos usar o API do Azure Cosmos DB para o MongoDB para este passo a passo. Para os que não estão familiarizados com o Mongoose, o Mongoose é uma arquitetura de modelação de objetos para o MongoDB em Node.js e fornece uma solução simples com base no esquema para modelar os dados da aplicação.

O cosmos DB é um serviço da base de dados com múltiplos modelos distribuída globalmente da Microsoft. Pode criar e consultar documentos e bases de dados de gráfico, que beneficiam de capacidades de escalamento horizontal no núcleo do Cosmos DB e distribuição global chave/valor rapidamente.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Versão [Node.js](https://nodejs.org/) v0.10.29 ou superior.

## <a name="create-a-cosmos-account"></a>Criar uma conta do Cosmos

Vamos criar uma conta do Cosmos. Se já tiver uma conta que pretende utilizar, pode avançar diretamente para o conjunto a sua aplicação node. js. Se estiver a utilizar o emulador do Azure Cosmos DB, siga os passos indicados em [emulador do Azure Cosmos DB](local-emulator.md) para configurar o emulador e avance para o conjunto a sua aplicação node. js.

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="set-up-your-nodejs-application"></a>Configurar a aplicação Node.js

>[!Note]
> Se pretende ver apenas as instruções do código de exemplo em vez de configurar a própria aplicação, clone o [exemplo](https://github.com/Azure-Samples/Mongoose_CosmosDB) utilizado para este tutorial e crie a sua aplicação Mongoose Node.js no Azure Cosmos DB.

1. Para criar uma aplicação Node.js na pasta à sua escolha, execute o seguinte comando numa linha de comandos do nó.

    ```npm init```

    Responda às perguntas e o projeto estará pronto para começar.

1. Adicione um novo ficheiro à pasta e dê-lhe o nome ```index.js```.
1. Instale os pacotes necessários com uma das opções ```npm install```:
   * Mongoose: ```npm install mongoose@5 --save```

     > [!Note]
     > A ligação de exemplo do Mongoose abaixo baseia-se no Mongoose 5 +, o que mudou desde as versões anteriores.
    
   * Dotenv (se pretende carregar os segredos a partir de um ficheiro .env):```npm install dotenv --save```

     >[!Note]
     > O sinalizador ```--save``` adiciona a dependência ao ficheiro package.json.

1. Importe as dependências no ficheiro index.js.
    ```JavaScript
    var mongoose = require('mongoose');
    var env = require('dotenv').load();    //Use the .env file to load the variables
    ```

1. Adicione a cadeia de ligação do Cosmos DB e o Nome do Cosmos DB ao ficheiro ```.env```.

    ```JavaScript
    COSMOSDB_CONNSTR=mongodb://{cosmos-user}.documents.azure.com:10255/{dbname}
    COSMODDB_USER=cosmos-user
    COSMOSDB_PASSWORD=cosmos-secret
    ```

1. Ligar ao Cosmos DB com a arquitetura Mongoose, adicionando o seguinte código ao final do Index.
    ```JavaScript
    mongoose.connect(process.env.COSMOSDB_CONNSTR+"?ssl=true&replicaSet=globaldb", {
      auth: {
        user: process.env.COSMODDB_USER,
        password: process.env.COSMOSDB_PASSWORD
      }
    })
    .then(() => console.log('Connection to CosmosDB successful'))
    .catch((err) => console.error(err));
    ```
    >[!Note]
    > Aqui, as variáveis de ambiente são carregadas com process.env.{variableName} utilizando o pacote npm «dotenv».

    Assim que estiver ligado ao Azure Cosmos DB, pode começar a configurar modelos de objetos no Mongoose.

## <a name="caveats-to-using-mongoose-with-cosmos-db"></a>Avisos sobre a utilização do Mongoose com o Cosmos DB

Para cada modelo que criar, o Mongoose cria uma nova coleção. No entanto, tendo em conta o modelo de faturação por coleção do Cosmos DB, pode não ser a forma mais económica para aceder, se tem vários modelos de objetos que estão pouco preenchidos.

Estas instruções abrangem ambos os modelos. Primeiro, vamos ver as instruções sobre o armazenamento de um tipo de dados por coleção. Este é o comportamento predefinido do Mongoose.

O Mongoose também tem um conceito chamado [Discriminadores](https://mongoosejs.com/docs/discriminators.html). Os discriminadores são um mecanismo de herança de esquemas. Permitem-lhe ter vários modelos com esquemas sobrepostos por cima da mesma coleção MongoDB subjacente.

Pode armazenar os vários modelos de dados na mesma coleção e, em seguida, utilizar uma cláusula de filtro no momento da consulta para obter apenas os dados necessários.

### <a name="one-collection-per-object-model"></a>Uma coleção por modelo de objeto

O comportamento predefinido do Mongoose é criar uma coleção MongoDB sempre que criar um modelo de objeto. Esta secção explica como fazê-lo com a API do Azure Cosmos DB para o MongoDB. Este método é recomendado quando tiver modelos de objeto com grandes quantidades de dados. Este é o modelo operativo predefinido para o Mongoose, pelo que já deve estar familiarizado com ele, se estiver familiarizado com o Mongoose.

1. Abra novamente o ficheiro ```index.js```.

1. Crie a definição de esquema para «Family».

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. Crie um objeto para «Family».

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. Por fim, vamos guardar o objeto do Cosmos DB. Esta ação cria uma coleção nos bastidores.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. Agora, vamos criar outro esquema e objeto. Desta vez, vamos criar um para «Vacation Destinations» no qual as famílias poderão estar interessadas.
   1. Tal como da última vez, vamos criar o esquema.
      ```JavaScript
      const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
       name: String,
       country: String
      }));
      ```

   1. Crie um objeto de exemplo (pode adicionar vários objetos a este esquema) e guarde-o.
      ```JavaScript
      const vacaySpot = new VacationDestinations({
       name: "Honolulu",
       country: "USA"
      });

      vacaySpot.save((err, saveVacay) => {
       console.log(JSON.stringify(saveVacay));
      });
      ```

1. Agora, vai no portal do Azure, para que o nas duas coleções criadas no Cosmos DB.

    ![NODE. js tutorial nó - captura de ecrã do portal do Azure, com uma conta do Azure Cosmos DB, com vários nomes de coleções realçados - base de dados][multiple-coll]

1. Por fim, vamos ler os dados do Cosmos DB. Uma vez que estamos a utilizar o modelo operativo Mongoose predefinido, as leituras são idênticas a quaisquer outras leituras com o Mongoose.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Utilizar os discriminadores do Mongoose para armazenar dados numa única coleção

Nesse método, utilizamos [os Discriminadores do Mongoose](https://mongoosejs.com/docs/discriminators.html) para ajudar a otimizar os custos de cada coleção. Os discriminadores permitem-lhe definir uma «Chave» diferenciadora, que lhe permite armazenar, diferenciar e filtrar modelos de objetos diferentes.

Aqui, vamos criar um modelo de objeto base, definir uma chave diferenciadora e adicionar «Family» e «VacationDestinations» como uma extensão ao modelo base.

1. Vamos definir a configuração base e definir a chave de discriminador.

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. Em seguida, vamos definir o modelo de objeto comum

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. Agora, definimos o modelo «Family». Repare que estamos a utilizar ```commonModel.discriminator``` em vez de ```mongoose.model```. Além disso, estamos a adicionar também a configuração base ao esquema mongoose. Por isso, aqui, a discriminatorKey é ```FamilyType```.

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. Da mesma forma, vamos adicionar outro esquema, desta vez para «VacationDestinations». Aqui, a DiscriminatorKey é ```VacationDestinationsType```.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. Por fim, vamos criar objetos para o modelo e guardá-lo.
   1. Vamos adicionar objetos ao modelo «Family».
      ```JavaScript
      const family_common = new Family_common({
       lastName: "Volum",
       parents: [
           { firstName: "Thomas" },
           { firstName: "Mary Kay" }
       ],
       children: [
           { firstName: "Ryan", gender: "male", grade: 8 },
           { firstName: "Patrick", gender: "male", grade: 7 }
       ],
       pets: [
           { givenName: "Blackie" }
       ],
       address: { country: "USA", state: "WA", city: "Seattle" }
      });

      family_common.save((err, saveFamily) => {
       console.log("Saved: " + JSON.stringify(saveFamily));
      });
      ```

   1. Em seguida, vamos adicionar objetos ao modelo «VacationDestinations» e guardá-lo.
      ```JavaScript
      const vacay_common = new Vacation_common({
       name: "Honolulu",
       country: "USA"
      });

      vacay_common.save((err, saveVacay) => {
       console.log("Saved: " + JSON.stringify(saveVacay));
      });
      ```

1. Agora, se voltar ao portal do Azure, poderá notar que tem apenas uma coleção chamada ```alldata``` com os dados de «Family» e «VacationDestinations».

    ![NODE. js tutorial nó - captura de ecrã do portal do Azure, com uma conta do Azure Cosmos DB, com o nome da coleção realçado - base de dados][alldata]

1. Além disso, repare que cada objeto tem outro atributo denominado ```__type```, que ajuda a diferenciar entre os dois modelos de objeto diferentes.

1. Por fim, vamos ler os dados armazenados no Azure Cosmos DB. O Mongoose encarrega-se de filtrar os dados com base no modelo. Por isso, não terá de fazer nada diferente durante a leitura dos dados. Basta especificar o seu modelo (neste caso, ```Family_common```) e o Mongoose encarrega-se da filtragem na «DiscriminatorKey».

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Como pode ver, é fácil trabalhar com os discriminadores do Mongoose. Então, se tiver uma aplicação que utiliza a arquitetura mongoose, este tutorial é uma maneira de obter a sua aplicação em execução com a API do Azure Cosmos do MongoDB sem a necessidade de demasiadas alterações.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [utilizar Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para o MongoDB.
- Saiba como [utilizar Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para o MongoDB.
- Explorar o MongoDB [amostras](mongodb-samples.md) com a API do Azure Cosmos DB para o MongoDB.

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[multiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png
