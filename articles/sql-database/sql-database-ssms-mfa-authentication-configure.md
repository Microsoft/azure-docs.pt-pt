---
title: Configurar a autenticação multifator
description: Saiba como utilizar a autenticação multi-fatorizada com SSMS para Base de Dados SQL e Azure Synapse Analytics
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
ms.openlocfilehash: 38d8eba5dd451c8e8709ce4d43aba107e5346bfc
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82627369"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Configure a autenticação de vários fatores para o Estúdio de Gestão de Servidores SQL e a AD Azure

Este tópico mostra-lhe como utilizar a autenticação multifactor do Azure Ative Directory (MFA) com o SQL Server Management Studio. O Azure AD MFA pode ser utilizado na ligação de SSMS ou SqlPackage.exe à Base de [Dados Azure SQL](sql-database-technical-overview.md) e [azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para uma visão geral da autenticação multifactor da Base de Dados Azure SQL, consulte [a Autenticação Universal com base de dados SQL e Synapse Azure (suporte SSMS para MFA)](sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Este tópico aplica-se ao servidor Azure SQL, e tanto às bases de dados SQL como ao Azure Synapse que são criados no servidor Azure SQL. Para a simplicidade, a Base de Dados SQL é utilizada quando se refere tanto à Base de Dados SQL como ao Synapse Azure.

## <a name="configuration-steps"></a>Passos de configuração

1. **Configure um Diretório Ativo Azure** - Para mais informações, consulte a administração do [seu diretório De AD Azure](https://msdn.microsoft.com/library/azure/hh967611.aspx), [integrando as suas identidades no local com o Diretório Ativo Azure,](../active-directory/hybrid/whatis-hybrid-identity.md) [adicione o seu próprio nome de domínio ao Azure AD,](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)o Microsoft Azure agora suporta a [federação com o Diretório Ativo](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)do Windows Server e gere o Anúncio [Azure utilizando](https://msdn.microsoft.com/library/azure/jj151815.aspx)o Windows PowerShell .
2. **Configure MFA** - Para instruções passo a passo, consulte O que [Conditional Access (MFA) with Azure SQL Database and Azure Synapse](sql-database-conditional-access.md) [é a autenticação de multi-factores Azure?](../active-directory/authentication/multi-factor-authentication.md) (O acesso condicional completo requer um Diretório Ativo Premium Azure (Azure AD). MFA limitado está disponível com um Azure AD padrão.)
3. Configure a Base de **Dados SQL ou o Synapse Azure para autenticação de anúncios azure** - Para instruções passo a passo, consulte a ligação à Base de Dados [SQL ou ao Synapse Azure utilizando a autenticação de diretório ativo Azure](sql-database-aad-authentication.md).
4. **Baixe SSMS** - No computador cliente, descarregue o mais recente SSMS, a partir do [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Para todas as funcionalidades deste tema, utilize pelo menos julho de 2017, versão 17.2.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Ligação utilizando a autenticação universal com SSMS

Os seguintes passos mostram como ligar-se à Base de Dados SQL ou ao SAzure Synapse utilizando o mais recente SSMS.

1. Para ligar utilizando a Autenticação Universal, na caixa de diálogo **Connect to Server,** selecione **Ative Directory - Universal com suporte MFA**. (Se vir autenticação universal do **Diretório Ativo,** não está na versão mais recente do SSMS.)  
   ![1mfa-universal-connect][1]  
2. Complete a caixa de **nomes do Utilizador** com `user_name@domain.com`as credenciais de Diretório Ativo Azure, no formato .  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Se estiver a ligar-se como utilizador convidado, já não precisa de completar o nome de domínio AD ou o campo de IDENTIFICAÇÃO do inquilino para os utilizadores convidados, uma vez que o SSMS 18.x ou posteriormente o reconhece automaticamente. Para mais informações, consulte a [Autenticação Universal com base de dados SQL e Synapse Azure (suporte SSMS para MFA)](sql-database-ssms-mfa-authentication.md).
   ![mfa-no-inquilino-ssms](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

   No entanto, se estiver a ligar-se como utilizador convidado utilizando SSMS 17.x ou mais antigo, deve clicar em **Opções**, e na caixa de diálogo **Connection Property,** e completar o nome de **domínio AD ou** caixa de identificação do inquilino.
   ![mfa-inquilino-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

4. Como de costume para a Base de Dados SQL e para o Azure Synapse, tem de clicar em **Opções** e especificar a base de dados na caixa de diálogo **Opções.** (Se o utilizador conectado for um utilizador joe@outlook.comconvidado (ou seja), deve verificar a caixa e adicionar o nome atual de domínio AD ou ID do inquilino como parte das Opções. Ver Autenticação Universal com Base de [Dados SQL e Synapse Azure (suporte SSMS para MFA)](sql-database-ssms-mfa-authentication.md). Em seguida, clique em **Connect** (Ligar).  
5. Quando aparecer a caixa de diálogo da **sua conta,** forneça a conta e a palavra-passe da sua identidade de Diretório Ativo Azure. Não é necessária nenhuma palavra-passe se um utilizador fizer parte de um domínio federado com AD Azure.  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > Para autenticação universal com uma conta que não requer MFA, você se conecta neste ponto. Para os utilizadores que necessitem de MFA, continue com os seguintes passos:
   >  
   
6. Podem aparecer duas caixas de diálogo de configuração mfa. Esta operação de uma vez depende da definição do administrador do MFA, pelo que pode ser opcional. Para um domínio ativado por MFA este passo é por vezes pré-definido (por exemplo, o domínio requer que os utilizadores utilizem um smartcard e pin).  
   ![Configuração 3mfa][3]  
7. A segunda caixa de diálogo de uma vez permite selecionar os detalhes do seu método de autenticação. As opções possíveis são configuradas pelo seu administrador.  
   ![4mfa-check-1][4]  
8. O Diretório Ativo Azure envia-lhe as informações de confirmação. Quando receber o código de verificação, introduza-o na caixa de **códigode verificação Enter** e clique **em Iniciar sessão**.  
   ![5mfa-check-2][5]  

Quando a verificação está concluída, o SSMS liga normalmente a presumir credenciais válidas e o acesso à firewall.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral da autenticação multifactor da Base de Dados Azure SQL, consulte a Autenticação Universal com base de [dados SQL e Synapse Azure (suporte SSMS para MFA)](sql-database-ssms-mfa-authentication.md).  
- Conceda a outros acesso à sua base de dados: Autenticação e Autorização da Base de [Dados SQL: Acesso de Concessão](sql-database-manage-logins.md)  
- Certifique-se de que outros podem ligar através da firewall: Configure uma regra de firewall de nível de servidor de base [de dados Azure SQL utilizando o portal Azure](sql-database-configure-firewall-settings.md)  
- Ao utilizar **o Diretório Activo- Universal com** autenticação MFA, o rastreio da ADAL está disponível a partir do [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Desligado por defeito, pode ligar o rastreio ADAL utilizando o menu **Tools**, **Options,** em **Serviços Azure,** **Nuvem Azure,** Nível de Traço de Janela de **Saída ADAL,** seguido de ativação **da saída** no menu **'Ver'.** Os vestígios estão disponíveis na janela de saída ao selecionar a **opção Azure Ative Directory**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

