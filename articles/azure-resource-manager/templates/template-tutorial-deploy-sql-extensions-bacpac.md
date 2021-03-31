---
title: Importar ficheiros BACPAC SQL com modelos
description: Saiba como utilizar extensões de base de dados Azure SQL para importar ficheiros SQL BACPAC com modelos de Gestor de Recursos Azure (modelos ARM).
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 1bd9f7408baf40791c31626ea9e87a73c65b999c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97964002"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>Tutorial: Importar ficheiros BACPAC SQL com modelos ARM

Saiba como utilizar extensões de base de dados Azure SQL para importar um ficheiro [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) com modelos de Gestor de Recursos Azure (modelos ARM). Os artefactos de implantação são quaisquer ficheiros, além dos ficheiros principais do modelo, que são necessários para completar uma implementação. O ficheiro BACPAC é um artefacto.

Neste tutorial, você cria um modelo para implementar um [servidor SQL lógico](../../azure-sql/database/logical-servers.md) e uma única base de dados e importar um ficheiro BACPAC. Para obter informações sobre como implementar extensões de máquinas virtuais Azure utilizando modelos ARM, consulte [Tutorial: Implemente extensões de máquinas virtuais com modelos ARM](./template-tutorial-deploy-vm-extensions.md).

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
>
> * Prepare um ficheiro BACPAC.
> * Abra um modelo de arranque rápido.
> * Editar o modelo.
> * Implemente o modelo.
> * Verifique a implantação.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code com a extensão Ferramentas do Resource Manager. Consulte [Quickstart: Crie modelos ARM com Código de Estúdio Visual](./quickstart-create-templates-use-visual-studio-code.md).
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador do servidor. Aqui está uma amostra que pode usar para gerar uma palavra-passe:

    ```console
    openssl rand -base64 32
    ```

    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para obter mais informações, consulte [Tutorial: Integre o cofre da chave azul na implementação do modelo ARM](./template-tutorial-use-key-vault.md). Também recomendamos que atualize a palavra-passe a cada três meses.

## <a name="prepare-a-bacpac-file"></a>Preparar um ficheiro BACPAC

Um ficheiro BACPAC é partilhado no [GitHub.](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac) Para criar a sua própria base [de dados, consulte a Export uma base de dados da Base de Dados Azure SQL para um ficheiro BACPAC.](../../azure-sql/database/database-export.md) Se optar por publicar o ficheiro na sua própria localização, tem de atualizar o modelo mais tarde no tutorial.

O ficheiro BACPAC deve ser armazenado numa conta de Armazenamento Azure antes de poder ser importado utilizando um modelo ARM. O seguinte script PowerShell prepara o ficheiro BACPAC com estes passos:

* Descarregue o ficheiro BACPAC.
* Criar uma conta de Armazenamento do Azure.
* Crie um recipiente de bolhas de conta de armazenamento.
* Faça o upload do ficheiro BACPAC para o recipiente.
* Exiba a chave da conta de armazenamento e o URL blob.

1. Selecione **Experimente-o** para abrir a Azure Cloud Shell. Em seguida, cole o seguinte script PowerShell na janela da concha.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Anote a chave da conta de armazenamento e o URL de ficheiro BACPAC. Precisa destes valores quando implementar o modelo.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de início rápido

O modelo utilizado neste tutorial é armazenado no [GitHub.](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json)

1. A partir do Código do Estúdio Visual, selecione Ficheiro Aberto **de**  >  **Ficheiros**.
1. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. Selecione **Abrir** para abrir o ficheiro.

    Existem dois recursos definidos no modelo:

   * `Microsoft.Sql/servers`. Veja a [referência de modelo](/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL.servers/databases`. Veja a [referência de modelo](/azure/templates/microsoft.sql/servers/databases).

        É útil obter alguma compreensão básica do modelo antes de personalizá-lo.
1. Selecione   >  **'Guardar ficheiros' para** guardar uma cópia do ficheiro no computador local com o nome *azuredeploy.jsligado*.

## <a name="edit-the-template"></a>Editar o modelo

1. Adicione mais dois parâmetros no final da `parameters` secção para definir a chave da conta de armazenamento e o URL BACPAC.

    ```json
        "storageAccountKey": {
          "type":"string",
          "metadata":{
            "description": "Specifies the key of the storage account where the BACPAC file is stored."
          }
        },
        "bacpacUrl": {
          "type":"string",
          "metadata":{
            "description": "Specifies the URL of the BACPAC file."
          }
        }
    ```

    Adicione uma vírgula após o `adminPassword` fecho da cinta encaracolada da propriedade `}` (). Para formatar o ficheiro JSON a partir do Código do Estúdio Visual, selecione Shift+Alt+F.

    Para obter estes dois valores, consulte [Preparar um ficheiro BACPAC](#prepare-a-bacpac-file).

1. Adicione dois recursos adicionais ao modelo.

    * Para permitir a extensão da Base de Dados SQL para importar ficheiros BACPAC, é necessário permitir o tráfego a partir dos serviços Azure. Adicione a seguinte definição de regra de firewall na definição do servidor:

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2015-05-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        O modelo parece:

        ![Modelo com definição de regra de firewall](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Adicione um recurso de extensão de Base de Dados SQL à definição de base de dados com o seguinte JSON:

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        O modelo parece:

        ![Modelo com extensão de base de dados SQL](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Para compreender a definição do recurso, veja a [referência de extensão de Base de Dados SQL](/azure/templates/microsoft.sql/servers/databases/extensions). Seguem alguns elementos importantes:

        * `dependsOn`: O recurso de extensão deve ser criado após a criação da base de dados.
        * `storageKeyType`: Especificar o tipo da chave de armazenamento a utilizar. O valor pode ser `StorageAccessKey` ou `SharedAccessKey`. Use `StorageAccessKey` neste tutorial.
        * `storageKey`: Especifique a chave para a conta de armazenamento onde o ficheiro BACPAC está armazenado. Se o tipo de chave de armazenamento `SharedAccessKey` for, deve ser precedido de um "?".
        * `storageUri`: Especifique o URL do ficheiro BACPAC armazenado numa conta de armazenamento.
        * `administratorLoginPassword`: A palavra-passe do administrador do SQL. Use uma senha gerada. Veja [Pré-requisitos](#prerequisites).

O modelo completo assemelha-se a:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>Implementar o modelo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Veja a secção [Implementar o modelo](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) para obter o procedimento de implementação. Em alternativa, utilize o seguinte script de implementação do PowerShell:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

Considere usar o mesmo nome de projeto que usou quando preparou o ficheiro BACPAC para que todos os recursos sejam armazenados dentro do mesmo grupo de recursos. Desta forma, é mais fácil gerir tarefas de recursos, como limpar os recursos. Se utilizar o mesmo nome do projeto, pode remover o `New-AzResourceGroup` comando do script ou responder sim (y) ou não (n) quando lhe perguntarem se pretende atualizar o grupo de recursos existente.

Use uma senha gerada. Veja [Pré-requisitos](#prerequisites).

## <a name="verify-the-deployment"></a>Verificar a implementação

Para aceder ao servidor a partir do computador do seu cliente, tem de adicionar uma regra adicional de firewall. Para obter mais informações, consulte [Criar e gerir as regras de firewall IP](../../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules).

No portal Azure, selecione a base de dados do grupo de recursos recém-implantado. Selecione **Editor de consultas (pré-visualização)** e, em seguida, introduza as credenciais de administrador. Verá duas mesas importadas para a base de dados.

![Editor de consulta (pré-visualização)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal Azure, selecione **Grupo de Recursos** a partir do menu esquerdo.
1. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
1. Selecione o nome do grupo de recursos. Verá um total de seis recursos no grupo de recursos.
1. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou um servidor e uma base de dados e importou um ficheiro BACPAC. Para aprender a resolver problemas na implementação do modelo, consulte:

> [!div class="nextstepaction"]
> [Resolver problemas de implementações de modelos do Resource Manager](./template-tutorial-troubleshoot.md)
