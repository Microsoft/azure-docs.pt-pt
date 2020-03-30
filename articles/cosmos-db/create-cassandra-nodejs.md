---
title: 'Início Rápido: API para Cassandra com Node.js – Azure Cosmos DB'
description: Este guia de introdução mostra como utilizar a Cassandra API do Azure Cosmos DB para criar uma aplicação de perfil com o Node.js
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: ffc2681e487a51ce630d9433d6ded86961b5276c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77210374"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>Quickstart: Construa uma app Cassandra com Node.js SDK e Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Nó.js](create-cassandra-nodejs.md)
> * [Pitão](create-cassandra-python.md)
>  

Neste arranque rápido, cria-se uma conta API da API da Azure Cosmos DB Cassandra, e usa-se uma aplicação Cassandra Node.js clonada do GitHub para criar uma base de dados e um contentor da Cassandra. Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, basede-chaves e bases de dados de gráficos com capacidades de distribuição global e escala horizontal.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]Em alternativa, pode [experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure, gratuitamente e compromissos.

Além disso, necessita:
* [Node.js](https://nodejs.org/dist/v0.10.29/x64/node-v0.10.29-x64.msi) versão v0.10.29 ou superior
* [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

Antes de poder criar uma base de dados de documentos, tem de criar uma conta do Cassandra com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos clonar uma aplicação Cassandra API do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

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

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em saber de que forma o código cria os recursos da base de dados, pode rever os fragmentos seguintes. Os fragmentos são todos retirados do ficheiro `uprofile.js` na pasta `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started`. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-string). 

* Os valores nome de utilizador e palavra-passe são definidos através da página da cadeia de ligação no portal do Azure. O `path\to\cert` fornece um caminho para um certificado X509. 

   ```javascript
   var ssl_option = {
        cert : fs.readFileSync("path\to\cert"),
        rejectUnauthorized : true,
        secureProtocol: 'TLSv1_2_method'
        };
   const authProviderLocalCassandra = new cassandra.auth.PlainTextAuthProvider(config.username, config.password);
   ```

* O `client` é inicializado com informações de contactPoint. O contactPoint é obtido a partir do portal do Azure.

    ```javascript
    const client = new cassandra.Client({contactPoints: [config.contactPoint], authProvider: authProviderLocalCassandra, sslOptions:ssl_option});
    ```

* O `client` liga à Cassandra API do Azure Cosmos DB.

    ```javascript
    client.connect(next);
    ```

* É criado um novo keyspace.

    ```javascript
    function createKeyspace(next) {
        var query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }";
        client.execute(query, next);
        console.log("created keyspace");    
  }
    ```

* É criada uma nova tabela.

   ```javascript
   function createTable(next) {
    var query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        client.execute(query, next);
        console.log("created table");
   },
   ```

* São introduzidas entidades de chave/valor.

    ```javascript
        function insert(next) {
            console.log("\insert");
            const arr = ['INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (1, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (2, \'JiriK\', \'Toronto\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (3, \'IvanH\', \'Mumbai\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (4, \'IvanH\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (5, \'IvanaV\', \'Belgaum\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (6, \'LiliyaB\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (7, \'JindrichH\', \'Buenos Aires\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (8, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (9, \'JozefM\', \'Seattle\')'];
            arr.forEach(element => {
            client.execute(element);
            });
            next();
        },
    ```

* Consulte para obter todas as chaves-valor.

    ```javascript
        function selectAll(next) {
            console.log("\Select ALL");
            var query = 'SELECT * FROM uprofile.user';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        },
    ```  
    
* Consulte para obter uma chave-valor.

    ```javascript
        function selectById(next) {
            console.log("\Getting by id");
            var query = 'SELECT * FROM uprofile.user where user_id=1';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        }
    ```  

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação. A cadeia de ligação permite que a aplicação comunique com a base de dados alojada.

1. Na sua conta Azure Cosmos DB no [portal Azure,](https://portal.azure.com/)selecione **Connection String**. 

    Utilize o ![botão Copiar](./media/create-cassandra-nodejs/copy.png) botão à direita do ecrã, para copiar o valor superior, o PONTO DE CONTACTO.

    ![Ver e copiar o PONTO DE CONTACTO, o NOME DE UTILIZADOR e a PALAVRA-PASSE do portal do Azure, página da cadeia de ligação](./media/create-cassandra-nodejs/keys.png)

2. Abra o ficheiro `config.js`. 

3. Cole o valor do PONTO DE CONTACTO do portal em `<FillMEIN>` na linha 4.

    A linha 4 deve ter agora um aspeto semelhante a 

    `config.contactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350"`

4. Copie o valor do NOME DE UTILIZADOR no portal e cole-o em `<FillMEIN>` na linha 2.

    A linha 2 deve ter agora um aspeto semelhante a 

    `config.username = 'cosmos-db-quickstart';`
    
5. Copie o valor da PALAVRA-PASSE no portal e cole-o em `<FillMEIN>` na linha 3.

    A linha 3 deve ter agora um aspeto semelhante a

    `config.password = '2Ggkr662ifxz2Mg==';`

6. Guarde o ficheiro `config.js`.
    
## <a name="use-the-x509-certificate"></a>Utilizar o certificado X509

1. Descarregue o certificado Baltimore [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt)CyberTrust Root localmente a partir de . Mude o nome do ficheiro através da extensão de ficheiro `.cer`.

   O certificado tem o número de série `02:00:00:b9` e a identificação digital SHA1 `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Abra `uprofile.js` e mude o `path\to\cert` para apontar para o novo certificado.

3. Guarde `uprofile.js`.

> [!NOTE]
> Se sentir um erro relacionado com um certificado nos passos posteriores e estiver a funcionar numa máquina do Windows, certifique-se de que seguiu o processo para converter corretamente um ficheiro .crt no formato Microsoft .cer abaixo.
> 
> Clique duas vezes no ficheiro .crt para o abrir no visor do certificado. 
>
> ![Ver e verificar a saída](./media/create-cassandra-nodejs/crtcer1.gif)
>
> Prima next on the Certificate Wizard. Selecione Base-64 codificada X.509 (. CER), então Seguinte.
>
> ![Ver e verificar a saída](./media/create-cassandra-nodejs/crtcer2.gif)
>
> Selecione Browse (para localizar um destino) e escreva num nome de ficheiro.
> Selecione Seguinte e depois termine.
>
> Agora deve ter um ficheiro .cer devidamente formatado. Certifique-se de `uprofile.js` que o caminho em pontos para este ficheiro.

## <a name="run-the-nodejs-app"></a>Execute a aplicação Node.js

1. Na janela do terminal git, certifique-se de que está no diretório de amostras que clonou anteriormente:

    ```bash
    cd azure-cosmos-db-cassandra-nodejs-getting-started
    ```

2. Corra `npm install` para instalar os módulos npm necessários.

3. Execute `node uprofile.js` para iniciar a aplicação de nó.

4. Verifique os resultados como esperado na linha de comandos.

    ![Ver e verificar a saída](./media/create-cassandra-nodejs/output.png)

    Prima CTRL+C para parar a execução do programa e fechar a janela da consola. 

5. No portal do Azure, abra o **Data Explorer** para consultar, modificar e trabalhar com estes dados novos. 

    ![Ver os dados no Data Explorer](./media/create-cassandra-nodejs/data-explorer.png) 

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma conta Azure Cosmos DB com a Cassandra API, e executou uma app Cassandra Node.js que cria uma base de dados e um contentor cassandra. Agora pode importar dados adicionais para a sua conta Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados do Cassandra para o Azure Cosmos DB](cassandra-import-data.md)