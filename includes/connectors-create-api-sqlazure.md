---
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 11/08/2019
ms.openlocfilehash: ea0ae1b1527aa1f527c8ac8fbcd3b4e4f6b6fe2f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789206"
---
* Se estiver a utilizar a Base de Dados Azure SQL, siga os passos em [Connect to Azure SQL Database](#connect-azure-sql-db).

* Se estiver a utilizar o Servidor SQL, siga os passos em [Connect to SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Ligar à Base de Dados Azure SQL

Quando o gatilho ou ação SQL o solicitar para obter informações de ligação, siga estes passos, que funcionam tanto para gatilhos como para ações.

1. Para **o Nome de Ligação,** crie um nome para a sua ligação.

1. Sob **o nome do servidor SQL,** selecione o seu servidor Azure SQL. Quando aparecer a lista de nomes da base de **dados SQL,** selecione a sua base de dados. Forneça o nome de utilizador e a palavra-passe para o seu servidor Azure SQL.

   Também pode encontrar esta informação no portal Azure sob as suas propriedades de base de dados SQL ou na sua cadeia de ligação:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Criar ligação à Base de Dados Azure SQL](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Quando terminar, selecione **Criar**.

1. Depois de criar a sua ligação, continue com [adicionar um gatilho SQL](#add-sql-trigger) ou [adicionar uma ação SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Ligar ao SQL Server

Quando o gatilho ou ação SQL o solicitar para obter informações de ligação, siga estes passos, que funcionam tanto para gatilhos como para ações. Para cenários que exijam a instalação da porta de dados no local num computador local e crie o recurso de gateway de [dados Azure,](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection) [certifique-se](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install) de que preenche estes requisitos primeiro. Caso contrário, o seu recurso gateway não aparecerá na lista de gateways quando criar a sua ligação.

Além disso, para utilizar a autenticação do Windows com o conector SQL Server num ambiente de serviço de [integração (ISE)](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview), utilize a versão não ISE do conector e o portal de dados no local. A versão com a etiqueta ISE não suporta a autenticação do Windows.

1. Para **o Nome de Ligação,** crie um nome para a sua ligação.

1. No gatilho ou ação, selecione **Connect via gateway de dados no local** para que as opções do servidor SQL apareçam.

1. Para o nome do **servidor SQL** e nome da base de **dados SQL,** forneça o endereço do seu servidor SQL e nome para a sua base de dados. Para **username** e **Password,** forneça o nome de utilizador e a palavra-passe para o seu servidor.

   Também pode encontrar esta informação na sua cadeia de ligação:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Criar ligação ao Servidor SQL](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Se o seu servidor SQL utilizar o Windows ou a autenticação Básica, selecione o Tipo de **Autenticação**.

1. Em **Gateways,** selecione a subscrição Azure associada ao seu portal de dados previamente criado no local e selecione o nome para o seu portal de dados no local.

   Se o seu portal não aparecer na lista, verifique se configura corretamente [o seu portal](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   ![Criar a ligação do Servidor SQL concluída](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Quando terminar, selecione **Criar**.

1. Depois de criar a sua ligação, continue com [o gatilho Add SQL](#add-sql-trigger) ou [add sQL action](#add-sql-action).
