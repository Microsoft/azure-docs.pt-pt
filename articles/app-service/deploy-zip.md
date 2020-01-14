---
title: Implantar código com um arquivo ZIP ou WAR
description: Saiba como implantar seu aplicativo no serviço de Azure App com um arquivo ZIP (ou um arquivo WAR para desenvolvedores de Java).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 28bd45b0e9bdaf87c29b0118c47595db9179edc5
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921163"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Implantar seu aplicativo no serviço de Azure App com um arquivo ZIP ou WAR

Este artigo mostra como usar um arquivo ZIP ou um arquivo WAR para implantar seu aplicativo Web no [serviço Azure app](overview.md). 

Essa implantação de arquivo ZIP usa o mesmo serviço kudu que capacita implantações baseadas em integração contínua. O kudu dá suporte à seguinte funcionalidade para implantação de arquivo ZIP: 

- Exclusão de arquivos deixados de uma implantação anterior.
- Opção para ativar o processo de compilação padrão, que inclui a restauração do pacote.
- [Personalização da implantação](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), incluindo a execução de scripts de implantação.  
- Logs de implantação. 
- Um limite de tamanho de arquivo de 2048 MB.

Para obter mais informações, consulte a [documentação do kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

A implantação de arquivo WAR implanta seu arquivo [War](https://wikipedia.org/wiki/WAR_(file_format)) no serviço de aplicativo para executar seu aplicativo Web Java. Consulte [implantar arquivo War](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo:

* [Crie uma aplicação do Serviço de Aplicações](/azure/app-service/) ou utilize uma aplicação que tenha criado para outro tutorial.

## <a name="create-a-project-zip-file"></a>Criar um ficheiro ZIP do projeto

>[!NOTE]
> Se você baixou os arquivos em um arquivo ZIP, extraia os arquivos primeiro. Por exemplo, se você baixou um arquivo ZIP do GitHub, não poderá implantar esse arquivo no estado em que se encontra. O GitHub adiciona diretórios aninhados adicionais, que não funcionam com o serviço de aplicativo. 
>

Em uma janela de terminal local, navegue até o diretório raiz do seu projeto de aplicativo. 

Esse diretório deve conter o arquivo de entrada para seu aplicativo Web, como _index. html_, _index. php_e _app. js_. Ele também pode conter arquivos de gerenciamento de pacotes como _Project. JSON_, _Composer. JSON_, _Package. JSON_, _Bower. JSON_e _requirements. txt_.

Crie um arquivo ZIP de tudo no seu projeto. O comando seguinte utiliza a ferramenta predefinida no seu terminal:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
O ponto de extremidade acima não funciona para os serviços de aplicativos do Linux no momento. Considere usar o FTP ou a [API de implantação de zip](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment) em vez disso.

## <a name="deploy-zip-file-with-azure-cli"></a>Implantar arquivo ZIP com CLI do Azure

Verifique se sua versão do CLI do Azure é 2.0.21 ou posterior. Para ver qual versão você tem, execute `az --version` comando na janela do seu terminal.

Implante o arquivo ZIP carregado em seu aplicativo Web usando o comando [AZ webapp Deployment Source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) .  

O exemplo a seguir implanta o arquivo ZIP que você carregou. Ao usar uma instalação local do CLI do Azure, especifique o caminho para o arquivo ZIP local para `--src`.

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Este comando implementa os ficheiros e os diretórios do ficheiro ZIP na pasta de aplicações do Serviço de Aplicações predefinido (`\home\site\wwwroot`) e reinicia a aplicação.

Por padrão, o mecanismo de implantação assume que um arquivo ZIP está pronto para ser executado no estado em que se encontra e não executa nenhuma automação de compilação. Para habilitar a mesma automação de compilação que em uma [implantação do git](deploy-local-git.md), defina a configuração do aplicativo `SCM_DO_BUILD_DURING_DEPLOYMENT` executando o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```



Para obter mais informações, consulte a [documentação do kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Implantar arquivo WAR

Para implantar um arquivo WAR no serviço de aplicativo, envie uma solicitação POST para `https://<app_name>.scm.azurewebsites.net/api/wardeploy`. O pedido POST tem de conter o ficheiro .war no corpo da mensagem. As credenciais de implementação para a sua aplicação são fornecidas no pedido através da autenticação básica HTTP.

Sempre use `/api/wardeploy` ao implantar arquivos WAR. Essa API expandirá o arquivo WAR e o posicionará na unidade de arquivo compartilhada. o uso de outras APIs de implantação pode resultar em um comportamento inconsistente. 

Para a autenticação básica HTTP, você precisa de suas credenciais de implantação do serviço de aplicativo. Para ver como definir suas credenciais de implantação, consulte [definir e redefinir credenciais de nível de usuário](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Com ondulação

O exemplo a seguir usa a ferramenta de rotação para implantar um arquivo. War. Substitua os espaços reservados `<username>`, `<war-file-path>`e `<app-name>`. Quando solicitado por ondulação, digite a senha.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
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
