---
title: Use Robo 3T para ligar ao Azure Cosmos DB
description: Saiba como ligar-se à Azure Cosmos DB usando a API de Robo 3T e Azure Cosmos DB para a MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: e096de5eea9bf5bbd47b976976192e62eb6aa33e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096415"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Utilizar o Robo 3T da API do Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Para ligar à conta cosmos usando Robo 3T, você deve:

* Descarregue e instale [Robo 3T](https://robomongo.org/)
* Tenha a sua [informação](connect-mongodb-account.md) sobre a cadeia de conexão Cosmos DB

> [!NOTE]
> Atualmente, robo 3T v1.2 e versões inferiores são suportados com API da Cosmos DB para o MongoDB.

## <a name="connect-using-robo-3t"></a>Ligar com o Robo 3T

Para adicionar a sua conta Cosmos ao gestor de conexão Robo 3T, execute os seguintes passos:

1. Recupere as informações de ligação da sua conta Cosmos configuradas com a API MongoDB da Azure Cosmos DB utilizando as instruções [aqui](connect-mongodb-account.md).

    :::image type="content" source="./media/mongodb-robomongo/connectionstringblade.png" alt-text="Screenshot da lâmina de corda de ligação":::
2. Executar a aplicação *Robomongo.*

3. Clique no botão de ligação em **'Ficheiro'** para gerir as suas ligações. Em seguida, clique em **Criar** na janela **Ligações MongoDB,** que abrirá a janela **Definições de Ligação.**

4. Na janela **Definições de Ligação,** escolha um nome. Em seguida, encontre o **Anfitrião** e o **Porto** a partir das suas informações de ligação no Passo 1 e insira-as no **Endereço** e **Porto,** respectivamente.

    :::image type="content" source="./media/mongodb-robomongo/manageconnections.png" alt-text="Screenshot das Ligações de Gestão robomongo":::
5. No **separador Autenticação,** clique em **Realizar autenticação.** Em seguida, introduza a sua Base de Dados (por defeito é *Administrador),* **Nome de Utilizador** e **Palavra-passe**.
Tanto **o Nome de Utilizador** como a **Palavra-Passe** podem ser encontrados nas informações de ligação no Passo 1.

    :::image type="content" source="./media/mongodb-robomongo/authentication.png" alt-text="Screenshot do Separador de Autenticação robomongo":::
6. No separador **SSL,** verifique **o protocolo Utilizar SSL** e, em seguida, alterar o **Método de Autenticação** para **Certificado auto-assinado**.

    :::image type="content" source="./media/mongodb-robomongo/SSL.png" alt-text="Screenshot do Separador Robomongo SSL":::
7. Por fim, clique em **Test** para verificar se é capaz de se conectar e, em seguida, **Guardar**.

## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar o [Studio 3T](mongodb-mongochef.md) com a API da Azure Cosmos DB para a MongoDB.
- Explore [as amostras](mongodb-samples.md) do MongoDB com a API da Azure Cosmos para a MongoDB.
