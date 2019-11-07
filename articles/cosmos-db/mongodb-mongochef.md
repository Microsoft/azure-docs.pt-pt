---
title: Conectar-se à API do Azure Cosmos DB para MongoDB usando o Studio 3T
description: Saiba como se conectar à API de um Azure Cosmos DB para MongoDB usando o Studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/01/2019
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: c9be9893d28439fdcff9f33f0303a816bd94c7b8
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584427"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Conectar-se a uma conta do Azure Cosmos usando o Studio 3T

Para se conectar à API de um Azure Cosmos DB para MongoDB usando o Studio 3T, você deve:

* Baixe e instale o [Studio 3T](https://studio3t.com/).
* Tenha as informações da cadeia de [conexão](connect-mongodb-account.md) da sua conta do Azure Cosmos.

> [!NOTE]
> Atualmente, Robo 3T v 1.2 e versões inferiores têm suporte com a API de Cosmos DB para MongoDB.

## <a name="create-the-connection-in-studio-3t"></a>Criar a conexão no Studio 3T

Para adicionar sua conta do Azure Cosmos ao Gerenciador de conexões do 3T Studio, use as seguintes etapas:

1. Recupere as informações de conexão para a conta da API para MongoDB do Azure Cosmos DB usando as instruções no artigo [conectar um aplicativo do MongoDB ao Azure Cosmos DB](connect-mongodb-account.md) .

    ![Captura de tela da página cadeia de conexão](./media/mongodb-mongochef/ConnectionStringBlade.png)

2. Clique em **conectar** para abrir o Gerenciador de conexões e clique em **nova conexão**

    ![Captura de tela do Gerenciador de conexões do Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. Na janela **nova conexão** , na guia **servidor** , insira o host (FQDN) da conta do Azure Cosmos e da porta.

    ![Captura de tela da guia servidor do Gerenciador de conexões do Studio 3T](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Na janela **nova conexão** , na guia **autenticação** , escolha modo de autenticação **básico (MONGODB-CR ou SCARM-SHA-1)** e insira o nome de usuário e a senha.  Aceite o banco de BD de autenticação padrão (admin) ou forneça seu próprio valor.

    ![Captura de tela da guia de autenticação do Gerenciador de conexões do Studio 3T](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Na janela **nova conexão** , na guia **SSL** , marque a caixa de seleção **usar protocolo SSL para se conectar** e o botão de opção **aceitar certificados SSL autoassinados do servidor** .

    ![Captura de tela da guia SSL do Gerenciador de conexões do Studio 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Clique no botão **testar conexão** para validar as informações de conexão, clique em **OK** para retornar à janela nova conexão e, em seguida, clique em **salvar**.

    ![Captura de tela da janela de conexão de teste do Studio 3T](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Usar o Studio 3T para criar um banco de dados, uma coleção e documentos
Para criar um banco de dados, uma coleção e documentos usando o Studio 3T, execute as seguintes etapas:

1. No **Gerenciador de conexões**, realce a conexão e clique em **conectar**.

    ![Captura de tela do Gerenciador de conexões do Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Clique com o botão direito do mouse no host e escolha **Adicionar Banco de dados**.  Forneça um nome de banco de dados e clique em **OK**.

    ![Captura de tela da opção de adição de banco de dados do Studio 3T](./media/mongodb-mongochef/AddDatabase1.png)
3. Clique com o botão direito do mouse no banco de dados e escolha **Adicionar coleção**.  Forneça um nome de coleção e clique em **criar**.

    ![Captura de tela da opção de adição de coleção do Studio 3T](./media/mongodb-mongochef/AddCollection.png)
4. Clique no item de menu **coleção** e, em seguida, clique em **Adicionar documento**.

    ![Captura de tela do item de menu Adicionar documento do Studio 3T](./media/mongodb-mongochef/AddDocument1.png)
5. Na caixa de diálogo Adicionar documento, Cole o seguinte e, em seguida, clique em **Adicionar documento**.

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
7. Executar uma consulta de exemplo. Por exemplo, pesquise famílias com o sobrenome ' Andersen ' e retorne os campos de pais e estado.

    ![Captura de tela dos resultados da consulta do chefe Mongo](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [usar o Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para MongoDB.
- Explore os [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.
