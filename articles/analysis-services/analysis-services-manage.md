---
title: Gerenciar Azure Analysis Services | Microsoft Docs
description: Saiba como gerenciar um servidor de Analysis Services no Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 63591da8d7661f7f3a29b321c20a2e05351c71fd
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298604"
---
# <a name="manage-analysis-services"></a>Gerir o Analysis Services
Depois de criar um servidor de Analysis Services no Azure, pode haver algumas tarefas de administração e gerenciamento que você precisa executar imediatamente ou ao mesmo tempo. Por exemplo, execute o processamento para os dados de atualização, controle quem pode acessar os modelos em seu servidor ou monitore a integridade do servidor. Algumas tarefas de gerenciamento só podem ser executadas no portal do Azure, outras no SQL Server Management Studio (SSMS) e algumas tarefas podem ser feitas em qualquer um.

## <a name="azure-portal"></a>Portal do Azure
[Portal do Azure](https://portal.azure.com/) é onde você pode criar e excluir servidores, monitorar recursos do servidor, alterar o tamanho e gerenciar quem tem acesso aos seus servidores.  Se você estiver tendo alguns problemas, também poderá enviar uma solicitação de suporte.

![Obter o nome do servidor no Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Conectar-se ao servidor no Azure é como conectar-se a uma instância de servidor em sua própria organização. No SSMS, você pode executar muitas das mesmas tarefas, como processar dados ou criar um script de processamento, gerenciar funções e usar o PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Baixar e instalar o SSMS
Para obter todos os recursos mais recentes e a experiência mais suave ao se conectar ao seu servidor de Azure Analysis Services, verifique se você está usando a versão mais recente do SSMS. 

[Baixar SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Para se conectar com o SSMS
 Ao usar o SSMS, antes de se conectar ao seu servidor na primeira vez, verifique se o nome de usuário está incluído no grupo Analysis Services admins. Para saber mais, consulte [Administradores de servidor e usuários de banco de dados](#server-administrators-and-database-users) mais adiante neste artigo.

1. Antes de se conectar, você precisa obter o nome do servidor. No **portal do Azure** > servidor > **Descrição geral** > **Nome do servidor**, copie o nome do servidor.
   
    ![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. No SSMS > **Object Explorer**, clique em **Ligar** > **Analysis Services**.
3. Na caixa de diálogo **conectar ao servidor** , Cole o nome do servidor e, em **autenticação**, escolha um dos seguintes tipos de autenticação:   
    > [!NOTE]
    > O tipo de autenticação, **Active Directory-universal com suporte a MFA**, é recomendado.

    > [!NOTE]
    > Se você entrar com uma conta da Microsoft, Live ID, Yahoo, Gmail, etc., deixe o campo de senha em branco. Uma senha será solicitada depois que você clicar em conectar.

    **Autenticação do Windows** para usar suas credenciais do domínio \ nomedeusuário do Windows e senha.

    **Active Directory autenticação de senha** para usar uma conta organizacional. Por exemplo, ao conectar-se de um computador não ingressado no domínio.

    **Active Directory-universal com suporte a MFA** para usar [autenticação multifator ou não interativa](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Ligar no SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Administradores de servidor e usuários de banco de dados
No Azure Analysis Services, há dois tipos de usuários, administradores de servidor e usuários de banco de dados. Os dois tipos de usuários devem estar na sua Azure Active Directory e devem ser especificados por endereço de email organizacional ou UPN. Para saber mais,v eja [Authentication and user permissions](analysis-services-manage-users.md) (Autenticação e permissões de utilizador).


## <a name="troubleshooting-connection-problems"></a>Solucionando problemas de conexão
Ao se conectar usando o SSMS, se você encontrar problemas, talvez seja necessário limpar o cache de logon. Nada é armazenado em cache no disco. Para limpar o cache, feche e reinicie o processo de conexão. 

## <a name="next-steps"></a>Passos seguintes
Se você ainda não implantou um modelo de tabela para o novo servidor, agora é um bom momento. Para obter mais informações, consulte [Deploy to Azure Analysis Services](analysis-services-deploy.md) (Implementar no Azure Analysis Services).

Se você tiver implantado um modelo em seu servidor, você estará pronto para se conectar a ele usando um cliente ou navegador. Para saber mais, consulte [obter dados do Azure Analysis Services Server](analysis-services-connect.md).

