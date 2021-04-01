---
title: Utilizando o serviço ative directory conectado (Visual Studio)
description: Adicione um Diretório Ativo Azure utilizando a caixa de diálogo visual Studio Add Connected Services
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: a1ba7db72743ac122a697bf271e783ec64e041e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88165486"
---
# <a name="add-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Adicione um Diretório Ativo Azure utilizando serviços conectados no Estúdio Visual

Ao utilizar o Azure Ative Directory (Azure AD), pode suportar Sign-On únicos (SSO) para aplicações web ASP.NET MVC ou Autenticação de Diretório Ativo nos serviços web da API. Com a Autenticação AD Azure, os seus utilizadores podem utilizar as suas contas a partir do Azure Ative Diretório para se conectarem às suas aplicações web. As vantagens da autenticação AD AD Azure com a API web incluem segurança de dados melhorada ao expor uma API a partir de uma aplicação web. Com o Azure AD, não é preciso gerir um sistema de autenticação separado com conta própria e gestão de utilizadores.

Este artigo e os seus artigos de acompanhante fornecem detalhes da utilização da funcionalidade visual Studio Connected Service para Ative Directory. A capacidade está disponível no Visual Studio 2015 e mais tarde.

Atualmente, o serviço Ative Directory connected não suporta ASP.NET aplicações Core.

## <a name="prerequisites"></a>Pré-requisitos

- Conta Azure: se não tiver uma conta Azure, pode [inscrever-se para um teste gratuito](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar os benefícios do seu subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
- **Visual Studio 2015** ou mais tarde. [Baixe o Visual Studio agora](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Ligue-se ao Diretório Ativo Azure utilizando o diálogo de serviços conectados

1. No Visual Studio, crie ou abra um projeto de MVC ASP.NET ou um projeto de API web ASP.NET. Pode utilizar os modelos MVC, Web API, Single-Page, Azure API App, Azure Mobile App e Azure Mobile Service.

1. Selecione o **project > Adicionar Serviço Conectado...** comando do menu ou clique duas vezes no nó de **Serviços Conectados** encontrado no âmbito do projeto no Solution Explorer.

1. Na página **Serviços Conectados,** selecione **Autenticação com Diretório Ativo Azure**.

    ![Página de Serviços Conectados](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Na página **Introdução,** selecione **Seguinte**. Se vir erros nesta página, consulte os [erros de diagnóstico com o Serviço de Ligação ao Diretório Ativo Azure](vs-active-directory-error.md).

    ![Página de introdução](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Na página **Single-Sign On,** selecione um domínio da lista de drop-down do **domínio.** A lista contém todos os domínios acessíveis pelas contas listadas no diálogo de Definições de Conta do Estúdio Visual **(Definições de > de Ficheiros...**). Como alternativa, pode introduzir um nome de domínio se não encontrar o que procura, `mydomain.onmicrosoft.com` como. Pode escolher a opção de criar uma aplicação Azure Ative Directory ou utilizar as definições de uma aplicação existente do Azure Ative Directory. Selecione **Next** quando terminar.

    ![Sinal único na página](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Na página **'Acesso ao Directório',** selecione a opção **de dados do diretório ler** conforme pretendido. Os desenvolvedores normalmente incluem esta opção.

    ![Página de acesso ao diretório](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. **Selecione Terminar** para iniciar modificações no seu projeto para ativar a autenticação AZure AD. Visual Studio mostra progresso durante este tempo:

    ![Progresso do serviço ligado ao diretório ativo](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Quando o processo estiver concluído, o Visual Studio abre o seu navegador a um dos seguintes artigos, conforme apropriado ao seu tipo de projeto:

    - [Introdução aos projetos .NET MVC](vs-active-directory-dotnet-getting-started.md)
    - [Introdução aos projetos WebAPI](vs-active-directory-webapi-getting-started.md)

1. Também pode ver o domínio ative directory no [portal Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>Como o seu projeto é modificado

Quando adiciona o serviço conectado ao assistente, o Visual Studio adiciona o Azure Ative Directory e referências associadas ao seu projeto. Os ficheiros de configuração e os ficheiros de código do seu projeto também são modificados para adicionar suporte ao Azure AD. As modificações específicas que o Visual Studio faz dependem do tipo de projeto. Consulte os seguintes artigos para mais detalhes:

- [O que aconteceu ao meu projeto .NET MVC?](vs-active-directory-dotnet-what-happened.md)
- [O que aconteceu com o meu projeto web API?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Passos seguintes

- [Cenários de autenticação para o Azure Ative Directory](./authentication-vs-authorization.md)
- [Adicione o s-in com a Microsoft a uma aplicação web ASP.NET](quickstart-v2-aspnet-webapp.md)