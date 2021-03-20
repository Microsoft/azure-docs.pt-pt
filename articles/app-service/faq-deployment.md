---
title: Qas de implantação - Azure App Service | Microsoft Docs
description: Obtenha respostas para perguntas frequentes sobre implementação para a funcionalidade de Aplicações Web do Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 163a6940e50d1f8beacc23855fd1e6f9daad0085
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88080478"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>FAQs de implementação para Aplicações Web do Azure

Este artigo tem respostas para perguntas frequentes (FAQs) sobre questões de implementação para a [funcionalidade de Aplicações Web do Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Estou apenas começando com aplicações web do Serviço de Aplicações. Como publico o meu código?

Aqui estão algumas opções para publicar o seu código de aplicação web:

*   Implementar utilizando o Visual Studio. Se tiver a solução Visual Studio, clique com o botão direito no projeto de aplicação web e, em seguida, **selecione Publicar**.
*   Implemente utilizando um cliente FTP. No portal Azure, descarregue o perfil de publicação para a aplicação web para a quais pretende implementar o seu código. Em seguida, faça o upload dos ficheiros para \site\wwwroot utilizando as mesmas credenciais de perfil de publicação FTP.

Para obter mais informações, consulte [Implementar a sua aplicação no Serviço de Aplicações.](deploy-local-git.md)

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this-error"></a>Vejo uma mensagem de erro quando tento ser lançada do Visual Studio. Como é que resolvo este erro?

Se vir a seguinte mensagem, poderá estar a utilizar uma versão mais antiga do SDK: "Erro durante a implementação do recurso 'YourResourceName' no grupo de recursos 'YourResourceGroup': MissingRegistrationForLocation: A subscrição não está registada para os 'componentes' do tipo de recurso na localização 'Central US'. Recandistar-se a este fornecedor para ter acesso a este local." 

Para resolver este erro, atualize para o [SDK mais recente](https://azure.microsoft.com/downloads/). Se vir esta mensagem e tiver o mais recente SDK, envie um pedido de apoio.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Como posso implementar uma aplicação ASP.NET do Visual Studio para o App Service?
<a id="deployasp"></a>

O tutorial [Criar a sua primeira aplicação web ASP.NET em Azure em cinco minutos](quickstart-dotnetcore.md) mostra-lhe como implementar uma aplicação web ASP.NET para uma aplicação web no App Service, utilizando o Visual Studio.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Quais são os diferentes tipos de credenciais de implantação?

O Serviço de Aplicações suporta dois tipos de credenciais para a implementação local do Git e para a implementação ftp/S. Para obter mais informações sobre como configurar credenciais de implementação, consulte credenciais de [implementação configure para o Serviço de Aplicações.](deploy-configure-credentials.md)

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Qual é o ficheiro ou estrutura de diretório da minha aplicação web do Serviço de Aplicações?

Para obter informações sobre a estrutura de ficheiros da sua aplicação App Service, consulte [a estrutura do Ficheiro em Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Como resolvo "FTP Error 550 - Não há espaço suficiente no disco" quando tento ftp os meus ficheiros?

Se vir esta mensagem, é provável que esteja a encontrar uma quota de disco no plano de serviço para a sua aplicação web. Pode ser necessário escalar até um nível de serviço mais elevado com base nas necessidades do seu espaço em disco. Para obter mais informações sobre planos de preços e limites de recursos, consulte [os preços do Serviço de Aplicações.](https://azure.microsoft.com/pricing/details/app-service/)

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Como posso configurar uma implementação contínua para a minha aplicação web do Serviço de Aplicações?

Você pode configurar a implementação contínua a partir de vários recursos, incluindo Azure DevOps, OneDrive, GitHub, Bitbucket, Dropbox, e outros repositórios Git. Estas opções estão disponíveis no portal. [A implementação contínua para o Serviço de Aplicações](deploy-continuous-deployment.md) é um tutorial útil que explica como configurar a implementação contínua.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Como resolvo problemas com a implantação contínua do GitHub e do Bitbucket?

Para ajudar a investigar problemas com a implementação contínua do GitHub ou bitbucket, consulte [a investigação da implementação contínua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this-issue"></a>Não posso publicar o meu código. Como resolvo esta questão?

Para resolver problemas de FTP:

1. Verifique se está a introduzir o nome e as credenciais do anfitrião corretos. Para obter informações detalhadas sobre diferentes tipos de credenciais e como utilizá-las, consulte [as credenciais de implantação](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Verifique se as portas FTP não estão bloqueadas por uma firewall. As portas devem ter estas definições:
    * Porta de ligação de controlo de FTP: 21
    * Porta de ligação de dados FTP: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Como publico o meu código no Serviço de Aplicações?

O Azure Quickstart foi concebido para o ajudar a implementar a sua aplicação utilizando a pilha de implementação e o método à sua escolha. Para utilizar o Quickstart, no portal Azure, aceda ao seu serviço de aplicações, em **Implementação,** selecione **Quickstart**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Porque é que a minha aplicação, por vezes, recomeça após a implementação do Serviço de Aplicações?

Para saber as circunstâncias em que uma implementação de aplicação pode resultar num reinício, consulte [questões de execução vs. .](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts") Como o artigo descreve, o Serviço de Aplicações implementa ficheiros na pasta wwwroot. Nunca reinicia diretamente a sua aplicação.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Como posso integrar o código do Azure DevOps com o Serviço de Aplicações?

Tem duas opções para utilizar a implementação contínua com Azure DevOps:

*   Use um projeto Git. Conecte-se através do Serviço de Aplicações utilizando o Centro de Implantação.
*   Utilize um projeto team foundation version control (TFVC). Implementar utilizando o agente de construção para o Serviço de Aplicações.

A implementação contínua de códigos para ambas estas opções depende dos fluxos de trabalho do desenvolvedor existentes e dos procedimentos de check-in. Para obter mais informações, veja estes artigos: 

*   [Implementar a implementação contínua da sua app num website da Azure](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Crie uma organização Azure DevOps para que possa implementar para uma aplicação web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Como posso utilizar FTP ou FTPS para implementar a minha app no Serviço de Aplicações?

Para obter informações sobre a utilização de FTP ou FTPS para implementar a sua aplicação web no Serviço de [Aplicações, consulte implementar a sua aplicação para o Serviço de Aplicações utilizando FTP/S](deploy-ftp.md).
