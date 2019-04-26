---
title: Adicionar um Azure Active Directory com os serviços ligados no Visual Studio
description: Adicione um Azure Active Directory, utilizando a caixa de diálogo do Visual Studio adicionar serviços ligados
services: active-directory
author: ghogen
manager: douge
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbbef13d2e64a5d1b177d8232c5b6ef1e64a1da9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60250929"
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Adicionar um Azure Active Directory com os serviços ligados no Visual Studio

Ao utilizar o Azure Active Directory (Azure AD), pode suportar o único início de sessão (SSO) para aplicativos da web de ASP.NET MVC, ou autenticação do Active Directory em serviços da Web API. Autenticação do Azure AD, seus usuários podem usar suas contas do Azure Active Directory para ligar às suas aplicações web. As vantagens da autenticação do Azure AD com a Web API incluem a segurança de dados avançados ao expor uma API de um aplicativo web. Com o Azure AD, não é necessário que gerir um sistema de autenticação separado com sua própria gestão de utilizadores e de conta.

Este artigo e os respetivos artigos complementar fornecem detalhes de utilizar a funcionalidade de serviço ligado do Visual Studio para o Active Directory. A capacidade está disponível no Visual Studio 2017 e no Visual Studio 2015.

No momento, o serviço ligado do Active Directory não suporta aplicações ASP.NET Core.

## <a name="prerequisites"></a>Pré-requisitos

- Conta do Azure: Se não tiver uma conta do Azure, pode [Inscreva-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
- **Visual Studio 2015** ou posterior. [Baixe agora o Visual Studio 2017](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Ligar ao Azure Active Directory com a caixa de diálogo de serviços ligados

1. No Visual Studio, crie ou abra um projeto ASP.NET MVC, ou um projeto ASP.NET Web API. Pode usar o MVC, Web API, aplicação de página única, aplicação de API do Azure, aplicação móvel do Azure e modelos de serviço do Azure Mobile.

1. Selecione o **projeto > Adicionar serviço ligado...**  comando de menu ou faça duplo clique o **serviços ligados** nó encontrado-se no projeto no Explorador de soluções.

1. Sobre o **serviços ligados** página, selecione **autenticação com o Azure Active Directory**.

    ![Página de serviços ligada](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Sobre o **introdução** página, selecione **próxima**. Se vir erros nesta página, consulte [diagnosticar erros com o serviço do Azure Active Directory ligado](vs-active-directory-error.md).

    ![Página de introdução](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Na **no início de sessão único** , selecione um domínio a partir do **domínio** na lista pendente. A lista contém todos os domínios acessíveis por contas listadas na caixa de diálogo Definições de conta do Visual Studio (**ficheiro > definições da conta...** ). Como alternativa, pode introduzir um nome de domínio se não encontrar o que está procurando, tais como `mydomain.onmicrosoft.com`. Pode escolher a opção de criar uma aplicação do Azure Active Directory ou utilizar as definições de uma aplicação do Azure Active Directory existente. Selecione **seguinte** quando tiver terminado.

    ![Início de sessão único na página](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Sobre o **acesso ao diretório** página, selecione a **ler dados do diretório** opção conforme pretendido. Os desenvolvedores incluem, geralmente, esta opção.

    ![Página de acesso do diretório](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Selecione **concluir** para iniciar as modificações ao seu projeto para ativar a autenticação do Azure AD. Durante este período, o Visual Studio mostra progresso:

    ![Progresso de serviço do Active Directory ligado](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Quando o processo estiver concluído, o Visual Studio abre o browser para um dos artigos seguintes, conforme adequado ao seu tipo de projeto:

    - [Introdução aos projetos .NET MVC](vs-active-directory-dotnet-getting-started.md)
    - [Introdução aos projetos WebAPI](vs-active-directory-webapi-getting-started.md)

1. Também pode ver o domínio do Active Directory no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>Como o seu projeto é modificado

Ao adicionar o serviço ligado do assistente, o Visual Studio adiciona o Azure Active Directory e as referências associadas ao seu projeto. Ficheiros de configuração e arquivos de código no seu projeto também são modificados para adicionar suporte para o Azure AD. As modificações específicas que o Visual Studio faz dependem do tipo de projeto. Veja os artigos seguintes para obter mais detalhes:

- [O que aconteceu ao meu projeto .NET MVC?](vs-active-directory-dotnet-what-happened.md)
- [O que aconteceu ao meu projeto de Web API?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Passos Seguintes

- [Cenários de autenticação do Azure Active Directory](authentication-scenarios.md)
- [Adicionar início de sessão com a Microsoft a uma aplicação web ASP.NET](quickstart-v1-aspnet-webapp.md)
