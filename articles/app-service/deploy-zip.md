---
title: Implantar código com um arquivo ZIP ou WAR
description: Saiba como implantar seu aplicativo no serviço de Azure App com um arquivo ZIP (ou um arquivo WAR para desenvolvedores de Java).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 716f6813e37aec086a7d496e001fe2ca0f4aab57
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945136"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Implantar seu aplicativo no serviço de Azure App com um arquivo ZIP ou WAR

Este artigo mostra como usar um arquivo ZIP ou um arquivo WAR para implantar seu aplicativo Web no [serviço Azure app](overview.md). 

Essa implantação de arquivo ZIP usa o mesmo serviço kudu que capacita implantações baseadas em integração contínua. O kudu dá suporte à seguinte funcionalidade para implantação de arquivo ZIP: 

- Exclusão de arquivos deixados de uma implantação anterior.
- Opção para ativar o processo de compilação padrão, que inclui a restauração do pacote.
- Personalização da implantação, incluindo a execução de scripts de implantação.  
- Logs de implantação. 
- Um limite de tamanho de arquivo de 2048 MB.

Para obter mais informações, consulte a [documentação do kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

A implantação de arquivo WAR implanta seu arquivo [War](https://wikipedia.org/wiki/WAR_(file_format)) no serviço de aplicativo para executar seu aplicativo Web Java. Consulte [implantar arquivo War](#deploy-war-file).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, [crie um aplicativo do serviço de aplicativo](/azure/app-service/)ou use um aplicativo que você criou para outro tutorial.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
O ponto de extremidade acima não funciona para os serviços de aplicativos do Linux no momento. Considere usar o FTP ou a [API de implantação de zip](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment) em vez disso.

## <a name="deploy-zip-file-with-azure-cli"></a>Implantar arquivo ZIP com CLI do Azure

Implante o arquivo ZIP carregado em seu aplicativo Web usando o comando [AZ webapp Deployment Source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) .  

O exemplo a seguir implanta o arquivo ZIP que você carregou. Ao usar uma instalação local do CLI do Azure, especifique o caminho para o arquivo ZIP local para `--src`.

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Este comando implementa os ficheiros e os diretórios do ficheiro ZIP na pasta de aplicações do Serviço de Aplicações predefinido (`\home\site\wwwroot`) e reinicia a aplicação.

Por padrão, o mecanismo de implantação assume que um arquivo ZIP está pronto para ser executado no estado em que se encontra e não executa nenhuma automação de compilação. Para habilitar a mesma automação de compilação que em uma [implantação do git](deploy-local-git.md), defina a configuração do aplicativo `SCM_DO_BUILD_DURING_DEPLOYMENT` executando o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Para obter mais informações, consulte a [documentação do kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Implantar arquivo WAR

Para implantar um arquivo WAR no serviço de aplicativo, envie uma solicitação POST para `https://<app-name>.scm.azurewebsites.net/api/wardeploy`. O pedido POST tem de conter o ficheiro .war no corpo da mensagem. As credenciais de implementação para a sua aplicação são fornecidas no pedido através da autenticação básica HTTP.

Sempre use `/api/wardeploy` ao implantar arquivos WAR. Essa API expandirá o arquivo WAR e o posicionará na unidade de arquivo compartilhada. o uso de outras APIs de implantação pode resultar em um comportamento inconsistente. 

Para a autenticação básica HTTP, você precisa de suas credenciais de implantação do serviço de aplicativo. Para ver como definir suas credenciais de implantação, consulte [definir e redefinir credenciais de nível de usuário](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Com ondulação

O exemplo a seguir usa a ferramenta de rotação para implantar um arquivo. War. Substitua os espaços reservados `<username>`, `<war-file-path>`e `<app-name>`. Quando solicitado por ondulação, digite a senha.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Com o PowerShell

O exemplo a seguir usa [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) para carregar o arquivo. War. Substitua os espaços reservados `<group-name>`, `<app-name>`e `<war-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Passos seguintes

Para cenários de implantação mais avançados, tente [implantar no Azure com o Git](deploy-local-git.md). A implantação baseada em git para o Azure habilita o controle de versão, a restauração de pacote, o MSBuild e muito mais.

## <a name="more-resources"></a>Mais recursos

* [Kudu: implantando a partir de um arquivo zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Azure App credenciais de implantação do serviço](deploy-ftp.md)
