---
title: Configurar a autenticação multifator-SQL do Azure | Microsoft Docs
description: Saiba como usar a autenticação multifator com o SSMS para o banco de dados SQL e SQL Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 5ffe9de6ecb740a2d8445e88a478e718585eb5d1
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018882"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Configurar a autenticação multifator para o SQL Server Management Studio e o Azure AD

Este tópico mostra como usar Azure Active Directory MFA (autenticação multifator) com SQL Server Management Studio. O Azure AD MFA pode ser usado ao conectar o SSMS ou SqlPackage. exe ao [banco de dados SQL](sql-database-technical-overview.md) do Azure e [SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para obter uma visão geral da autenticação multifator do banco de dados SQL do Azure, consulte [autenticação universal com Banco de dados SQL e SQL data warehouse (suporte do SSMS para MFA)](sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Este tópico aplica-se ao servidor SQL do Azure, bem como às bases de dados da Base de Dados SQL e do SQL Data Warehouse que são criadas no servidor SQL do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.

## <a name="configuration-steps"></a>Passos de configuração

1. **Configurar um Azure Active Directory** -para obter mais informações, consulte [administrando seu diretório do Azure ad](https://msdn.microsoft.com/library/azure/hh967611.aspx), [integrando suas identidades locais com Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Adicionar seu próprio nome de domínio ao Azure ad](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [ O Microsoft Azure agora dá suporte à Federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)e a [gerenciar o Azure ad usando o Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Configurar o MFA** – para obter instruções passo a passo, consulte [o que é a autenticação multifator do Azure?](../active-directory/authentication/multi-factor-authentication.md), [acesso condicional (MFA) com o banco de dados SQL do Azure e data warehouse](sql-database-conditional-access.md). (O acesso condicional completo requer um Azure Active Directory Premium (Azure AD). A MFA limitada está disponível com um Azure AD padrão.)
3. **Configurar o banco de dados SQL ou o SQL data warehouse para autenticação do Azure ad** – para obter instruções passo a passo, consulte [conectando ao banco de dados SQL ou SQL data warehouse usando a autenticação Azure Active Directory](sql-database-aad-authentication.md).
4. **Baixar o SSMS** -no computador cliente, baixe o SSMS mais recente, do [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Para todos os recursos deste tópico, use pelo menos julho de 2017, versão 17,2.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Conectando usando a autenticação universal com o SSMS

As etapas a seguir mostram como se conectar ao banco de dados SQL ou SQL Data Warehouse usando o SSMS mais recente.

1. Para se conectar usando a autenticação universal, na caixa de diálogo **conectar ao servidor** , selecione **Active Directory-universal com suporte a MFA**. (Se você vir **Active Directory autenticação universal** , não estará na versão mais recente do SSMS.)  
   ![1mfa-universal-connect][1]  
2. Complete a caixa **nome de usuário** com as credenciais de Azure Active Directory, no `user_name@domain.com`formato.  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Se você estiver se conectando como um usuário convidado, não precisará mais concluir o campo nome de domínio do AD ou ID de locatário para usuários convidados, pois o SSMS 18. x ou posterior o reconhece automaticamente. Para obter mais informações, consulte [autenticação universal com o banco de dados SQL e SQL data warehouse (suporte do SSMS para MFA)](sql-database-ssms-mfa-authentication.md).
   ![MFA-no-Tenant-SSMS](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

   No entanto, se você estiver se conectando como um usuário convidado usando o SSMS 17. x ou mais antigo, você deve clicar em **Opções**e na caixa de diálogo **propriedade de conexão** e concluir a caixa nome de **domínio do AD ou ID de locatário** .
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

4. Como de costume para o banco de dados SQL e SQL Data Warehouse, você deve clicar em **Opções** e especificar o banco de dados na caixa de diálogo **Opções** . (Se o usuário conectado for um usuário convidado (ou seja joe@outlook.com,), você deverá marcar a caixa e adicionar o nome de domínio do AD atual ou a ID de locatário como parte das opções. Consulte [autenticação universal com o banco de dados SQL e SQL data warehouse (suporte do SSMS para MFA)](sql-database-ssms-mfa-authentication.md). Em seguida, clique em **Connect** (Ligar).  
5. Quando a caixa de diálogo **entrar na sua conta** for exibida, forneça a conta e a senha de sua identidade de Azure Active Directory. Nenhuma senha será necessária se um usuário fizer parte de um domínio federado com o Azure AD.  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > Para autenticação universal com uma conta que não requer MFA, você se conecta neste ponto. Para os usuários que precisam de MFA, continue com as seguintes etapas:
   >  
   
6. Duas caixas de diálogo de configuração de MFA podem ser exibidas. Essa operação de uma vez depende da configuração do administrador de MFA e, portanto, pode ser opcional. Para um domínio habilitado para MFA, essa etapa é, às vezes, predefinida (por exemplo, o domínio requer que os usuários usem um cartão inteligente e PIN).  
   ![3mfa-instalação][3]  
7. A segunda caixa de diálogo possível de uma única vez permite que você selecione os detalhes do seu método de autenticação. As opções possíveis são configuradas pelo seu administrador.  
   ![4mfa-verificar-1][4]  
8. O Azure Active Directory envia as informações de confirmação para você. Quando você receber o código de verificação, insira-o na caixa de **código inserir verificação** e clique em **entrar**.  
   ![5mfa-verify-2][5]  

Quando a verificação for concluída, o SSMS se conectará normalmente, presumindo credenciais válidas e acesso de firewall.

## <a name="next-steps"></a>Passos Seguintes

- Para obter uma visão geral da autenticação multifator do banco de dados SQL do Azure, consulte autenticação universal com [banco de dados SQL e SQL data warehouse (suporte do SSMS para MFA)](sql-database-ssms-mfa-authentication.md).  
- Conceder a outros acesso ao seu banco de dados: [Autenticação e autorização do banco de dados SQL: Concedendo acesso](sql-database-manage-logins.md)  
- Verifique se outras pessoas podem se conectar por meio do firewall: [Configurar uma regra de firewall no nível de servidor do banco de dados SQL do Azure usando o portal do Azure](sql-database-configure-firewall-settings.md)  
- Ao usar o **Active Directory-universal com** autenticação de MFA, o rastreamento de Adal está disponível a partir do [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Desativado por padrão, você pode ativar o rastreamento de ADAL usando o **menu ferramentas**, **Opções** , em **Serviços do Azure**, **nuvem do Azure**, **Adal janela de saída nível de rastreamento**, seguido por habilitar a **saída** no menu **Exibir** . Os rastreamentos estão disponíveis na janela saída ao selecionar **Azure Active Directory opção**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

