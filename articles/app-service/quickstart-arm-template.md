---
title: Crie uma aplicação de Serviço de Aplicações usando um modelo de Gestor de Recursos Azure
description: Crie a sua primeira aplicação para o Azure App Service em segundos usando um Modelo de Gestor de Recursos Azure, que é uma das muitas formas de implementar no Serviço de Aplicações.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 05/25/2020
ms.custom: subject-armqs
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: e577616e0976ca050a55c8524e68129545ed1912
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89653598"
---
# <a name="create-app-service-app-using-an-azure-resource-manager-template"></a>Crie uma aplicação de Serviço de Aplicações usando um modelo de Gestor de Recursos Azure

Começa com o [Azure App Service](overview.md) implementando uma aplicação na nuvem utilizando um modelo de Gestor de Recursos Azure e [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) em Cloud Shell. Como utiliza um nível de Serviço de Aplicações gratuito, não tem custos para completar este arranque rápido.

 [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-azure-app-service-app"></a>Criar uma aplicação Azure App Service

### <a name="review-the-template"></a>Rever o modelo

::: zone pivot="platform-windows"
O modelo utilizado neste início rápido pertence aos [modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/). Implementa um plano de Serviço de Aplicações e uma aplicação de Serviço de Aplicações no Windows. É compatível com aplicações .NET Core, .NET Framework, PHP, Node.js e Static HTML. Para Java, consulte [a aplicação Create Java.](app-service-web-get-started-java.md) 

[!code-json[<Azure Resource Manager template App Service Windows app>](~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json)]

Dois recursos Azure são definidos no modelo:

* [**Microsoft.Web/serverfarms:**](/azure/templates/microsoft.web/serverfarms)criar um plano de Serviço de Aplicações.
* [**Microsoft.Web/sites:**](/azure/templates/microsoft.web/sites)criar uma aplicação de Serviço de Aplicações.

Este modelo contém vários parâmetros que são predefinidos para a sua conveniência. Consulte a tabela abaixo para ver as predefinições dos parâmetros e as suas descrições:

| Parâmetros | Tipo    | Valor predefinido                | Descrição |
|------------|---------|------------------------------|-------------|
| webAppName | string  | **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)**"webApp-" | Nome da aplicação |
| localização   | string  | ["[grupo de recursos().localização]"](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup) | Região de aplicações |
| sku        | string  | "F1"                         | Tamanho da instância (F1 = Nível Livre) |
| language   | string  | ".net"                       | Pilha de linguagem de programação (.net, php, nó, html) |
| OláWorld | boolean | Falso                        | True = Implementar a aplicação "Hello World" |
| repoUrl    | string  | " "                          | Repo Git externo (opcional) |
::: zone-end
::: zone pivot="platform-linux"
O modelo utilizado neste início rápido pertence aos [modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/). Implementa um plano de Serviço de Aplicações e uma aplicação de Serviço de Aplicações no Linux. É compatível com todas as linguagens de programação suportadas no Serviço de Aplicações.

[!code-json[<Azure Resource Manager template App Service Linux app>](~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json)]

Dois recursos Azure são definidos no modelo:

* [**Microsoft.Web/serverfarms:**](/azure/templates/microsoft.web/serverfarms)criar um plano de Serviço de Aplicações.
* [**Microsoft.Web/sites:**](/azure/templates/microsoft.web/sites)criar uma aplicação de Serviço de Aplicações.

Este modelo contém vários parâmetros que são predefinidos para a sua conveniência. Consulte a tabela abaixo para ver as predefinições dos parâmetros e as suas descrições:

| Parâmetros | Tipo    | Valor predefinido                | Descrição |
|------------|---------|------------------------------|-------------|
| webAppName | string  | **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)**"webApp-" | Nome da aplicação |
| localização   | string  | ["[grupo de recursos().localização]"](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup) | Região de aplicações |
| sku        | string  | "F1"                         | Tamanho da instância (F1 = Nível Livre) |
| linuxFxVersion   | string  | "DOTNETCORE&#124;3.0        | "Pilha de linguagem de programação &#124; versão" |
| repoUrl    | string  | " "                          | Repo Git externo (opcional) |

---
::: zone-end


### <a name="deploy-the-template"></a>Implementar o modelo

O Azure CLI é usado aqui para implementar o modelo. Também pode utilizar o portal Azure, Azure PowerShell e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-powershell.md). 

O código a seguir cria um grupo de recursos, um plano de Serviço de Aplicações e uma aplicação web. Um grupo de recursos predefinido, plano de Serviço de Aplicações e localização foram definidos para si. Substitua `<app-name>` por um nome de aplicação globalmente único (caracteres válidos são , e `a-z` `0-9` `-` ).

::: zone pivot="platform-windows"
Execute o código abaixo para implementar uma aplicação de quadro .NET no Windows.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" sample="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
::: zone-end
::: zone pivot="platform-linux"
Run the code below to create a Python app on Linux. 

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```

Para implementar uma pilha de idiomas diferente, atualize `linuxFxVersion` com valores apropriados. As amostras são mostradas abaixo. Para mostrar as versões atuais, executar o seguinte comando na Cloud Shell: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Linguagem    | Exemplo                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.js** | linuxFxVersion="NODE&#124;10,15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1,8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---
::: zone-end

> [!NOTE]
> Pode encontrar [mais amostras do modelo do Azure App Service aqui.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites)


## <a name="validate-the-deployment"></a>Validar a implementação

Procure `http://<app_name>.azurewebsites.net/` e verifique se foi criado.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, [elimine o grupo de recursos](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Implementar a partir do Git local](deploy-local-git.md)

> [!div class="nextstepaction"]
> [ASP.NET Core com a Base de Dados SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Python com Postgres](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [PHP com MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Ligue à base de dados Azure SQL com a Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Mapear domínio personalizado](app-service-web-tutorial-custom-domain.md)