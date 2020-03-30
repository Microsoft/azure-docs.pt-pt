---
title: Implementar código com um ficheiro ZIP ou WAR
description: Saiba como implementar a sua aplicação para o Azure App Service com um ficheiro ZIP (ou um ficheiro WAR para desenvolvedores de Java).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 716f6813e37aec086a7d496e001fe2ca0f4aab57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945136"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Implemente a sua aplicação para o Azure App Service com um ficheiro ZIP ou WAR

Este artigo mostra-lhe como usar um ficheiro ZIP ou ficheiro WAR para implementar a sua aplicação web para [o Azure App Service](overview.md). 

Esta implementação de ficheiros ZIP utiliza o mesmo serviço Kudu que alimenta implementações contínuas baseadas na integração. Kudu suporta a seguinte funcionalidade para a implementação de ficheiros ZIP: 

- Eliminação de ficheiros que sobrados de uma implantação anterior.
- Opção de ativar o processo de construção predefinido, que inclui a restauração do pacote.
- Personalização de implementação, incluindo scripts de implementação de execução.  
- Registos de implantação. 
- Um limite de tamanho de ficheiro de 2048 MB.

Para mais informações, consulte a [documentação de Kudu.](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)

A implementação de ficheiros WAR implementa o seu ficheiro [WAR](https://wikipedia.org/wiki/WAR_(file_format)) para o App Service para executar a sua aplicação web Java. Ver [Deploy WAR file](#deploy-war-file).

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos deste artigo, [crie uma app de Serviço de Aplicações](/azure/app-service/), ou use uma app que criou para outro tutorial.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
O ponto final acima não funciona para os Serviços de Aplicações Linux neste momento. Considere utilizar ftp ou a API de [implantação ZIP.](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment)

## <a name="deploy-zip-file-with-azure-cli"></a>Implementar ficheiro ZIP com o Azure CLI

Implemente o ficheiro ZIP enviado para a sua aplicação web utilizando o comando config-zip de fonte de [implementação da webapp az.](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip)  

O exemplo seguinte implementa o ficheiro ZIP que fez upload. Quando utilizar uma instalação local do Azure CLI, `--src`especifique o caminho para o seu ficheiro ZIP local para .

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Este comando implementa os ficheiros e os diretórios do ficheiro ZIP na pasta de aplicações do Serviço de Aplicações predefinido (`\home\site\wwwroot`) e reinicia a aplicação.

Por padrão, o motor de implantação pressupõe que um ficheiro ZIP está pronto para funcionar como está e não executa qualquer automatização de construção. Para permitir a mesma automatização de construção `SCM_DO_BUILD_DURING_DEPLOYMENT` que numa [implementação git,](deploy-local-git.md)defina a definição da aplicação executando o seguinte comando na Cloud [Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Para mais informações, consulte a [documentação de Kudu.](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Implementar ficheiro WAR

Para implementar um ficheiro WAR para o `https://<app-name>.scm.azurewebsites.net/api/wardeploy`Serviço de Aplicações, envie um pedido post para . O pedido POST tem de conter o ficheiro .war no corpo da mensagem. As credenciais de implementação para a sua aplicação são fornecidas no pedido através da autenticação básica HTTP.

Utilize `/api/wardeploy` sempre ao implementar ficheiros WAR. Esta API expandirá o seu ficheiro WAR e colocá-lo-á na unidade de ficheiros partilhados. a utilização de outras APIs de implantação pode resultar em comportamento inconsistente. 

Para a autenticação HTTP BASIC, precisa das suas credenciais de implementação do Serviço de Aplicações. Para ver como definir as suas credenciais de implementação, consulte [Definir e redefinir as credenciais de nível de utilizador](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Com cURL

O exemplo seguinte utiliza a ferramenta cURL para implantar um ficheiro .war. Substitua os `<username>`espaços `<war-file-path>`reservados, e `<app-name>`. Quando solicitado por cURL, digite a palavra-passe.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Com o PowerShell

O exemplo seguinte utiliza o upload do Ficheiro .war [Publish-AzWebapp.](/powershell/module/az.websites/publish-azwebapp) Substitua os `<group-name>`espaços `<app-name>`reservados, e `<war-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Passos seguintes

Para cenários de implantação mais avançados, tente [implantar-se para Azure com Git](deploy-local-git.md). A implantação baseada em Git para o Azure permite o controlo da versão, restauro de pacotes, MSBuild e muito mais.

## <a name="more-resources"></a>Mais recursos

* [Kudu: Implantação de um ficheiro zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Credenciais de implantação do serviço de aplicações Azure](deploy-ftp.md)
