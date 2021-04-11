---
title: Registar e digitalizar base de dados Azure SQL
description: Este tutorial descreve como digitalizar a Base de Dados Azure SQL
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 74a8bc4209696682c294bccab450d25ae86e3645
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643932"
---
# <a name="register-and-scan-an-azure-sql-database"></a>Registe-se e digitalize uma Base de Dados Azure SQL

Este artigo descreve como registar uma fonte de dados da Base de Dados Azure SQL em Purview e configurar uma verificação sobre o mesmo.

## <a name="supported-capabilities"></a>Capacidades suportadas

A fonte de dados da Base de Dados Azure SQL suporta a seguinte funcionalidade:

- **Varreduras completas e incrementais** para capturar metadados e classificação na Base de Dados Azure SQL.

- **Linhagem** entre ativos de dados para a cópia ADF e atividades de fluxo de dados.

### <a name="known-limitations"></a>Limitações conhecidas

O Azure Purview não suporta a digitalização de [visualizações](/sql/relational-databases/views/views?view=azuresqldb-current&preserve-view=true) na Base de Dados Azure SQL.

## <a name="prerequisites"></a>Pré-requisitos

1. Crie uma nova conta Desemis se ainda não tiver uma.

1. Acesso em rede entre a conta Desreserva e Azure SQL Database.


### <a name="set-up-authentication-for-a-scan"></a>Configurar a autenticação para uma varredura

Autenticação para digitalizar base de dados Azure SQL. Se precisar de criar uma nova autenticação, tem de autorizar o acesso à [base de dados à Base de Dados SQL.](../azure-sql/database/logins-create-manage.md) Existem três métodos de autenticação que a Purview suporta hoje:

- Autenticação do SQL
- Principal de Serviço
- Identidade Gerida

#### <a name="sql-authentication"></a>Autenticação do SQL

> [!Note]
> Apenas o login principal de nível de servidor (criado pelo processo de provisionamento) ou os membros da função de `loginmanager` base de dados na base de dados principal podem criar novos logins. Demora cerca de **15 minutos** após a concessão de permissão, a conta Purview deve ter as permissões adequadas para poder digitalizar os recursos.

Pode seguir as instruções no [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) para criar um login para a Base de Dados Azure SQL se não tiver esta informação disponível. Vai precisar de **nome de utilizador** e **senha** para os próximos passos.

1. Navegue até ao cofre chave no portal Azure
1. Selecione **Definições > Segredos**
1. Selecione **+ Gerar/Importar** e insira o **Nome** e **Valor** como *palavra-passe* da sua Base de Dados Azure SQL
1. Selecione **Criar** para completar
1. Se o cofre da chave ainda não estiver ligado ao Purview, terá de [criar uma nova ligação](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) ao cofre de chaves
1. Finalmente, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando o nome de **utilizador** e **senha** para configurar a sua digitalização

#### <a name="service-principal-and-managed-identity"></a>Diretor de serviço e identidade gerida

Existem vários passos para permitir que a Purview utilize o principal de serviço ou a **identidade gerida** da Purview para digitalizar a sua Base de Dados Azure SQL

   > [!Note]
   > A Competência necessitará da Identificação da **Aplicação (cliente)** e do segredo do **cliente** para poder digitalizar.

##### <a name="create-or-use-an-existing-service-principal"></a>Criar ou utilizar um principal de serviço existente

> [!Note]
> Ignore este passo se estiver a usar a **identidade gerida** do Purview

Para utilizar um principal de serviço, pode utilizar um existente ou criar um novo. 

> [!Note]
> Se tiver de criar um novo Diretor de Serviços, siga estes passos:
> 1. Navegue até ao [portal Azure.](https://portal.azure.com)
> 1. Selecione **Azure Ative Directory** a partir do menu do lado esquerdo.
> 1. Selecione **Registos de aplicações**.
> 1. Selecione **+ Novo registo de inscrição.**
> 1. Introduza um nome para a **inscrição** (nome principal do serviço).
> 1. Selecione **Contas apenas neste diretório organizacional**.
> 1. Para redirecionar uri selecione **Web** e introduza qualquer URL que pretenda; não tem que ser real ou trabalho.
> 1. Em seguida, selecione **Registar**.

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Configurar a autenticação Azure AD na conta de base de dados

O principal do serviço ou identidade gerida deve ter permissão para obter metadados para a base de dados, esquemas e tabelas. Deve também poder consultar as tabelas a amostrar para a classificação.

- [Configure e gere a autenticação AD AD com Azure SQL](../azure-sql/database/authentication-aad-configure.md)
- Se estiver a usar identidade gerida, a sua conta Purview tem a sua própria identidade gerida, que é basicamente o seu nome Depview quando a criou. Tem de criar um utilizador AZure AD na Base de Dados Azure SQL com a identidade gerida exata da Purview ou com o seu próprio chefe de serviço, seguindo o tutorial sobre [criar o utilizador principal do serviço na Base de Dados Azure SQL](../azure-sql/database/authentication-aad-service-principal-tutorial.md#create-the-service-principal-user-in-azure-sql-database). Tem de atribuir a devida permissão (por `db_owner` exemplo, `db_datareader` ou) à identidade. Exemplo de sintaxe SQL para criar o utilizador e conceder permissão:

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [Username]
    GO
    ```

    > [!Note]
    > É `Username` o seu próprio chefe de serviço ou a identidade gerida de Purview. Pode ler mais sobre [funções de base de dados fixas e suas capacidades.](/sql/relational-databases/security/authentication-access/database-level-roles#fixed-database-roles)
    
##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Adicione o principal de serviço ao cofre-chave e à credencial de Purview

> [!Note]
> Se está a planear usar a **identidade gerida** do Purview, pode saltar este passo porque a identidade gerida por Defeito da Purview já se encontra na credencial **Purview-MSI.**

É necessário obter o ID de aplicação do diretor do serviço e segredo:

1. Navegue para o seu diretor de serviço no [portal Azure](https://portal.azure.com)
1. Copie os valores que o ID da **Aplicação (cliente)** do **Resumo** e **do Segredo** do Cliente a partir de **Certificados & segredos.**
1. Navegue até ao cofre da chave
1. Selecione **Definições > Segredos**
1. Selecione **+ Gerar/Importar** e insira o **Nome** da sua escolha e **Valor** como segredo do **Cliente** do seu Diretor de Serviço
1. Selecione **Criar** para completar
1. Se o cofre da chave ainda não estiver ligado ao Purview, terá de [criar uma nova ligação](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) ao cofre de chaves
1. Finalmente, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando o Principal de Serviço para configurar a sua digitalização

### <a name="firewall-settings"></a>Definições de firewall

O seu servidor de base de dados deve permitir ativar as ligações Azure. Isto permitirá ao Azure Purview alcançar e ligar o servidor. Pode seguir o guia de como fazer [as ligações a partir do interior do Azure.](../azure-sql/database/firewall-configure.md#connections-from-inside-azure)

1. Navegue para a sua conta de base de dados
1. Selecione o nome do servidor na página **'Vista Geral'**
1. Selecione **Firewalls > de Segurança e redes virtuais**
1. Selecione **Sim** para **permitir que os serviços e recursos da Azure acedam a este servidor**

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="Permitir que os serviços e recursos da Azure acedam a este servidor." border="true":::
    
> [!Note]
> Atualmente, o Azure Purview não suporta a configuração VNET. Portanto, não é possível fazer definições de firewall baseadas em IP.

## <a name="register-an-azure-sql-database-data-source"></a>Registar uma fonte de dados da Base de Dados Azure SQL

Para registar uma nova Base de Dados Azure SQL no seu catálogo de dados, faça o seguinte:

1. Navegue para a sua conta Desemis

1. Selecione **Fontes** na navegação à esquerda

1. Selecione **Registar-se**

1. Nas **fontes de registo**, selecione **Azure SQL Database**. Selecione **Continuar**.

:::image type="content" source="media/register-scan-azure-sql-database/register-new-data-source.png" alt-text="registar nova fonte de dados" border="true":::

No ecrã **'Azure SQL Database',** faça o seguinte:

1. Introduza um **Nome** que a fonte de dados será listada no Catálogo.
1. **Selecione A partir da subscrição Azure**, selecione a subscrição apropriada da caixa de entrega de **subscrição Azure** e o servidor apropriado a partir da caixa de entrega do **nome do Servidor.**
1. **Termine** para registar a fonte de dados.

:::image type="content" source="media/register-scan-azure-sql-database/add-azure-sql-database.png" alt-text="registar opções de fontes" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> * A eliminação da sua verificação não elimina os seus ativos de análises anteriores da Base de Dados Azure SQL.
> * O ativo deixará de ser atualizado com alterações de esquema se a sua tabela de origem for alterada e rescandatar a tabela de origem após a edição da descrição no separador esquema de Purview.

## <a name="next-steps"></a>Passos seguintes

- [Navegue no catálogo de dados Azure Purview Data](how-to-browse-catalog.md)
- [Pesse o Catálogo de Dados da Azure Purview](how-to-search-catalog.md)
