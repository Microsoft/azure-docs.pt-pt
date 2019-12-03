---
title: Perguntas frequentes sobre implantação-serviço de Azure App | Microsoft Docs
description: Obtenha respostas para perguntas frequentes sobre a implantação do recurso de aplicativos Web do serviço de Azure App.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 25f43051f11d42c4f8565039d3168f2ae162eb37
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671690"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Perguntas frequentes sobre implantação para aplicativos Web no Azure

Este artigo tem respostas para perguntas frequentes sobre problemas de implantação para o [recurso de aplicativos Web do serviço de Azure app](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Estou apenas começando a usar os aplicativos Web do serviço de aplicativo. Como fazer publicar meu código?

Aqui estão algumas opções para publicar o código do aplicativo Web:

*   Implantar usando o Visual Studio. Se você tiver a solução do Visual Studio, clique com o botão direito do mouse no projeto de aplicativo Web e selecione **publicar**.
*   Implantar usando um cliente FTP. No portal do Azure, baixe o perfil de publicação para o aplicativo Web no qual você deseja implantar seu código. Em seguida, carregue os arquivos para \site\wwwroot usando as mesmas credenciais de FTP do perfil de publicação.

Para obter mais informações, consulte [implantar seu aplicativo no serviço de aplicativo](deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this-error"></a>Vejo uma mensagem de erro quando tento implantar do Visual Studio. Como fazer resolver esse erro?

Se você vir a seguinte mensagem, talvez esteja usando uma versão mais antiga do SDK: "erro durante a implantação do recurso ' YourResourceName ' no grupo de recursos ' Seugruporecursos ': MissingRegistrationForLocation: a assinatura não está registrada para o tipo de recurso ' componentes ' no local ' EUA Central '. Registre-se novamente para esse provedor para ter acesso a esse local. " 

Para resolver esse erro, atualize para o [SDK mais recente](https://azure.microsoft.com/downloads/). Se você vir essa mensagem e tiver o SDK mais recente, envie uma solicitação de suporte.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Como fazer implantar um aplicativo ASP.NET do Visual Studio para o serviço de aplicativo?
<a id="deployasp"></a>

O tutorial [criar seu primeiro aplicativo web ASP.net no Azure em cinco minutos](app-service-web-get-started-dotnet.md) mostra como implantar um aplicativo Web ASP.net em um aplicativo Web no serviço de aplicativo usando o Visual Studio.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Quais são os diferentes tipos de credenciais de implantação?

O serviço de aplicativo dá suporte a dois tipos de credenciais para implantação local do git e implantação de FTP/S. Para obter mais informações sobre como configurar credenciais de implantação, consulte [configurar as credenciais de implantação para o serviço de aplicativo](deploy-configure-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Qual é a estrutura de arquivos ou diretórios do meu aplicativo Web do serviço de aplicativo?

Para obter informações sobre a estrutura de arquivos do seu aplicativo do serviço de aplicativo, consulte [estrutura de arquivos no Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Como fazer resolver "erro de FTP 550-não há espaço suficiente no disco" quando tento o FTP meus arquivos?

Se você vir essa mensagem, é provável que você esteja executando uma cota de disco no plano de serviço para seu aplicativo Web. Talvez seja necessário escalar verticalmente para uma camada de serviço superior com base nas necessidades de espaço em disco. Para obter mais informações sobre planos de preços e limites de recursos, consulte [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Como fazer configurar a implantação contínua para meu aplicativo Web do serviço de aplicativo?

Você pode configurar a implantação contínua de vários recursos, incluindo Azure DevOps, OneDrive, GitHub, bitbucket, Dropbox e outros repositórios git. Essas opções estão disponíveis no Portal. [A implantação contínua no serviço de aplicativo](deploy-continuous-deployment.md) é um tutorial útil que explica como configurar a implantação contínua.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Como fazer solucionar problemas com a implantação contínua do GitHub e do bitbucket?

Para ajudar a investigar problemas com a implantação contínua do GitHub ou do bitbucket, consulte [investigando a implantação contínua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this-issue"></a>Não consigo fazer o FTP no meu site e publicar meu código. Como fazer resolver esse problema?

Para resolver problemas de FTP:

1. Verifique se você está inserindo o nome de host e as credenciais corretas. Para obter informações detalhadas sobre diferentes tipos de credenciais e como usá-las, consulte [credenciais de implantação](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Verifique se as portas de FTP não estão bloqueadas por um firewall. As portas devem ter essas configurações:
    * Porta de ligação de controlo de FTP: 21
    * Porta de conexão de dados FTP: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Como fazer publicar meu código no serviço de aplicativo?

O início rápido do Azure foi projetado para ajudá-lo a implantar seu aplicativo usando a pilha de implantação e o método de sua escolha. Para usar o início rápido, na portal do Azure, vá para o serviço de aplicativo, em **implantação**, selecione **início rápido**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Por que meu aplicativo às vezes reinicia após a implantação no serviço de aplicativo?

Para saber mais sobre as circunstâncias sob as quais uma implantação de aplicativo pode resultar em uma reinicialização, consulte [implantação versus problemas de tempo de execução](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). Como descreve o artigo, o serviço de aplicativo implanta arquivos na pasta wwwroot. Ele nunca reinicia seu aplicativo diretamente.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Como fazer integrar o código DevOps do Azure com o serviço de aplicativo?

Você tem duas opções para usar a implantação contínua com o Azure DevOps:

*   Use um projeto git. Conecte-se por meio do serviço de aplicativo usando o centro de implantação.
*   Use um projeto Controle de Versão do Team Foundation (TFVC). Implante usando o agente de compilação para o serviço de aplicativo.

A implantação contínua de código para essas duas opções depende dos fluxos de trabalho do desenvolvedor e dos procedimentos de check-in existentes. Para obter mais informações, veja estes artigos: 

*   [Implementar a implantação contínua de seu aplicativo em um site do Azure](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Configurar uma organização do Azure DevOps para que ela possa ser implantada em um aplicativo Web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Como fazer usar FTP ou FTPS para implantar meu aplicativo no serviço de aplicativo?

Para obter informações sobre como usar FTP ou FTPS para implantar seu aplicativo Web no serviço de aplicativo, consulte [implantar seu aplicativo no serviço de aplicativo usando FTP/S](deploy-ftp.md).
