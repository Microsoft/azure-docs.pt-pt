---
title: Como digitalizar a Azure Synapse Analytics
description: Esta forma de orientar descreve detalhes de como digitalizar a Azure Synapse Analytics.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: d287f5dc239339f79d2d8237e7739de7793920c4
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108613"
---
# <a name="register-and-scan-azure-synapse-analytics"></a>Registar e digitalizar Azure Synapse Analytics

Este artigo discute como registar e digitalizar um exemplo de Azure Synapse Analytics (anteriormente SQL DW) em Purview.

## <a name="supported-capabilities"></a>Capacidades suportadas

A Azure Synapse Analytics (anteriormente SQL DW) suporta digitalizações completas e incrementais para capturar os metadados e esquemas. As análises também classificam os dados automaticamente com base nas regras de classificação do sistema e personalizados.

### <a name="known-limitations"></a>Limitações conhecidas

Azure Purview não suporta digitalização de [pontos](/sql/relational-databases/views/views?view=azure-sqldw-latest&preserve-view=true) de vista no Azure Synapse Analytics

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registar fontes de dados, crie uma conta Azure Purview. Para obter mais informações sobre a criação de uma conta Desemis, consulte [Quickstart: Criar uma conta Azure Purview](create-catalog-portal.md).
- Você precisa ser um Azure Purview Data Source Admin
- Acesso em rede entre a conta Purview e Azure Synapse Analytics.
 
## <a name="setting-up-authentication-for-a-scan"></a>Configuração da autenticação para uma varredura

Existem três formas de configurar a autenticação para a Azure Synapse Analytics:

- Identidade Gerida
- Autenticação SQL
- Principal de Serviço

    > [!Note]
    > Apenas o login principal de nível de servidor (criado pelo processo de provisionamento) ou os membros da função de `loginmanager` base de dados na base de dados principal podem criar novos logins. Demora cerca de 15 minutos após a concessão de permissão, a conta Purview deve ter as permissões adequadas para poder digitalizar os recursos.

### <a name="managed-identity-recommended"></a>Identidade gerida (Recomendada) 
   
A sua conta Depview tem a sua própria Identidade Gerida, que é basicamente o seu nome Depview quando a criou. Tem de criar um utilizador Azure AD em Azure Synapse Analytics (anteriormente SQL DW) com o nome exato de Identidade Gerida da Purview, seguindo os pré-requisitos e tutoriais sobre [utilizadores de AD Create Azure usando aplicações AD Azure](../azure-sql/database/authentication-aad-service-principal-tutorial.md).

Exemplo de sintaxe SQL para criar o utilizador e conceder permissão:

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [PurviewManagedIdentity]
GO
```

A autenticação deve ter permissão para obter metadados para a base de dados, esquemas e tabelas. Deve também poder consultar as tabelas a amostrar para a classificação. A recomendação é atribuir `db_owner` permissão à identidade.

### <a name="service-principal"></a>Principal de Serviço

Para utilizar a autenticação principal do serviço para digitalizações, pode utilizar uma existente ou criar uma nova. 

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

É necessário obter a iD de aplicação do diretor de serviço e segredo:

1. Navegue para o seu Diretor de Serviço no [portal Azure](https://portal.azure.com)
1. Copie os valores que o ID da **Aplicação (cliente)** do **Resumo** e **do Segredo** do Cliente a partir de **Certificados & segredos.**
1. Navegue até ao cofre da chave
1. Selecione **Definições > Segredos**
1. Selecione **+ Gerar/Importar** e insira o **Nome** da sua escolha e **Valor** como segredo do **Cliente** do seu Diretor de Serviço
1. Selecione **Criar** para completar
1. Se o cofre da chave ainda não estiver ligado ao Purview, terá de [criar uma nova ligação](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) ao cofre de chaves
1. Finalmente, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando o Principal de Serviço para configurar a sua digitalização 

#### <a name="granting-the-service-principal-access-to-your-azure-synapse-analytics-formerly-sql-dw"></a>Concessão do acesso principal do serviço ao seu Azure Synapse Analytics (anteriormente SQL DW)

Além disso, deve também criar um utilizador Azure AD em Azure Synapse Analytics seguindo os pré-requisitos e tutoriais sobre [utilizadores de AD Create Azure usando aplicações AD Azure](../azure-sql/database/authentication-aad-service-principal-tutorial.md). Exemplo de sintaxe SQL para criar o utilizador e conceder permissão:

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

ALTER ROLE db_owner ADD MEMBER [ServicePrincipalName]
GO
```

> [!Note]
> A Competência necessitará da Identificação da **Aplicação (cliente)** e do segredo do **cliente** para poder digitalizar.

### <a name="sql-authentication"></a>Autenticação do SQL

Pode seguir as instruções no [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-1) para criar um login para a Azure Synapse Analytics (anteriormente SQL DW) se ainda não tiver uma.

Quando o método de autenticação selecionado for **a autenticação SQL,** tem de obter a sua palavra-passe e armazenar no cofre da chave:

1. Obtenha a palavra-passe para o seu login SQL
1. Navegue até ao cofre da chave
1. Selecione **Definições > Segredos**
1. Selecione **+ Gerar/Importar** e insira o **Nome** e **Valor** como *palavra-passe* para o seu login SQL
1. Selecione **Criar** para completar
1. Se o cofre da chave ainda não estiver ligado ao Purview, terá de [criar uma nova ligação](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) ao cofre de chaves
1. Finalmente, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando a chave para configurar o seu scan

## <a name="register-an-azure-synapse-analytics-instance-formerly-sql-dw"></a>Registar um exemplo de Azure Synapse Analytics (anteriormente SQL DW)

Para registar um novo servidor Azure Synapse Analytics no seu Catálogo de Dados, faça o seguinte:

1. Navegue para a sua conta Desemis
1. Selecione **Fontes** na navegação à esquerda
1. Selecione **Registar-se**
1. Nas **fontes de registo**, selecione **Azure Synapse Analytics (anteriormente SQL DW)**
1. Selecione **Continuar**

No ecrã **'Azure Synapse Analytics',** faça o seguinte:

1. Introduza um **Nome** que a fonte de dados será listada no Catálogo.
1. Escolha como pretende apontar para o servidor SQL lógico desejado:
   1. **Selecione A partir da subscrição Azure**, selecione a subscrição apropriada da caixa de entrega de **subscrição Azure** e o servidor apropriado a partir da caixa de entrega do **nome do Servidor.**
   1. Ou, pode selecionar **Entrar manualmente** e inserir um **nome de Servidor**.
1. **Termine** para registar a fonte de dados.

:::image type="content" source="media/register-scan-azure-synapse-analytics/register-sources.png" alt-text="registar opções de fontes" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Passos seguintes

- [Navegue no catálogo de dados Azure Purview Data](how-to-browse-catalog.md)
- [Pesse o Catálogo de Dados da Azure Purview](how-to-search-catalog.md)
