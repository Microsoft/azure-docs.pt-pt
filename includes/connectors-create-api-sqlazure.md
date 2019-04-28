---
title: incluir ficheiro
description: incluir ficheiro
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/15/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: da03c5247b8ebe0a3305b08a05d661264497663f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60541148"
---
* Se estiver a utilizar a base de dados do Azure SQL, siga os passos em [Connect to Azure SQL Database](#connect-azure-sql-db). 

* Se estiver a utilizar o SQL Server, siga os passos em [ligar ao SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Ligar à base de dados SQL do Azure

1. Quando o acionador SQL ou a ação pede-lhe informações de ligação, siga estes passos:

   1. Crie um nome para a sua ligação.

   2. Selecione o seu servidor SQL e, em seguida, selecione a base de dados. 

      É apresentada a lista de base de dados apenas depois de selecionar o SQL server.
 
   3. Fornece seu nome de utilizador e palavra-passe para o seu servidor.

      Pode encontrar estas informações no portal do Azure em suas propriedades de base de dados SQL ou na sua cadeia de ligação: 
      
      "ID de utilizador = <*yourUserName*>"
      <br>
      "Password=<*yourPassword*>"

   Este exemplo mostra as informações de ligação para um acionador, mas estes passos funcionam para ações demasiado.

   ![Criar ligação de base de dados do Azure SQL](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)
   <br>
   Asteriscos (*) indicam os valores necessários.

   | Propriedade | Value | Detalhes | 
   |----------|-------|---------| 
   | Nome da Ligação | <*my-sql-connection*> | O nome para a sua ligação | 
   | Nome do Servidor SQL | <*my-sql-server*> | O nome do seu servidor SQL |
   | Nome da Base de Dados SQL | <*my-sql-database*>  | O nome da base de dados SQL | 
   | Nome de utilizador | <*my-sql-username*> | O nome de utilizador para aceder à sua base de dados |
   | Palavra-passe | <*my-sql-password*> | A palavra-passe para aceder à sua base de dados | 
   |||| 

2. Quando tiver terminado, escolha **Create** (Criar).

3. Depois de criar a ligação, prosseguir [acionador de adicionar SQL](#add-sql-trigger) ou [ação de adicionar SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Ligar ao SQL Server

Antes de poder selecionar o gateway, certifique-se de que já [configurar o gateway de dados](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). Dessa forma, o gateway aparece na lista de gateways ao criar a ligação.

1. Quando o acionador SQL ou a ação pede-lhe informações de ligação, siga estes passos:

   1. No acionador ou ação, selecione **ligar através do gateway de dados no local** para que as opções de servidor SQL aparecem.

   2. Crie um nome para a sua ligação.

   3. Forneça o endereço do seu servidor SQL, em seguida, forneça o nome da base de dados.
   
      Pode encontrar estas informações na sua cadeia de ligação: 
      
      * "Server=<*yourServerAddress*>"
      * "Database=<*yourDatabaseName*>"

   4. Fornece seu nome de utilizador e palavra-passe para o seu servidor.

      Pode encontrar estas informações na sua cadeia de ligação: 
      
      * "ID de utilizador = <*yourUserName*>"
      * "Password=<*yourPassword*>"

   5. Se o SQL server utiliza o Windows ou a autenticação básica, selecione o tipo de autenticação.

   6. Selecione o nome para o seu gateway de dados no local que criou anteriormente.
   
      Verifique se o gateway não aparecer na lista, que corretamente [configurar o gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   Este exemplo mostra as informações de ligação para um acionador, mas estes passos funcionam para ações demasiado.

   ![Criar a ligação do SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)
   <br>
   Asteriscos (*) indicam os valores necessários.

   | Propriedade | Value | Detalhes | 
   |----------|-------|---------| 
   | Ligar através do gateway no local | Selecione esta opção primeiro para definições do SQL Server. | | 
   | Nome da Ligação | <*my-sql-connection*> | O nome para a sua ligação | 
   | Nome do Servidor SQL | <*my-sql-server*> | O nome do seu servidor SQL |
   | Nome da Base de Dados SQL | <*my-sql-database*>  | O nome da base de dados SQL |
   | Nome de utilizador | <*my-sql-username*> | O nome de utilizador para aceder à sua base de dados |
   | Palavra-passe | <*my-sql-password*> | A palavra-passe para aceder à sua base de dados | 
   | Tipo de Autenticação | Windows ou Basic | Opcional: O tipo de autenticação utilizado pelo seu servidor SQL | 
   | Gateways | <*my-data-gateway*> | O nome para o seu gateway de dados no local | 
   |||| 

2. Quando tiver terminado, escolha **Create** (Criar). 

3. Depois de criar a ligação, prosseguir [acionador de adicionar SQL](#add-sql-trigger) ou [ação de adicionar SQL](#add-sql-action).
