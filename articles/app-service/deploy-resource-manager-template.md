---
title: Implementar aplicativos com modelos
description: Encontre orientação sobre a criação de modelos de Gestor de Recursos Azure para a disponibilização e implementação de aplicações do Serviço de Aplicações.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: ed6edeadfb1c6f73cc10771d4a5328e7bddb3642
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835169"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Orientação sobre a implementação de aplicações web utilizando modelos de Gestor de Recursos Azure

Este artigo fornece recomendações para criar modelos de Gestor de Recursos Azure para implementar soluções de Serviço de Aplicações Azure. Estas recomendações podem ajudá-lo a evitar problemas comuns.

## <a name="define-dependencies"></a>Definir dependências

Definir dependências para aplicações web requer uma compreensão de como os recursos dentro de uma aplicação web interagem. Se especificar as dependências numa ordem incorreta, poderá causar erros de implantação ou criar uma condição de corrida que impeça a implantação.

> [!WARNING]
> Se incluir uma extensão do site MSDeploy no seu modelo, deve definir quaisquer recursos de configuração como dependentes do recurso MSDeploy. As alterações de configuração fazem com que o site reinicie assíncroticamente. Ao tornar os recursos de configuração dependentes do MSDeploy, certifique-se de que a MSDeploy termina antes do reinício do site. Sem estas dependências, o site pode reiniciar durante o processo de implantação da MSDeploy. Para um modelo de exemplo, consulte [o modelo WordPress com a dependência de implementação da web](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

A imagem a seguir mostra a ordem de dependência de vários recursos do Serviço de Aplicações:

![Dependências de aplicativos web](media/web-sites-rm-template-guidance/web-dependencies.png)

Utiliza recursos na seguinte ordem:

**Nível 1**
* Plano de Serviço de Aplicações.
* Quaisquer outros recursos relacionados, como bases de dados ou contas de armazenamento.

**Camada 2**
* A aplicação web depende do plano de Serviço de Aplicações.
* A exemplo de Azure Application Insights que visa a fazenda do servidor - depende do plano de Serviço de Aplicações.

**Nível 3**
* O controlo de origem depende da aplicação web.
* A extensão do site MSDeploy depende da aplicação web.
* A exemplo de Azure Application Insights que visa a aplicação web -- depende da aplicação web.

**Nível 4**
* O certificado de Serviço de Aplicação depende do controlo de origem ou do MSDeploy se um deles estiver presente. Caso contrário, depende da aplicação web.
* As definições de configuração (cadeias de ligação, valores de web.config, definições de aplicações)-- dependem do controlo de origem ou do MSDeploy se ambos estiverem presentes. Caso contrário, depende da aplicação web.

**Nível 5**
* As vinculações do nome do anfitrião dependem do certificado se estiverem presentes. Caso contrário, depende de um recurso de nível superior.
* As extensões do site dependem das definições de configuração se estiverem presentes. Caso contrário, depende de um recurso de nível superior.

Normalmente, a sua solução inclui apenas alguns destes recursos e níveis. Para os níveis em falta, mapear recursos mais baixos para o nível seguinte mais alto.

O exemplo a seguir mostra parte de um modelo. O valor da configuração da cadeia de ligação depende da extensão MSDeploy. A extensão MSDeploy depende da aplicação web e da base de dados. 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Para obter uma amostra pronta a ser executada que utilize o código acima, consulte [o Modelo: Construa uma simples Aplicação Web Umbraco](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>Encontre informações sobre erros da MSDeploy

Se o seu modelo de Gestor de Recursos utilizar o MSDeploy, as mensagens de erro de implementação podem ser difíceis de entender. Para obter mais informações após uma implantação falhada, experimente os seguintes passos:

1. Aceda à [consola Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console)do site.
2. Navegue na pasta em D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Procure os ficheiros appManagerStatus.xml e appManagerLog.xml. O primeiro ficheiro regista o estado. O segundo ficheiro regista informações sobre o erro. Se o erro não for claro para si, pode incluí-lo quando estiver a pedir ajuda no [fórum.](/answers/topics/azure-webapps.html)

## <a name="choose-a-unique-web-app-name"></a>Escolha um nome exclusivo de aplicativos web

O nome da sua aplicação web deve ser globalmente único. Você pode usar uma convenção de nomeação que é provável que seja única, ou você pode usar a [função de tura única](../azure-resource-manager/templates/template-functions-string.md#uniquestring) para ajudar a gerar um nome único.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Implementar certificado de aplicativo web a partir de Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se o seu modelo incluir um recurso [Microsoft.Web/certificados](/azure/templates/microsoft.web/certificates) para a ligação TLS/SSL, e o certificado for armazenado num Cofre-Chave, deve certificar-se de que a identidade do Serviço de Aplicações pode aceder ao certificado.

No Global Azure, o responsável pelo serviço de aplicações tem o ID de **abfa0a7c-a6b6-4736-8310-5855508787cd.** Para conceder acesso ao Key Vault para o serviço de aplicações principal, utilize:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

No Governo de Azure, o serviço de aplicações tem o ID de **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Use essa identificação no exemplo anterior.

No seu Cofre-Chave, selecione **Certificados** e **Gere/Importe** para carregar o certificado.

![Importar o certificado](media/web-sites-rm-template-guidance/import-certificate.png)

No seu modelo, forneça o nome do certificado para `keyVaultSecretName` .

Para obter um modelo de exemplo, consulte [implementar um certificado de Aplicação Web do segredo key Vault e usá-lo para criar a ligação SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault).

## <a name="next-steps"></a>Passos seguintes

* Para um tutorial sobre a implementação de aplicações web com um modelo, consulte [Provision e implemente microserviços previsivelmente em Azure](deploy-complex-application-predictably.md).
* Para saber mais sobre a sintaxe JSON e propriedades para tipos de recursos em modelos, consulte [a referência do modelo do Gestor de Recursos Azure](/azure/templates/).
