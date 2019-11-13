---
title: Adicionar um Azure Active Directory usando os serviços conectados | Azure
description: Adicionar um Azure Active Directory usando a caixa de diálogo Adicionar serviços conectados do Visual Studio
author: ghogen
manager: jillfra
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.custom: aaddev, vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36827401060e330d57b6afac3db0abbde0c27dc2
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73941935"
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Adicionando um Azure Active Directory usando os serviços conectados no Visual Studio

Usando o Azure Active Directory (AD do Azure), você pode dar suporte ao SSO (logon único) para aplicativos Web ASP.NET MVC ou Active Directory autenticação nos serviços de API da Web. Com a autenticação do Azure AD, os usuários podem usar suas contas de Azure Active Directory para se conectarem aos seus aplicativos Web. As vantagens da autenticação do Azure AD com a API da Web incluem segurança de dados aprimorada ao expor uma API de um aplicativo Web. Com o Azure AD, você não precisa gerenciar um sistema de autenticação separado com sua própria conta e gerenciamento de usuário.

Este artigo e seus artigos complementares fornecem detalhes sobre como usar o recurso de serviço conectado do Visual Studio para Active Directory. O recurso está disponível no Visual Studio 2015 e posterior.

No momento, o serviço conectado do Active Directory não oferece suporte a aplicativos ASP.NET Core.

## <a name="prerequisites"></a>Pré-requisitos

- Conta do Azure: se você não tiver uma conta do Azure, poderá [se inscrever para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar seus benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
- **Visual Studio 2015** ou posterior. [Baixe o Visual Studio agora mesmo](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Conectar-se a Azure Active Directory usando a caixa de diálogo serviços conectados

1. No Visual Studio, crie ou abra um projeto MVC do ASP.NET ou um projeto ASP.NET Web API. Você pode usar o MVC, API da Web, aplicativo de página única, aplicativo de API do Azure, aplicativo móvel do Azure e modelos de serviço móvel do Azure.

1. Selecione o comando de menu **projeto > adicionar serviço conectado...** ou clique duas vezes no nó **Serviços conectados** encontrado no projeto no Gerenciador de soluções.

1. Na página **Serviços conectados** , selecione **autenticação com Azure Active Directory**.

    ![Página de serviços conectados](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Na página **introdução** , selecione **Avançar**. Se você encontrar erros nesta página, consulte [diagnosticando erros com o serviço conectado do Azure Active Directory](vs-active-directory-error.md).

    ![Página de introdução](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Na página de **logon único** , selecione um domínio na lista suspensa **domínio** . A lista contém todos os domínios acessíveis pelas contas listadas na caixa de diálogo Configurações de conta do Visual Studio (**arquivo > configurações de conta...** ). Como alternativa, você pode inserir um nome de domínio se não encontrar aquele que está procurando, como `mydomain.onmicrosoft.com`. Você pode escolher a opção para criar um aplicativo Azure Active Directory ou usar as configurações de um aplicativo Azure Active Directory existente. Selecione **Avançar** quando terminar.

    ![Página de logon único](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Na página **acesso ao diretório** , selecione a opção **ler dados do diretório** conforme desejado. Os desenvolvedores normalmente incluem essa opção.

    ![Página de acesso ao diretório](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Selecione **concluir** para iniciar as modificações em seu projeto para habilitar a autenticação do Azure AD. O Visual Studio mostra o andamento durante esse tempo:

    ![Progresso do Active Directory serviço conectado](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Quando o processo for concluído, o Visual Studio abrirá o navegador para um dos seguintes artigos, conforme apropriado para o tipo de projeto:

    - [Introdução aos projetos .NET MVC](vs-active-directory-dotnet-getting-started.md)
    - [Introdução aos projetos WebAPI](vs-active-directory-webapi-getting-started.md)

1. Você também pode ver o domínio Active Directory no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>Como seu projeto é modificado

Quando você adiciona o serviço conectado, o assistente, o Visual Studio adiciona Azure Active Directory e referências associadas ao seu projeto. Arquivos de configuração e arquivos de código em seu projeto também são modificados para adicionar suporte ao Azure AD. As modificações específicas que o Visual Studio faz dependem do tipo de projeto. Consulte os seguintes artigos para obter detalhes:

- [O que aconteceu ao meu projeto .NET MVC?](vs-active-directory-dotnet-what-happened.md)
- [O que aconteceu com meu projeto de API Web?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Passos seguintes

- [Cenários de autenticação para Azure Active Directory](authentication-scenarios.md)
- [Adicionar entrada com a Microsoft a um aplicativo Web ASP.NET](quickstart-v1-aspnet-webapp.md)
