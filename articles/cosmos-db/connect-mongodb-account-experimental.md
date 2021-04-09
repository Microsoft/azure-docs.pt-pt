---
title: Ligar uma aplicação MongoDB ao Azure Cosmos DB
description: Saiba como ligar uma aplicação MongoDB à Azure Cosmos DB obtendo a cadeia de conexão do portal Azure
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 02/08/2021
ms.reviewer: sngun
robots: noindex
ms.openlocfilehash: bd244b0bb0aa5c5b2377b5a5675466645da3256c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99982111"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Ligar uma aplicação MongoDB ao Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Saiba como ligar a aplicação MongoDB a um Azure Cosmos DB com uma cadeia de ligação do MongoDB. Em seguida, pode utilizar uma base de dados Azure Cosmos como arquivo de dados para a aplicação MongoDB. Além do tutorial abaixo, você pode explorar [amostras](mongodb-samples.md) de MongoDB com API da Azure Cosmos DB para MongoDB.

Este tutorial fornece duas formas de recuperar informações da cadeia de ligação:

- [O método quickstart](#get-the-mongodb-connection-string-by-using-the-quick-start), para ser utilizado com condutores .NET, Node.js, MongoDB Shell, Java e Python
- [O método de cadeia de conexão personalizada,](#get-the-mongodb-connection-string-to-customize)para ser utilizado com outros controladores

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se não tiver uma conta Azure, crie agora uma [conta Azure gratuita.](https://azure.microsoft.com/free/)
- Uma conta cosmos. Para obter instruções, consulte [Construir uma aplicação web utilizando a API da Azure Cosmos DB para a MongoDB e .NET SDK](create-mongodb-dotnet.md).

## <a name="get-the-mongodb-connection-string-by-using-the-quick-start"></a>Obtenha a cadeia de ligação MongoDB usando o arranque rápido

1. Num navegador de Internet, inscreva-se no [portal Azure.](https://portal.azure.com)
2. Na lâmina **DB Azure Cosmos,** selecione a API.
3. No painel esquerdo da lâmina da conta, clique em **Arranque Rápido**.
4. Escolha a sua plataforma **(.NET,** **Node.js,** **MongoDB Shell,** **Java,** **Python).** Se não vir o seu controlador ou ferramenta listado, não se preocupe- documentamos continuamente mais cortes de código de ligação. Por favor, comente abaixo o que gostaria de ver. Para aprender a criar a sua própria ligação, leia [Obtenha a informação do string de ligação da conta](#get-the-mongodb-connection-string-to-customize).
5. Copie e cole o código na sua aplicação MongoDB.

    :::image type="content" source="./media/connect-mongodb-account/QuickStartBlade.png" alt-text="Lâmina de arranque rápido":::

## <a name="get-the-mongodb-connection-string-to-customize"></a>Obtenha a cadeia de conexão MongoDB para personalizar

1. Num navegador de Internet, inscreva-se no [portal Azure.](https://portal.azure.com)
2. Na lâmina **DB Azure Cosmos,** selecione a API.
3. No painel esquerdo da lâmina da conta, clique em **Connection String**.
4. A **lâmina de ligação** abre-se. Tem todas as informações necessárias para ligar à conta utilizando um controlador para o MongoDB, incluindo uma cadeia de ligação pré-reconstruída.

   :::image type="content" source="./media/connect-mongodb-account/ConnectionStringBlade.png" alt-text="Painel Cadeia de Ligação" lightbox= "./media/connect-mongodb-account/ConnectionStringBlade.png" :::

## <a name="connection-string-requirements"></a>Requisitos de cadeia de ligação

> [!Important]
> A Azure Cosmos DB tem rigorosos requisitos e padrões de segurança. As contas DB da Azure Cosmos requerem autenticação e comunicação segura via *TLS*.

A Azure Cosmos DB suporta o formato URI de cadeia de ligação padrão MongoDB, com alguns requisitos específicos: As contas DB do Azure Cosmos requerem autenticação e comunicação segura via TLS. Assim, o formato de cadeia de ligação é:

`mongodb://username:password@host:port/[database]?ssl=true`

Os valores desta cadeia estão disponíveis na lâmina **de corda de ligação** mostrada anteriormente:

* Nome de utilizador (obrigatório): Nome da conta Cosmos.
* Palavra-passe (requerida): Senha de conta cosmos.
* Anfitrião (obrigatório): FQDN da conta Cosmos.
* Porto (obrigatório): 10255.
* Base de dados (opcional): A base de dados que a ligação utiliza. Se não for fornecida nenhuma base de dados, a base de dados predefinida é "teste".
* ssl=verdadeiro (obrigatório)

Por exemplo, considere a conta mostrada na lâmina **de corda de ligação.** Uma cadeia de ligação válida é:

`mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true`

## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para a MongoDB.
- Aprenda a [usar Robo 3T](mongodb-robomongo.md) com API da Azure Cosmos DB para a MongoDB.
- Explore [as amostras](mongodb-samples.md) do MongoDB com a API da Azure Cosmos para a MongoDB.
