---
title: Gerir o Azure Analysis Services | Documentos da Microsoft
description: Saiba como gerir um servidor de Analysis Services no Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0bae06d46c2c96ba9dd058e9c2d380379523811c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61065198"
---
# <a name="manage-analysis-services"></a>Gerir os serviços de análise
Depois de criar um servidor do Analysis Services no Azure, pode haver algumas tarefas de administração e gerenciamento, que precisará executar imediatamente ou algum tempo, mais adiante. Por exemplo, executar processamento para os atualização de dados, controlar quem pode aceder os modelos no seu servidor, ou monitorizar o estado de funcionamento do seu servidor. Algumas tarefas de gestão só podem ser executadas no portal do Azure, as outras pessoas no SQL Server Management Studio (SSMS), e algumas tarefas podem ser feitas em qualquer um.

## <a name="azure-portal"></a>Portal do Azure
[Portal do Azure](https://portal.azure.com/) é onde pode criar e eliminar servidores, monitorizar os recursos de servidor, alterar o tamanho e gerir quem tem acesso aos seus servidores.  Se estiver a ter alguns problemas, também pode submeter um pedido de suporte.

![Obter o nome do servidor no Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Ligar ao seu servidor no Azure é igual a ligar a uma instância de servidor na sua organização. SSMS, pode efetuar muitas das mesmas tarefas como processamento de dados ou criar um script de processamento, gerir funções e utilizar o PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Transferir e instalar o SSMS
Para obter todas as funcionalidades mais recentes e a experiência mais tranqüila quando se liga ao seu servidor Azure Analysis Services, certifique-se de que está a utilizar a versão mais recente do SSMS. 

[Baixe o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Para estabelecer ligação com o SSMS
 Ao utilizar o SSMS, antes de ligar ao seu servidor pela primeira vez, certifique-se de que o seu nome de utilizador está incluído no grupo de administradores do Analysis Services. Para obter mais informações, consulte [os administradores de servidor e os utilizadores de base de dados](#server-administrators-and-database-users) mais adiante neste artigo.

1. Antes de ligar, terá de obter o nome do servidor. No **portal do Azure** > servidor > **Descrição geral** > **Nome do servidor**, copie o nome do servidor.
   
    ![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. No SSMS > **Object Explorer**, clique em **Ligar** > **Analysis Services**.
3. Na **ligar ao servidor** caixa de diálogo, cole o nome do servidor, em seguida, em **autenticação**, escolha um dos seguintes tipos de autenticação:   
    > [!NOTE]
    > Tipo de autenticação **Active Directory - Universal com o suporte MFA**, é recomendado.

    > [!NOTE]
    > Se iniciar sessão com um Microsoft Account, Live ID, Yahoo, Gmail, etc., deixe o campo de palavra-passe em branco. Lhe for pedida uma palavra-passe depois de clicar em Connect.

    **Autenticação do Windows** utilizar as credenciais de domínio \ nomedeutilizador e palavra-passe do Windows.

    **Autenticação de palavra-passe do Active Directory** para utilizar uma conta institucional. Por exemplo, quando ligar a partir de um domínio não associados ao computador.

    **O Active Directory - Universal com o suporte MFA** utilizar [autenticação não interativa ou multifator](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Ligar no SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Os administradores de servidor e os utilizadores de base de dados
No Azure Analysis Services, existem dois tipos de usuários, os administradores de servidor e os utilizadores de base de dados. Ambos os tipos de utilizadores tem de estar no seu Azure Active Directory e tem de ser especificados por endereço de e-mail empresarial ou UPN. Para saber mais,v eja [Authentication and user permissions](analysis-services-manage-users.md) (Autenticação e permissões de utilizador).


## <a name="troubleshooting-connection-problems"></a>Resolução de problemas de ligação
Ao ligar-se com o SSMS, caso se depare com problemas, terá de limpar a cache de início de sessão. Nada é colocado em cache para disco. Para limpar a cache, feche e reinicie o processo do connect. 

## <a name="next-steps"></a>Passos Seguintes
Se já não tiver implementado um modelo de tabela para o novo servidor, agora é um bom momento. Para obter mais informações, consulte [Deploy to Azure Analysis Services](analysis-services-deploy.md) (Implementar no Azure Analysis Services).

Se tiver implementado um modelo para o seu servidor, está pronto para ligá-la utilizando um cliente ou browser. Para obter mais informações, consulte [obter dados do servidor Azure Analysis Services](analysis-services-connect.md).

