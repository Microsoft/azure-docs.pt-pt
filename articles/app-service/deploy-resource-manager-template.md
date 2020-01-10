---
title: Implantar aplicativos com modelos
description: Encontre orientações sobre como criar modelos de Azure Resource Manager para provisionar e implantar aplicativos do serviço de aplicativo.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: e9647c1833416b9b225be988acaffb4022f655c1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422099"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Orientação sobre a implantação de aplicativos Web usando modelos de Azure Resource Manager

Este artigo fornece recomendações para a criação de modelos de Azure Resource Manager para implantar soluções de serviço Azure App. Essas recomendações podem ajudá-lo a evitar problemas comuns.

## <a name="define-dependencies"></a>Definir dependências

Definir dependências para aplicativos Web requer uma compreensão de como os recursos em um aplicativo Web interagem. Se você especificar dependências em uma ordem incorreta, poderá causar erros de implantação ou criar uma condição de corrida que interrompe a implantação.

> [!WARNING]
> Se você incluir uma extensão de site do MSDeploy em seu modelo, deverá definir qualquer recurso de configuração como dependente do recurso do MSDeploy. Alterações de configuração fazem com que o site seja reiniciado de forma assíncrona. Ao tornar os recursos de configuração dependentes do MSDeploy, você garante que o MSDeploy seja concluído antes da reinicialização do site. Sem essas dependências, o site pode ser reiniciado durante o processo de implantação do MSDeploy. Para obter um modelo de exemplo, consulte [modelo do WordPress com dependência de implantação da Web](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

A imagem a seguir mostra a ordem de dependência para vários recursos do serviço de aplicativo:

![Dependências do aplicativo Web](media/web-sites-rm-template-guidance/web-dependencies.png)

Você implanta recursos na seguinte ordem:

**Camada 1**
* Plano do serviço de aplicativo.
* Quaisquer outros recursos relacionados, como bancos de dados ou contas de armazenamento.

**Camada 2**
* Aplicativo Web-depende do plano do serviço de aplicativo.
* Aplicativo Azure instância do insights que tem como destino o farm de servidores – depende do plano do serviço de aplicativo.

**Camada 3**
* Controle do código-fonte – depende do aplicativo Web.
* Extensão de site do MSDeploy – depende do aplicativo Web.
* Aplicativo Azure instância do insights que se destina ao aplicativo Web – depende do aplicativo Web.

**Camada 4**
* Certificado do serviço de aplicativo – depende do controle do código-fonte ou do MSDeploy se algum deles estiver presente. Caso contrário, depende do aplicativo Web.
* Definições de configuração (cadeias de conexão, valores de Web. config, configurações de aplicativo) – depende do controle do código-fonte ou do MSDeploy se houver uma. Caso contrário, depende do aplicativo Web.

**Camada 5**
* Associações de nome de host – depende do certificado, se presente. Caso contrário, depende de um recurso de nível superior.
* Extensões de site – depende das definições de configuração, se presentes. Caso contrário, depende de um recurso de nível superior.

Normalmente, sua solução inclui apenas alguns desses recursos e camadas. Para camadas ausentes, mapeie os recursos menores para a próxima camada mais alta.

O exemplo a seguir mostra parte de um modelo. O valor da configuração da cadeia de conexão depende da extensão do MSDeploy. A extensão MSDeploy depende do aplicativo Web e do banco de dados. 

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

Para um exemplo pronto para execução que usa o código acima, consulte [modelo: compilar um aplicativo Web Umbraco simples](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>Encontrar informações sobre erros de MSDeploy

Se o modelo do Resource Manager usar o MSDeploy, as mensagens de erro de implantação poderão ser difíceis de entender. Para obter mais informações após uma implantação com falha, tente as seguintes etapas:

1. Vá para o console do [kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console)do site.
2. Navegue até a pasta em D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Procure os arquivos arquivos appmanagerstatus. xml e appManagerLog. xml. O primeiro arquivo registra o status. O segundo arquivo registra informações sobre o erro. Se o erro não estiver claro para você, você poderá incluí-lo quando estiver solicitando ajuda no fórum.

## <a name="choose-a-unique-web-app-name"></a>Escolha um nome de aplicativo Web exclusivo

O nome do seu aplicativo Web deve ser globalmente exclusivo. Você pode usar uma Convenção de nomenclatura que provavelmente seja exclusiva ou pode usar a [função uniquestring](../azure-resource-manager/templates/template-functions-string.md#uniquestring) para auxiliar na geração de um nome exclusivo.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Implantar o certificado do aplicativo Web do Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se o modelo incluir um recurso [Microsoft. Web/Certificates](/azure/templates/microsoft.web/certificates) para associação SSL e o certificado for armazenado em um Key Vault, você deverá verificar se a identidade do serviço de aplicativo pode acessar o certificado.

No Azure global, a entidade de serviço do serviço de aplicativo tem a ID de **abfa0a7c-a6b6-4736-8310-5855508787cd**. Para conceder acesso a Key Vault para a entidade de serviço do serviço de aplicativo, use:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

No Azure governamental, a entidade de serviço do serviço de aplicativo tem a ID de **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Use essa ID no exemplo anterior.

No Key Vault, selecione **certificados** e **gerar/importar** para carregar o certificado.

![Importar certificado](media/web-sites-rm-template-guidance/import-certificate.png)

Em seu modelo, forneça o nome do certificado para o `keyVaultSecretName`.

Para obter um modelo de exemplo, consulte [implantar um certificado de aplicativo Web de Key Vault segredo e usá-lo para criar a associação SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault).

## <a name="next-steps"></a>Passos seguintes

* Para obter um tutorial sobre como implantar aplicativos Web com um modelo, consulte [provisionar e implantar microservices previsíveis no Azure](deploy-complex-application-predictably.md).
* Para saber mais sobre a sintaxe JSON e as propriedades para tipos de recursos em modelos, consulte [Azure Resource Manager referência de modelo](/azure/templates/).
