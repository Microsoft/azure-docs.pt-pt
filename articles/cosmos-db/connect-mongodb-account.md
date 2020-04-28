---
title: Ligar uma aplicação MongoDB ao Azure Cosmos DB
description: Saiba como ligar uma aplicação MongoDB ao Azure Cosmos DB obtendo a cadeia de ligação do portal Azure
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/19/2020
ms.reviewer: sngun
ms.openlocfilehash: cba16d79b6506f9809a76f0128938a68afd15c92
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80617043"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Ligar uma aplicação MongoDB ao Azure Cosmos DB

Saiba como ligar a aplicação MongoDB a um Azure Cosmos DB com uma cadeia de ligação do MongoDB. Em seguida, pode utilizar uma base de dados Azure Cosmos como arquivo de dados para a aplicação MongoDB.

Este tutorial fornece duas formas de recuperar informações da cadeia de ligação:

- [O método de arranque rápido](#get-the-mongodb-connection-string-by-using-the-quick-start), para uso com .NET, Node.js, MongoDB Shell, Java e Python
- O método de cadeia de [ligação personalizado,](#get-the-mongodb-connection-string-to-customize)para utilização com outros condutores

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se não tiver uma conta Azure, crie uma [conta Azure gratuita](https://azure.microsoft.com/free/) agora.
- Uma conta cosmos. Para obter instruções, consulte Construir uma aplicação web utilizando a [API do Azure Cosmos DB para MongoDB e .NET SDK](create-mongodb-dotnet.md).

## <a name="get-the-mongodb-connection-string-by-using-the-quick-start"></a>Obtenha a cadeia de ligação MongoDB usando o arranque rápido

1. Num navegador de Internet, inscreva-se no [portal Azure.](https://portal.azure.com)
2. Na lâmina **Azure Cosmos DB,** selecione a API.
3. No painel esquerdo da lâmina da conta, clique em **arranque rápido**.
4. Escolha a sua plataforma (**.NET,** **Node.js,** **MongoDB Shell,** **Java,** **Python).** Se não vir o seu controlador ou ferramenta listado, não se preocupe- documentamos continuamente mais fragmentos de código de ligação. Por favor, comente abaixo o que gostaria de ver. Para aprender a criar a sua própria ligação, leia [as informações de cadeia de ligação da conta](#get-the-mongodb-connection-string-to-customize).
5. Copie e cole o código na sua aplicação MongoDB.

    ![Lâmina de arranque rápida](./media/connect-mongodb-account/QuickStartBlade.png)

## <a name="get-the-mongodb-connection-string-to-customize"></a>Obtenha a cadeia de ligação MongoDB para personalizar

1. Num navegador de Internet, inscreva-se no [portal Azure.](https://portal.azure.com)
2. Na lâmina **Azure Cosmos DB,** selecione a API.
3. No painel esquerdo da lâmina da conta, clique em **Fio de Ligação**.
4. A lâmina de corda de **ligação** abre- se. Dispõe de todas as informações necessárias para se ligar à conta utilizando um controlador para o MongoDB, incluindo uma cadeia de ligação pré-construída.

   [![Lâmina de](./media/connect-mongodb-account/ConnectionStringBlade.png) corda de ligação](./media/connect-mongodb-account/ConnectionStringBlade.png#lightbox)

## <a name="connection-string-requirements"></a>Requisitos de cordas de ligação

> [!Important]
> A Azure Cosmos DB tem rigorosos requisitos e padrões de segurança. As contas Da Azure Cosmos DB requerem autenticação e comunicação segura via *TLS*. 
>
>

A Azure Cosmos DB suporta o formato de cadeia de cordas de ligação MongoDB padrão URI, com um par de requisitos específicos: as contas Azure Cosmos DB requerem autenticação e comunicação segura via TLS. Então, o formato de cadeia de ligação é:

    mongodb://username:password@host:port/[database]?ssl=true

Os valores desta cadeia estão disponíveis na lâmina de corda de **ligação** mostrada anteriormente:

* Nome de utilizador (obrigatório): Nome da conta Cosmos.
* Palavra-passe (necessária): Senha de conta Cosmos.
* Anfitrião (obrigatório): FQDN da conta Cosmos.
* Porto (obrigatório): 10255.
* Base de dados (opcional): Base de dados que a ligação utiliza. Se não for fornecida nenhuma base de dados, a base de dados padrão é "teste".
* ssl=verdadeiro (obrigatório)

Por exemplo, considere a conta indicada na lâmina de fio de **ligação.** Uma cadeia de ligação válida é:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para MongoDB.
- Aprenda a [usar robo 3T](mongodb-robomongo.md) com API da Azure Cosmos DB para MongoDB.
- Explore [as amostras](mongodb-samples.md) de MongoDB com a API da Azure Cosmos DB para o MongoDB.
