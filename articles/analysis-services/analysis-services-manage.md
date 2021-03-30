---
title: Gerir os serviços de análise Azure | Microsoft Docs
description: Este artigo descreve as ferramentas utilizadas para gerir tarefas de administração e gestão para um servidor Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 91ffcac98b2b919a8fc131d235e699aad4fa215d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93078905"
---
# <a name="manage-analysis-services"></a>Gerir o Analysis Services
Uma vez criado um servidor de Serviços de Análise em Azure, pode haver algumas tarefas de administração e gestão que você precisa realizar imediatamente ou em algum momento na estrada. Por exemplo, executar o processamento para os dados de atualização, controlar quem pode aceder aos modelos no seu servidor ou monitorizar a saúde do seu servidor. Algumas tarefas de gestão só podem ser executadas no portal Azure, outras no SQL Server Management Studio (SSMS), e algumas tarefas podem ser feitas em qualquer uma delas.

## <a name="azure-portal"></a>Portal do Azure
[O portal Azure](https://portal.azure.com/) é onde pode criar e eliminar servidores, monitorizar os recursos do servidor, alterar o tamanho e gerir quem tem acesso aos seus servidores.  Se tiver alguns problemas, também pode apresentar um pedido de apoio.

![Screenshot que mostra o portal Azure onde pode criar e eliminar servidores, monitorizar os recursos do servidor, alterar o tamanho e gerir quem tem acesso aos seus servidores.](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Ligar-se ao seu servidor em Azure é como ligar-se a uma instância de servidor na sua própria organização. A partir de SSMS, pode executar muitas das mesmas tarefas, tais como processar dados ou criar um script de processamento, gerir funções e usar o PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Transferir e instalar o SSMS
Para obter todas as funcionalidades mais recentes e a experiência mais suave ao ligar-se ao servidor Azure Analysis Services, certifique-se de que está a utilizar a versão mais recente do SSMS. 

[Baixar SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Para ligar com sSMS
 Quando utilizar o SSMS, antes de ligar ao seu servidor pela primeira vez, certifique-se de que o seu nome de utilizador está incluído no grupo de Administração de Serviços de Análise. Para saber mais, consulte [os administradores do Servidor e os utilizadores de bases de dados](#server-administrators-and-database-users) mais tarde neste artigo.

1. Antes de se ligar, tem de obter o nome do servidor. No **portal do Azure** > servidor > **Descrição geral** > **Nome do servidor**, copie o nome do servidor.
   
    ![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. No SSMS > **Object Explorer**, clique em **Ligar** > **Analysis Services**.
3. Na caixa de diálogo **'Ligar ao Servidor',** colar no nome do servidor e, em seguida, na **Autenticação),** escolha um dos seguintes tipos de autenticação:   
    > [!NOTE]
    > Recomenda-se o tipo de autenticação, **Diretório Ativo - Universal com suporte MFA.**

    > [!NOTE]
    > Se iniciar sôs com uma Conta Microsoft, Live ID, Yahoo, Gmail, etc., deixe o campo de palavra-passe em branco. É solicitado uma palavra-passe depois de clicar em Connect.

    **Autenticação do Windows** para utilizar o seu domínio Windows\username e credenciais de palavra-passe.

    **Autenticação de passwords** do Diretório Ativo para utilizar uma conta organizacional. Por exemplo, ao ligar a partir de um computador de ligação não-domínio.

    **Diretório Ativo - Universal com suporte MFA** para utilizar [autenticação não interativa ou multi-factor](../azure-sql/database/authentication-mfa-ssms-overview.md). 
   
    ![Ligar no SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Administradores de servidores e utilizadores de bases de dados
Nos Serviços de Análise Azure, existem dois tipos de utilizadores, administradores de servidores e utilizadores de bases de dados. Ambos os tipos de utilizadores devem estar no seu Diretório Ativo Azure e devem ser especificados por endereço de e-mail organizacional ou UPN. Para saber mais, veja [Authentication and user permissions](analysis-services-manage-users.md) (Permissões de autenticação e utilizador).


## <a name="troubleshooting-connection-problems"></a>Problemas de ligação à resolução de problemas
Ao ligar o SSMS, se tiver problemas, poderá ter de limpar a cache de início de sessão. Nada está em cache para o disco. Para limpar a cache, feche e reinicie o processo de ligação. 

## <a name="next-steps"></a>Passos seguintes
Se ainda não implementou um modelo tabular para o seu novo servidor, agora é uma boa altura. Para obter mais informações, consulte [Deploy to Azure Analysis Services](analysis-services-deploy.md) (Implementar no Azure Analysis Services).

Se implementou um modelo no seu servidor, está pronto para o ligar usando um cliente ou navegador. Para saber mais, consulte [obter dados do servidor Azure Analysis Services](analysis-services-connect.md).