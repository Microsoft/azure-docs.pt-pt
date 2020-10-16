---
title: Ligue uma aplicação Node.js Mongoose à Azure Cosmos DB
description: Saiba como utilizar a Estrutura Mongoose para armazenar e gerir dados em Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18, devx-track-js
ms.openlocfilehash: 84d96344f20c56c9fab9eb5b3affcca3a437c096
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324557"
---
# <a name="connect-a-nodejs-mongoose-application-to-azure-cosmos-db"></a>Ligue uma aplicação Node.js Mongoose à Azure Cosmos DB

Este tutorial demonstra como usar a [Estrutura mongoosa](https://mongoosejs.com/) ao armazenar dados em Cosmos DB. Usamos a API da Azure Cosmos para a MongoDB para esta passagem. Para os que não estão familiarizados com o Mongoose, o Mongoose é uma arquitetura de modelação de objetos para o MongoDB em Node.js e fornece uma solução simples com base no esquema para modelar os dados da aplicação.

Cosmos DB é o serviço de base de dados multi-modelo distribuído globalmente pela Microsoft. Pode criar e consultar rapidamente documentos, bases de dados chave/valor e gráficos, que beneficiam da distribuição global e das capacidades de escala horizontal no núcleo da Cosmos DB.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Versão [Node.js](https://nodejs.org/) v0.10.29 ou superior.

## <a name="create-a-cosmos-account"></a>Criar uma conta Cosmos

Vamos criar uma conta cosmos. Se já tiver uma conta que pretende utilizar, pode avançar diretamente para Configurar a aplicação Node.js. Se estiver a utilizar o Emulador Azure Cosmos DB, siga os passos no [Azure Cosmos DB Emulator](local-emulator.md) para configurar o emulador e avance para configurar a sua aplicação Node.js.

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

### <a name="create-a-database"></a>Criar uma base de dados 
Nesta aplicação vamos abranger duas formas de criar coleções em Azure Cosmos DB: 
- **Armazenar cada modelo de objeto numa coleção separada**: Recomendamos a [criação de uma base de dados com produção dedicada.](set-throughput.md#set-throughput-on-a-database) A utilização deste modelo de capacidade irá dar-lhe uma melhor eficiência de custos.

    :::image type="content" source="./media/mongodb-mongoose/db-level-throughput.png" alt-text="Node.js tutorial - Screenshot do portal Azure, mostrando como criar uma base de dados no Data Explorer para uma conta DB Azure Cosmos, para utilização com o módulo Nó Mongoose":::

- **Armazenar todos os modelos de objetos numa única coleção de Cosmos DB**: Se preferir armazenar todos os modelos numa única coleção, pode apenas criar uma nova base de dados sem selecionar a opção Provision Throughput. A utilização deste modelo de capacidade criará cada coleção com a sua própria capacidade de produção para cada modelo de objeto.

Depois de criar a base de dados, usará o nome na `COSMOSDB_DBNAME` variável ambiente abaixo.

## <a name="set-up-your-nodejs-application"></a>Configurar a aplicação Node.js

>[!Note]
> Se pretende ver apenas as instruções do código de exemplo em vez de configurar a própria aplicação, clone o [exemplo](https://github.com/Azure-Samples/Mongoose_CosmosDB) utilizado para este tutorial e crie a sua aplicação Mongoose Node.js no Azure Cosmos DB.

1. Para criar uma aplicação Node.js na pasta à sua escolha, execute o seguinte comando numa linha de comandos do nó.

    ```npm init```

    Responda às perguntas e o projeto estará pronto para começar.

2. Adicione um novo ficheiro à pasta e dê-lhe o nome ```index.js```.
3. Instale os pacotes necessários com uma das opções ```npm install```:
   * Mongoose: ```npm install mongoose@5 --save```

     > [!Note]
     > A ligação de exemplo mongoose abaixo é baseada no Mongoose 5+, que mudou desde versões anteriores.
    
   * Dotenv (se pretende carregar os segredos a partir de um ficheiro .env):```npm install dotenv --save```

     >[!Note]
     > O sinalizador ```--save``` adiciona a dependência ao ficheiro package.json.

4. Importe as dependências no ficheiro index.js.

    ```JavaScript
   var mongoose = require('mongoose');
   var env = require('dotenv').config();   //Use the .env file to load the variables
    ```

5. Adicione a cadeia de ligação do Cosmos DB e o Nome do Cosmos DB ao ficheiro ```.env```. Substitua os espaços reservados {cosmos-account-name} e {dbname} pelo nome da conta cosmos e nome da base de dados, sem os símbolos da cinta.

    ```JavaScript
   # You can get the following connection details from the Azure portal. You can find the details on the Connection string pane of your Azure Cosmos account.

   COSMODDB_USER = "<Azure Cosmos account's user name, usually the database account name>"
   COSMOSDB_PASSWORD = "<Azure Cosmos account password, this is one of the keys specified in your account>"
   COSMOSDB_DBNAME = "<Azure Cosmos database name>"
   COSMOSDB_HOST= "<Azure Cosmos Host name>"
   COSMOSDB_PORT=10255
    ```

6. Ligue-se à Cosmos DB utilizando a estrutura mongoose adicionando o seguinte código ao fim de index.js.
    ```JavaScript
   mongoose.connect("mongodb://"+process.env.COSMOSDB_HOST+":"+process.env.COSMOSDB_PORT+"/"+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb", {
      auth: {
        user: process.env.COSMODDB_USER,
        password: process.env.COSMOSDB_PASSWORD
      },
    useNewUrlParser: true,
    useUnifiedTopology: true,
    retryWrites: false
    })
    .then(() => console.log('Connection to CosmosDB successful'))
    .catch((err) => console.error(err));
    ```
    >[!Note]
    > Aqui, as variáveis de ambiente são carregadas com process.env.{variableName} utilizando o pacote npm «dotenv».

    Assim que estiver ligado ao Azure Cosmos DB, pode começar a configurar modelos de objetos no Mongoose.

## <a name="best-practices-for-using-mongoose-with-cosmos-db"></a>Melhores práticas para usar Mongoose com Cosmos DB

Para cada modelo que cria, a Mongoose cria uma nova coleção. Este artigo é melhor endereçado utilizando a [opção De Saída de Nível de Base de Dados,](set-throughput.md#set-throughput-on-a-database)que foi previamente discutida. Para utilizar uma única coleção, você precisa usar Mongoose [Discriminators](https://mongoosejs.com/docs/discriminators.html). Os discriminadores são um mecanismo de herança de esquemas. Permitem-lhe ter vários modelos com esquemas sobrepostos por cima da mesma coleção MongoDB subjacente.

Pode armazenar os vários modelos de dados na mesma coleção e, em seguida, utilizar uma cláusula de filtro no momento da consulta para obter apenas os dados necessários. Vamos percorrer cada uma das modelos.

### <a name="one-collection-per-object-model"></a>Uma coleção por modelo de objeto

Esta secção explora como conseguir isso com a API da Azure Cosmos DB para a MongoDB. Este método é a nossa abordagem recomendada, uma vez que permite controlar o custo e a capacidade. Como resultado, a quantidade de Unidades de Pedido na base de dados não depende do número de modelos de objetos. Este é o modelo de funcionamento padrão para Mongoose, por isso, pode estar familiarizado com isto.

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
            { givenName: "Buddy" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. Finalmente, vamos salvar o objeto para cosmos DB. Esta ação cria uma coleção nos bastidores.

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

1. Agora, entrando no portal Azure, nota-se duas coleções criadas na Cosmos DB.

   :::image type="content" source="./media/mongodb-mongoose/mongo-mutliple-collections.png" alt-text="Node.js tutorial - Screenshot do portal Azure, mostrando como criar uma base de dados no Data Explorer para uma conta DB Azure Cosmos, para utilização com o módulo Nó Mongoose":::

1. Finalmente, vamos ler os dados da Cosmos DB. Uma vez que estamos a utilizar o modelo operativo Mongoose predefinido, as leituras são idênticas a quaisquer outras leituras com o Mongoose.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Utilizar os discriminadores do Mongoose para armazenar dados numa única coleção

Neste método, utilizamos [os Discriminadores mongoose](https://mongoosejs.com/docs/discriminators.html) para ajudar a otimizar os custos de cada coleção. Os discriminadores permitem-lhe definir uma «Chave» diferenciadora, que lhe permite armazenar, diferenciar e filtrar modelos de objetos diferentes.

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
           { givenName: "Buddy" }
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

   :::image type="content" source="./media/mongodb-mongoose/mongo-collections-alldata.png" alt-text="Node.js tutorial - Screenshot do portal Azure, mostrando como criar uma base de dados no Data Explorer para uma conta DB Azure Cosmos, para utilização com o módulo Nó Mongoose":::

1. Além disso, repare que cada objeto tem outro atributo denominado ```__type```, que ajuda a diferenciar entre os dois modelos de objeto diferentes.

1. Por fim, vamos ler os dados armazenados no Azure Cosmos DB. O Mongoose encarrega-se de filtrar os dados com base no modelo. Por isso, não terá de fazer nada diferente durante a leitura dos dados. Basta especificar o seu modelo (neste caso, ```Family_common```) e o Mongoose encarrega-se da filtragem na «DiscriminatorKey».

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Como pode ver, é fácil trabalhar com os discriminadores do Mongoose. Portanto, se você tem uma aplicação que usa a estrutura mongoose, este tutorial é uma maneira de você colocar a sua aplicação em funcionamento usando a API de Azure Cosmos para MongoDB sem exigir muitas alterações.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para a MongoDB.
- Aprenda a [usar Robo 3T](mongodb-robomongo.md) com API da Azure Cosmos DB para a MongoDB.
- Explore [as amostras](mongodb-samples.md) do MongoDB com a API da Azure Cosmos para a MongoDB.

[dbleveltp]: ./media/mongodb-mongoose/db-level-throughput.png
