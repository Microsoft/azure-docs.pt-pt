---
title: Crie a sua primeira função utilizando modelos de Gestor de Recursos Azure
description: Crie e implemente para a Azure uma função simples de servidorizada HTTP utilizando um modelo de Gestor de Recursos Azure (modelo ARM).
ms.date: 3/5/2020
ms.topic: quickstart
ms.service: azure-functions
ms.custom: subject-armqs
ms.openlocfilehash: 1e623405faa89ff41eccdaa57578bc8ac94cd78c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93422829"
---
# <a name="quickstart-create-and-deploy-azure-functions-resources-from-an-arm-template"></a>Quickstart: Criar e implementar recursos de funções Azure a partir de um modelo ARM

Neste artigo, utiliza um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma função que responda a pedidos HTTP. 

Completar este quickstart incorre num pequeno custo de alguns usd ou menos na sua conta Azure. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-function-app-create-dynamic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-account"></a>Conta do Azure 

Antes de começar, deve ter uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/)

### <a name="create-a-local-functions-project"></a>Criar um projeto de funções locais

Este artigo requer um projeto de código de funções locais para executar os recursos Azure que você cria. Se não criar um projeto para publicar, não poderá completar a secção de implantação deste artigo. 

Escolha um dos seguintes separadores, siga o link e complete a secção para criar uma aplicação de função no idioma à sua escolha:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Crie o seu projeto de funções locais no seu idioma escolhido no Código do Estúdio Visual:  

+ [C#](create-first-function-vs-code-csharp.md)
+ [Java](create-first-function-vs-code-java.md)
+ [JavaScript](create-first-function-vs-code-node.md)
+ [PowerShell](create-first-function-vs-code-powershell.md)
+ [Python](create-first-function-vs-code-python.md)
+ [TypeScript](create-first-function-vs-code-typescript.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[Crie o seu projeto de funções locais no Visual Studio](functions-create-your-first-function-visual-studio.md#create-a-function-app-project)

# <a name="command-line"></a>[Linha de comandos](#tab/command-line)

Crie o seu projeto de funções locais no seu idioma escolhido a partir da linha de comando:

+ [C#](create-first-function-cli-csharp.md)
+ [Java](create-first-function-cli-java.md)
+ [JavaScript](create-first-function-cli-node.md)
+ [PowerShell](create-first-function-cli-powershell.md)
+ [Python](create-first-function-cli-python.md)
+ [TypeScript](create-first-function-cli-typescript.md)

---

Depois de ter criado o seu projeto localmente, cria os recursos necessários para executar a sua nova função em Azure. 

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-function-app-create-dynamic/).

:::code language="json" source="~/quickstart-templates/101-function-app-create-dynamic/azuredeploy.json":::

Os seguintes quatro recursos Azure são criados por este modelo:

+ [**Microsoft.Storage/storageAstas:**](/azure/templates/microsoft.storage/storageaccounts)criar uma conta de Armazenamento Azure, que é exigida por Funções.
+ [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): crie um plano de hospedagem de consumo sem servidor para a aplicação de função.
+ [**Microsoft.Web/sites:**](/azure/templates/microsoft.web/sites)criar uma aplicação de função.
+ [**microsoft.insights/componentes**](/azure/templates/microsoft.insights/components): criar uma instância de Insights de Aplicação para monitorização.

## <a name="deploy-the-template"></a>Implementar o modelo

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli-interactive
read -p "Enter a resource group name that is used for generating resource names:" resourceGroupName &&
read -p "Enter the location (like 'eastus' or 'northeurope'):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a resource group name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (like 'eastus' or 'northeurope')"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="validate-the-deployment"></a>Validar a implementação

Em seguida, valida os recursos de hospedagem de aplicações de função que criou publicando o seu projeto ao Azure e chamando o ponto final HTTP da função.

### <a name="publish-the-function-project-to-azure"></a>Publicar o projeto de função para a Azure

Utilize os seguintes passos para publicar o seu projeto nos novos recursos da Azure:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

Na saída, copie o URL do gatilho HTTP. Utilize isto para testar a sua função em funcionamento em Azure. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. No **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Publicar**.

1. Em **Escolher um alvo de publicação,** escolha o plano de consumo de **funções Azure** com **Select existente** e selecione Criar **perfil**.

    :::image type="content" source="media/functions-create-first-function-arm/choose-publish-target-visual-studio.png" alt-text="Escolha um alvo de publicação existente":::

1. Escolha a sua **Subscrição,** expanda o grupo de recursos, selecione a sua aplicação de função e selecione **OK**.

1. Após a publicação concluída, copie o URL do **site**.

    :::image type="content" source="media/functions-create-first-function-arm/publish-summary-site-url.png" alt-text="Copiar o URL do site a partir do resumo da publicação":::

1. Apêndi-se o `/api/<FUNCTION_NAME>?name=Functions` caminho, onde `<FUNCTION_NAME>` está o nome da sua função. O URL que chama a sua função de gatilho HTTP está no seguinte formato:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=Functions`

Utilize este URL para testar a função de gatilho HTTP em funcionamento em Azure.

# <a name="command-line"></a>[Linha de comandos](#tab/command-line)

Para publicar o seu código local numa aplicação de função em Azure, utilize o `publish` comando:

```cmd
func azure functionapp publish <FUNCTION_APP_NAME>
```

Neste exemplo, `<FUNCTION_APP_NAME>` substitua-o pelo nome da sua aplicação de função. Pode ter de voltar a iniciar sinsu seu s signatário utilizando `az login` . 

Na saída, copie o URL do gatilho HTTP. Utilize isto para testar a sua função em funcionamento em Azure.

---

### <a name="invoke-the-function-on-azure"></a>Invocar a função em Azure

Cole o URL que copiou para o pedido HTTP na barra de endereços do seu navegador, certifique-se de que a `name` cadeia de consulta tal como foi `?name=Functions` anexada ao final deste URL e, em seguida, execute o pedido. 

Devia ver uma resposta como:

<pre>Hello Functions!</pre>

## <a name="clean-up-resources"></a>Limpar os recursos

Se continuar até ao próximo passo e adicionar uma bagagem de saída da fila Azure Storage, mantenha todos os seus recursos no lugar, pois irá basear-se no que já fez.

Caso contrário, utilize o seguinte comando para eliminar o grupo de recursos e todos os seus recursos contidos para evitar custos adicionais.

```azurecli
az group delete --name <RESOURCE_GROUP_NAME>
```

`<RESOURCE_GROUP_NAME>`Substitua-o pelo nome do seu grupo de recursos.

## <a name="next-steps"></a>Passos seguintes

Agora que publicou a sua primeira função, saiba mais adicionando uma ligação de saída à sua função.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento Azure](functions-add-output-binding-storage-queue-vs-code.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento Azure](functions-add-output-binding-storage-queue-vs.md)

# <a name="command-line"></a>[Linha de comandos](#tab/command-line)

> [!div class="nextstepaction"]
> [Ligue-se a uma fila de armazenamento Azure](functions-add-output-binding-storage-queue-cli.md)

---
