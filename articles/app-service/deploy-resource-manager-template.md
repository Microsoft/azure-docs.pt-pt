---
title: Implementar aplicativos com modelos
description: Encontre orientações sobre a criação de modelos do Gestor de Recursos Azure para fornecer e implementar aplicações do App Service.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: dfdfa9f69e00aa644c21fc96cb70e9fa460ca0c1
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211704"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Orientação sobre a implementação de aplicações web utilizando modelos de Gestor de Recursos Azure

Este artigo fornece recomendações para a criação de modelos do Gestor de Recursos Azure para implementar soluções do Serviço de Aplicações Azure. Estas recomendações podem ajudá-lo a evitar problemas comuns.

## <a name="define-dependencies"></a>Definir dependências

Definir dependências para aplicações web requer uma compreensão de como os recursos dentro de uma aplicação web interagem. Se especificar dependências numa ordem incorreta, poderá causar erros de implantação ou criar uma condição de corrida que trave a implantação.

> [!WARNING]
> Se incluir uma extensão do site MSDeploy no seu modelo, deve definir quaisquer recursos de configuração conforme dependente do recurso MSDeploy. As alterações de configuração fazem com que o site reinicie assíncronamente. Ao tornar os recursos de configuração dependentes do MSDeploy, certifique-se de que o MSDeploy termina antes do reinício do site. Sem estas dependências, o site poderá reiniciar durante o processo de implementação do MSDeploy. Para um modelo de exemplo, consulte o [modelo WordPress com dependência](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json)de implementação web .

A imagem seguinte mostra a ordem de dependência de vários recursos do Serviço de Aplicações:

![Dependências de aplicativos web](media/web-sites-rm-template-guidance/web-dependencies.png)

Desdobre recursos na seguinte ordem:

**Nível 1**
* Plano de Serviço de Aplicações.
* Quaisquer outros recursos relacionados, como bases de dados ou contas de armazenamento.

**Nível 2**
* A aplicação web depende do plano do Serviço de Aplicações.
* A instância Azure Application Insights que visa a exploração do servidor depende do plano de Serviço de Aplicações.

**Nível 3**
* O controlo de fontes depende da aplicação web.
* A extensão do site MSDeploy depende da aplicação web.
* A instância Azure Application Insights que visa a aplicação web depende da aplicação web.

**Nível 4**
* O certificado do Serviço de Aplicações-- depende do controlo de origem ou do MSDeploy se um dos dois estiver presente. Caso contrário, depende da aplicação web.
* Definições de configuração (strings de ligação, valores web.config, definições de aplicações) - depende do controlo de origem ou do MSDeploy se um dos dois estiver presente. Caso contrário, depende da aplicação web.

**Nível 5**
* As encadernações do nome do anfitrião dependem do certificado se estiverem presentes. Caso contrário, depende de um recurso de nível superior.
* Extensões do site -- depende das definições de configuração se presentes. Caso contrário, depende de um recurso de nível superior.

Normalmente, a sua solução inclui apenas alguns destes recursos e níveis. Para os níveis em falta, mapeie recursos mais baixos para o nível mais próximo.

O exemplo que se segue mostra parte de um modelo. O valor da configuração da cadeia de ligação depende da extensão MSDeploy. A extensão MSDeploy depende da aplicação web e da base de dados. 

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

Para uma amostra pronta a ser executada que utilize o código acima, consulte [Modelo: Construa uma simples Aplicação Web Umbraco](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>Encontre informações sobre erros do MSDeploy

Se o seu modelo de Gestor de Recursos utilizar o MSDeploy, as mensagens de erro de implementação podem ser difíceis de entender. Para obter mais informações após uma implementação falhada, tente os seguintes passos:

1. Vá à consola [Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console)do site.
2. Navegue na pasta em D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Procure os ficheiros appManagerStatus.xml e appManagerLog.xml. O primeiro ficheiro regista o estado. O segundo ficheiro regista informações sobre o erro. Se o erro não for claro para si, pode incluí-lo quando estiver a pedir ajuda no [fórum.](https://docs.microsoft.com/answers/topics/azure-webapps.html)

## <a name="choose-a-unique-web-app-name"></a>Escolha um nome único de aplicativo web

O nome da sua aplicação web deve ser globalmente único. Você pode usar uma convenção de nomeação que é provável que seja única, ou você pode usar a [função String única](../azure-resource-manager/templates/template-functions-string.md#uniquestring) para ajudar a gerar um nome único.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Implementar certificado de aplicativo web a partir do Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se o seu modelo incluir um recurso [Microsoft.Web/certificados](/azure/templates/microsoft.web/certificates) para a ligação SSL, e o certificado estiver armazenado num Cofre de Chaves, deve certificar-se de que a identidade do Serviço de Aplicações pode aceder ao certificado.

No Azure global, o diretor de serviços de aplicações tem a identificação de **abfa0a7c-a6b6-4736-8310-5855508787cd**. Para conceder acesso ao Key Vault para o diretor de serviço de aplicações, utilize:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

No Governo azure, o diretor de serviço seletiva tem a identificação de **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Utilize essa identificação no exemplo anterior.

No seu Cofre-Chave, selecione **Certificados** e **Gera/Import** para fazer o upload do certificado.

![Importar certificado](media/web-sites-rm-template-guidance/import-certificate.png)

No seu modelo, forneça o nome do certificado para o `keyVaultSecretName`.

Para um modelo de exemplo, consulte [a implementação de um certificado de aplicação web a partir do segredo key vault e use-o para criar a ligação SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault).

## <a name="next-steps"></a>Passos seguintes

* Para um tutorial sobre a implementação de aplicações web com um modelo, consulte [provision e implemente microserviços previsivelmente em Azure](deploy-complex-application-predictably.md).
* Para conhecer a sintaxe jSON e propriedades para tipos de recursos em modelos, consulte a referência do modelo do Gestor de [Recursos Azure](/azure/templates/).
