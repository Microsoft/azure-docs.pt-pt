---
title: Use o Studio 3T para ligar à API da Azure Cosmos DB para mongoDB
description: Aprenda a ligar-se a um API da Azure Cosmos DB para MongoDB usando o Studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 9b8c3a36dbdd3a14d0bd325c22421033a1765df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063687"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Ligue-se a uma conta Azure Cosmos usando o Studio 3T

Para ligar a uma API da Azure Cosmos DB para MongoDB usando o Studio 3T, você deve:

* Descarregue e instale o [Studio 3T.](https://studio3t.com/)
* Tenha a informação de cordas de [ligação](connect-mongodb-account.md) da sua conta Azure Cosmos.

> [!NOTE]
> Atualmente, robo 3T v1.2 e versões inferiores são suportados com API da Cosmos DB para MongoDB.

## <a name="create-the-connection-in-studio-3t"></a>Criar a ligação no Studio 3T

Para adicionar a sua conta Azure Cosmos ao gestor de ligação Studio 3T, use os seguintes passos:

1. Recupere as informações de ligação para a sua conta API da Azure Cosmos DB para a conta MongoDB utilizando as instruções na [aplicação Connect a MongoDB para o artigo da Azure Cosmos DB.](connect-mongodb-account.md)

    ![Screenshot da página de cadeia de ligação](./media/mongodb-mongochef/ConnectionStringBlade.png)

2. Clique **em Ligar** para abrir o Gestor de Ligação e, em seguida, clique em New **Connection**

    ![Screenshot do gestor de conexão Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. Na janela **New Connection,** no separador **Server,** introduza o HOST (FQDN) da conta Azure Cosmos e a PORT.

    ![Screenshot do separador de servidor de servidor de ligação Studio 3T](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Na janela **New Connection,** no separador **Autenticação,** escolha o Modo de Autenticação **Básico (MONGODB-CR ou SCARM-SHA-1)** e introduza o USERNAME e a PASSWORD.  Aceite a autenticação padrão db (administrador) ou forneça o seu próprio valor.

    ![Screenshot do separador de autenticação do gestor de ligação Studio 3T](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Na janela **New Connection,** no separador **SSL,** verifique o **protocolo Use SSL para ligar** a caixa de verificação e o botão de rádio **SSL auto-assinado** do servidor.

    ![Screenshot do separador SSL do gestor de ligação Studio 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Clique no botão **de ligação** de teste para validar as informações de ligação, clique em **OK** para voltar à janela New Connection e, em seguida, clique em **Guardar**.

    ![Screenshot da janela de ligação de teste Studio 3T](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Use o Studio 3T para criar uma base de dados, recolha e documentos
Para criar uma base de dados, recolha e documentos utilizando o Studio 3T, execute os seguintes passos:

1. No **Gestor de Ligação,** realce a ligação e clique em **Ligar**.

    ![Screenshot do gestor de conexão Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Clique no anfitrião e escolha **Adicionar Base de Dados**.  Forneça um nome de base de dados e clique EM **OK**.

    ![Screenshot da opção Studio 3T Add Database](./media/mongodb-mongochef/AddDatabase1.png)
3. Clique na base de dados e escolha **Adicionar Coleção**.  Forneça um nome de coleção e clique em **Criar**.

    ![Screenshot da opção Studio 3T Add Collection](./media/mongodb-mongochef/AddCollection.png)
4. Clique no item do menu **Coleção** e, em seguida, clique em **Adicionar Documento**.

    ![Screenshot do artigo de menu Studio 3T Add Document](./media/mongodb-mongochef/AddDocument1.png)
5. No diálogo 'Adicionar Documento', colar o seguinte e, em seguida, clicar em **Adicionar Documento**.

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Adicione outro documento, desta vez com o seguinte conteúdo:

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Execute uma consulta de amostra. Por exemplo, procure famílias com o apelido 'Andersen' e devolva os pais e os campos do Estado.

    ![Screenshot dos resultados da consulta do Mongo Chef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Passos seguintes

- Aprenda a [usar robo 3T](mongodb-robomongo.md) com API da Azure Cosmos DB para MongoDB.
- Explore [as amostras](mongodb-samples.md) de MongoDB com a API da Azure Cosmos DB para o MongoDB.
