---
title: Configurar a autenticação multifator
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Aprenda a utilizar a autenticação multi-factored com SSMS para Azure SQL Database, Azure SQL Managed Instance e Azure Synapse Analytics.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: has-adal-ref, sqldbrb=3
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 4f90299daed46d06dad9ab37103e3b8f53763ed4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96454376"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Configure a autenticação multi-factor para SQL Server Management Studio e AZure AD
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Este artigo mostra-lhe como utilizar a autenticação multi-factor Azure Ative (Azure AD) com o SQL Server Management Studio (SSMS). O Azure AD MFA pode ser utilizado ao ligar SSMS ou SqlPackage.exe à [Base de Dados Azure SQL](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) e [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para uma visão geral da autenticação multi-factor, consulte [a Autenticação Universal com Base de Dados SQL, SQL Managed Instance e Azure Synapse (suporte SSMS para MFA)](../database/authentication-mfa-ssms-overview.md).

> [!IMPORTANT]
> As bases de dados em Azure SQL Database, Azure SQL Managed Instance e Azure Synapse são referidas coletivamente no restante deste artigo como bases de dados, e o servidor refere-se ao [servidor](logical-servers.md) que acolhe bases de dados para Azure SQL Database e Azure Synapse.

## <a name="configuration-steps"></a>Passos de configuração

1. **Configure um Diretório Ativo Azure** - Para mais informações, consulte [administrar o seu diretório AD Azure,](/previous-versions/azure/azure-services/hh967611(v=azure.100)) [integrando as suas identidades no local com o Azure Ative Directory](../../active-directory/hybrid/whatis-hybrid-identity.md), [Adicione o seu próprio nome de domínio ao Azure AD,](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)o Microsoft [Azure agora suporta a federação com o Windows Server Ative Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), e [Gere a Azure AD utilizando o Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)).
2. **Configure MFA** - Para instruções passo a passo, consulte [o que é autenticação multi-factor Azure AD?](../../active-directory/authentication/concept-mfa-howitworks.md) [](conditional-access-configure.md) (O acesso condicional completo requer um Diretório Ativo Premium Azure. O MFA limitado está disponível com um Azure AD padrão.)
3. **Configure Autenticação Ad Ad** - Para instruções passo a passo, consulte [Ligação à Base de Dados SQL, SQL Managed Instance ou Azure Synapse utilizando autenticação do Diretório Ativo Azure](authentication-aad-overview.md).
4. **Baixar SSMS** - No computador cliente, descarregue os mais recentes SSMS, do [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Conectar utilizando a autenticação universal com SSMS

Os passos a seguir mostram como ligar utilizando o SSMS mais recente.

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]

1. Para ligar utilizando a Autenticação Universal, na caixa de diálogo **Connect to Server** no SQL Server Management Studio (SSMS), selecione Ative **Directory - Universal com suporte MFA**. (Se vir **Autenticação Universal do Diretório Ativo** não está na versão mais recente do SSMS.)

   ![A screenshot do separador Connection Properties no diálogo 'Ligar ao Servidor' em S S M. S. "MyDatabase" é selecionada no "Connect to database dropdown".](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  
2. Preencha a caixa **de nome do Utilizador** com as credenciais do Azure Ative Directory, no formato `user_name@domain.com` .

   ![Screenshot das definições de diálogo 'Ligar ao Servidor' para o tipo de servidor, nome do servidor, autenticação e nome do utilizador.](./media/authentication-mfa-ssms-configure/1mfa-universal-connect-user.png)
3. Se estiver a ligar-se como utilizador convidado, já não precisa de completar o nome de domínio AD ou o campo de identificação do inquilino para os utilizadores convidados, uma vez que o SSMS 18.x ou mais tarde o reconhece automaticamente. Para mais informações, consulte [a Autenticação Universal com Base de Dados SQL, SqL Managed Instance e Azure Synapse (suporte SSMS para MFA)](../database/authentication-mfa-ssms-overview.md).

   ![A screenshot do separador Connection Properties no diálogo 'Ligar ao Servidor' em S S M. S. "MyDatabase" é selecionada no "Connect to database dropdown".](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)

   No entanto, se estiver a ligar-se como utilizador convidado utilizando sSMS 17.x ou mais velho, tem de clicar em **Opções**, e na caixa de diálogo **De Propriedade de Ligação,** e completar o nome de domínio AD ou caixa **de identificação do inquilino.**

   ![Screenshot do separador Connection Properties no diálogo 'Ligar ao Servidor' em S S M.S.O nome de domínio AD ou propriedade de ID do inquilino é preenchido.](./media/authentication-mfa-ssms-configure/mfa-tenant-ssms.png)

4. Selecione **Opções** e especifique a base de dados na caixa de diálogo **Opções.** (Se o utilizador ligado for um utilizador convidado (ou joe@outlook.com seja, deve verificar a caixa e adicionar o nome de domínio AD atual ou iD do inquilino como parte das Opções. Consulte [a Autenticação Universal com Base de Dados SQL e Azure Synapse Analytics (suporte SSMS para MFA)](../database/authentication-mfa-ssms-overview.md). Em seguida, clique em **Connect** (Ligar).  
5. Quando aparecer a caixa de diálogo de **inscrição na sua conta,** forneça a conta e a palavra-passe da sua identidade do Azure Ative Directory. Não é necessária nenhuma palavra-passe se um utilizador fizer parte de um domínio federado com Azure AD.

   ![Screenshot do Sinal para o diálogo da sua conta para Azure SQL Database e Data Warehouse. A conta e a senha estão preenchidas.](./media/authentication-mfa-ssms-configure/2mfa-sign-in.png)  

   > [!NOTE]
   > Para Autenticação Universal com uma conta que não requer MFA, ligue-se neste momento. Para os utilizadores que necessitem de MFA, prossiga com os seguintes passos:
   >  

6. Podem aparecer duas caixas de diálogo de configuração MFA. Esta operação de uma só vez depende da definição do administrador MFA, pelo que pode ser opcional. Para um domínio ativado por MFA, este passo é por vezes pré-definido (por exemplo, o domínio requer que os utilizadores utilizem um smartcard e pin).

   ![Screenshot do Sinal para o diálogo da sua conta para Azure SQL Database e Data Warehouse com um pedido para configurar uma verificação de segurança adicional.](./media/authentication-mfa-ssms-configure/3mfa-setup.png)
  
7. A segunda caixa de diálogo de uma vez possível permite-lhe selecionar os detalhes do seu método de autenticação. As opções possíveis são configuradas pelo seu administrador.

   ![Screenshot do diálogo adicional de verificação de segurança com opções para selecionar e configurar um método de autenticação.](./media/authentication-mfa-ssms-configure/4mfa-verify-1.png)  
8. O Diretório Ativo Azure envia-lhe a informação de confirmação. Quando receber o código de verificação, introduza-o na caixa **de código de verificação de introdução** e clique **em Iniciar sção .**

   ![Screenshot do Sinal para o diálogo da sua conta para Azure SQL Database e Data Warehouse com um pedido para introduzir um código de verificação.](./media/authentication-mfa-ssms-configure/5mfa-verify-2.png)  

Quando a verificação está concluída, o SSMS liga-se normalmente presumindo credenciais válidas e acesso a firewall.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral da autenticação multi-factor, consulte [a Autenticação Universal com Base de Dados SQL, SQL Managed Instance e Azure Synapse (suporte SSMS para MFA)](../database/authentication-mfa-ssms-overview.md).  
- Concede acesso à sua base de dados: [Autenticação e Autorização de Dados sql: Concessão de Acesso](logins-create-manage.md)  
- Certifique-se de que outros podem ligar através da firewall: [Configurar uma regra de firewall ao nível do servidor utilizando o portal Azure](./firewall-configure.md)  
- Ao utilizar **o Ative Directory- Universal com** autenticação MFA, o rastreio ADAL está disponível a partir de [SSMS 17.3](/sql/ssms/download-sql-server-management-studio-ssms). Desligado por predefinição, pode ligar o rastreio ADAL utilizando o menu **Ferramentas**, **Opções,** em **Serviços Azure**, **Azure Cloud,** **Nível de Rastreio da Janela de Saída ADAL,** seguido de ativar a **saída** no menu **Ver.** Os vestígios estão disponíveis na janela de saída ao selecionar a **opção Azure Ative Directory**.