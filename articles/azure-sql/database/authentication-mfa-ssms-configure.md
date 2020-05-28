---
title: Configurar a autenticação multifator
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Saiba como utilizar a autenticação multi-factored com SSMS para base de dados Azure SQL, Azure SQL Managed Instance e Azure Synapse Analytics.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: has-adal-ref, sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: ed263b71621e0eb82171cca76a143bd0bb133e66
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048911"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Configure a autenticação de vários fatores para o Estúdio de Gestão de Servidores SQL e a AD Azure
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Este artigo mostra-lhe como utilizar a autenticação multifactor do Azure Ative Directory (MFA) com o SQL Server Management Studio. O Azure AD MFA pode ser utilizado na ligação de SSMS ou SqlPackage.exe à Base de [Dados Azure SQL,](sql-database-paas-overview.md) [À Instância Gerida azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) e à [Azure Synapse Analytics (anteriormente SQL DW)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para uma visão geral da autenticação multifactor, consulte a [Autenticação Universal com Base de Dados SQL, Instância Gerida SQL e Synapse Azure (suporte SSMS para MFA)](../database/authentication-mfa-ssms-overview.md).

> [!IMPORTANT]
> As bases de dados em Azure SQL Database, Azure SQL Managed Instance e Azure Synapse (ex-Azure SQL Data Warehouse) são referidas colectivamente no restante deste artigo como bases de dados, e o servidor está se referindo ao [servidor](logical-servers.md) que acolhe bases de dados para a Base de Dados Azure SQL e Azure Synapse.

## <a name="configuration-steps"></a>Passos de configuração

1. **Configure um Diretório Ativo Azure** - Para mais informações, consulte a administração do [seu diretório De AD Azure](https://msdn.microsoft.com/library/azure/hh967611.aspx), [integrando as suas identidades no local com o Diretório Ativo Azure,](../../active-directory/hybrid/whatis-hybrid-identity.md) [adicione o seu próprio nome de domínio ao Azure AD,](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)o Microsoft Azure agora suporta a [federação com o Diretório Ativo](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)do Windows Server e gere o Anúncio [Azure utilizando](https://msdn.microsoft.com/library/azure/jj151815.aspx)o Windows PowerShell .
2. **Configure MFA** - Para instruções passo a passo, consulte o que [Conditional Access (MFA) with Azure SQL Database and Data Warehouse](conditional-access-configure.md)é a [autenticação de multi-factores Azure?](../../active-directory/authentication/multi-factor-authentication.md) (O acesso condicional completo requer um Diretório Ativo Premium Azure (Azure AD). MFA limitado está disponível com um Azure AD padrão.)
3. **Configure Autenticação AD Azure** - Para instruções passo a passo, consulte a ligação à base de [dados SQL, à Instância Gerida sQL ou ao Azure Synapse utilizando a autenticação de diretório ativo Azure](authentication-aad-overview.md).
4. **Baixe SSMS** - No computador cliente, descarregue o mais recente SSMS, a partir do [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Ligação utilizando a autenticação universal com SSMS

Os seguintes passos mostram como se conectar usando o mais recente SSMS.

1. Para ligar usando a Autenticação Universal, na caixa de diálogo **Connect to Server** no SQL Server Management Studio (SSMS), selecione Ative **Directory - Universal com suporte MFA**. (Se vir autenticação universal do **Diretório Ativo,** não está na versão mais recente do SSMS.)  
   ! [1mfa-universal-connect] [1]  
2. Complete a caixa de **nomes do Utilizador** com as credenciais de Diretório Ativo Azure, no formato `user_name@domain.com` .  
   ![1mfa-universal-connect-user](./media/authentication-mfa-ssms-configure/1mfa-universal-connect-user.png)
3. Se estiver a ligar-se como utilizador convidado, já não precisa de completar o nome de domínio AD ou o campo de IDENTIFICAÇÃO do inquilino para os utilizadores convidados, uma vez que o SSMS 18.x ou posteriormente o reconhece automaticamente. Para mais informações, consulte a Autenticação Universal com Base de [Dados SQL, Instância Gerida SQL e Synapse Azure (suporte SSMS para MFA)](../database/authentication-mfa-ssms-overview.md).
   ![mfa-no-inquilino-ssms](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)

   No entanto, se estiver a ligar-se como utilizador convidado utilizando SSMS 17.x ou mais antigo, deve clicar em **Opções**, e na caixa de diálogo **Connection Property,** e completar o nome de **domínio AD ou** caixa de identificação do inquilino.
   ![mfa-inquilino-ssms](./media/authentication-mfa-ssms-configure/mfa-tenant-ssms.png)

4. Selecione **Opções** e especifique a base de dados na caixa de diálogo **Opções.** (Se o utilizador conectado for um utilizador convidado (ou seja), deve verificar a caixa e adicionar o nome atual de joe@outlook.com domínio AD ou ID do inquilino como parte das Opções. Ver Autenticação Universal com Base de [Dados SQL e Armazém de Dados SQL (suporte SSMS para MFA)](../database/authentication-mfa-ssms-overview.md). Em seguida, clique em **Connect** (Ligar).  
5. Quando aparecer a caixa de diálogo da **sua conta,** forneça a conta e a palavra-passe da sua identidade de Diretório Ativo Azure. Não é necessária nenhuma palavra-passe se um utilizador fizer parte de um domínio federado com AD Azure.  
   ! [2mfa-sign-in] [2]  

   > [!NOTE]
   > Para autenticação universal com uma conta que não requer MFA, você se conecta neste ponto. Para os utilizadores que necessitem de MFA, continue com os seguintes passos:
   >  

6. Podem aparecer duas caixas de diálogo de configuração mfa. Esta operação de uma vez depende da definição do administrador do MFA, pelo que pode ser opcional. Para um domínio ativado por MFA este passo é por vezes pré-definido (por exemplo, o domínio requer que os utilizadores utilizem um smartcard e pin).  
   ! [3mfa-setup] [3]  
7. A segunda caixa de diálogo de uma vez permite selecionar os detalhes do seu método de autenticação. As opções possíveis são configuradas pelo seu administrador.  
   ! [4mfa-check-1] [4]  
8. O Diretório Ativo Azure envia-lhe as informações de confirmação. Quando receber o código de verificação, introduza-o na caixa de **códigode verificação Enter** e clique **em Iniciar sessão**.  
   ! [5mfa-check-2] [5]  

Quando a verificação está concluída, o SSMS liga normalmente a presumir credenciais válidas e o acesso à firewall.

## <a name="next-steps"></a>Próximos passos

- Para uma visão geral da autenticação multifactor, consulte a [Autenticação Universal com Base de Dados SQL, Instância Gerida SQL e Synapse Azure (suporte SSMS para MFA)](../database/authentication-mfa-ssms-overview.md).  
- Conceda a outros acesso à sua base de dados: Autenticação e Autorização da Base de [Dados SQL: Acesso de Concessão](logins-create-manage.md)  
- Certifique-se de que outros podem ligar através da firewall: [Configure uma regra de firewall ao nível do servidor utilizando o portal Azure](/database/firewall-configure.md)  
- Ao utilizar **o Diretório Activo- Universal com** autenticação MFA, o rastreio da ADAL está disponível a partir do [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Desligado por defeito, pode ligar o rastreio ADAL utilizando o menu **Tools**, **Options,** em **Serviços Azure,** **Nuvem Azure,** Nível de Traço de Janela de **Saída ADAL,** seguido de ativação **da saída** no menu **'Ver'.** Os vestígios estão disponíveis na janela de saída ao selecionar a **opção Azure Ative Directory**.
