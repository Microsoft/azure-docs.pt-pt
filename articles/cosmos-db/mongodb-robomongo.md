---
title: Utilizar Robo 3T para ligar ao Azure Cosmos DB
description: Saiba como ligar ao Azure Cosmos DB com Robo 3T e a API do Azure Cosmos DB do MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: ab066fef4bffe35408fdc5f3ad6d66796b4d0818
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443975"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Utilizar Robo 3T com a API do Azure Cosmos DB para o MongoDB

Para ligar à conta do Cosmos com Robo 3T, faça o seguinte:

* Transferir e instalar [Robo 3T](https://robomongo.org/)
* Ter seu Cosmos DB [cadeia de ligação](connect-mongodb-account.md) informações

## <a name="connect-using-robo-3t"></a>Ligar com Robo 3T
Para adicionar a sua conta do Cosmos para o Gerenciador de conexões de 3T Robo, execute os seguintes passos:

1. Obter as informações de ligação para a sua conta do Cosmos configurada com o MongoDB de API do Azure Cosmos DB ao seguir as instruções [aqui](connect-mongodb-account.md).

    ![Captura de ecrã do painel de cadeia de ligação](./media/mongodb-robomongo/connectionstringblade.png)
2. Executar *Robomongo.exe*

3. Clique no botão de ligação sob **ficheiro** para gerir as suas ligações. Em seguida, clique em **criar** no **ligações MongoDB** janela, que abrirá a **definições de ligação** janela.

4. Na **definições de ligação** janela, escolha um nome. Em seguida, procure o **Host** e **porta** do suas informações de ligação no passo 1 e introduza-os em **endereço** e **porta**, respectivamente.

    ![Captura de ecrã do Robomongo gerir ligações](./media/mongodb-robomongo/manageconnections.png)
5. Sobre o **autenticação** separador, clique em **executar autenticação**. Em seguida, introduza a sua base de dados (a predefinição é *administrador*), **nome de utilizador** e **palavra-passe**.
Ambos **nome de utilizador** e **palavra-passe** podem ser encontrados nas informações de ligação no passo 1.

    ![Captura de ecrã do separador autenticação Robomongo](./media/mongodb-robomongo/authentication.png)
6. Na **SSL** separador, verificação **utilizar o protocolo SSL**, em seguida, altere o **método de autenticação** para **certificado autoassinado**.

    ![Captura de ecrã do separador de Robomongo SSL](./media/mongodb-robomongo/SSL.png)
7. Por fim, clique em **teste** para verificar a capacidade de ligar, em seguida, **guardar**.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [utilizar Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para o MongoDB.
- Explorar o MongoDB [amostras](mongodb-samples.md) com a API do Azure Cosmos DB para o MongoDB.
