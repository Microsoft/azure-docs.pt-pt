---
title: Usando o serviço conectado do Active Directory (Visual Studio)
description: Adicione um Diretório Ativo Azure utilizando a caixa de diálogo Visual Studio Add Connected Services
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
ms.openlocfilehash: 3e246ffe66dca38916d1072c489cf8e3ab265334
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76700155"
---
# <a name="add-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Adicionar um Azure Active Directory usando os serviços conectados no Visual Studio

Utilizando o Azure Ative Directory (Azure AD), pode apoiar o Single Sign-On (SSO) para aplicações web ASP.NET MVC ou autenticação de diretório ativo nos serviços Web API. Com a Autenticação AD Azure, os seus utilizadores podem utilizar as suas contas a partir do Diretório Ativo Azure para se conectarem às suas aplicações web. As vantagens da Autenticação AD Azure com API web incluem uma maior segurança de dados ao expor uma API de uma aplicação web. Com a Azure AD, não é necessário gerir um sistema de autenticação separado com a sua própria conta e gestão de utilizadores.

Este artigo e os seus artigos de companhia fornecem detalhes sobre a utilização da funcionalidade Visual Studio Connected Service para Ative Directory. A capacidade está disponível no Visual Studio 2015 e mais tarde.

Atualmente, o serviço de ligação Ative Directory não suporta aplicações ASP.NET Core.

## <a name="prerequisites"></a>Pré-requisitos

- Conta Azure: se não tiver uma conta Azure, pode [inscrever-se para um teste gratuito](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou ativar os benefícios de subscrição do Estúdio [Visual.](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)
- **Estúdio Visual 2015** ou mais tarde. [Baixe o Visual Studio agora.](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Ligue-se ao Diretório Ativo Azure utilizando o diálogo de serviços conectados

1. No Visual Studio, crie ou abra um projeto ASP.NET MVC, ou um projeto ASP.NET Web API. Pode utilizar os modelos MVC, Web API, Aplicação de página única, aplicação API Azure, Aplicação Móvel Azure e modelos de Serviço Móvel Azure.

1. Selecione o comando **do menu Project > Add Connected Service...** ou clique duas vezes no nó de **Serviços Conectados** encontrado no âmbito do projeto no Solution Explorer.

1. Na página **Serviços Conectados,** selecione **Autenticação com Diretório Ativo Azure**.

    ![Página de Serviços Conectados](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Na página **Introdução,** selecione **Next**. Se vir erros nesta página, consulte erros de diagnóstico com o Serviço Ligado ao [Diretório Ativo Azure](vs-active-directory-error.md).

    ![Página de introdução](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Na página **Single-Sign On,** selecione um domínio da lista de abandono do **domínio.** A lista contém todos os domínios acessíveis pelas contas listadas no diálogo de Definições de Conta do Estúdio Visual ( Definições de**Ficheiro > Conta...** ). Como alternativa, pode introduzir um nome de domínio se não encontrar o que procura, como `mydomain.onmicrosoft.com`. Pode escolher a opção de criar uma aplicação Azure Ative Directory ou utilizar as definições a partir de uma aplicação de Diretório Ativo Azure existente. Selecione **Seguinte** quando estiver feito.

    ![Sinal único na página](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Na página de Acesso ao **Diretório,** selecione a opção de dados do **diretório De leitura** conforme desejado. Os desenvolvedores normalmente incluem esta opção.

    ![Página de acesso ao diretório](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Selecione **Finish** para iniciar modificações no seu projeto para ativar a autenticação da AD Azure. O Estúdio Visual mostra progresso durante este tempo:

    ![Progresso do serviço ligado ao Diretório Ativo](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Quando o processo estiver concluído, o Visual Studio abre o seu navegador a um dos seguintes artigos, conforme apropriado ao seu tipo de projeto:

    - [Introdução aos projetos .NET MVC](vs-active-directory-dotnet-getting-started.md)
    - [Introdução aos projetos WebAPI](vs-active-directory-webapi-getting-started.md)

1. Pode também ver o domínio do Diretório Ativo no [portal Azure.](https://go.microsoft.com/fwlink/p/?LinkID=525040)

## <a name="how-your-project-is-modified"></a>Como o seu projeto é modificado

Ao adicionar o serviço conectado o assistente, o Visual Studio adiciona o Azure Ative Directory e referências associadas ao seu projeto. Os ficheiros de configuração e os ficheiros de código no seu projeto também são modificados para adicionar suporte para a AD Azure. As modificações específicas que o Visual Studio faz dependem do tipo de projeto. Consulte os seguintes artigos para mais detalhes:

- [O que aconteceu ao meu projeto .NET MVC?](vs-active-directory-dotnet-what-happened.md)
- [O que aconteceu com o meu projeto da Web API?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Passos seguintes

- [Cenários de autenticação para o Diretório Ativo do Azure](authentication-scenarios.md)
- [Adicione o sessão com a Microsoft a uma aplicação web ASP.NET](quickstart-v1-aspnet-webapp.md)
