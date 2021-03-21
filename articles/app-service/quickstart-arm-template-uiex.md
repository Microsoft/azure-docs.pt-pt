---
title: Crie uma aplicação de Serviço de Aplicações usando um modelo de Gestor de Recursos Azure
description: Crie a sua primeira aplicação para o Azure App Service em segundos usando um modelo de Gestor de Recursos Azure (modelo ARM), que é uma das muitas formas de implantar no Serviço de Aplicações.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7437a5208f94b435576b8a38dc65a6e798303a72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179090"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>Quickstart: Criar app de Serviço de Aplicações usando um modelo ARM

Começar com [o Azure App Service](overview.md) implementando uma aplicação para a nuvem usando um <abbr title="Um ficheiro JSON que define declarativamente um ou mais recursos e dependências da Azure entre os recursos implantados. O modelo pode ser utilizado para implementar os recursos de forma consistente e repetida.">Modelo ARM</abbr> e [Azure CLI](/cli/azure/get-started-with-azure-cli) em Cloud Shell. Como utiliza um nível de Serviço de Aplicações gratuito, não tem custos para completar este arranque rápido. <abbr title="Em sintaxe declarativa o programador descreve a implementação pretendida sem escrever a sequência de comandos de programação para criar a implementação.">O modelo utiliza sintaxe declarativa.</abbr>

 Se o seu ambiente satisfaça os pré-requisitos e estiver familiarizado com a utilização de [modelos ARM,](../azure-resource-manager/templates/overview.md)selecione o botão **Implementar para Azul.** O modelo será aberto no portal do Azure.

::: zone pivot="platform-windows"
[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-windows%2Fazuredeploy.json)
::: zone-end

::: zone pivot="platform-linux"
[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-linux%2Fazuredeploy.json)
::: zone-end

<hr/>

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<hr/>

## <a name="2-review-the-template"></a>2. Rever o modelo

::: zone pivot="platform-windows"
O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-app-service-docs-windows). Implementa um plano de Serviço de Aplicações e uma aplicação de Serviço de Aplicações no Windows.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json":::

<details>
<summary>Que recursos e parâmetros são definidos no modelo?</summary>

Dois recursos Azure são definidos no modelo:

* [**Microsoft.Web/serverfarms:**](/azure/templates/microsoft.web/serverfarms)criar um plano de Serviço de Aplicações.
* [**Microsoft.Web/sites:**](/azure/templates/microsoft.web/sites)criar uma aplicação de Serviço de Aplicações.

Os seguintes detalhes da tabela predefinições parâmetros e suas descrições:

| Parâmetros | Tipo    | Valor predefinido                | Description |
|------------|---------|------------------------------|-------------|
| webAppName | string  | **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)**"webApp-" | Nome da aplicação |
| localização   | string  | ["[grupo de recursos().localização]"](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) | Região de aplicações |
| sku        | string  | "F1"                         | Tamanho da instância (F1 = Nível Livre) |
| language   | string  | ".net"                       | Pilha de linguagem de programação (.net, php, nó, html) |
| OláWorld | boolean | Falso                        | True = Implementar a aplicação "Hello World" |
| repoUrl    | string  | " "                          | Repo Git externo (opcional) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-app-service-docs-linux). Implementa um plano de Serviço de Aplicações e uma aplicação de Serviço de Aplicações no Windows.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json":::

Este modelo inclui recursos Estaure e parâmetros definidos para sua conveniência.

<details>
<summary>Que recursos e parâmetros são definidos no modelo?</summary>

Dois recursos Azure são definidos no modelo:

* [**Microsoft.Web/serverfarms:**](/azure/templates/microsoft.web/serverfarms)criar um plano de Serviço de Aplicações.
* [**Microsoft.Web/sites:**](/azure/templates/microsoft.web/sites)criar uma aplicação de Serviço de Aplicações.

Os seguintes detalhes da tabela predefinições parâmetros e suas descrições:

| Parâmetros | Tipo    | Valor predefinido                | Description |
|------------|---------|------------------------------|-------------|
| webAppName | string  | **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)**"webApp-" | Nome da aplicação |
| localização   | string  | ["[grupo de recursos().localização]"](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) | Região de aplicações |
| sku        | string  | "F1"                         | Tamanho da instância (F1 = Nível Livre) |
| linuxFxVersion   | string  | "DOTNETCORE&#124;3.0        | "Pilha de linguagem de programação &#124; versão" |
| repoUrl    | string  | " "                          | Repo Git externo (opcional) |

---

</details>

::: zone-end

<hr/>

## <a name="3-deploy-the-template"></a>3. Implementar o modelo

::: zone pivot="platform-windows"
Execute o código abaixo para implementar uma aplicação de quadro .NET no Windows utilizando o Azure CLI. 

Substituir <abbr title="Os caracteres de caracteres válidos são `a-z` `0-9` , e `-` .">`<app-name>`</abbr> com um nome de aplicativo globalmente único. Para aprender outro <abbr title="Também pode utilizar o portal Azure, Azure PowerShell e REST API.">métodos de implantação</abbr>, consulte [os modelos de implantação](../azure-resource-manager/templates/deploy-powershell.md). Pode encontrar [mais amostras do modelo do Azure App Service aqui.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites)

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
```
::: zone-end
::: zone pivot="platform-linux"
Execute o código abaixo para criar uma aplicação Python no Linux. 

Substituir <abbr title="Os caracteres de caracteres válidos são `a-z` `0-9` , e `-` .">`<app-name>`</abbr> com um nome de aplicativo globalmente único.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```
::: zone-end

<details>
<summary>O que está o código a fazer?</summary>
<p>Os comandos fazem as seguintes ações:</p>
<ul>
<li>Criar um padrão <abbr title="Um recipiente lógico para recursos Azure relacionados que você pode gerir como uma unidade.">grupo de recursos</abbr>.</li>
<li>Criar um padrão <abbr title="O plano que especifica a localização, tamanho e funcionalidades da fazenda de servidores web que hospeda a sua aplicação.">Plano do Serviço de Aplicações</abbr>.</li>
<li><a href="/cli/azure/webapp#az-webapp-create">Criar uma <abbr title="A representação da sua aplicação web, que contém o seu código de aplicação, nomes de anfitriões DNS, certificados e recursos relacionados. "> App Service</abbr></a> com o nome especificado.</li>
</ul>
</details>

::: zone pivot="platform-windows"
<details>
<summary>Como posso implantar uma pilha de linguagem diferente?</summary>
Para implementar uma pilha de linguagem diferente, atualização <abbr title="Este modelo é compatível com aplicações .NET Core, .NET Framework, PHP, Node.js e Static HTML. "> parâmetro da linguagem</abbr> com valores apropriados. Para Java, consulte <a href="/azure/app-service/quickstart-java-uiex">a aplicação Create Java.</a>

| Parâmetros | Tipo    | Valor predefinido                | Description |
|------------|---------|------------------------------|-------------|
| language   | string  | ".net"                       | Pilha de linguagem de programação (.net, php, nó, html) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
<details>
<summary>Como posso implantar uma pilha de linguagem diferente?</summary>
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

</details>
::: zone-end

<hr/>

## <a name="4-validate-the-deployment"></a>4. Validar a implantação

Procure `http://<app_name>.azurewebsites.net/` e verifique se foi criado.

<hr/>

## <a name="5-clean-up-resources"></a>5. Limpar recursos

Quando já não for necessário, [elimine o grupo de recursos](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

<hr/>

## <a name="next-steps"></a>Passos seguintes

- [Implementar a partir do Git local](deploy-local-git.md)
- [ASP.NET Core com a Base de Dados SQL](tutorial-dotnetcore-sqldb-app.md)
- [Python com Postgres](tutorial-python-postgresql-app.md)
- [PHP com MySQL](tutorial-php-mysql-app.md)
- [Ligue à base de dados Azure SQL com a Java](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)
- [Mapear domínio personalizado](app-service-web-tutorial-custom-domain-uiex.md)

