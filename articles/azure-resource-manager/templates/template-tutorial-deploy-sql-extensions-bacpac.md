---
title: Importar arquivos BACPAC do SQL com modelos
description: Saiba como usar as extensões de banco de dados SQL do Azure para importar arquivos BACPAC do SQL com modelos de Azure Resource Manager.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 500e07296040305f1e469fde78988f2551440e58
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472626"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Tutorial: Importar ficheiros SQL BACPAC com modelos do Azure Resource Manager

Saiba como usar as extensões de banco de dados SQL do Azure para importar um arquivo BACPAC com modelos de Azure Resource Manager. Os artefatos de implantação são todos os arquivos, além dos arquivos de modelo principais, necessários para concluir uma implantação. Ficheiro BACPAC é um artefato. 

Neste tutorial, você cria um modelo para implantar um SQL Server do Azure e um banco de dados SQL e importar um arquivo BACPAC. Para obter informações sobre como implantar extensões de máquina virtual do Azure usando modelos de Azure Resource Manager, consulte [tutorial: implantar extensões de máquina virtual com modelos de Azure Resource Manager](./template-tutorial-deploy-vm-extensions.md).

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Prepare um arquivo BACPAC.
> * Abra um modelo de início rápido.
> * Edite o modelo.
> * Implante o modelo.
> * Verificar a implementação.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code com a extensão de ferramentas do Resource Manager. Consulte [usar Visual Studio Code para criar modelos de Azure Resource Manager](./use-vs-code-to-create-template.md).
* Para aumentar a segurança, use uma senha gerada para a conta de administrador de SQL Server do Azure. Aqui está um exemplo que você pode usar para gerar uma senha:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para obter mais informações, veja [Tutorial: Integrar o Azure Key Vault na implementação de modelos do Resource Manager](./template-tutorial-use-key-vault.md). Também recomendamos que atualize a palavra-passe a cada três meses.

## <a name="prepare-a-bacpac-file"></a>Preparar um ficheiro BACPAC

Um arquivo BACPAC é compartilhado no [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Para criar o seu próprio, veja [Exportar uma base de dados SQL do Azure para um ficheiro BACPAC](../../sql-database/sql-database-export.md). Se optar por publicar o ficheiro na sua própria localização, tem de atualizar o modelo mais tarde no tutorial.

O arquivo BACPAC deve ser armazenado em uma conta de armazenamento do Azure para que possa ser importado usando um modelo do Resource Manager. O script do PowerShell a seguir prepara o arquivo BACPAC com estas etapas:

* Transfira o ficheiro BACPAC.
* Criar uma conta de Armazenamento do Azure.
* Crie um contêiner de BLOB da conta de armazenamento.
* Carregue o ficheiro BACPAC para o contentor.
* Exiba a chave da conta de armazenamento e a URL do blob.

1. Selecione **Experimente** para abrir o Cloud Shell. Em seguida, Cole o seguinte script do PowerShell na janela do Shell.

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

1. Anote a chave da conta de armazenamento e a URL do arquivo BACPAC. Você precisará desses valores ao implantar o modelo.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de início rápido

O modelo usado neste tutorial é armazenado no [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. No Visual Studio Code, selecione **Ficheiro** > **Abrir Ficheiro**.
1. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. Selecione **Abrir** para abrir o ficheiro.

    Há dois recursos definidos no modelo:

   * `Microsoft.Sql/servers`. Veja a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL.servers/databases`. Veja a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

        É útil obter alguma compreensão básica do modelo antes de personalizá-lo.
1. Selecione **Ficheiro** > **Guardar Como** para guardar uma cópia do ficheiro no computador local, com o nome *azuredeploy.json*.

## <a name="edit-the-template"></a>Editar o modelo

1. Adicione mais dois parâmetros ao final da seção de **parâmetros** para definir a chave da conta de armazenamento e a URL do BACPAC.

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

    Adicione uma vírgula após **adminPassword**. Para formatar o arquivo JSON de Visual Studio Code, selecione Shift + Alt + F.

    Para obter esses dois valores, consulte [preparar um arquivo BACPAC](#prepare-a-bacpac-file).

1. Adicione dois recursos adicionais ao modelo.

    * Para permitir que a extensão do banco de dados SQL importe arquivos BACPAC, você precisa permitir o tráfego dos serviços do Azure. Adicione a seguinte definição de regra de firewall na definição do SQL Server:

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

        O modelo é semelhante a:

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

        O modelo é semelhante a:

        ![Modelo com extensão do banco de dados SQL](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Para compreender a definição do recurso, veja a [referência de extensão de Base de Dados SQL](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Seguem alguns elementos importantes:

        * **dependsOn**: O recurso de extensão tem de ser criado após a criação da base de dados SQL.
        * **storageKeyType**: especifique o tipo da chave de armazenamento a ser usada. O valor pode ser `StorageAccessKey` ou `SharedAccessKey`. Use `StorageAccessKey` neste tutorial.
        * **storageKey**: Especifique a chave para a conta de armazenamento onde o arquivo BACPAC está armazenado. Se o tipo de chave de armazenamento for `SharedAccessKey`, ele deverá ser precedido por um "?".
        * **storageUri**: ESPECIFIQUE a URL do arquivo BACPAC armazenado em uma conta de armazenamento.
        * **administratorLoginPassword**: A palavra-passe do administrador do SQL. Utilize uma palavra-passe gerada. Veja [Pré-requisitos](#prerequisites).

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

Considere usar o mesmo nome de projeto que você usou ao preparar o arquivo BACPAC para que todos os recursos sejam armazenados no mesmo grupo de recursos. Dessa forma, é mais fácil gerenciar tarefas de recursos, como limpar os recursos. Se você usar o mesmo nome de projeto, poderá remover o comando `New-AzResourceGroup` do script ou responder sim (y) ou não (n) quando for perguntado se deseja atualizar o grupo de recursos existente.

Utilize uma palavra-passe gerada. Veja [Pré-requisitos](#prerequisites).

## <a name="verify-the-deployment"></a>Verificar a implementação

Para acessar o SQL Server do computador cliente, você precisa adicionar uma regra de firewall adicional. Para obter mais informações, consulte [criar e gerenciar regras de firewall de IP](../../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules).

No portal do Azure, selecione o banco de dados SQL do grupo de recursos implantado recentemente. Selecione **Editor de consultas (pré-visualização)** e, em seguida, introduza as credenciais de administrador. Você verá duas tabelas importadas para o banco de dados.

![Editor de consultas (pré-visualização)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **grupo de recursos** no menu à esquerda.
1. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
1. Selecione o nome do grupo de recursos. Você verá um total de seis recursos no grupo de recursos.
1. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você implantou um SQL Server e um banco de dados SQL e importou um arquivo BACPAC. O arquivo BACPAC é armazenado em uma conta de armazenamento do Azure. Qualquer pessoa com a URL pode acessar o arquivo. Para saber como proteger o arquivo BACPAC (artefato), consulte:

> [!div class="nextstepaction"]
> [Proteger os artefatos](./template-tutorial-secure-artifacts.md)
