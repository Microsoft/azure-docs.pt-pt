---
title: Configurar a autenticação multifator - SQL do Azure | Documentos da Microsoft
description: Saiba como utilizar a autenticação de Multi-Factored com o SSMS para base de dados SQL e SQL Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 453a048bc9e7c2878c0730b9002b10bdeb8c22fa
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316222"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Configurar a autenticação multifator para o SQL Server Management Studio e o Azure AD

Este tópico mostra-lhe como utilizar a autenticação multifator do Azure Active Directory (MFA) com o SQL Server Management Studio. MFA do Azure AD podem ser utilizado ao ligar o SSMS ou SqlPackage.exe para o Azure [base de dados SQL](sql-database-technical-overview.md) e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para uma descrição geral da autenticação multifator de base de dados do Azure SQL, consulte [autenticação Universal com a base de dados SQL e SQL Data Warehouse (suporte do SSMS para a MFA)](sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Este tópico aplica-se ao servidor SQL do Azure, bem como às bases de dados da Base de Dados SQL e do SQL Data Warehouse que são criadas no servidor SQL do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.

## <a name="configuration-steps"></a>Passos de configuração

1. **Configurar um Azure Active Directory** – para obter mais informações, consulte [administrar o seu diretório do Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [integrar as identidades no local com o Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [ Adicionar o seu próprio nome de domínio para o Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure suporta agora a Federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), e [gerir do Azure AD com o Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Configurar a MFA** – para obter instruções passo a passo, consulte [o que é o Azure multi-factor Authentication?](../active-directory/authentication/multi-factor-authentication.md), [condicional acesso (MFA) com o Azure SQL Database e o armazém de dados](sql-database-conditional-access.md). (O acesso condicional completa requer um Premium do Azure Active Directory (Azure AD). MFA limitado é um padrão do Azure AD.)
3. **Configurar a base de dados SQL ou SQL Data Warehouse para autenticação do Azure AD** – para obter instruções passo a passo, consulte [ligar à base de dados SQL ou SQL Data Warehouse por através do Azure autenticação do Active Directory](sql-database-aad-authentication.md).
4. **Baixe o SSMS** – no computador cliente, transfira o SSMS mais recente, a partir [baixar o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Para todas as funcionalidades neste tópico, utilize, pelo menos, Julho de 2017, versão 17,2.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Ligar utilizando a autenticação universal com o SSMS

Os passos seguintes mostram como ligar à base de dados SQL ou SQL Data Warehouse com o SSMS mais recente.

1. Para ligar através da autenticação Universal, sobre o **ligar ao servidor** caixa de diálogo, selecione **Active Directory - Universal com o suporte MFA**. (Se vir **Active Directory Universal Authentication** não estão na versão mais recente do SSMS.)  
   ![1mfa-universal-connect][1]  
2. Concluir o **nome de utilizador** caixa de com as credenciais do Azure Active Directory, no formato `user_name@domain.com`.  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Se estiver a ligar como um utilizador convidado, tem de clicar em **opções**e, no **propriedade de ligação** caixa de diálogo, completa o **ID de inquilino ou nome de domínio do AD** caixa. Para obter mais informações, consulte [autenticação Universal com a base de dados SQL e SQL Data Warehouse (suporte do SSMS para a MFA)](sql-database-ssms-mfa-authentication.md).
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   
4. Como de costume para base de dados SQL e SQL Data Warehouse, tem de clicar em **opções** e especifique a base de dados na **opções** caixa de diálogo. (Se o usuário conectado é um utilizador convidado (ou seja, joe@outlook.com), tem da caixa de verificação e adicionar o nome de domínio do AD atual ou ID de inquilino como parte das opções. Ver [autenticação Universal com a base de dados SQL e SQL Data Warehouse (suporte do SSMS para a MFA)](sql-database-ssms-mfa-authentication.md). Em seguida, clique em **Connect** (Ligar).  
5. Quando o **iniciar sessão na sua conta** caixa de diálogo for apresentada, forneça a conta e palavra-passe da sua identidade do Azure Active Directory. Nenhuma palavra-passe é necessária se um utilizador faz parte de um domínio Federado com o Azure AD.  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > Para a autenticação Universal com uma conta que não exija a MFA, vai ligar neste momento. Para os utilizadores exigir a MFA, continue com os seguintes passos:
   >  
   
6. Duas caixas de diálogo de configuração MFA podem aparecer. Uma vez operação depende do administrador MFA definição e, portanto, podendo ser opcional. Para um domínio MFA ativada neste passo, às vezes, é predefinido (por exemplo, o domínio requer que os utilizadores utilizem um pin e de smart card).  
   ![3mfa-setup][3]  
7. A segunda possível uma vez que a caixa de diálogo permite-lhe selecionar os detalhes do seu método de autenticação. As opções possíveis são configuradas pelo seu administrador.  
   ![4mfa-verify-1][4]  
8. O Azure Active Directory envia as informações de confirmação para. Quando receber o código de verificação, introduza-o para o **introduza o código de verificação** caixa e clique em **iniciar sessão**.  
   ![5mfa-verify-2][5]  

Quando a verificação estiver concluída, o SSMS liga-se normalmente a pressupor credenciais válidas e acesso de firewall.

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral da autenticação multifator de base de dados do Azure SQL, consulte autenticação Universal com [base de dados SQL e SQL Data Warehouse (suporte do SSMS para a MFA)](sql-database-ssms-mfa-authentication.md).  
- Outras pessoas conceda acesso à base de dados: [Autorização e autenticação de base de dados SQL: Conceder acesso](sql-database-manage-logins.md)  
- Certifique-se de que outras pessoas podem ligar-se através da firewall: [Configurar uma regra de firewall ao nível do servidor de base de dados do Azure SQL com o portal do Azure](sql-database-configure-firewall-settings.md)  
- Ao usar **Active Directory - Universal com a MFA** autenticação, o rastreamento da ADAL é disponíveis a partir [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Desativado por predefinição, pode ativar o rastreio da ADAL ao utilizar o **ferramentas**, **opções** menu, em **dos serviços do Azure**, **na Cloud do Azure**,  **O nível de rastreio de janela de saída da ADAL**, seguido pela ativação **saída** no **vista** menu. Os rastreios estão disponíveis na janela de saída ao selecionar **opção do Azure Active Directory**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

