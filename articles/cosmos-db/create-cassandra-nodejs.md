---
title: 'Início Rápido: API para Cassandra com Node.js – Azure Cosmos DB'
description: Este guia de introdução mostra como utilizar a Cassandra API do Azure Cosmos DB para criar uma aplicação de perfil com o Node.js
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/10/2021
ms.custom: devx-track-js
ms.openlocfilehash: 126ece1327fa92c9b92c587922f1b8d9335d1a01
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559280"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>Quickstart: Construa uma app Cassandra com Node.js SDK e Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Neste quickstart, você cria uma conta Azure Cosmos DB Cassandra API, e usa uma app Cassandra Node.js clonada do GitHub para criar uma base de dados e um recipiente Cassandra. Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, valor-chave e bases de dados de gráficos com capacidades de distribuição global e escala horizontal.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Em alternativa, pode [experimentar gratuitamente a Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure, gratuitamente e compromissos.

Além disso, necessita:
* [Node.js](https://nodejs.org/dist/v0.10.29/x64/node-v0.10.29-x64.msi) versão v0.10.29 ou superior
* [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Antes de poder criar uma base de dados de documentos, tem de criar uma conta do Cassandra com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos clonar uma aplicação da Cassandra API do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma linha de comandos. Crie uma nova pasta com o nome `git-samples`. Em seguida, feche a linha de comandos.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal do Git, como git bash. Utilize o comando `cd` para mudar para a nova pasta para instalar a aplicação de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

1. Instale as dependências Node.js com a npm.

    ```bash
    npm install
    ```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em saber de que forma o código cria os recursos da base de dados, pode rever os fragmentos seguintes. Os fragmentos são todos retirados do ficheiro `uprofile.js` na pasta `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started`. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-string). 

* Os valores nome de utilizador e palavra-passe são definidos através da página da cadeia de ligação no portal do Azure. 

   ```javascript
    let authProvider = new cassandra.auth.PlainTextAuthProvider(
        config.username,
        config.password
    );
   ```

* O `client` é inicializado com informações de contactPoint. O contactPoint é obtido a partir do portal do Azure.

    ```javascript
    let client = new cassandra.Client({
        contactPoints: [`${config.contactPoint}:10350`],
        authProvider: authProvider,
        localDataCenter: config.localDataCenter,
        sslOptions: {
            secureProtocol: "TLSv1_2_method"
        },
    });
    ```

* O `client` liga à Cassandra API do Azure Cosmos DB.

    ```javascript
    client.connect();
    ```

* É criado um novo keyspace.

    ```javascript
    var query =
        `CREATE KEYSPACE IF NOT EXISTS ${config.keySpace} WITH replication = {'class': 'NetworkTopologyStrategy', 'datacenter' : '1' }`;
    await client.execute(query);
  }
    ```

* É criada uma nova tabela.

   ```javascript
    query =
        `CREATE TABLE IF NOT EXISTS ${config.keySpace}.user (user_id int PRIMARY KEY, user_name text, user_bcity text)`;
    await client.execute(query);
   },
   ```

* São introduzidas entidades de chave/valor.

    ```javascript
    const arr = [
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (1, 'AdrianaS', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (2, 'JiriK', 'Toronto')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (3, 'IvanH', 'Mumbai')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (4, 'IvanH', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (5, 'IvanaV', 'Belgaum')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (6, 'LiliyaB', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (7, 'JindrichH', 'Buenos Aires')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (8, 'AdrianaS', 'Seattle')`,
        `INSERT INTO  ${config.keySpace}.user (user_id, user_name , user_bcity) VALUES (9, 'JozefM', 'Seattle')`,
    ];
    for (const element of arr) {
        await client.execute(element);
    }
    ```

* Consulte para obter todas as chaves-valor.

    ```javascript
    query = `SELECT * FROM ${config.keySpace}.user`;
    const resultSelect = await client.execute(query);

    for (const row of resultSelect.rows) {
        console.log(
            "Obtained row: %d | %s | %s ",
            row.user_id,
            row.user_name,
            row.user_bcity
        );
    }
    ```  

* Consulte para obter uma chave-valor.

    ```javascript
    query = `SELECT * FROM ${config.keySpace}.user where user_id=1`;
    const resultSelectWhere = await client.execute(query);

    for (const row of resultSelectWhere.rows) {
        console.log(
            "Obtained row: %d | %s | %s ",
            row.user_id,
            row.user_name,
            row.user_bcity
        );
    }
    ```  

* Ligação próxima. 

    ```javascript
    client.shutdown();
    ```  

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação. A cadeia de ligação permite que a aplicação comunique com a base de dados alojada.

1. Na sua conta DB Azure Cosmos no [portal Azure,](https://portal.azure.com/)selecione **Connection String**. 

1. Utilize o :::image type="icon" source="./media/create-cassandra-nodejs/copy.png"::: botão no lado direito do ecrã para copiar o valor superior, o PONTO DE CONTACTO.

    :::image type="content" source="./media/create-cassandra-nodejs/keys.png" alt-text="Ver e copiar o PONTO DE CONTACTO, o NOME DE UTILIZADOR e a PALAVRA-PASSE do portal do Azure, página da cadeia de ligação":::

1. Abra o ficheiro `config.js`. 

1. Cole o valor PONTO DE CONTACTO do portal sobre a `'CONTACT-POINT` linha 9.

    A linha 9 deve agora parecer-se com 

    `contactPoint: "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com",`

1. Copie o valor do NOME DE UTILIZADOR no portal e cole-o em `<FillMEIN>` na linha 2.

    A linha 2 deve ter agora um aspeto semelhante a 

    `username: 'cosmos-db-quickstart',`

1. Copie o valor da PALAVRA-PASSE no portal e cole-o em `USERNAME` na linha 8.

    A linha 8 deve ter agora um aspeto semelhante a

    `password: '2Ggkr662ifxz2Mg==',`

1. Substitua a REGIÃO pela região Azure em que criou este recurso.

1. Guarde o ficheiro `config.js`.


## <a name="run-the-nodejs-app"></a>Execute a aplicação Node.js

1. Na janela do terminal, certifique-se de que está no diretório de amostras que clonou anteriormente:

    ```bash
    cd azure-cosmos-db-cassandra-nodejs-getting-started
    ```

1. Execute a sua aplicação de nó:

    ```bash
    npm start
    ```

4. Verifique os resultados como esperado na linha de comandos.

    :::image type="content" source="./media/create-cassandra-nodejs/output.png" alt-text="Ver e verificar a saída":::

    Prima CTRL+C para parar a execução do programa e feche a janela da consola. 

5. No portal do Azure, abra o **Data Explorer** para consultar, modificar e trabalhar com estes dados novos. 

    :::image type="content" source="./media/create-cassandra-nodejs/data-explorer.png" alt-text="Ver os dados no Data Explorer"::: 

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar uma conta DB Azure Cosmos com a Cassandra API, e executou uma app Cassandra Node.js que cria uma base de dados e um recipiente Cassandra. Pode agora importar dados adicionais na sua conta DB Azure Cosmos. 

> [!div class="nextstepaction"]
> [Importar dados do Cassandra para o Azure Cosmos DB](cassandra-import-data.md)