---
title: Criar seu primeiro banco de dados relacional- C# -banco de dados SQL do Azure | Microsoft Docs
description: Aprenda a projetar seu primeiro banco de dados relacional em um único banco de dados no C# banco de dados SQL do Azure com o uso de ADO.net.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
ms.date: 07/29/2019
ms.openlocfilehash: 3359c39b21edad72ce0b9d7feb35d442c842f002
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640107"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-cx23-and-adonet"></a>Tutorial: Criar um banco de dados relacional em um único banco de dados no&#x23; banco de dados SQL do Azure C e ADO.net

O banco de dados SQL do Azure é um DBaaS (banco de dados como serviço) relacional no Microsoft Cloud (Azure). Neste tutorial, vai aprender a utilizar o portal do Azure e o ADO.NET com o Visual Studio para:

> [!div class="checklist"]
> * Criar um banco de dados individual usando o portal do Azure *
> * Configurar uma regra de firewall de IP no nível de servidor usando o portal do Azure
> * Ligar à base de dados com o ADO.NET e o Visual Studio
> * Criar tabelas com o ADO.NET
> * Inserir, atualizar e eliminar os dados com o ADO.NET
> * Consultar dados com o ADO.NET

\* Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!TIP]
> O módulo Microsoft Learn a seguir ajuda você a aprender gratuitamente como [desenvolver e configurar um aplicativo ASP.NET que consulta um banco de dados SQL do Azure](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/), incluindo a criação de um banco de dados simples.

## <a name="prerequisites"></a>Pré-requisitos

Uma instalação do [Visual Studio 2019](https://www.visualstudio.com/downloads/) ou posterior.

## <a name="create-a-blank-single-database"></a>Criar uma base de dados única em branco

Um banco de dados individual no banco de dados SQL do Azure é criado com um conjunto definido de recursos de computação e armazenamento. O banco de dados é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) e é gerenciado usando um [servidor de banco de dados](sql-database-servers.md).

Siga estas etapas para criar um banco de dados individual em branco.

1. Clique em **Criar um recurso**, no canto superior esquerdo do portal do Azure.
2. Na página **Nova**, selecione **Bases de Dados** na secção de Microsoft Azure Marketplace e, em seguida, clique em **Base de Dados SQL** na secção **Em Destaque**.

   ![create empty-database](./media/sql-database-design-first-database/create-empty-database.png)

3. Preencha o formulário do **banco de dados SQL** com as seguintes informações, conforme mostrado na imagem anterior:

    | Definição       | Valor sugerido | Descrição |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nome da base de dados** | *yourDatabase* | Para obter nomes de banco de dados válidos, consulte identificadores de [banco de dados](/sql/relational-databases/databases/database-identifiers). |
    | **Subscrição** | *yourSubscription*  | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
    | **Grupo de recursos** | *yourResourceGroup* | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). |
    | **Selecionar origem** | Base de dados vazia | Especifica que deve ser criada uma base de dados vazia. |

4. Clique em **servidor** para usar um servidor de banco de dados existente ou criar e configurar um novo servidor de banco de dados. Selecione um servidor existente ou clique em **criar um novo servidor** e preencha o formulário **novo servidor** com as seguintes informações:

    | Definição       | Valor sugerido | Descrição |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nome do servidor** | Qualquer nome globalmente exclusivo | Para nomes de servidores válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). |
    | **Início de sessão de administrador do servidor** | Qualquer nome válido | Para obter nomes de logon válidos, consulte identificadores de [banco de dados](/sql/relational-databases/databases/database-identifiers). |
    | **Palavra-passe** | Qualquer palavra-passe válida | Sua senha deve ter pelo menos oito caracteres e deve usar caracteres de três das seguintes categorias: caracteres maiúsculos, caracteres minúsculos, números e caracteres não alfanuméricos. |
    | **Location** | Nenhuma localização válida | Para obter mais informações sobre regiões, veja [Azure Regions](https://azure.microsoft.com/regions/) (Regiões do Azure). |

    ![criar servidor de base de dados](./media/sql-database-design-first-database/create-database-server.png)

5. Clique em **Selecionar**.
6. Clique em **Escalão de preço** para especificar o escalão de serviço, o número de DTUs ou vCores e a quantidade de armazenamento. Você pode explorar as opções para o número de DTUs/vCores e armazenamento que está disponível para cada camada de serviço.

    Depois de selecionar a camada de serviço, o número de DTUs ou vCores e a quantidade de armazenamento, clique em **aplicar**.

7. Insira um **agrupamento** para o banco de dados em branco (para este tutorial, use o valor padrão). Para obter mais informações sobre agrupamentos, veja [Agrupamentos](/sql/t-sql/statements/collations)

8. Agora que você concluiu o formulário do **banco de dados SQL** , clique em **criar** para provisionar o banco de dados individual. Esta etapa pode levar alguns minutos.

9. Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.

   ![notificação](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Criar uma regra de firewall de IP no nível de servidor

O serviço do banco de dados SQL cria um firewall IP no nível do servidor. Esse Firewall impede que aplicativos e ferramentas externos se conectem ao servidor e a qualquer banco de dados no servidor, a menos que uma regra de firewall permita seu IP por meio do firewall. Para habilitar a conectividade externa ao seu banco de dados individual, você deve primeiro adicionar uma regra de firewall IP para seu endereço IP (ou intervalo de endereços IP). Siga estas etapas para criar uma [regra de firewall de IP no nível de servidor do banco de dados SQL](sql-database-firewall-configure.md).

> [!IMPORTANT]
> O serviço do banco de dados SQL se comunica pela porta 1433. Se você estiver tentando se conectar a esse serviço de dentro de uma rede corporativa, o tráfego de saída pela porta 1433 pode não ser permitido pelo firewall da sua rede. Nesse caso, você não poderá se conectar ao banco de dados individual, a menos que o administrador Abra a porta 1433.

1. Após a conclusão da implantação, clique em **bancos de dados SQL** no menu à esquerda e, em seguida, clique em *yourDatabase* na página **bancos de dados SQL** . A página Visão geral do seu banco de dados é aberta, mostrando o nome totalmente qualificado do **servidor** (como *yourserver.Database.Windows.net*) e fornece opções para configuração adicional.

2. Copie esse nome de servidor totalmente qualificado para usar para se conectar ao seu servidor e bancos de dados do SQL Server Management Studio.

   ![nome do servidor](./media/sql-database-design-first-database/server-name.png)

3. Clique em **Definir firewall do servidor** na barra de ferramentas. É aberta a página **Definições da firewall** do servidor da Base de Dados SQL.

   ![regra de firewall de IP de nível de servidor](./media/sql-database-design-first-database/server-firewall-rule.png)

4. Clique em **Adicionar IP do cliente** na barra de ferramentas para adicionar o endereço IP atual a uma nova regra de firewall de IP. Uma regra de firewall IP pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

5. Clique em **Guardar**. Uma regra de firewall de IP no nível de servidor é criada para o endereço IP atual abrindo a porta 1433 no servidor do banco de dados SQL.

6. Clique em **OK** e, em seguida, feche a página **Definições da firewall**.

Seu endereço IP agora pode passar pelo firewall de IP. Agora você pode se conectar ao seu banco de dados individual usando SQL Server Management Studio ou outra ferramenta de sua escolha. Certifique-se de usar a conta de administrador do servidor que você criou anteriormente.

> [!IMPORTANT]
> Por padrão, o acesso por meio do firewall de IP do banco de dados SQL está habilitado para todos os serviços do Azure. Clique em **DESATIVAR** nesta página para desativar todos os serviços do Azure.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você aprendeu tarefas básicas de banco de dados, como criar um banco de dados e tabelas, conectar-se ao banco de dados, carregar e executar consultas. Aprendeu a:

> [!div class="checklist"]
> * Criar uma base de dados
> * Configurar uma regra de firewall
> * Ligar à base de dados com o [Visual Studio e o C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Criar tabelas
> * Inserir, atualizar, excluir e consultar dados

Avance para o próximo tutorial para saber mais sobre a migração de dados.

> [!div class="nextstepaction"]
> [Migrar o SQL Server para a Base de Dados SQL do Azure offline com o DMS](../dms/tutorial-sql-server-to-azure-sql.md)
