---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/12/2019
ms.author: cephalin
ms.openlocfilehash: 92e39f128e90ba83a919388e217f0edc86f81770
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018451"
---
## <a name="deploy-zip-file-with-rest-apis"></a><a name="rest"></a>Implementar ficheiro ZIP com APIs REST 

Pode utilizar o [serviço de implementação REST APIs](https://github.com/projectkudu/kudu/wiki/REST-API) para implementar o ficheiro .zip na sua aplicação em Azure. Para implementar, envie um pedido DEM para https://<app_name>.scm.azurewebsites.net/api/zipdeploy. O pedido do POST deve conter o ficheiro .zip no corpo da mensagem. As credenciais de implementação para a sua aplicação são fornecidas no pedido através da autenticação básica HTTP. Para obter mais informações, consulte a [referência de utilização de .zip push](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

Para a autenticação HTTP BASIC, precisa das suas credenciais de implementação do Serviço de Aplicações. Para ver como definir as suas credenciais de implementação, consulte [definição e reinicie as credenciais de nível de utilizador](../articles/app-service/deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Com cURL

O exemplo a seguir utiliza a ferramenta cURL para implantar um ficheiro .zip. Substitua os espaços `<deployment_user>` `<zip_file_path>` reservados, e . `<app_name>` . Quando solicitado pela cURL, escreva a palavra-passe.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Este pedido aciona a implementação do ficheiro de .zip carregado. Pode rever as implementações atuais e passadas utilizando o `https://<app_name>.scm.azurewebsites.net/api/deployments` ponto final, como mostra o exemplo cURL a seguir. Mais uma vez, `<app_name>` substitua-o pelo nome da sua aplicação e `<deployment_user>` pelo nome de utilizador das suas credenciais de implementação.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>Com o PowerShell

O exemplo a seguir utiliza o upload do ficheiro [.zip.](/powershell/module/az.websites/publish-azwebapp) Substitua os espaços `<group-name>` `<app-name>` reservados, e . `<zip-file-path>` .

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <zip-file-path>
```

Este pedido aciona a implementação do ficheiro de .zip carregado. 

Para rever as implementações atuais e anteriores, executar os seguintes comandos. Mais uma vez, substitua os `<deployment-user>` `<deployment-password>` espaços `<app-name>` reservados e reservados.

```bash
$username = "<deployment-user>"
$password = "<deployment-password>"
$apiUrl = "https://<app-name>.scm.azurewebsites.net/api/deployments"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
