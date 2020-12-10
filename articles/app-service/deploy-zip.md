---
title: Implementar código com um ficheiro ZIP ou WAR
description: Saiba como implementar a sua aplicação no Azure App Service com um ficheiro ZIP (ou um ficheiro WAR para desenvolvedores java).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 150f6b57f1dec0b6d925ef53b4a7001ae9f23607
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007913"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Implemente a sua aplicação para o Azure App Service com um ficheiro ZIP ou WAR

Este artigo mostra-lhe como usar um ficheiro ZIP ou WAR para implementar a sua aplicação web para [o Azure App Service](overview.md). 

Esta implementação de ficheiros ZIP utiliza o mesmo serviço Kudu que alimenta implementações baseadas em integração contínua. A Kudu suporta a seguinte funcionalidade para a implementação de ficheiros ZIP: 

- Eliminação de ficheiros que sobram de uma implantação anterior.
- Opção de ligar o processo de construção predefinido, que inclui a restauração do pacote.
- Personalização de implementação, incluindo scripts de implementação.  
- Registos de implantação. 
- Um limite de tamanho de ficheiro de 2048 MB.

Para mais informações, consulte [a documentação da Kudu.](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)

A implementação do ficheiro WAR implementa o seu ficheiro [WAR](https://wikipedia.org/wiki/WAR_(file_format)) para o Serviço de Aplicações para executar a sua aplicação web Java. Consulte [o ficheiro Deploy WAR](#deploy-war-file).

> [!NOTE]
> Ao `ZipDeploy` utilizar, os ficheiros só serão copiados se os seus cartões de tempo não corresponderem ao que já está implantado. Gerar um fecho de correr utilizando um processo de construção que caches saídas pode resultar em implementações mais rápidas. Consulte [a implementação de um ficheiro zip ou url,](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)para obter mais informações.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos deste artigo, [crie uma aplicação de Serviço de Aplicações,](./index.yml)ou use uma app que criou para outro tutorial.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
O ponto final acima não funciona para os Serviços de Aplicações Linux neste momento. Considere a utilização de FTP ou a [API de implementação ZIP.](faq-app-service-linux.md#continuous-integration-and-deployment)

## <a name="deploy-zip-file-with-azure-cli"></a>Implementar ficheiro ZIP com Azure CLI

Implemente o ficheiro ZIP carregado para a sua aplicação web utilizando o comando [config-zip de config-zip da fonte de implementação do webapp.](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-zip)  

O exemplo a seguir implementa o ficheiro ZIP que carregou. Ao utilizar uma instalação local do Azure CLI, especifique o caminho para o seu ficheiro ZIP local para `--src` .

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Este comando implementa os ficheiros e os diretórios do ficheiro ZIP na pasta de aplicações do Serviço de Aplicações predefinido (`\home\site\wwwroot`) e reinicia a aplicação.

Por predefinição, o motor de implantação assume que um ficheiro ZIP está pronto para funcionar como está e não funciona qualquer automatização de construção. Para ativar a mesma automatização de construção como numa [implementação git,](deploy-local-git.md)defina a definição da `SCM_DO_BUILD_DURING_DEPLOYMENT` aplicação executando o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Para mais informações, consulte [a documentação da Kudu.](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Implementar ficheiro WAR

Para implementar um ficheiro WAR no Serviço de Aplicações, envie um pedido DEM para `https://<app-name>.scm.azurewebsites.net/api/wardeploy` . O pedido POST tem de conter o ficheiro .war no corpo da mensagem. As credenciais de implementação para a sua aplicação são fornecidas no pedido através da autenticação básica HTTP.

Utilize sempre `/api/wardeploy` ao utilizar ficheiros WAR. Esta API expandirá o seu ficheiro WAR e colocá-lo-á na unidade de ficheiros partilhada. usando outras APIs de implantação pode resultar em comportamento inconsistente. 

Para a autenticação HTTP BASIC, precisa das suas credenciais de implementação do Serviço de Aplicações. Para ver como definir as suas credenciais de implementação, consulte [definição e reinicie as credenciais de nível de utilizador](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Com cURL

O exemplo a seguir utiliza a ferramenta cURL para implantar um ficheiro .war. Substitua os espaços `<username>` `<war-file-path>` reservados, e . `<app-name>` . Quando solicitado pela cURL, escreva a palavra-passe.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Com o PowerShell

O exemplo a seguir utiliza [o upload do](/powershell/module/az.websites/publish-azwebapp) ficheiro .war. Substitua os espaços `<group-name>` `<app-name>` reservados, e . `<war-file-path>` .

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Passos seguintes

Para cenários de implementação mais avançados, tente [implementar para Azure com Git](deploy-local-git.md). A implementação baseada em Git para o Azure permite o controlo da versão, a restauração do pacote, o MSBuild e muito mais.

## <a name="more-resources"></a>Mais recursos

* [Kudu: Implantação a partir de um ficheiro zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Credenciais de implementação do serviço de aplicações Azure](deploy-ftp.md)
