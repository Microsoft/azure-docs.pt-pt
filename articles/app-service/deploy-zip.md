---
title: Implementar código com um ficheiro ZIP ou WAR - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como implementar a aplicação no App Service do Azure com um arquivo ZIP (ou um ficheiro WAR para programadores Java).
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: cephalin;sisirap
ms.custom: seodec18
ms.openlocfilehash: 1bc8dc822622ee7b16b3e0a31e7b0b66ed7556e6
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59488410"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Implementar a aplicação no App Service do Azure com um ficheiro ZIP ou WAR

Este artigo mostra-lhe como utilizar um ficheiro ZIP ou o ficheiro WAR para implementar a aplicação web para [App Service do Azure](overview.md). 

Esta implementação de arquivo ZIP utiliza o mesmo serviço de Kudu esse implementações baseadas em integração contínua do powers. Kudu suporta as seguintes funcionalidades para a implementação de arquivo ZIP: 

- Eliminações de ficheiros left de uma implantação anterior.
- Opção de ativar o processo de compilação padrão, que inclui o restauro do pacote.
- [Personalização da implementação](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), incluindo executar scripts de implementação.  
- Logs de implantação. 
- Um limite de tamanho de ficheiro de 2048 MB.

Para obter mais informações, consulte [documentação de Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

A implementação de ficheiro WAR implementa sua [WAR](https://wikipedia.org/wiki/WAR_(file_format)) ficheiro para o serviço de aplicações para executar a aplicação web Java. Ver [ficheiro WAR implementar](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste artigo:

* [Crie uma aplicação do Serviço de Aplicações](/azure/app-service/) ou utilize uma aplicação que tenha criado para outro tutorial.

## <a name="create-a-project-zip-file"></a>Criar um ficheiro ZIP do projeto

>[!NOTE]
> Se transferiu os ficheiros num arquivo ZIP, extraia os ficheiros em primeiro lugar. Por exemplo, se tiver transferido um ficheiro ZIP do GitHub, não é possível implementar esse arquivo como – é. GitHub adiciona diretórios aninhados adicionais, que não funcionam com o serviço de aplicações. 
>

Na janela do terminal local, navegue para o diretório de raiz do seu projeto de aplicação. 

Este diretório deve conter o ficheiro de entrada para a sua aplicação web, tal como _Index. HTML_, _Index_, e _App. js_. Também pode conter ficheiros do pacote de gestão, como _Project_, _Composer. JSON_, _Package. JSON_, _bower_e o _Requirements. txt_.

Crie um arquivo ZIP de tudo no seu projeto. O comando seguinte utiliza a ferramenta predefinida no seu terminal:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="deploy-zip-file-with-azure-cli"></a>Implementar o ficheiro ZIP com a CLI do Azure

Certifique-se de que a versão da CLI do Azure é 2.0.21 ou posterior. Para ver qual é a versão, executou `az --version` comando na janela de terminal.

Implementar o ficheiro ZIP carregado na sua aplicação web com o [az webapp config origem de implementação de software-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) comando.  

O exemplo seguinte implementa o ficheiro ZIP carregado. Quando utilizar uma instalação local da CLI do Azure, especifique o caminho para o ficheiro ZIP local para `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Este comando implementa os ficheiros e os diretórios do ficheiro ZIP na pasta de aplicações do Serviço de Aplicações predefinido (`\home\site\wwwroot`) e reinicia a aplicação. Se estiver configurado qualquer processo de compilação personalizado adicional, este é executado corretamente. Para obter mais informações, consulte [documentação de Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Implementar ficheiro WAR

Para implementar um ficheiro WAR para o serviço de aplicações, envie um pedido POST para https://<app_name>.scm.azurewebsites.net/api/wardeploy. O pedido POST tem de conter o ficheiro .war no corpo da mensagem. As credenciais de implementação para a sua aplicação são fornecidas no pedido através da autenticação básica HTTP. 

Para a autenticação básica HTTP, terá de suas credenciais de implementação do serviço de aplicações. Para ver como definir as suas credenciais de implementação, consulte [definido e repor as credenciais de nível de usuário](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Com cURL

O exemplo seguinte utiliza a ferramenta cURL para implementar um ficheiro. War. Substitua os marcadores de posição `<username>`, `<war_file_path>`, e `<app_name>`. Quando lhe for pedido curl, escreva a palavra-passe.

```bash
curl -X POST -u <username> --data-binary @"<war_file_path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Com o PowerShell

O exemplo seguinte utiliza [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) para enviar um pedido que contém o ficheiro. War. Substitua os marcadores de posição `<deployment_user>`, `<deployment_password>`, `<zip_file_path>`, e `<app_name>`.

```powershell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<war_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/wardeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Passos Seguintes

Para cenários de implementação mais avançados, tente [implementar no Azure com o Git](deploy-local-git.md). Implementação baseada no Git para o Azure permite o controle de versão, o restauro de pacote, MSBuild e muito mais.

## <a name="more-resources"></a>Mais recursos

* [Kudu: Implementar a partir de um arquivo zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Credenciais de implementação do serviço de aplicações do Azure](deploy-ftp.md)
