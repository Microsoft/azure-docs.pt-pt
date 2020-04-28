---
title: Implementação de FAQs - Serviço de Aplicações Azure [ Microsoft Docs
description: Obtenha respostas a perguntas frequentes sobre a implementação para a funcionalidade de Aplicações Web do Serviço de Aplicações Azure.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 25f43051f11d42c4f8565039d3168f2ae162eb37
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74671690"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>FAQs de implementação para Aplicações Web do Azure

Este artigo tem respostas a perguntas frequentes (PERGUNTAS) sobre questões de implementação para a [funcionalidade de Aplicações Web do Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Estou apenas a começar com aplicações web do App Service. Como publico o meu código?

Aqui estão algumas opções para publicar o seu código de aplicação web:

*   Implementar utilizando o Estúdio Visual. Se tiver a solução Visual Studio, clique no projeto de aplicação web e, em seguida, **selecione Publicar**.
*   Implementar utilizando um cliente FTP. No portal Azure, faça o download do perfil de publicação para a aplicação web para a a ação a que pretende implementar o seu código. Em seguida, faça o upload dos ficheiros para \site\wwwroot utilizando as mesmas credenciais FTP de perfil de publicação.

Para mais informações, consulte [A implementação da sua aplicação para o Serviço](deploy-local-git.md)de Aplicações .

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this-error"></a>Vejo uma mensagem de erro quando tento ser lançada do Estúdio Visual. Como resolvo este erro?

Se vir a seguinte mensagem, poderá estar a utilizar uma versão mais antiga do SDK: "Erro durante a implementação do recurso 'YourResourceName' no grupo de recursos 'YourResourceGroup': MissingRegistrationForLocation: A subscrição não está registada para os componentes do tipo de recurso no local 'Central US'. Reinscreva-se para este fornecedor de forma a ter acesso a este local." 

Para resolver este erro, atualize para o [mais recente SDK](https://azure.microsoft.com/downloads/). Se vir esta mensagem e tiver o mais recente SDK, envie um pedido de apoio.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Como implemento uma aplicação ASP.NET do Visual Studio para o App Service?
<a id="deployasp"></a>

O tutorial [Criar a sua primeira ASP.NET aplicação web em Azure em cinco minutos](app-service-web-get-started-dotnet.md) mostra-lhe como implementar uma aplicação web ASP.NET para uma aplicação web no App Service utilizando o Visual Studio.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Quais são os diferentes tipos de credenciais de implantação?

O Serviço de Aplicações suporta dois tipos de credenciais para a implantação local de Git e implantação FTP/S. Para obter mais informações sobre como configurar credenciais de implementação, consulte as credenciais de [implementação do Configure para o Serviço](deploy-configure-credentials.md)de Aplicações .

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Qual é o ficheiro ou estrutura de diretório da minha aplicação web do App Service?

Para obter informações sobre a estrutura de ficheiros da sua app App Service, consulte [a estrutura de ficheiros no Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Como posso resolver o "Erro FTP 550 - Não há espaço suficiente no disco" quando tento pôr os meus ficheiros?

Se vir esta mensagem, é provável que esteja a encontrar uma quota de disco no plano de serviço para a sua aplicação web. Você pode precisar escalar até um nível de serviço mais alto com base nas necessidades do seu espaço de disco. Para obter mais informações sobre planos de preços e limites de recursos, consulte os preços do [Serviço de Aplicações.](https://azure.microsoft.com/pricing/details/app-service/)

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Como configurar a implementação contínua para a minha aplicação web do App Service?

Pode configurar uma implementação contínua a partir de vários recursos, incluindo Azure DevOps, OneDrive, GitHub, Bitbucket, Dropbox e outros repositórios Git. Estas opções estão disponíveis no portal. [A implementação contínua para](deploy-continuous-deployment.md) o Serviço de Aplicações é um tutorial útil que explica como configurar a implementação contínua.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Como posso resolver problemas com a implantação contínua do GitHub e do Bitbucket?

Para ajudar a investigar problemas com a implantação contínua do GitHub ou bitbucket, consulte [a investigação contínua de implantação](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this-issue"></a>Não posso usar o FTP no meu site e publicar o meu código. Como posso resolver esta questão?

Para resolver as questões do PFT:

1. Verifique se está a introduzir o nome e credenciais corretas do anfitrião. Para obter informações detalhadas sobre diferentes tipos de credenciais e como usá-las, consulte [as credenciais de implantação.](https://github.com/projectkudu/kudu/wiki/Deployment-credentials)
2. Verifique se as portas FTP não estão bloqueadas por uma firewall. As portas devem ter estas definições:
    * Porta de ligação de controlo de FTP: 21
    * Porta de ligação de dados FTP: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Como publico o meu código no Serviço de Aplicações?

O Azure Quickstart foi concebido para o ajudar a implementar a sua aplicação utilizando a pilha de implementação e o método à sua escolha. Para utilizar o Quickstart, no portal Azure, vá ao seu serviço de aplicações, em **Implementação,** selecione **Quickstart**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Porque é que a minha aplicação às vezes reinicia após a implementação no App Service?

Para conhecer as circunstâncias em que uma implementação de aplicação pode resultar num reinício, consulte questões de [implantação vs. tempo](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts")de execução . Como o artigo descreve, o Serviço de Aplicações implementa ficheiros na pasta wwwroot. Nunca reinicia diretamente a sua aplicação.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Como posso integrar o código do Azure DevOps com o Serviço de Aplicações?

Tem duas opções para utilizar a implantação contínua com o Azure DevOps:

*   Use um projeto Git. Ligue-se através do Serviço de Aplicações utilizando o Centro de Implantação.
*   Utilize um projeto team foundation version control (TFVC). Implemente utilizando o agente de construção para o Serviço de Aplicações.

A implementação contínua de códigos para ambas as opções depende dos fluxos de trabalho existentes para o desenvolvedor e dos procedimentos de check-in. Para obter mais informações, veja estes artigos: 

*   [Implementar a implementação contínua da sua app para um website do Azure](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Criar uma organização Azure DevOps para que possa implementar para uma aplicação web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Como uso ftp ou FTPS para implementar a minha aplicação no App Service?

Para obter informações sobre a utilização de FTP ou FTPS para implementar a sua aplicação web para o App Service, consulte [a implementação da sua aplicação para o Serviço de Aplicações utilizando FTP/S](deploy-ftp.md).
