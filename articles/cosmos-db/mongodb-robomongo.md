---
title: Use Robo 3T para ligar ao Azure Cosmos DB
description: Saiba como ligar-se ao Azure Cosmos DB utilizando a API de Robo 3T e Azure Cosmos DB para mongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: 627849acb902a721c80b98adbd16103b4729591b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81114197"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Utilizar o Robo 3T da API do Azure Cosmos DB para MongoDB

Para ligar à conta Cosmos usando Robo 3T, deve:

* Descarregue e [instale Robo 3T](https://robomongo.org/)
* Tenha a sua informação de cadeia de [conexão](connect-mongodb-account.md) Cosmos DB

> [!NOTE]
> Atualmente, robo 3T v1.2 e versões inferiores são suportados com API da Cosmos DB para MongoDB.

## <a name="connect-using-robo-3t"></a>Ligar com o Robo 3T

Para adicionar a sua conta Cosmos ao gestor de conexão Robo 3T, execute os seguintes passos:

1. Recupere as informações de ligação para a sua conta Cosmos configuradas com o API MongoDB da Azure Cosmos DB utilizando as instruções [aqui](connect-mongodb-account.md).

    ![Screenshot da lâmina de corda de ligação](./media/mongodb-robomongo/connectionstringblade.png)
2. Executar a aplicação *Robomongo.*

3. Clique no botão de ligação em **Ficheiro** para gerir as suas ligações. Em seguida, clique em **Criar** na janela de **ligações MongoDB,** que abrirá a janela Definições de **Ligação.**

4. Na janela Definições de **Ligação,** escolha um nome. Em seguida, encontre o **Anfitrião** e o **Porto** a partir das suas informações de ligação no Passo 1 e insira-os no **Endereço** e **Porta,** respectivamente.

    ![Screenshot das Ligações de Gestão robomongo](./media/mongodb-robomongo/manageconnections.png)
5. No separador **Autenticação,** clique em **Executar a autenticação**. Em seguida, introduza a sua Base de Dados (predefinido é *Administrador),* **Nome de Utilizador** e **Palavra-passe**.
Tanto o **Nome do Utilizador** como a **Palavra-Passe** podem ser encontrados nas informações de ligação no passo 1.

    ![Screenshot do separador de autenticação robomongo](./media/mongodb-robomongo/authentication.png)
6. No separador **SSL,** verifique utilize o **protocolo SSL**e, em seguida, altere o método de **autenticação** para **certificado auto-assinado**.

    ![Screenshot do Guia Robomongo SSL](./media/mongodb-robomongo/SSL.png)
7. Por fim, clique em **Test** para verificar se é capaz de ligar e, em seguida, **guardar**.

## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para MongoDB.
- Explore [as amostras](mongodb-samples.md) de MongoDB com a API da Azure Cosmos DB para o MongoDB.
