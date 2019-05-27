---
title: Documentação de orientação sobre a implementação de aplicações com modelos - serviço de aplicações do Azure | Documentos da Microsoft
description: Recomendações para a criação de modelos Azure Resource Manager para implementar aplicações web.
services: app-service
documentationcenter: app-service
author: tfitzmac
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: d8fa8b216ca6044adefe1398b58f5d14630540e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66137152"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Documentação de orientação sobre a implementação de aplicações web utilizando modelos Azure Resource Manager

Este artigo fornece recomendações para a criação de modelos Azure Resource Manager para implementar soluções de serviço de aplicações do Azure. Estas recomendações podem ajudar a evitar problemas comuns.

## <a name="define-dependencies"></a>Definir as dependências

Definição de dependências para aplicações web requer uma compreensão de como os recursos dentro de uma aplicação web interagem. Se especificar dependências numa ordem incorreta, poderá causar erros de implementação ou criar uma condição de corrida que paralisa a implementação.

> [!WARNING]
> Se incluir uma extensão de site de MSDeploy no seu modelo, tem de definir quaisquer recursos de configuração como dependente do recurso de MSDeploy. Alterações de configuração com que o site de forma assíncrona a reiniciar. Ao tornar os recursos de configuração depende de MSDeploy, garante que MSDeploy é concluída antes de reinicia o site. Sem essas dependências, o site poderá reiniciar durante o processo de implementação de MSDeploy. Para um modelo de exemplo, consulte [modelo de WordPress com a dependência de implementar Web](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

A imagem seguinte mostra a ordem da dependência para vários recursos de serviço de aplicações:

![Dependências de aplicações Web](media/web-sites-rm-template-guidance/web-dependencies.png)

Implementar recursos na seguinte ordem:

**Camada 1**
* Plano do serviço de aplicações.
* Quaisquer outros recursos relacionados, como bases de dados ou contas de armazenamento.

**Camada 2**
* Web app – depende o plano do serviço de aplicações.
* A instância do Azure Application Insights que tenha como destino o farm de servidores – depende o plano do serviço de aplicações.

**Camada 3**
* Origem de controle – depende a aplicação web.
* Extensão de site de MSDeploy, depende da aplicação web.
* Instância do Application Insights que tenha como destino o farm de servidores – depende a aplicação web.

**Camada 4**
* Certificado de serviço de aplicações – depende do controlo de origem ou MSDeploy se qualquer um dos está presente. Caso contrário, ele depende a aplicação web.
* Definições de configuração (cadeias de ligação, os valores de Web. config, as definições da aplicação) – depende de controlo de origem ou MSDeploy se qualquer um estiver presente. Caso contrário, ele depende a aplicação web.

**Camada 5**
* Enlaces de nome - do anfitrião depende do certificado se estiver presente. Caso contrário, depende de um recurso de nível superior.
* Extensões – de sites depende das definições de configuração, se estiver presente. Caso contrário, depende de um recurso de nível superior.

Normalmente, a solução inclui apenas alguns desses recursos e camadas. Para os escalões em falta, mapear recursos inferior ao escalão mais elevado de avançar.

O exemplo seguinte mostra a parte de um modelo. O valor da configuração da cadeia de ligação depende da extensão de MSDeploy. A extensão de MSDeploy depende a aplicação web e a base de dados. 

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

Para obter um exemplo de ready-to-run que usa o código acima, veja [modelo: Criar uma simples aplicação de Web do Umbraco](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>Encontre informações sobre erros de MSDeploy

Se o modelo do Resource Manager utiliza MSDeploy, as mensagens de erro de implementação podem ser difícil de entender. Para obter mais informações após uma falha na implementação, experimente os seguintes passos:

1. Vá para o site [consola Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console).
2. Navegue até à pasta na D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Procure os ficheiros appManagerStatus.xml e appManagerLog.xml. O primeiro arquivo regista o estado. O segundo arquivo regista informações sobre o erro. Se o erro não está claro para, pode incluí-lo quando está pedindo para obter ajuda no Fórum.

## <a name="choose-a-unique-web-app-name"></a>Escolha um nome de aplicação web exclusivo

O nome da sua aplicação web tem de ser globalmente exclusivo. Pode usar uma convenção de nomenclatura que é provável que seja exclusivo, ou pode utilizar o [uniqueString função](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) para ajudar a gerar um nome exclusivo.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Implementar o certificado de aplicação web do Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se o seu modelo inclui uma [Microsoft.Web/certificates](/azure/templates/microsoft.web/certificates) recurso para o enlace SSL e o certificado é armazenado no Cofre de chaves, deve certificar-se de que a identidade de serviço de aplicações pode aceder ao certificado.

No global Azure, o principal de serviço do serviço de aplicações tem o ID de **abfa0a7c-a6b6-4736-8310-5855508787cd**. Para conceder acesso ao Key Vault para o principal de serviço de serviço de aplicações, utilize:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

No Azure Government, o principal de serviço do serviço de aplicações tem o ID de **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Use essa identificação no exemplo anterior.

No seu Cofre de chaves, selecione **certificados** e **gerar/importar** para carregar o certificado.

![Importar certificado](media/web-sites-rm-template-guidance/import-certificate.png)

No seu modelo, forneça o nome do certificado para o `keyVaultSecretName`.

Para um modelo de exemplo, consulte [implementar um certificado de aplicação Web do segredo do Key Vault e utilizá-lo para criar o enlace SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault).

## <a name="next-steps"></a>Passos Seguintes

* Para obter um tutorial sobre como implementar aplicações web com um modelo, consulte [aprovisionar e implementar microsserviços de forma previsível no Azure](deploy-complex-application-predictably.md).
* Para saber mais sobre a sintaxe JSON e propriedades para tipos de recursos em modelos, veja [referência de modelo do Azure Resource Manager](/azure/templates/).
