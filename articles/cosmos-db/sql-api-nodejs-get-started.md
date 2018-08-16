---
title: Tutorial do Node.js para a API SQL do Azure Cosmos DB | Microsoft Docs
description: Um tutorial do Node.js que cria um Cosmos DB com a API SQL.
keywords: tutorial de node.js, base de dados de node
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/14/2017
ms.author: sngun
ms.openlocfilehash: 8eba0f70141fe28a478e842523023a22c2db50fe
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628754"
---
# <a name="nodejs-tutorial-use-the-sql-api-in-azure-cosmos-db-to-create-a-nodejs-console-application"></a>Tutorial do Node.js: Utilizar a API SQL no Azure Cosmos DB para criar uma aplicação de consola do Node.js

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js para MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Node.js - Pré-visualização v2.0](sql-api-nodejs-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)


Bem-vindo ao tutorial de Node.js para o SDK Node.js do Cosmos DB! Depois de seguir este tutorial, terá de uma aplicação de consola que cria e consulta recursos do Cosmos DB.

Iremos abranger:

* Criar e ligar a uma conta do Azure Cosmos DB
* Configurar a sua aplicação
* Criar uma base de dados Node
* Criação de uma coleção
* Criação de documentos JSON
* Consulta da coleção
* Substituir um documento
* Eliminar um documento
* Eliminar uma base de dados Node

Não tem tempo? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). Consulte o artigo [Obter a solução completa](#GetSolution) para instruções rápidas.

Quando tiver concluído o tutorial Node.js, utilize os botões de voto na parte superior e inferior desta página para submeter os seus comentários. Se preferir que entremos diretamente em contacto consigo, não hesite em indicar o seu endereço de e-mail nos seus comentários.

Agora comecemos!

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Pré-requisitos para o tutorial Node.js

Certifique-se de que tem o seguinte:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Versão [Node.js](https://nodejs.org/) v0.10.29 ou superior.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Passo 1: Criar uma conta do Azure Cosmos DB

Vamos criar uma conta do Azure Cosmos DB. Se já tiver uma conta que pretende utilizar, pode avançar diretamente para [Configurar a aplicação Node.js](#SetupNode). Se estiver a utilizar o Emulador da BD do Cosmos do Azure, siga os passos em [Emulador do Azure Cosmos DB](local-emulator.md) para configurar o emulador e avance para [Configurar a aplicação Node.js](#SetupNode).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Passo 2: Configurar a aplicação Node.js

1. Abra o seu terminal favorito.
2. Localize a pasta ou o diretório onde pretende guardar a sua aplicação Node.js.
3. Crie dois ficheiros JavaScript vazios com os seguintes comandos:
   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```
   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```
4. Instalar o módulo documentdb através do npm. Utilize o seguinte comando:
   * ```npm install documentdb --save```

Ótimo! Agora que concluiu a configuração, comecemos a escrever certos códigos.

## <a id="Config"></a>Passo 3: Configurar as configurações da sua aplicação

Abra ```config.js``` no seu editor de texto favorito.

Em seguida, copie e cole o fragmento de código abaixo e defina as propriedades ```config.endpoint``` e ```config.primaryKey``` para o uri e chave primária do ponto final do seu Azure Cosmos DB. Pode encontrar ambas as configurações no [portal do Azure](https://portal.azure.com).

![Tutorial Node.js - Captura de ecrã do portal do Azure que mostra uma conta do Azure Cosmos DB com o ACTIVE hub realçado, o botão CHAVES realçado no painel de conta do Azure Cosmos DB e os valores URI, CHAVE PRIMÁRIA e CHAVE SECUNDÁRIA realçados no painel Chaves - Base de dados Node][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

Copie e cole o ```database id```, ```collection id```, e ```JSON documents``` para seu objeto ```config``` por baixo do local onde configurou o seu ```config.endpoint``` e ```config.primaryKey``` Propriedades. Se já tiver dados que pretende armazenar na sua base de dados, pode utilizar a [ferramenta de Migração de Dados](import-data.md) do Azure Cosmos DB em vez de adicionar as definições do documento.

    config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };

As definições da base de dados, da coleção e do documento irão funcionar como o seu Azure Cosmos DB ```database id```, ```collection id```, e dados dos documentos.

Por fim, exporte o seu objeto ```config``` para poder referenciá-lo no ficheiro ```app.js```.

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

## <a id="Connect"></a>Passo 4: Ligar a uma conta do Azure Cosmos DB

Abra o seu ficheiro ```app.js``` vazio no editor de texto. Copie e cole o código abaixo para importar o módulo ```documentdb``` e o módulo ```config``` que criou recentemente.

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    const uriFactory = require('documentdb').UriFactory;
    var config = require("./config");

Copie e cole o código a utilizar guardado anteriormente ```config.endpoint``` e ```config.primaryKey``` para criar um novo DocumentClient.

    var config = require("./config");

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

Agora que tem o código para inicializar o cliente do Azure Cosmos DB, vamos ver como utilizar os recursos do Azure Cosmos DB.

## <a name="step-5-create-a-node-database"></a>Passo 5: Criar uma base de dados Node

Copie e cole o código abaixo para definir o estado HTTP para Não Encontrado, o ID da base de dados e o ID da coleção. Estes IDs vão permitir ao cliente do Azure Cosmos DB encontrar a base de dados e coleção corretas.

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseId = config.database.id;
    var collectionId = config.collection.id;

Pode criar uma [base de dados](sql-api-resources.md#databases) utilizando a função [createDatabase](/javascript/api/documentdb/documentclient) da classe **DocumentClient**. Uma base de dados é o contentor lógico do armazenamento de documentos particionados em coleções.

Copie e cole a função **getDatabase** para criar a sua nova base de dados no ficheiro app.js com ```databaseId``` especificado no objeto ```config```. A função irá verificar se já existe uma base de dados com o mesmo id ```FamilyRegistry```. Se existir, iremos devolver essa base de dados em vez de criar uma nova.

    // ADD THIS PART TO YOUR CODE
    function getDatabase() {
        console.log(`Getting database:\n${databaseId}\n`);
        let databaseUrl = uriFactory.createDatabaseUri(databaseId);
        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase({ id: databaseId }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Copie e cole o código por baixo do local onde o configurou a função **getDatabase** para adicionar a função de programa auxiliar **sair** que irá imprimir a mensagem de saída e a chamada para a função **getDatabase**.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    };

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: ```node app.js```

Parabéns! Criou uma base de dados do Azure Cosmos DB com êxito.

## <a id="CreateColl"></a>Passo 6: Criar uma coleção

> [!WARNING]
> O **createCollection** irá criar uma nova coleção, o que irá causar repercussões sobre os preços. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).

Pode criar uma [coleção](sql-api-resources.md#collections) utilizando a função [createCollection](/javascript/api/documentdb/documentclient) da classe **DocumentClient**. Uma coleção é um contentor de documentos JSON e a lógica da aplicação associada JavaScript.

Copie e cole a função **getCollection** por baixo da função **getDatabase** no ficheiro app.js para criar a sua nova coleção com ```collectionId``` especificado no objeto ```config```. Mais uma vez, iremos certificar-nos de que não existe nenhuma coleção com o mesmo id ```FamilyCollection```. Se existir, iremos devolver essa coleção em vez de criar uma nova.

                } else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${collectionId}\n`);
        let collectionUrl = uriFactory.createDocumentCollectionUri(databaseId, collectionId);
        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        let databaseUrl = uriFactory.createDatabaseUri(databaseId);
                        client.createCollection(databaseUrl, { id: collectionId }, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Copie e cole o código por baixo da chamada para **getDatabase** para executar a função **getCollection**.

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: ```node app.js```

Parabéns! Criou uma coleção do Azure Cosmos DB com êxito.

## <a id="CreateDoc"></a>Passo 7: Criar um documento

Pode criar um [documento](sql-api-resources.md#documents) utilizando a função [createDocument](/javascript/api/documentdb/documentclient) da classe **DocumentClient**. Os documentos são conteúdos (arbitrários) JSON definidos pelo utilizador. Pode agora inserir um documento no Azure Cosmos DB.

Copie e cole a função **getFamilyDocument** por baixo da função **getCollection** para criar documentos que contêm os dados JSON guardados no objeto ```config```. Mais uma vez, iremos certificar-nos de que não existe nenhum documento com o mesmo ID.

                } else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function getFamilyDocument(document) {
        console.log(`Getting document:\n${document.id}\n`);
        let documentUrl = uriFactory.createDocumentUri(databaseId, collectionId, document.id);
        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        let collectionUrl = uriFactory.createDocumentCollectionUri(databaseId, collectionId);
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Copie e cole o código por baixo da chamada para **getCollection** para executar a função **getFamilyDocument**.

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: ```node app.js```

Parabéns! Criou um documento do Azure Cosmos DB com êxito.

![Tutorial Node.js - Diagrama que ilustra a relação hierárquica entre a conta, a base de dados, a coleção e os documentos - base de dados Node](./media/sql-api-nodejs-get-started/node-js-tutorial-cosmos-db-account.png)

## <a id="Query"></a>Passo 8: Consultar recursos do Azure Cosmos DB
O Azure Cosmos DB suporta [consultas extensas](sql-api-sql-query.md) de documentos JSON armazenados em cada coleção. O seguinte código de exemplo mostra uma consulta que pode executar nos documentos da sua coleção.

Copie e cole a função **queryCollection** por baixo da função **getFamilyDocument** no ficheiro app.js. O Azure Cosmos DB suporta consultas de tipo SQL, conforme mostrado abaixo. Para obter mais informações sobre a criação de consultas complexas, consulte o artigo o [Query Playground](https://www.documentdb.com/sql/demo) e a [documentação sobre consultas](sql-api-sql-query.md).

                } else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${collectionId}`);
        let collectionUrl = uriFactory.createDocumentCollectionUri(databaseId, collectionId);
        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };

O seguinte diagrama ilustra como a sintaxe de consulta SQL do Azure Cosmos DB é chamada na coleção que criou.

![Tutorial Node.js - Diagrama que ilustra o âmbito e o significado da consulta - base de dados Node](./media/sql-api-nodejs-get-started/node-js-tutorial-collection-documents.png)

A palavra-chave [FROM](sql-api-sql-query.md#FromClause) é opcional na consulta porque as consultas do Azure Cosmos DB já estão confinadas a uma única coleção. Por conseguinte, "FROM Families f" pode ser trocada por "FROM root r" ou qualquer outra variável de nome escolher. Por predefinição, o Azure Cosmos DB irá inferir essas Famílias, raiz ou o nome da variável que escolheu, referenciar a atual coleção.

Copie e cole o código por baixo da chamada para **getFamilyDocument** para executar a função **queryCollection**.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: ```node app.js```

Parabéns! Consultou documentos do Azure Cosmos DB com êxito.

## <a id="ReplaceDocument"></a>Passo 9: Substituir um documento
O Azure Cosmos DB suporta a substituição de documentos JSON.

Copie e cole a função **replaceFamilyDocument** por baixo da função **queryCollection** no ficheiro app.js.

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        console.log(`Replacing document:\n${document.id}\n`);
        let documentUrl = uriFactory.createDocumentUri(databaseId, collectionId, document.id);
        document.children[0].grade = 6;
        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copie e cole o código por baixo da chamada para **queryCollection** para executar a função **replaceDocument**. Além disso, adicione o código para chamar novamente **queryCollection** para confirmar que o documento foi alterado com êxito.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: ```node app.js```

Parabéns! Substituiu documentos do Azure Cosmos DB com êxito.

## <a id="DeleteDocument"></a>Passo 10: Eliminar um documento

O Azure Cosmos DB suporta a eliminação de documentos JSON.

Copie e cole a função **deleteFamilyDocument** por baixo da função **replaceFamilyDocument**.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        console.log(`Deleting document:\n${document.id}\n`);
        let documentUrl = uriFactory.createDocumentUri(databaseId, collectionId, document.id);
        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copie e cole o código por baixo da chamada da segunda **queryCollection** para executar a função **deleteDocument**.

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: ```node app.js```

Parabéns! Eliminou documentos do Azure Cosmos DB com êxito.

## <a id="DeleteDatabase"></a>Passo 11: Eliminar a base de dados Node

Eliminar a base de dados criada irá remover a base de dados e todos os recursos subordinados (coleções, documentos, etc.).

Copie e cole afunção **cleanup** baixo da função **deleteFamilyDocument** para remover a base de dados e todos os recursos subordinados.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${databaseId}`);
        let databaseUrl = uriFactory.createDatabaseUri(databaseId);
        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    };

Copie e cole o código por baixo da chamada para **deleteFamilyDocument** para executar a função **limpeza**.

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

## <a id="Run"></a>Passo 12: Executar a sua aplicação Node.js em conjunto!

No seu conjunto, a sequência para chamar as suas funções deve ter o seguinte aspeto:

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: ```node app.js```

Deverá ver o resultado da sua aplicação Introdução. A saída deve corresponder ao texto de exemplo abaixo.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

Parabéns! Criou e completou o tutorial Node.js e tem a sua primeira aplicação de consola do Azure Cosmos DB!

## <a id="GetSolution"></a>Obter a solução completa do tutorial Node.js

Se não tiver tempo de completar os passos deste tutorial, ou apenas pretender transferir o código, pode obtê-lo a partir do [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started).

Para executar a solução GetStarted que contém todos os exemplos deste artigo, deverá ter o seguinte:

* [Conta do Azure Cosmos DB][create-account].
* A solução [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) está disponível no GitHub.

Instalar o módulo **documentdb** através do npm. Utilize o seguinte comando:

* ```npm install documentdb --save```

Em seguida, no ficheiro ```config.js```, atualize os valores config.endpoint e config.primaryKey, conforme descrito em [Passo 3: Definir as configurações da sua aplicação](#Config). 

No seu terminal, localize o seu ficheiro ```app.js``` e execute o comando: ```node app.js```.

Já está, basta criar e está pronto! 

## <a name="next-steps"></a>Passos seguintes
* Pretende um exemplo de Node.js mais complexo? Consulte [Build a Node.js web application using Azure Cosmos DB](sql-api-nodejs-application.md) (Criar uma aplicação Web Node.js com o Azure Cosmos DB).
* Saiba como [monitorizar uma conta do Azure Cosmos DB](monitor-accounts.md).
* Execute consultas no nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
