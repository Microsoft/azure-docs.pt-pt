---
title: Criar e consultar um armazém de dados (portal Azure)
description: Crie e consulta uma piscina Azure Synapse Analytics SQL utilizando o portal Azure
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 05/28/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7a3dbe5d74dc1e88d0615937b8c6e6d2a77b64a7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381102"
---
# <a name="quickstart-create-and-query-an-azure-synapse-analytics-sql-pool-using-the-azure-portal"></a>Quickstart: Criar e consultar uma piscina Azure Synapse Analytics SQL utilizando o portal Azure

Crie e consulta rapidamente um armazém de dados, disponibilizando piscina SQL em Azure Synapse Analytics (anteriormente SQL DW) utilizando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

1. Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

   > [!NOTE]
   > A criação de uma piscina SQL em Azure Synapse pode resultar num novo serviço de faturação. Para mais informações, consulte o preço da [Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

2. Transfira e instale a versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-sql-pool"></a>Criar uma piscina SQL

Os armazéns de dados são criados usando piscina SQL em Azure Synapse Analytics. Um pool SQL é criado com um conjunto definido de [recursos computacionais.](memory-concurrency-limits.md) A base de dados é criada num [Grupo de recursos do Azure](../azure-resource-manager/management/overview.md) e num [servidor lógico SQL do Azure](../sql-database/sql-database-servers.md).

Siga estes passos para criar um armazém de dados que contenha os dados da amostra **AdventureWorksDW.**

1. selecione **Criar um recurso** no canto superior esquerdo do portal Azure.

   ![criar um recurso no portal Azure](media/create-data-warehouse-portal/create-a-resource.png)

2. Selecione **Bases** de Dados na **nova** página e selecione **Azure Synapse Analytics (anteriormente SQL DW)** na lista **Em destaque.**

   ![criar um armazém de dados vazio](media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. No **Basics,** forneça a sua subscrição, grupo de recursos, nome de piscina SQL e nome do servidor:

   | Definição | Valor sugerido | Descrição |
   | :------ | :-------------- | :---------- |
   | **Subscrição** | A sua subscrição | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
   | **Grupo de recursos** | myResourceGroup | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Atribuição de nomes de regras e restrições). |
   | **Nome do armazém de dados** | Qualquer nome globalmente único (Um exemplo é *mySampleDataWarehouse)* | Para nomes de bases de dados válidos, veja [Database Identifiers](/sql/relational-databases/databases/database-identifiers) (Identificadores de Bases de Dados). Tenha em atenção que um armazém de dados é um tipo de base de dados. |
   | **Servidor** | Qualquer nome globalmente exclusivo | Selecione o servidor existente ou crie um novo nome de servidor, selecione **Criar novo**. Para nomes de servidores válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Atribuição de nomes de regras e restrições). |

   ![criar um armazém de dados detalhes básicos](media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. Sob **o nível de Desempenho,** **selecione o nível** de desempenho para alterar opcionalmente a sua configuração com um slider.

   ![alterar nível de desempenho do armazém de dados](media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   Para obter mais informações sobre os níveis de desempenho, consulte Gerir a computação no Armazém de [Dados Azure SQL](sql-data-warehouse-manage-compute-overview.md).

5. Agora que completou o separador Basics do formulário Azure Synapse Analytics, selecione **Review + Create** e, em seguida, **Crie** para criar o armazém de dados na piscina SQL. O aprovisionamento demora alguns minutos.

   ![selecionar Rever + Criar](media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![selecione criar](media/create-data-warehouse-portal/create-sql-pool-create.png)

6. Na barra de ferramentas, selecione **Notificações** para monitorizar o processo de implementação.

   ![notificação](media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall ao nível do servidor

O serviço Azure Synapse cria uma firewall ao nível do servidor. Esta firewall impede que aplicações e ferramentas externas se conectem ao servidor ou a quaisquer bases de dados no servidor. Para permitir a conectividade, pode adicionar regras de firewall que permitem a conectividade para endereços IP específicos. Siga estes passos para criar uma [regra de firewall ao nível do servidor](../sql-database/sql-database-firewall-configure.md) para o endereço IP do cliente.

> [!NOTE]
> Azure Synapse comunica sobre o porto 1433. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Se assim for, não poderá ligar ao servidor da Base de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.

1. Depois de concluída a implementação, selecione **Todos os serviços** do menu à esquerda. Selecione Bases de **Dados**, selecione a estrela ao lado do **Azure Synapse Analytics** para adicionar o Azure Synapse Analytics aos seus favoritos.

2. Selecione **Azure Synapse Analytics** a partir do menu à esquerda e, em seguida, selecione **mySampleDataWarehouse** na página **Azure Synapse Analytics.** A página de visão geral da sua base de dados abre-se, mostrando-lhe o nome do servidor totalmente qualificado (como **sqlpoolservername.database.windows.net)** e fornece opções para uma configuração posterior.

3. Copie este nome de servidor totalmente qualificado para utilização para ligar ao seu servidor e às suas bases de dados neste e noutros arranques rápidos. Para abrir as definições do servidor, selecione o nome do servidor.

   ![localizar nome do servidor](media/create-data-warehouse-portal/find-server-name.png)

4. Selecione **Mostrar definições de firewall**.

   ![definições do servidor](media/create-data-warehouse-portal/server-settings.png)

5. É aberta a página **Definições da firewall** do servidor da Base de Dados SQL.

   ![regra de firewall do servidor](media/create-data-warehouse-portal/server-firewall-rule.png)

6. Para adicionar o seu endereço IP atual a uma nova regra de firewall, **selecione Adicionar IP do cliente** na barra de ferramentas. Uma regra de firewall consegue abrir a porta 1433 para um único endereço IP ou para um intervalo de endereços IP.

7. selecionar **Guardar**. É criada uma regra de firewall ao nível do servidor para a sua porta de abertura 1433 do endereço IP atual no servidor lógico.

8. Selecione **OK** e, em seguida, feche a página de **definições de Firewall.**

Agora, pode ligar ao servidor SQL e aos respetivos armazéns de dados com este endereço IP. A ligação funciona a partir do SQL Server Management Studio ou de outra ferramenta à sua escolha. Ao ligar, utilize a conta ServerAdmin que criou anteriormente.

> [!IMPORTANT]
> Por predefinição, o acesso através da firewall da Base de Dados SQL está ativado para todos os serviços do Azure. Selecione **OFF** nesta página e, em seguida, selecione **Guardar** para desativar a firewall para todos os serviços Azure.

## <a name="get-the-fully-qualified-server-name"></a>Obter o nome de servidor completamente qualificado

Obtenha o nome de servidor completamente qualificado para o servidor SQL no portal do Azure. Utilizará mais tarde o nome completamente qualificado quando ligar ao servidor.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione **Azure Synapse Analytics** a partir do menu à esquerda e selecione o seu armazém de dados na página **Azure Synapse Analytics.**

3. No painel **Essentials** na página do portal do Azure da sua base de dados, localize e, em seguida, copie o **Nome do servidor**. Neste exemplo, o nome totalmente qualificado é sqlpoolservername.database.windows.net.

    ![informações da ligação](media/create-data-warehouse-portal/find-server-name-copy.png)

## <a name="connect-to-the-server-as-server-admin"></a>Ligar ao servidor como administrador do servidor

Esta secção utiliza o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) para estabelecer uma ligação ao servidor SQL do Azure.

1. Abra o SQL Server Management Studio.

2. Na caixa de dialogo **Ligar ao Servidor**, introduza as seguintes informações:

   | Definição | Valor sugerido | Descrição |
   | :------ | :-------------- | :---------- |
   | Tipo de servidor | Motor de base de dados | Este valor é obrigatório |
   | Nome do servidor | O nome de servidor completamente qualificado | Eis um exemplo: **sqlpoolservername.database.windows.net.** |
   | Autenticação | Autenticação do SQL Server | A Autenticação do SQL é o único tipo de autenticação configurado neste tutorial. |
   | Iniciar sessão | A conta de administrador do servidor | Conta que especificou quando criou o servidor. |
   | Palavra-passe | A palavra-passe da sua conta de administrador do servidor | Palavra-passe que especificou quando criou o servidor. |
   ||||

   ![ligar ao servidor](media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. selecionar **Ligar**. A janela do Object Explorer é aberta no SSMS. 

4. No Object Explorer, expanda **Databases**. Em seguida, expanda **mySampleDatabase** para ver os objetos na nova base de dados.

   ![objetos da base de dados](media/create-data-warehouse-portal/connected-ssms.png) 

## <a name="run-some-queries"></a>Executar algumas consultas

O SQL Data Warehouse utiliza o T-SQL como linguagem de consulta. Para abrir uma janela de consulta e executar algumas consultas de T-SQL, utilize os seguintes passos:

1. Selecione o **mySampleDataWarehouse** e selecione **New Query**. É aberta uma nova janela de consulta.

2. Na janela da consulta, introduza o seguinte comando para ver uma lista de bases de dados.

    ```sql
    SELECT * FROM sys.databases
    ```

3. selecionar **Executar**. Os resultados da consulta mostram duas bases de dados: **master** e **mySampleDataWarehouse**.

   ![Consultar bases de dados](media/create-data-warehouse-portal/query-databases.png)

4. Para ver alguns dados, utilize o seguinte comando para ver o número de clientes com o apelido Adams com três filhos em casa. Os resultados listam seis clientes. 

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![Consultar dbo.dimCustomer](media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Limpar recursos

Está a ser cobrado pelas unidades de armazém de dados e os dados armazenados no seu armazém de dados. Estes recursos de computação e armazenamento são faturados em separado.

- Se quiser manter os dados no armazenamento, pode interromper a computação quando não estiver a utilizar o armazém de dados. Ao fazer uma pausa na computação, só é cobrado para armazenamento de dados. Pode retomar a computação sempre que estiver pronto para trabalhar com os dados.

- Se quiser remover futuras cobranças, pode eliminar o armazém de dados.

Siga estes passos para limpar os recursos que já não precisa.

1. Inscreva-se no [portal Azure,](https://portal.azure.com)selecione no seu armazém de dados.

   ![Limpar recursos](media/create-data-warehouse-portal/clean-up-resources.png)

2. Para parar a computação, selecione o botão **Pausa.** Quando o armazém de dados é interrompido, vê um botão **Currículo.** Para retomar a computação, selecione **Resume**.

3. Para remover o armazém de dados para que não seja cobrado para computação ou armazenamento, **selecione Eliminar**.

4. Para remover o servidor SQL que criou, selecione **sqlpoolservername.database.windows.net** na imagem anterior e, em seguida, selecione **Delete**. Tenha cuidado com esta eliminação, uma vez que eliminar o servidor também elimina todas as bases de dados atribuídas ao mesmo.

5. Para remover o grupo de recursos, selecione **myResourceGroup**, e, em seguida, **selecione Eliminar o grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Criou agora um armazém de dados, criou uma regra de firewall, ligada ao seu armazém de dados, e executou algumas consultas. Para saber mais sobre o Azure SQL Data Warehouse, avance para o tutorial para carregar dados.

> [!div class="nextstepaction"]
> [Carregue os dados num Armazém de Dados SQL](load-data-from-azure-blob-storage-using-polybase.md)
