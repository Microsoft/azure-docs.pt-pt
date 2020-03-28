---
title: 'Desenhe a sua primeira base de dados relacional C #'
description: Aprenda a desenhar a sua primeira base de dados relacional numa única base de dados em Azure SQL Database com C# usando ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-lt-2019
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
ms.date: 07/29/2019
ms.openlocfilehash: 0f1140bbefc7508666e763fcd4f1a04ba48cdfdd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75354948"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-cx23-and-adonet"></a>Tutorial: Desenhe uma base de dados relacional numa única base de dados dentro da Base de Dados Azure SQL C&#x23; e ADO.NET

A Base de Dados Azure SQL é uma base de dados relacional (DBaaS) no Microsoft Cloud (Azure). Neste tutorial, vai aprender a utilizar o portal do Azure e o ADO.NET com o Visual Studio para:

> [!div class="checklist"]
> * Criar uma única base de dados utilizando o portal Azure*
> * Configurar uma regra de firewall IP ao nível do servidor utilizando o portal Azure
> * Ligar à base de dados com o ADO.NET e o Visual Studio
> * Criar tabelas com o ADO.NET
> * Inserir, atualizar e eliminar os dados com o ADO.NET
> * Consultar dados com o ADO.NET

*Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!TIP]
> O seguinte módulo Microsoft Learn ajuda-o a aprender gratuitamente como [desenvolver e configurar uma aplicação ASP.NET que questiona uma Base de Dados Azure SQL](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/), incluindo a criação de uma simples base de dados.

## <a name="prerequisites"></a>Pré-requisitos

Uma instalação do [Visual Studio 2019](https://www.visualstudio.com/downloads/) ou mais tarde.

## <a name="create-a-blank-single-database"></a>Criar uma base de dados única em branco

Uma única base de dados na Base de Dados Azure SQL é criada com um conjunto definido de recursos de computação e armazenamento. A base de dados é criada dentro de um grupo de [recursos Azure](../azure-resource-manager/management/overview.md) e é gerida através de um servidor de base de [dados](sql-database-servers.md).

Siga estes passos para criar uma base de dados única em branco.

1. Clique em **Criar um recurso** no canto superior esquerdo do portal Azure.
2. Na página **Nova**, selecione **Bases de Dados** na secção de Microsoft Azure Marketplace e, em seguida, clique em **Base de Dados SQL** na secção **Em Destaque**.

   ![create empty-database](./media/sql-database-design-first-database/create-empty-database.png)

3. Preencha o formulário de Base de **Dados SQL** com as seguintes informações, conforme mostrado na imagem anterior:

    | Definição       | Valor sugerido | Descrição |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nome da base de dados** | *sua Base de Dados* | Para obter nomes de base de dados [válidos, consulte os identificadores da Base de Dados](/sql/relational-databases/databases/database-identifiers). |
    | **Assinatura** | *sua Subscrição*  | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
    | **Grupo de recursos** | *seuGrupo de Recursos* | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Atribuição de nomes de regras e restrições). |
    | **Selecione fonte** | Base de dados vazia | Especifica que deve ser criada uma base de dados vazia. |

4. Clique no **Server** para utilizar um servidor de base de dados existente ou criar e configurar um novo servidor de base de dados. Selecione um servidor existente ou clique **em Criar um novo servidor** e preencher o formulário do Novo **servidor** com as seguintes informações:

    | Definição       | Valor sugerido | Descrição |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nome do servidor** | Qualquer nome globalmente exclusivo | Para nomes de servidores válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Atribuição de nomes de regras e restrições). |
    | **Login de administrador do servidor** | Qualquer nome válido | Para obter nomes válidos de login, consulte [identificadores de base de dados](/sql/relational-databases/databases/database-identifiers). |
    | **Palavra-passe** | Qualquer palavra-passe válida | A sua palavra-passe deve ter pelo menos oito caracteres e deve utilizar caracteres de três das seguintes categorias: caracteres maiúsculos, caracteres minúsculos, números e caracteres não alfanuméricos. |
    | **Localização** | Nenhuma localização válida | Para obter mais informações sobre regiões, veja [Azure Regions](https://azure.microsoft.com/regions/) (Regiões do Azure). |

    ![criar servidor de base de dados](./media/sql-database-design-first-database/create-database-server.png)

5. Clique em **Selecionar**.
6. Clique em **Escalão de preço** para especificar o escalão de serviço, o número de DTUs ou vCores e a quantidade de armazenamento. Poderá explorar as opções para o número de DTUs/vCores e armazenamento que está disponível para cada nível de serviço.

    Depois de selecionar o nível de serviço, o número de DTUs ou vCores, e a quantidade de armazenamento, clique **em Aplicar**.

7. Introduza uma **Colagem** para a base de dados em branco (para este tutorial, utilize o valor predefinido). Para obter mais informações sobre agrupamentos, veja [Agrupamentos](/sql/t-sql/statements/collations)

8. Agora que completou o formulário de Base de **Dados SQL,** clique em **Criar** para fornecer a base de dados única. Este passo pode demorar alguns minutos.

9. Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.

   ![notificação](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Criar uma regra de firewall IP ao nível do servidor

O serviço de base de dados SQL cria uma firewall IP ao nível do servidor. Esta firewall impede que aplicações e ferramentas externas se conectem ao servidor e a quaisquer bases de dados no servidor, a menos que uma regra de firewall permita o seu IP através da firewall. Para permitir a conectividade externa à sua única base de dados, tem primeiro de adicionar uma regra de firewall IP para o seu endereço IP (ou intervalo de endereço IP). Siga estes passos para criar uma regra de firewall IP de nível de servidor de base [de dados SQL](sql-database-firewall-configure.md).

> [!IMPORTANT]
> O serviço de base de dados SQL comunica sobre a porta 1433. Se estiver a tentar ligar-se a este serviço a partir de uma rede corporativa, o tráfego de saída sobre a porta 1433 pode não ser permitido pela firewall da sua rede. Em caso afirmativo, não poderá ligar-se à sua única base de dados a não ser que o seu administrador abra a porta 1433.

1. Depois de concluída a implementação, clique nas bases de **dados SQL** do menu à esquerda e, em seguida, clique na *sua Base de Dados* na página de bases de dados **SQL.** A página de visão geral da sua base de dados abre-se, mostrando-lhe o **nome do Servidor** totalmente qualificado (como *yourserver.database.windows.net)* e fornece opções para uma configuração posterior.

2. Copie este nome de servidor totalmente qualificado para utilização para ligar ao seu servidor e bases de dados do Estúdio de Gestão de Servidores SQL.

   ![nome do servidor](./media/sql-database-design-first-database/server-name.png)

3. Clique em **Definir firewall do servidor** na barra de ferramentas. É aberta a página **Definições da firewall** do servidor da Base de Dados SQL.

   ![regra de firewall IP de nível servidor](./media/sql-database-design-first-database/server-firewall-rule.png)

4. Clique em **Adicionar IP** do cliente na barra de ferramentas para adicionar o seu endereço IP atual a uma nova regra de firewall IP. Uma regra de firewall IP pode abrir a porta 1433 para um único endereço IP ou uma gama de endereços IP.

5. Clique em **Guardar**. Uma regra de firewall IP de nível de servidor foi criada para a sua porta de abertura de endereço IP atual 1433 no servidor Base de Dados SQL.

6. Clique em **OK** e, em seguida, feche a página **Definições da firewall**.

O seu endereço IP pode agora passar pela firewall IP. Agora pode ligar-se à sua única base de dados utilizando o SQL Server Management Studio ou outra ferramenta à sua escolha. Certifique-se de que utiliza a conta de administração do servidor que criou anteriormente.

> [!IMPORTANT]
> Por predefinição, o acesso através da firewall IP da Base de Dados SQL está ativado para todos os serviços Azure. Clique em **DESATIVAR** nesta página para desativar todos os serviços do Azure.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu tarefas básicas de base de dados como criar uma base de dados e tabelas, conectar-se à base de dados, carregar dados e executar consultas. Aprendeu a:

> [!div class="checklist"]
> * Criar uma base de dados
> * Configurar uma regra de firewall
> * Ligar à base de dados com o [Visual Studio e o C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Criar tabelas
> * Inserir, atualizar, excluir e consultar dados

Avance para o próximo tutorial para saber sobre a migração de dados.

> [!div class="nextstepaction"]
> [Migrar o SQL Server para a Base de Dados SQL do Azure offline com o DMS](../dms/tutorial-sql-server-to-azure-sql.md)
