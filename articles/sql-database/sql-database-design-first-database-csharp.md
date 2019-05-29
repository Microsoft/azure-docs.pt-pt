---
title: Conceber a sua primeira base de dados relacional - C# -base de dados SQL do Azure | Documentos da Microsoft
description: Saiba como criar a sua primeira base de dados relacional numa base de dados na base de dados do Azure SQL com o C# usando o ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
manager: craigg-msft
ms.date: 02/08/2019
ms.openlocfilehash: 31246f44be5645715c5c7041d0cf9bcff9c0fa52
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66303291"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-cx23-and-adonet"></a>Tutorial: Criar uma base de dados relacional numa base de dados no Azure SQL da base de dados C&#x23; e o ADO.NET

Base de dados SQL do Azure é um relacional da base de dados-como-serviço (DBaaS) no Microsoft Cloud (Azure). Neste tutorial, vai aprender a utilizar o portal do Azure e o ADO.NET com o Visual Studio para:

> [!div class="checklist"]
> * Criar uma base de dados com o portal do Azure *
> * Configurar uma regra de firewall do IP de ao nível do servidor com o portal do Azure
> * Ligar à base de dados com o ADO.NET e o Visual Studio
> * Criar tabelas com o ADO.NET
> * Inserir, atualizar e eliminar os dados com o ADO.NET
> * Consultar dados com o ADO.NET

* Se não tiver uma subscrição do Azure, [criar uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Uma instalação do [Visual Studio 2019](https://www.visualstudio.com/downloads/) ou posterior.

## <a name="create-a-blank-single-database"></a>Criar uma base de dados única em branco

Uma base de dados na base de dados do Azure SQL é criado com um conjunto definido de recursos de computação e armazenamento. A base de dados é criada dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) e é gerido através de um [servidor de base de dados](sql-database-servers.md).

Siga estes passos para criar uma base de dados em branco.

1. Clique em **Criar um recurso**, no canto superior esquerdo do portal do Azure.
2. Na página **Nova**, selecione **Bases de Dados** na secção de Microsoft Azure Marketplace e, em seguida, clique em **Base de Dados SQL** na secção **Em Destaque**.

   ![create empty-database](./media/sql-database-design-first-database/create-empty-database.png)

3. Preencha os **base de dados SQL** de formulário com as informações seguintes, conforme mostrado na imagem anterior:

    | Definição       | Valor sugerido | Descrição |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nome da base de dados** | *yourDatabase* | Para nomes de base de dados válida, veja [identificadores de bases de dados](/sql/relational-databases/databases/database-identifiers). |
    | **Subscrição** | *yourSubscription*  | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
    | **Grupo de recursos** | *yourResourceGroup* | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). |
    | **Selecionar origem** | Base de dados vazia | Especifica que deve ser criada uma base de dados vazia. |

4. Clique em **servidor** para utilizar um servidor de base de dados existente ou criar e configurar um novo servidor de base de dados. Selecione um servidor existente ou clique em **criar um novo servidor** e preencha a **novo servidor** formulário com as seguintes informações:

    | Definição       | Valor sugerido | Descrição |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nome do servidor** | Qualquer nome globalmente exclusivo | Para nomes de servidores válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). |
    | **Início de sessão de administrador do servidor** | Qualquer nome válido | Para nomes de início de sessão válido, veja [identificadores de bases de dados](/sql/relational-databases/databases/database-identifiers). |
    | **Palavra-passe** | Qualquer palavra-passe válida | A palavra-passe tem de ter, pelo menos, oito carateres e tem de utilizar carateres de três das seguintes categorias: carateres maiúsculos, carateres minúsculos, números e carateres não alfanuméricos. |
    | **Localização** | Nenhuma localização válida | Para obter mais informações sobre regiões, veja [Azure Regions](https://azure.microsoft.com/regions/) (Regiões do Azure). |

    ![criar servidor de base de dados](./media/sql-database-design-first-database/create-database-server.png)

5. Clique em **Selecionar**.
6. Clique em **Escalão de preço** para especificar o escalão de serviço, o número de DTUs ou vCores e a quantidade de armazenamento. Pode explorar as opções para o número de DTUs/vCores e armazenamento que está disponível para cada escalão de serviço.

    Depois de selecionar o escalão de serviço, o número de DTUs ou vCores e a quantidade de armazenamento, clique em **aplicar**.

7. Introduza um **agrupamento** para a base de dados em branco (para este tutorial, utilize o valor predefinido). Para obter mais informações sobre agrupamentos, veja [Agrupamentos](/sql/t-sql/statements/collations)

8. Agora que concluiu o **base de dados SQL** de formulário, clique em **criar** para aprovisionar a base de dados. Este passo pode demorar alguns minutos.

9. Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.

   ![notificação](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Criar uma regra de firewall do IP ao nível do servidor

O serviço de base de dados SQL cria uma firewall do IP ao nível do servidor. Esta firewall impede que aplicações e ferramentas externas estabeleçam uma ligação ao servidor e a quaisquer bases de dados no servidor, a menos que uma regra de firewall permite que seus IP através da firewall. Para ativar a conectividade externa à sua base de dados individual, primeiro tem de adicionar uma regra de firewall do IP para o seu endereço IP (ou o intervalo de endereços IP). Siga estes passos para criar uma [regra de firewall IP de ao nível do servidor de base de dados SQL](sql-database-firewall-configure.md).

> [!IMPORTANT]
> O serviço de base de dados SQL comunica através da porta 1433. Se estiver a tentar ligar a este serviço a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Se assim for, não é possível ligar à base de dados único, a menos que o administrador abra a porta 1433.

1. Depois de concluída a implementação, clique em **bases de dados SQL** no menu do lado esquerdo e clique em *yourDatabase* sobre o **bases de dados SQL** página. Abre a página de descrição geral da base de dados, mostrando a completamente qualificado **nome do servidor** (tal como *yourserver.database.windows.net*) e oferece opções para configuração adicional.

2. Copie este nome de servidor completamente qualificado para utilizar para ligar ao servidor e bases de dados do SQL Server Management Studio.

   ![nome do servidor](./media/sql-database-design-first-database/server-name.png)

3. Clique em **Definir firewall do servidor** na barra de ferramentas. É aberta a página **Definições da firewall** do servidor da Base de Dados SQL.

   ![regra de firewall do IP ao nível do servidor](./media/sql-database-design-first-database/server-firewall-rule.png)

4. Clique em **Adicionar IP de cliente** na barra de ferramentas para adicionar o seu endereço IP atual a uma nova regra de firewall do IP. Uma regra de firewall do IP pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

5. Clique em **Guardar**. Uma regra de firewall do IP ao nível do servidor é criada para o seu endereço IP atual abrindo a porta 1433 no servidor de base de dados SQL.

6. Clique em **OK** e, em seguida, feche a página **Definições da firewall**.

Agora pode transmitir o seu endereço IP através da firewall do IP. Pode agora ligar à base de dados único com SQL Server Management Studio ou outra ferramenta à sua escolha. Certifique-se de que utilizar a conta de administrador de servidor que criou anteriormente.

> [!IMPORTANT]
> Por predefinição, o acesso através da firewall do IP de base de dados SQL está ativado para todos os serviços do Azure. Clique em **DESATIVAR** nesta página para desativar todos os serviços do Azure.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu tarefas de base de dados básica, tais como criam uma base de dados e tabelas, ligar à base de dados, carregar dados e executar consultas. Aprendeu a:

> [!div class="checklist"]
> * Criar uma base de dados
> * Configurar uma regra de firewall
> * Ligar à base de dados com o [Visual Studio e o C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Criar tabelas
> * INSERT, update, delete e consultar dados

Avance para o próximo tutorial para saber mais sobre a migração de dados.

> [!div class="nextstepaction"]
> [Migrar o SQL Server para a Base de Dados SQL do Azure offline com o DMS](../dms/tutorial-sql-server-to-azure-sql.md)
