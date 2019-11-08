---
title: incluir ficheiro
description: incluir ficheiro
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.author: estfan
ms.custom: include file
ms.date: 11/08/2019
ms.openlocfilehash: 0be29f6f541aa58e57eb665ebaf29e35f42865e4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826329"
---
* Se você estiver usando o banco de dados SQL do Azure, siga as etapas em [conectar ao banco de dados SQL do Azure](#connect-azure-sql-db).

* Se você estiver usando SQL Server, siga as etapas em [conectar a SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Conectar-se ao banco de dados SQL do Azure

Quando o gatilho ou ação do SQL solicitar informações de conexão, siga estas etapas, que funcionam para gatilhos e ações.

1. Para **nome da conexão**, crie um nome para a conexão.

1. Em **nome do SQL Server**, selecione o servidor SQL do Azure. Quando a lista **nome do banco de dados SQL** for exibida, selecione seu banco de dados. Forneça o nome de usuário e a senha para o SQL Server do Azure.

   Você também pode encontrar essas informações na portal do Azure nas propriedades do banco de dados SQL ou na cadeia de conexão:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Criar conexão com o banco de dados SQL do Azure](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Quando terminar, selecione **Criar**.

1. Depois de criar a conexão, continue com [Adicionar um gatilho SQL](#add-sql-trigger) ou [adicione uma ação SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Ligar ao SQL Server

Quando o gatilho ou ação do SQL solicitar informações de conexão, siga estas etapas, que funcionam para gatilhos e ações. Para cenários que exigem que você instale o [Gateway de dados](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install) local em um computador local e [crie o recurso do gateway de dados do Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection), verifique se você concluiu primeiro esses requisitos. Caso contrário, o recurso de gateway não aparecerá na lista de gateways quando você criar a conexão.

Além disso, para usar a autenticação do Windows com o conector de SQL Server em um [ambiente do serviço de integração (ISE)](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview), use a versão sem ISE do conector e o gateway de dados local. A versão com rótulo de ISE não dá suporte à autenticação do Windows.

1. Para **nome da conexão**, crie um nome para a conexão.

1. No gatilho ou ação, selecione **conectar por meio do gateway de dados local** para que as opções do SQL Server sejam exibidas.

1. Para **nome do SQL Server** e **nome do banco de dados SQL**, forneça o endereço do seu SQL Server e o nome do seu banco de dados. Para **username** e **password**, forneça o nome de usuário e a senha para seu servidor.

   Você também pode encontrar essas informações em sua cadeia de conexão:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Criar conexão com SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Se o SQL Server usar a autenticação do Windows ou básica, selecione o **tipo de autenticação**.

1. Em **gateways**, selecione a assinatura do Azure associada ao gateway de dados local criado anteriormente e selecione o nome do seu gateway de dados local.

   Se o gateway não aparecer na lista, verifique se você [configurou corretamente seu gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   ![Criação de SQL Server conexão concluída](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Quando terminar, selecione **Criar**.

1. Depois de criar a conexão, continue com [Adicionar gatilho SQL](#add-sql-trigger) ou [Adicionar ação SQL](#add-sql-action).
