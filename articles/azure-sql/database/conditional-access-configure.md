---
title: Acesso Condicional
description: Saiba configurar o Acesso Condicional para a Base de Dados Azure SQL, a Instância Gerida Azure SQL e a Azure Synapse Analytics.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 04/28/2020
tag: azure-synpase
ms.openlocfilehash: cc4857c32eca924051ba72fb716e29231327f543
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84043528"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Acesso Condicional (MFA) com Base de Dados Azure SQL e Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Base de Dados Azure SQL,](sql-database-paas-overview.md) [Instância Gerida Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md)e [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) suportam o Microsoft Conditional Access.

Os seguintes passos mostram como configurar a Base de Dados Azure SQL, a Instância Gerida SQL ou a Azure Synapse para impor uma política de acesso condicional (CA).  

## <a name="prerequisites"></a>Pré-requisitos

- Deve configurar a sua base de dados SQL, Instância Gerida SQL ou piscina SQL em Azure Synapse para apoiar a autenticação do Diretório Ativo Azure. Para passos específicos, consulte [Configure e gerea autenticação de Diretório Ativo Azure com Base de Dados SQL ou Azure Synapse](authentication-aad-configure.md).  
- Quando a autenticação de vários fatores estiver ativada, deve ligar-se a uma ferramenta suportada, como o mais recente Estúdio de Gestão de Servidores SQL (SSMS). Para mais informações, consulte a [autenticação multifactor da Configure Azure SQL Database para o Estúdio de Gestão de Servidores SQL](authentication-mfa-ssms-configure.md).  

## <a name="configure-conditional-access"></a>Configurar o acesso condicional

> [!NOTE]
> O exemplo abaixo utiliza a Base de Dados Azure SQL, mas deve selecionar o produto apropriado que pretende configurar o acesso condicional.

1. Inscreva-se no Portal, selecione **Diretório Ativo Azure**e, em seguida, selecione **Acesso Condicional**. Para mais informações, consulte a referência técnica de acesso condicional do [Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference)  
   ![Lâmina de acesso condicional](./media/conditional-access-configure/conditional-access-blade.png)

2. Na lâmina **De Acesso Condicional,** clique em **Nova política,** forneça um nome e clique em **configurar as regras**.  
3. Em **Atribuições**, **selecione Utilizadores e grupos,** verifique **Selecione utilizadores e grupos,** e, em seguida, selecione o utilizador ou grupo para acesso condicional. Clique em **Selecionar**e, em seguida, clique em **Done** para aceitar a sua seleção.  
   ![selecionar utilizadores e grupos](./media/conditional-access-configure/select-users-and-groups.png)  

4. Selecione **aplicações Cloud,** clique em **Selecionar aplicações**. Você vê todas as aplicações disponíveis para Acesso Condicional. Selecione Base de **Dados SQL Azure,** no clique inferior **Selecione,** e, em seguida, clique **em Done**.  
   ![selecionar Base de Dados SQL](./media/conditional-access-configure/select-sql-database.png)  
   Se não encontrar a Base de **Dados Azure SQL** listada na seguinte terceira imagem, complete os seguintes passos:
   - Inscreva-se na sua Base de Dados Azure SQL utilizando SSMS com uma conta de administração Azure AD.  
   - Executar `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER` .  
   - Assine o Azure AD e verifique se a Base de Dados Azure SQL, a SQL Managed Instance ou a Azure Synapse estão listadas nas aplicações do seu AAD.  

5. Selecione **controlos de acesso,** selecione **Grant**e, em seguida, verifique a política que pretende aplicar. Para este exemplo, selecionamos **Exigir autenticação de vários fatores.**  
   ![selecionar acesso de concessão](./media/conditional-access-configure/grant-access.png)  

## <a name="summary"></a>Resumo

A aplicação selecionada (Base de Dados Azure SQL) utilizando o Azure AD Premium, aplica agora a política de acesso condicional selecionada, **a autenticação de vários fatores necessária.**

Para perguntas sobre a Base de Dados Azure SQL e o Azure Synapse sobre a autenticação de vários fatores, <MFAforSQLDB@microsoft.com> contacte.  

## <a name="next-steps"></a>Próximos passos  

Para um tutorial, consulte [Secure your Azure SQL Database](secure-database-tutorial.md).
