---
title: FAQs sobre a implementação - serviço de aplicações do Azure | Documentos da Microsoft
description: Obtenha respostas às perguntas mais frequentes sobre a implementação para a funcionalidade aplicações Web do serviço de aplicações do Azure.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 9d38e3caf3d835eaf73df90282157eed76c5ea25
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58350645"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>FAQs sobre a implementação de aplicações Web no Azure

Este artigo tem respostas a perguntas mais frequentes (FAQ) sobre problemas de implementação para o [funcionalidade de aplicações Web do serviço de aplicações do Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Eu estou apenas a começar com as aplicações web do serviço de aplicações. Como publicar o meu código?

Aqui estão algumas opções para a publicação de seu código de aplicação web:

*   Implante usando o Visual Studio. Se tiver a solução do Visual Studio, o projeto de aplicativo web com o botão direito e, em seguida, selecione **publicar**.
*   Implemente com um cliente de FTP. No portal do Azure, transfira o perfil de publicação da aplicação web que pretende implementar o seu código. Em seguida, carregue os ficheiros para \site\wwwroot utilizando as mesmas credenciais FTP do perfil de publicação.

Para obter mais informações, consulte [implementar a aplicação no serviço de aplicações](deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this"></a>Vejo uma mensagem de erro ao tentar implementar a partir do Visual Studio. Como posso resolver isto?

Se vir a mensagem seguinte, poderá estar a utilizar uma versão mais antiga do SDK: "Erro durante a implementação para o recurso"YourResourceName"no grupo de recursos"YourResourceGroup": MissingRegistrationForLocation: A subscrição não está registada para o tipo de recurso "componentes" na localização "E.U.A. Central". Volte a registar para este fornecedor para ter acesso a esta localização." 

Para resolver este erro, atualize para o [SDK mais recente](https://azure.microsoft.com/downloads/). Se vir esta mensagem e tem o SDK mais recente, submeta um pedido de suporte.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Como posso implementar uma aplicação ASP.NET do Visual Studio para o serviço de aplicações?
<a id="deployasp"></a>

O tutorial [criar a sua primeira aplicação web ASP.NET no Azure em cinco minutos](app-service-web-get-started-dotnet.md) mostra-lhe como implementar uma aplicação web ASP.NET numa aplicação web no serviço de aplicações com o Visual Studio 2017.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Quais são os diferentes tipos de credenciais de implementação?

Serviço de aplicações suporta dois tipos de credenciais para a implementação de FTP/S e de implementação de Git local. Para obter mais informações sobre como configurar as credenciais de implementação, consulte [configurar as credenciais de implementação para o serviço de aplicações](deploy-configure-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>O que é a estrutura de arquivo ou diretório do meu aplicativo web do serviço de aplicações?

Para obter informações sobre a estrutura de ficheiros da sua aplicação de serviço de aplicações, consulte [estrutura de ficheiros no Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Como posso resolver "FTP erro 550 - daí não há espaço suficiente no disco" quando tento de meus arquivos de FTP?

Se vir esta mensagem, é provável que se deparar com uma quota de disco no plano do serviço para a sua aplicação web. Poderá ter de aumentar verticalmente para um escalão de serviço superior com base nas suas necessidades de espaço em disco. Para obter mais informações sobre os preços de planos e os limites de recursos, consulte [preços do serviço de aplicações](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Como posso configurar a implementação contínua para a minha aplicação web do serviço de aplicações?

Pode configurar a implementação contínua a partir de vários recursos, incluindo o Azure DevOps, OneDrive, GitHub, Bitbucket, Dropbox e outros repositórios de Git. Estas opções estão disponíveis no portal. [Implementação contínua no serviço de aplicações](deploy-continuous-deployment.md) é um tutorial útil que explica como configurar a implementação contínua.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Como posso resolver problemas com implementação contínua a partir do GitHub e Bitbucket?

Para ajudar a investigar problemas com implementação contínua a partir do GitHub ou Bitbucket, consulte [investigar a implementação contínua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this"></a>Eu não é possível FTP para o meu site e publicar o meu código. Como posso resolver isto?

Para resolver problemas FTP:

1. Certifique-se de que se está a indicar o nome de anfitrião correto e as credenciais. Para obter informações detalhadas sobre os diferentes tipos de credenciais e como usá-las, consulte [credenciais de implementação](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Certifique-se de que as portas FTP não estão bloqueadas por uma firewall. As portas devem ter estas definições:
    * Porta de ligação de controlo de FTP: 21
    * Porta de ligação de dados FTP: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Como publicar o meu código no serviço de aplicações?

O guia de introdução do Azure foi concebido para ajudar a implementar a aplicação com a pilha de implementação e o método à sua escolha. Para utilizar o início rápido, no portal do Azure, aceda ao seu serviço de aplicações, em **implantação**, selecione **guia de introdução**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Por que a minha aplicação, por vezes, reiniciar após a implementação no serviço de aplicações?

Para saber mais sobre as circunstâncias em que uma implementação de aplicações pode resultar num reinício, veja [implementação vs. problemas de runtime](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). Tal como descrito no artigo, o serviço de aplicações implementa ficheiros para a pasta wwwroot. Nunca diretamente reinicia seu aplicativo.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Como posso integrar o código de DevOps do Azure com o serviço de aplicações?

Tem duas opções para utilizar a implementação contínua com o DevOps do Azure:

*   Utilize um projeto de Git. Ligar através do serviço de aplicações através do Centro de implementação.
*   Utilize um projeto de controle de versão Foundation (TFVC) da equipe. Implemente com o agente de compilação para o serviço de aplicações.

Implementação contínua de código para ambas estas opções depende de fluxos de trabalho de programador existentes e os procedimentos de check-in. Para obter mais informações, veja estes artigos: 

*   [Implementar a implementação contínua da sua aplicação para um site do Azure](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Configurar uma organização do Azure DevOps, para que ele pode implementar uma aplicação web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Como usar FTP ou FTPS para implementar a minha aplicação no serviço de aplicações?

Para obter informações sobre como utilizar o FTP ou FTPS para implementar a aplicação web no serviço de aplicações, consulte [implementar a aplicação no serviço de aplicações através de FTP/S](deploy-ftp.md).
