---
title: Conectar um aplicativo MongoDB ao Azure Cosmos DB
description: Saiba como conectar um aplicativo do MongoDB a Azure Cosmos DB obtendo a cadeia de conexão do portal do Azure
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: 0f2cd1b7228f2cc9cadb84232222f658a512a81f
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872167"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Conectar um aplicativo MongoDB ao Azure Cosmos DB
Saiba como ligar a aplicação MongoDB a um Azure Cosmos DB com uma cadeia de ligação do MongoDB. Em seguida, pode utilizar uma base de dados Azure Cosmos como arquivo de dados para a aplicação MongoDB. 

Este tutorial fornece duas formas de recuperar informações da cadeia de ligação:

- [O método de início rápido](#QuickstartConnection), para uso com os drivers .net, Node. js, Shell do MongoDB, Java e Python
- [O método de cadeia de conexão personalizada](#GetCustomConnection), para uso com outros drivers

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se você não tiver uma conta do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) agora mesmo. 
- Uma conta do cosmos. Para obter instruções, consulte [criar um aplicativo Web usando a API do Azure Cosmos DB para MongoDB e SDK do .net](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Obter a cadeia de conexão do MongoDB usando o início rápido
1. Em um navegador da Internet, entre no [portal do Azure](https://portal.azure.com).
2. Na folha **Azure Cosmos DB** , selecione a API. 
3. No painel esquerdo da folha da conta, clique em **início rápido**. 
4. Escolha sua plataforma ( **.net**, **node. js**, **shell do MongoDB**, **Java**, **Python**). Se você não vir o driver ou a ferramenta listada, não se preocupe, pois documentamos continuamente mais trechos de código de conexão. Faça um comentário abaixo sobre o que você gostaria de ver. Para saber como criar sua própria conexão, leia [obter as informações da cadeia de conexão da conta](#GetCustomConnection).
5. Copie e cole o trecho de código em seu aplicativo MongoDB.

    ![Folha início rápido](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a>Obter a cadeia de conexão do MongoDB para personalizar
1. Em um navegador da Internet, entre no [portal do Azure](https://portal.azure.com).
2. Na folha **Azure Cosmos DB** , selecione a API. 
3. No painel esquerdo da folha da conta, clique em **cadeia de conexão**. 
4. A folha **cadeia de conexão** é aberta. Ele tem todas as informações necessárias para se conectar à conta usando um driver para MongoDB, incluindo uma cadeia de conexão preconstruída.

    ![Painel Cadeia de Ligação](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Requisitos de cadeia de conexão
> [!Important]
> O Azure Cosmos DB tem padrões e requisitos de segurança estritos. Azure Cosmos DB contas exigem autenticação e comunicação segura via *SSL*. 
>
>

O Azure Cosmos DB dá suporte ao formato de URI de cadeia de conexão padrão do MongoDB, com alguns requisitos específicos: Azure Cosmos DB contas exigem autenticação e comunicação segura via SSL. Portanto, o formato da cadeia de conexão é:

    mongodb://username:password@host:port/[database]?ssl=true

Os valores dessa cadeia de caracteres estão disponíveis na folha **cadeia de conexão** mostrada anteriormente:

* Nome de usuário (obrigatório): Cosmos nome da conta.
* Senha (obrigatório): senha da conta do cosmos.
* Host (obrigatório): FQDN da conta Cosmos.
* Porta (obrigatório): 10255.
* Banco de dados (opcional): o banco de dados usado pela conexão. Se nenhum banco de dados for fornecido, o banco de dados padrão será "Test".
* SSL = true (obrigatório)

Por exemplo, considere a conta mostrada na folha **cadeia de conexão** . Uma cadeia de conexão válida é:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Passos seguintes

- Saiba como [usar o Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para MongoDB.
- Saiba como [usar o Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para MongoDB.
- Explore os [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.
