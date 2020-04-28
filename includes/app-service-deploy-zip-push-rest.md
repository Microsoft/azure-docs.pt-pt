---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/12/2019
ms.author: cephalin
ms.openlocfilehash: 92e39f128e90ba83a919388e217f0edc86f81770
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75769676"
---
## <a name="deploy-zip-file-with-rest-apis"></a><a name="rest"></a>Implementar ficheiro ZIP com APIs REST 

Pode utilizar o serviço de [implementação REST APIs](https://github.com/projectkudu/kudu/wiki/REST-API) para implementar o ficheiro .zip para a sua aplicação em Azure. Para implementar, envie um pedido post para https://<app_name>.scm.azurewebsites.net/api/zipdeploy. O pedido DO POST deve conter o ficheiro .zip no corpo da mensagem. As credenciais de implementação para a sua aplicação são fornecidas no pedido através da autenticação básica HTTP. Para mais informações, consulte a [referência de implantação de impulso .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

Para a autenticação HTTP BASIC, precisa das suas credenciais de implementação do Serviço de Aplicações. Para ver como definir as suas credenciais de implementação, consulte [Definir e redefinir as credenciais de nível de utilizador](../articles/app-service/deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Com cURL

O exemplo seguinte utiliza a ferramenta cURL para implantar um ficheiro .zip. Substitua os `<deployment_user>`espaços `<zip_file_path>`reservados, e `<app_name>`. Quando solicitado por cURL, digite a palavra-passe.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Este pedido aciona a colocação de pressão a partir do ficheiro .zip carregado. Pode rever as implementações atuais `https://<app_name>.scm.azurewebsites.net/api/deployments` e passadas utilizando o ponto final, como mostra o exemplo cURL seguinte. Mais uma `<app_name>` vez, substitua-o pelo nome da sua aplicação e `<deployment_user>` pelo nome de utilizador das suas credenciais de implementação.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>Com o PowerShell

O exemplo seguinte utiliza o upload do Ficheiro .zip [Publish-AzWebapp.](/powershell/module/az.websites/publish-azwebapp) Substitua os `<group-name>`espaços `<app-name>`reservados, e `<zip-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <zip-file-path>
```

Este pedido aciona a colocação de pressão a partir do ficheiro .zip carregado. 

Para rever as atuais e passadas implantações, execute os seguintes comandos. Mais uma `<deployment-user>`vez, substitua os espaços `<deployment-password>`reservados. `<app-name>`

```bash
$username = "<deployment-user>"
$password = "<deployment-password>"
$apiUrl = "https://<app-name>.scm.azurewebsites.net/api/deployments"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
