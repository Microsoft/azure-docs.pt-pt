---
title: 'Quickstart: Criar e consultar uma piscina SQL dedicada (anteriormente SQL DW) (portal Azure)'
description: Criar e consultar uma piscina SQL dedicada (anteriormente SQL DW) utilizando o portal Azure
services: synapse-analytics
author: pimorano
ms.author: pimorano
manager: craigg
ms.reviewer: igorstan
ms.date: 05/28/2019
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- mode-portal
ms.openlocfilehash: 99530b1949f03867a5b755208191341556ba8083
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535842"
---
# <a name="quickstart-create-and-query-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-using-the-azure-portal"></a>Quickstart: Criar e consultar uma piscina SQL dedicada (anteriormente SQL DW) em Azure synapse Analytics usando o portal Azure

Crie e questione rapidamente uma piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics usando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

1. Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

   > [!NOTE]
   > A criação de uma piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse pode resultar num novo serviço de faturação. Para mais informações, consulte [os preços do Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

2. Transfira e instale a versão mais recente do [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS). Nota: O SSMS só está disponível nas plataformas baseadas no Windows, consulte a [lista completa de plataformas suportadas.](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15&preserve-view=true#supported-operating-systems-ssms-185t)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-sql-pool"></a>Criar uma piscina SQL

Os armazéns de dados são criados utilizando piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics. Uma piscina SQL dedicada (anteriormente SQL DW) é criada com um conjunto definido de [recursos computativos.](memory-concurrency-limits.md) A base de dados é criada dentro de um [grupo de recursos Azure](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e num [servidor lógico SQL](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Siga estes passos para criar uma piscina SQL dedicada (anteriormente SQL DW) que contém os dados da amostra **AdventureWorksDW.**

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.

   ![criar um recurso no portal Azure](./media/create-data-warehouse-portal/create-a-resource.png)

2. No tipo de barra de pesquisa "piscina SQL dedicada" selecione piscina SQL dedicada (anteriormente SQL DW). Selecione **Criar** na página que abre.

   ![criar um armazém de dados vazio](./media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. Em **Basics,** forneça a sua subscrição, grupo de recursos, pool SQL dedicado (anteriormente SQL DW) e nome do servidor:

   | Definição | Valor sugerido | Descrição |
   | :------ | :-------------- | :---------- |
   | **Subscrição** | A sua subscrição | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
   | **Grupo de recursos** | myResourceGroup | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (Atribuição de nomes de regras e restrições). |
   | **Nome da piscina SQL** | Qualquer nome globalmente único (Um exemplo é *mySampleDataWarehouse)* | Para nomes de bases de dados válidos, veja [Database Identifiers](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (Identificadores de Bases de Dados).  |
   | **Servidor** | Qualquer nome globalmente exclusivo | Selecione o servidor existente ou crie um novo nome de servidor, **selecione Criar novo**. Para nomes de servidores válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (Atribuição de nomes de regras e restrições). |

   ![criar um armazém de dados detalhes básicos](./media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. No **nível de Desempenho**, **selecione Selecionar o nível de desempenho** para alterar opcionalmente a sua configuração com um slider.

   ![alterar o nível de desempenho do armazém de dados](./media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   Para obter mais informações sobre os níveis de desempenho, consulte [Gerir o cálculo no Azure Synapse Analytics](sql-data-warehouse-manage-compute-overview.md).

5. Selecione **Definições Adicionais**, em **Utilizar os dados existentes**, escolha **sample** para que o AdventureWorksDW seja criado como base de dados da amostra.

    ![selecionar Utilizar os dados existentes](./media/create-data-warehouse-portal/create-sql-pool-additional-1.png)

6. Agora que completou o separador Básicos do formulário Azure Synapse Analytics, selecione **Review + Create** e, em seguida, **Crie** para criar a piscina SQL. O aprovisionamento demora alguns minutos.

   ![selecionar Revisão + Criar](./media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![selecione criar](./media/create-data-warehouse-portal/create-sql-pool-create.png)

7. Na barra de ferramentas, selecione **Notificações** para monitorizar o processo de implementação.

   ![A screenshot mostra notificações com implementação em curso.](./media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall ao nível do servidor

O serviço Azure Synapse cria uma firewall ao nível do servidor. Esta firewall impede que aplicações e ferramentas externas se conectem ao servidor ou a quaisquer bases de dados no servidor. Para permitir a conectividade, pode adicionar regras de firewall que permitem a conectividade para endereços IP específicos. Siga estes passos para criar uma [regra de firewall ao nível do servidor](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) para o endereço IP do cliente.

> [!NOTE]
> Azure Synapse comunica sobre o porto 1433. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Em caso afirmativo, não é possível ligar-se ao seu servidor a menos que o seu departamento de TI abra a porta 1433.

1. Após a implementação concluída, selecione **Todos os serviços** do menu à esquerda. Selecione **Bases de Dados**, selecione a estrela ao lado do **Azure Synapse Analytics** para adicionar Azure Synapse Analytics aos seus favoritos.

2. Selecione **Azure Synapse Analytics** a partir do menu da mão esquerda e, em seguida, selecione **mySampleDataWarehouse** na página **Azure Synapse Analytics.** A página geral da sua base de dados abre, mostrando-lhe o nome do servidor totalmente qualificado (como **sqlpoolservername.database.windows.net)** e fornece opções para uma configuração posterior.

3. Copie este nome de servidor totalmente qualificado para ser utilizado para ligar ao seu servidor e às suas bases de dados neste e noutros arranques rápidos. Para abrir as definições do servidor, selecione o nome do servidor.

   ![localizar nome do servidor](./media/create-data-warehouse-portal/find-server-name.png)

4. Selecione **Mostrar definições da firewall**.

   ![definições do servidor](./media/create-data-warehouse-portal/server-settings.png)

5. A página **de definições de Firewall** para o servidor abre.

   ![regra de firewall do servidor](./media/create-data-warehouse-portal/server-firewall-rule.png)

6. Para adicionar o seu endereço IP atual a uma nova regra de firewall, **selecione Adicionar IP do cliente** na barra de ferramentas. Uma regra de firewall consegue abrir a porta 1433 para um único endereço IP ou para um intervalo de endereços IP.

7. selecionar **Guardar**. Uma regra de firewall ao nível do servidor é criada para o seu endereço IP atual que abre a porta 1433 no servidor.

8. selecione **OK** e, em seguida, feche a página **de definições de Firewall.**

Agora pode ligar-se ao servidor e às suas piscinas SQL utilizando este endereço IP. A ligação funciona a partir do SQL Server Management Studio ou de outra ferramenta à sua escolha. Ao ligar, utilize a conta ServerAdmin que criou anteriormente.

> [!IMPORTANT]
> Por predefinição, o acesso através da firewall da Base de Dados SQL está ativado para todos os serviços do Azure. selecione **OFF** nesta página e, em seguida, selecione **Guardar** para desativar a firewall para todos os serviços Azure.

## <a name="get-the-fully-qualified-server-name"></a>Obter o nome de servidor completamente qualificado

Obtenha o nome do servidor totalmente qualificado para o seu servidor no portal Azure. Utilizará mais tarde o nome completamente qualificado quando ligar ao servidor.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione **Azure Synapse Analytics** a partir do menu da mão esquerda e selecione o seu na página **Azure Synapse Analytics.**

3. No painel **Essentials** na página do portal do Azure da sua base de dados, localize e, em seguida, copie o **Nome do servidor**. Neste exemplo, o nome totalmente qualificado é sqlpoolservername.database.windows.net.

    ![informações da ligação](./media/create-data-warehouse-portal/find-server-name.png)

## <a name="connect-to-the-server-as-server-admin"></a>Ligar ao servidor como administrador do servidor

Esta secção utiliza [o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) para estabelecer uma ligação ao seu servidor.

1. Abra o SQL Server Management Studio.

2. Na caixa de dialogo **Ligar ao Servidor**, introduza as seguintes informações:

   | Definição | Valor sugerido | Descrição |
   | :------ | :-------------- | :---------- |
   | Tipo de servidor | Motor de base de dados | Este valor é obrigatório |
   | Nome do servidor | O nome de servidor completamente qualificado | Aqui está um exemplo: **sqlpoolservername.database.windows.net.** |
   | Autenticação | Autenticação do SQL Server | A Autenticação do SQL é o único tipo de autenticação configurado neste tutorial. |
   | Iniciar sessão | A conta de administrador do servidor | Conta que especificou quando criou o servidor. |
   | Palavra-passe | A palavra-passe da sua conta de administrador do servidor | Palavra-passe que especificou quando criou o servidor. |
   ||||

   ![ligar ao servidor](./media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. **selecione Connect**. A janela do Object Explorer é aberta no SSMS.

4. No Object Explorer, expanda **Databases**. Em seguida, expanda **mySampleDatabase** para ver os objetos na nova base de dados.

   ![objetos da base de dados](./media/create-data-warehouse-portal/connected-ssms.png)

## <a name="run-some-queries"></a>Executar algumas consultas

Não é aconselhável executar grandes consultas enquanto é registado como administrador do servidor, uma vez que utiliza uma [classe de recursos limitada](resource-classes-for-workload-management.md). Em vez disso, configurar [o isolamento da carga de trabalho](./quickstart-configure-workload-isolation-tsql.md) como ilustrado nos [tutoriais](./load-data-wideworldimportersdw.md#create-a-user-for-loading-data).

A Azure Synapse Analytics usa t-SQL como linguagem de consulta. Para abrir uma janela de consulta e executar algumas consultas de T-SQL, utilize os seguintes passos:

1. Selecione à direita **mySampleDataWarehouse** e **selecione Nova Consulta**. É aberta uma nova janela de consulta.

2. Na janela da consulta, introduza o seguinte comando para ver uma lista de bases de dados.

    ```sql
    SELECT * FROM sys.databases
    ```

3. selecionar **Executar**. Os resultados da consulta mostram duas bases de dados: **master** e **mySampleDataWarehouse**.

   ![Consultar bases de dados](./media/create-data-warehouse-portal/query-databases.png)

4. Para ver alguns dados, utilize o seguinte comando para ver o número de clientes com o apelido Adams com três filhos em casa. Os resultados listam seis clientes.

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![Consultar dbo.dimCustomer](./media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Está a ser cobrado por unidades de armazém de dados e os dados armazenaram a sua piscina DE SQL dedicada (anteriormente SQL DW). Estes recursos de computação e armazenamento são faturados em separado.

- Se quiser manter os dados armazenados, pode parar o cálculo quando não estiver a utilizar a piscina SQL dedicada (anteriormente SQL DW). Ao fazer uma pausa no cálculo, só é cobrado para armazenamento de dados. Pode retomar o cálculo sempre que estiver pronto para trabalhar com os dados.

- Se pretender remover encargos futuros, pode eliminar a piscina SQL dedicada (anteriormente SQL DW).

Siga estes passos para limpar os recursos que já não precisa.

1. Inscreva-se no [portal Azure,](https://portal.azure.com)selecione a sua piscina SQL dedicada (anteriormente SQL DW).

   ![Limpar os recursos](./media/create-data-warehouse-portal/clean-up-resources.png)

2. Para parar o cálculo, selecione o **botão Pausa.** Quando a piscina SQL dedicada (anteriormente SQL DW) é pausada, você vê um botão **Resume.** Para retomar o cálculo, **selecione Resume**.

3. Para remover a piscina SQL dedicada (anteriormente SQL DW) para que não seja cobrado para computação ou armazenamento, selecione **Delete**.

4. Para remover o servidor criado, selecione **sqlpoolservername.database.windows.net** na imagem anterior e, em seguida, selecione **Delete**. Tenha cuidado com esta eliminação, uma vez que eliminar o servidor também elimina todas as bases de dados atribuídas ao mesmo.

5. Para remover o grupo de recursos, selecione **myResourceGroup** e, em seguida, **selecione Delete resource group**.

Quer otimizar e economizar nos gastos na nuvem?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o carregamento de dados no seu pool de SQL dedicado (anteriormente SQL DW), continue a carregar os dados de carga num artigo dedicado de [piscina SQL.](load-data-from-azure-blob-storage-using-copy.md)
