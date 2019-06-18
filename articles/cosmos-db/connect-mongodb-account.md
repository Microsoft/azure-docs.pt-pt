---
title: Ligar uma aplicação MongoDB ao Azure Cosmos DB
description: Saiba como ligar a sua aplicação MongoDB ao Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: dabce19d60a380b47d3583dedb5c11303f416ce7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65978713"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Ligar uma aplicação MongoDB ao Azure Cosmos DB
Saiba como ligar a sua aplicação MongoDB ao Azure Cosmos DB ao utilizar uma cadeia de ligação do MongoDB. Em seguida, pode utilizar uma base de dados do Azure Cosmos como os dados de arquivo para a sua aplicação MongoDB. 

Este tutorial fornece duas formas de obter informações da cadeia de ligação:

- [O método de início rápido](#QuickstartConnection), para utilização com drivers de .NET, node. js, MongoDB Shell, Java e Python
- [O método de cadeia de caracteres de conexão personalizada](#GetCustomConnection), para utilização com outros controladores

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se não tiver uma conta do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) agora. 
- Uma conta do Cosmos. Para obter instruções, consulte [criar uma aplicação web com a API do Azure Cosmos DB para MongoDB e o SDK de .NET](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Obter a cadeia de ligação do MongoDB com o início rápido
1. Num browser da Internet, inicie sessão para o [portal do Azure](https://portal.azure.com).
2. Na **do Azure Cosmos DB** painel, selecione a API. 
3. No painel esquerdo do painel de conta, clique em **guia de introdução**. 
4. Escolha a sua plataforma ( **.NET**, **node. js**, **Shell de MongoDB**, **Java**, **Python**). Se não vir o controlador ou a ferramenta listada, não se preocupe, vamos continuar a documentar mais fragmentos de código de ligação. Comentário abaixo sobre o que gostaria de ver. Para saber como criar sua própria ligação, leia [obter informações de cadeia de ligação da conta](#GetCustomConnection).
5. Copie e cole o fragmento de código na sua aplicação MongoDB.

    ![Painel de início rápido](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Obter a cadeia de ligação do MongoDB para personalizar
1. Num browser da Internet, inicie sessão para o [portal do Azure](https://portal.azure.com).
2. Na **do Azure Cosmos DB** painel, selecione a API. 
3. No painel esquerdo do painel de conta, clique em **cadeia de ligação**. 
4. O **cadeia de ligação** é aberto o painel. Ele tem todas as informações necessárias para ligar à conta com um driver para o MongoDB, incluindo uma cadeia de ligação preconstructed.

    ![Painel Cadeia de Ligação](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Requisitos de cadeia de ligação
> [!Important]
> Azure Cosmos DB tem requisitos de segurança rigorosas e padrões. As contas do Azure Cosmos DB requerem autenticação e comunicação segura através de *SSL*. 
>
>

Azure Cosmos DB suporta o MongoDB ligação cadeia URI formato padrão, com alguns requisitos específicos: As contas do Azure Cosmos DB requerem autenticação e comunicação segura através de SSL. Por isso, é o formato de cadeia de ligação:

    mongodb://username:password@host:port/[database]?ssl=true

Os valores da cadeia de caracteres estão disponíveis no **cadeia de ligação** painel mostrado anteriormente:

* O nome de utilizador (obrigatório): Nome da conta do cosmos.
* Palavra-passe (obrigatório): Palavra-passe da conta de cosmos.
* Anfitrião (obrigatório): FQDN da conta do Cosmos.
* Porta (obrigatória): 10255.
* A base de dados (opcional): A base de dados que utiliza a ligação. Não se for fornecida nenhuma base de dados, a base de dados predefinido é "teste".
* SSL = true (obrigatório)

Por exemplo, considere a conta mostrada na **cadeia de ligação** painel. Uma cadeia de ligação válida é:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [utilizar Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para o MongoDB.
- Saiba como [utilizar Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para o MongoDB.
- Explorar o MongoDB [amostras](mongodb-samples.md) com a API do Azure Cosmos DB para o MongoDB.
