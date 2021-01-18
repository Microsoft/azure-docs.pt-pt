---
title: Registar e digitalizar Azure SQL Database Managed Instance
description: Este tutorial descreve como digitalizar Azure SQL Database Managed Instance
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 3513ba0cd1a894b55da604d54964affa79b6adf4
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555972"
---
# <a name="register-and-scan-an-azure-sql-database-managed-instance"></a>Registe e digitalize uma caixa de dados de base de dados Azure SQL

Este artigo descreve como registar uma fonte de dados gerida por frequências de dados Azure SQL em Purview e configurar uma verificação sobre o mesmo.

## <a name="supported-capabilities"></a>Capacidades suportadas

A fonte de dados gerida por exemplo da Azure SQL suporta a seguinte funcionalidade:

- **Varreduras completas e incrementais** para capturar metadados e classificação em Azure SQL Database Managed Instance.

- **Linhagem** entre ativos de dados para a cópia ADF e atividades de fluxo de dados.

### <a name="known-limitations"></a>Limitações conhecidas

O Azure Purview não suporta a digitalização de [visualizações](/sql/relational-databases/views/views?view=azuresqldb-mi-current&preserve-view=true) em Azure SQL Managed Instance.

## <a name="prerequisites"></a>Pré-requisitos

- Crie uma nova conta Desemis se ainda não tiver uma.

- [Configure o ponto final público em Azure SQL Gestd Instance](/azure/azure-sql/managed-instance/public-endpoint-configure)
    > [!Note]
    > A sua organização deve ser capaz de permitir o ponto final público, uma vez **que o ponto final privado ainda não é apoiado** pela Purview. Se utilizar o ponto final privado, a varredura não será bem sucedida.

### <a name="setting-up-authentication-for-a-scan"></a>Configuração da autenticação para uma varredura

Autenticação para digitalizar Azure SQL Database Managed Instance. Se precisar de criar uma nova autenticação, tem de autorizar o acesso à [base de dados para a Sql Database Managed Instance](/azure/azure-sql/database/logins-create-manage). Existem três métodos de autenticação que a Purview suporta hoje:

- Autenticação do SQL
- Principal de Serviço
- Identidade Gerida

#### <a name="sql-authentication"></a>Autenticação do SQL

> [!Note]
> Apenas o login principal de nível de servidor (criado pelo processo de provisionamento) ou os membros da função de `loginmanager` base de dados na base de dados principal podem criar novos logins. Demora cerca de **15 minutos** após a concessão de permissão, a conta Purview deve ter as permissões adequadas para poder digitalizar os recursos.

Pode seguir as instruções no [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) para criar um login para Azure SQL Database Managed Instance se não tiver este disponível. Vai precisar de **nome de utilizador** e **senha** para os próximos passos.

1. Navegue até ao cofre chave no portal Azure
1. Selecione **Definições > Segredos**
1. Selecione **+ Gerar/Importar** e insira o **Nome** e **Valor** como *palavra-passe* da sua Placa Gerida da Base de Dados Azure SQL
1. Selecione **Criar** para completar
1. Se o cofre da chave ainda não estiver ligado ao Purview, terá de [criar uma nova ligação](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) ao cofre de chaves
1. Finalmente, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando o nome de **utilizador** e **senha** para configurar a sua digitalização

#### <a name="service-principal-and-managed-identity"></a>Diretor de serviço e identidade gerida

Existem vários passos para permitir que a Purview utilize o principal do serviço para digitalizar a sua Azure SQL Database Managed Instance

##### <a name="create-or-use-an-existing-service-principal"></a>Criar ou utilizar um principal de serviço existente

> [!Note]
> Ignore este passo se estiver a usar **identidade gerida**

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
- [Configure e gere a autenticação AD AD com Azure SQL](/azure/azure-sql/database/authentication-aad-configure)
- Criar um utilizador AZure AD em Azure SQL Database Managed Instance seguindo os pré-requisitos e tutoriais na [Criar utilizadores contidos mapeados para identidades AD Azure](/azure/azure-sql/database/authentication-aad-configure?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities)
- Atribuir `db_owner` **(recomendado)** permissão à identidade

##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Adicione o principal de serviço ao cofre-chave e à credencial de Purview

> [!Note]
> Se está a planear usar a **identidade gerida,** pode saltar este passo porque a identidade padrão do Purview já está em **Purview-MSI**

É necessário obter o ID de aplicação do diretor do serviço e segredo:

1. Navegue para o seu Diretor de Serviço no [portal Azure](https://portal.azure.com)
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

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="registar opções de fontes" border="true":::
    
> [!Note]
> Atualmente, o Azure Purview não suporta a configuração VNET. Portanto, não é possível fazer definições de firewall baseadas em IP.

## <a name="register-an-azure-sql-database-managed-instance-data-source"></a>Registar uma fonte de dados gerida por exemplos de dados da Azure SQL

1. Navegue para a sua conta Desemis

1. Selecione **Fontes** na navegação à esquerda

1. Selecione **Registar-se**

1. Selecione **Azure SQL Database Managed Instance** e, em seguida, **Continue**

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="Configurar a fonte de dados SQL":::

1. Selecione **Entrar manualmente**

1. Forneça ao ponto final público o nome de domínio e **o número de porta** totalmente **qualificados** . Em seguida, **selecione Terminar** para registar a fonte de dados.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="Adicionar Azure SQL Base de Dados Caso Gerido":::

    Por exemplo, `foobar.public.123.database.windows.net,3342`

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> A eliminação da sua verificação não elimina os seus ativos de verificações anteriores da Base de Dados Azure SQL.

## <a name="next-steps"></a>Passos seguintes

- [Navegue no catálogo de dados Azure Purview Data](how-to-browse-catalog.md)
- [Pesse o Catálogo de Dados da Azure Purview](how-to-search-catalog.md)
