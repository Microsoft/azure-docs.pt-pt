---
title: Conectar-se à API do Azure Cosmos DB para MongoDB usando o Studio 3T
titleSuffix: Azure Cosmos DB
description: Saiba como se conectar ao Cosmos DB usando o Studio 3T e a API do Azure Cosmos DB para MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: 46cac6720bbef133d6b3f7a269cf97a81cddd0c5
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70062112"
---
# <a name="connect-to-cosmos-account-using-studio-3t"></a>Conectar-se à conta do cosmos usando o Studio 3T

Para se conectar a uma conta do cosmos usando a API do Azure Cosmos DB para MongoDB, você deve:

* Baixe e instale o [Studio 3T](https://studio3t.com/)
* Ter suas informações de [cadeia de conexão](connect-mongodb-account.md) Cosmos DB

> [!NOTE]
> Atualmente, o Robo 3T v 1.2 e inferior tem suporte com a API de Cosmos DB para MongoDB. 

## <a name="create-the-connection-in-studio-3t"></a>Criar a ligação no Studio 3T
Para adicionar sua conta do cosmos ao Gerenciador de conexões do 3T Studio, execute as seguintes etapas:

1. Recupere as informações de conexão para sua conta do cosmos configurada com a API do Azure Cosmos DB para MongoDB usando as instruções no artigo [conectar um aplicativo do MongoDB ao Azure Cosmos DB](connect-mongodb-account.md) .

    ![Captura de tela da página cadeia de conexão](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Clique em **Connect** para abrir o Gerenciador de conexões, em seguida, clique em **nova ligação**

    ![Captura de tela do Gerenciador de conexões do Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. Na **nova ligação** janela, no **servidor** separador, introduza o anfitrião (FQDN) da conta do Azure Cosmos DB e a porta.

    ![Captura de tela da guia servidor do Gerenciador de conexões do Studio 3T](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Na **nova ligação** janela, no **autenticação** separador, selecione o modo de autenticação **Basic (MONGODB CR ou SCARM-SHA-1)** e introduza o nome de utilizador e palavra-passe.  Aceite o banco de dados de autenticação predefinido (administrador) ou fornecer seu próprio valor.

    ![Captura de tela da guia de autenticação do Gerenciador de conexões do Studio 3T](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. No **nova ligação** janela, no **SSL** separador, verifique o **utilizar o protocolo SSL para ligar** caixa de verificação e o **aceitar o certificado SSL autoassinado do servidor**  botão de opção.

    ![Captura de tela da guia SSL do Gerenciador de conexões do Studio 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Clique nas **Testar ligação** botão para validar as informações de ligação, clique em **OK** para retornar à janela nova ligação e, em seguida, clique em **guardar**.

    ![Captura de tela da janela de conexão de teste do Studio 3T](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Utilizar Studio 3T para criar uma base de dados, uma coleção e documentos
Para criar uma base de dados, uma coleção e documentos, utilizar o Studio 3T, execute os seguintes passos:

1. Na **Gerenciador de conexões**, realce a ligação e clique em **Connect**.

    ![Captura de tela do Gerenciador de conexões do Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Com o botão direito no anfitrião e escolha **Adicionar base de dados**.  Forneça um nome de base de dados e clique em **OK**.

    ![Captura de tela da opção de adição de banco de dados do Studio 3T](./media/mongodb-mongochef/AddDatabase1.png)
3. A base de dados com o botão direito e escolher **adicionar coleção**.  Forneça um nome de coleção e clique em **criar**.

    ![Captura de tela da opção de adição de coleção do Studio 3T](./media/mongodb-mongochef/AddCollection.png)
4. Clique nas **recolha** menu item, em seguida, clique em **Adicionar documento**.

    ![Captura de tela do item de menu Adicionar documento do Studio 3T](./media/mongodb-mongochef/AddDocument1.png)
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

    ![Captura de tela dos resultados da consulta do chefe Mongo](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [usar o Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para MongoDB.
- Explore os [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.
