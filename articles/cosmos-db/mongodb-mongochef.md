---
title: Ligar à API do Azure Cosmos DB para MongoDB a utilizar o Studio 3T
titleSuffix: Azure Cosmos DB
description: Saiba como ligar ao Cosmos DB com o Studio 3T e a API do Azure Cosmos DB do MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: 9b0a6393d2372c831fdc964dee18acbccfd39a77
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449381"
---
# <a name="connect-to-cosmos-account-using-studio-3t"></a>Ligar à conta do Cosmos utilizar o Studio 3T

Para ligar a uma conta do Cosmos com a API do Azure Cosmos DB do MongoDB, faça o seguinte:

* Transferir e instalar [Studio 3T](https://studio3t.com/)
* Ter seu Cosmos DB [cadeia de ligação](connect-mongodb-account.md) informações

## <a name="create-the-connection-in-studio-3t"></a>Criar a ligação no Studio 3T
Para adicionar a sua conta do Cosmos para o Gestor de ligações do Studio 3T, execute os seguintes passos:

1. Obter as informações de ligação para a sua conta do Cosmos configurada com a API do Azure Cosmos DB para o MongoDB com as instruções no [ligar uma aplicação MongoDB ao Azure Cosmos DB](connect-mongodb-account.md) artigo.

    ![Captura de ecrã da página de cadeia de ligação](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Clique em **Connect** para abrir o Gerenciador de conexões, em seguida, clique em **nova ligação**

    ![Captura de ecrã do Gerenciador de conexões Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. Na **nova ligação** janela, no **servidor** separador, introduza o anfitrião (FQDN) da conta do Azure Cosmos DB e a porta.

    ![Captura de ecrã do Guia do servidor do Gerenciador de conexão do Studio 3T](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Na **nova ligação** janela, no **autenticação** separador, selecione o modo de autenticação **Basic (MONGODB CR ou SCARM-SHA-1)** e introduza o nome de utilizador e palavra-passe.  Aceite o banco de dados de autenticação predefinido (administrador) ou fornecer seu próprio valor.

    ![Captura de ecrã do separador de autenticação Studio 3T ligação manager](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. No **nova ligação** janela, no **SSL** separador, verifique o **utilizar o protocolo SSL para ligar** caixa de verificação e o **aceitar o certificado SSL autoassinado do servidor**  botão de opção.

    ![Captura de ecrã do separador SSL Studio 3T ligação manager](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Clique nas **Testar ligação** botão para validar as informações de ligação, clique em **OK** para retornar à janela nova ligação e, em seguida, clique em **guardar**.

    ![Janela de ligação de teste de captura de ecrã do Studio 3T](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Utilizar Studio 3T para criar uma base de dados, uma coleção e documentos
Para criar uma base de dados, uma coleção e documentos, utilizar o Studio 3T, execute os seguintes passos:

1. Na **Gerenciador de conexões**, realce a ligação e clique em **Connect**.

    ![Captura de ecrã do Gerenciador de conexões Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Com o botão direito no anfitrião e escolha **Adicionar base de dados**.  Forneça um nome de base de dados e clique em **OK**.

    ![Captura de ecrã da opção Studio 3T Adicionar base de dados](./media/mongodb-mongochef/AddDatabase1.png)
3. A base de dados com o botão direito e escolher **adicionar coleção**.  Forneça um nome de coleção e clique em **criar**.

    ![Captura de ecrã da opção Studio 3T adicionar coleção](./media/mongodb-mongochef/AddCollection.png)
4. Clique nas **recolha** menu item, em seguida, clique em **Adicionar documento**.

    ![Captura de ecrã do item de menu do Studio 3T Adicionar documento](./media/mongodb-mongochef/AddDocument1.png)
5. Na caixa de diálogo Adicionar documento, cole o seguinte e, em seguida, clique em **Adicionar documento**.

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
7. Execute uma consulta de exemplo. Por exemplo, procure famílias com o nome da última 'Andersen' e devolver os pais e campos de estado.

    ![Resultados da consulta de captura de ecrã do Mongo Chef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [utilizar Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para o MongoDB.
- Explorar o MongoDB [amostras](mongodb-samples.md) com a API do Azure Cosmos DB para o MongoDB.
