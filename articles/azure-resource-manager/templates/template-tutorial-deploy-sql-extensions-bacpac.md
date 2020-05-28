---
title: Importar ficheiros SQL BACPAC com modelos
description: Saiba como utilizar extensões de base de dados Azure SQL para importar ficheiros SQL BACPAC com modelos de Gestor de Recursos Azure.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 69e2b25a16a984445a32f884fab5caec6651df32
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84018400"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>Tutorial: Importar ficheiros BACPAC Com modelos ARM

Saiba como utilizar extensões de base de dados Azure SQL para importar um ficheiro BACPAC com modelos de Gestor de Recursos Azure (ARM). Os artefactos de implantação são quaisquer ficheiros, além dos ficheiros principais do modelo, que são necessários para completar uma implementação. O ficheiro BACPAC é um artefacto.

Neste tutorial, cria-se um modelo para implantar um [servidor SQL lógico](../../azure-sql/database/logical-servers.md) e uma única base de dados e importar um ficheiro BACPAC. Para obter informações sobre como implementar extensões de máquinas virtuais Azure utilizando modelos ARM, consulte [Tutorial: Implemente extensões de máquinas virtuais com modelos ARM](./template-tutorial-deploy-vm-extensions.md).

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
>
> * Prepare um ficheiro BACPAC.
> * Abra um modelo de arranque rápido.
> * Editar o modelo.
> * Desdobrar o modelo.
> * Verifique a colocação.

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code com a extensão Ferramentas do Resource Manager. Consulte [o Use Visual Studio Code para criar modelos ARM](./use-vs-code-to-create-template.md).
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador do servidor. Aqui está uma amostra que pode usar para gerar uma senha:

    ```console
    openssl rand -base64 32
    ```

    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para mais informações, consulte [Tutorial: Integre o Cofre chave Azure na implementação](./template-tutorial-use-key-vault.md)do modelo ARM . Também recomendamos que atualize a palavra-passe a cada três meses.

## <a name="prepare-a-bacpac-file"></a>Preparar um ficheiro BACPAC

Um ficheiro BACPAC é partilhado no [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Para criar o seu próprio, veja [Exportar uma base de dados SQL do Azure para um ficheiro BACPAC](../../azure-sql/database/database-export.md). Se optar por publicar o ficheiro na sua própria localização, tem de atualizar o modelo mais tarde no tutorial.

O ficheiro BACPAC deve ser armazenado numa conta de Armazenamento Azure antes de poder ser importado utilizando um modelo ARM. O seguinte script PowerShell prepara o ficheiro BACPAC com estes passos:

* Descarregue o ficheiro BACPAC.
* Criar uma conta de Armazenamento do Azure.
* Crie um recipiente de depósito.
* Faça o upload do ficheiro BACPAC para o recipiente.
* Mostre a chave da conta de armazenamento e o URL blob.

1. Selecione **Tente** abrir a casca da nuvem. Em seguida, colar o seguinte script PowerShell na janela da concha.

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

1. Anote a chave da conta de armazenamento e o URL do ficheiro BACPAC. Precisa destes valores quando implementa o modelo.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de início rápido

O modelo utilizado neste tutorial é armazenado no [GitHub.](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json)

1. A partir do **File**Código do Estúdio Visual, selecione  >  **File Open File**.
1. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. Selecione **Abrir** para abrir o ficheiro.

    Existem dois recursos definidos no modelo:

   * `Microsoft.Sql/servers`. Veja a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL.servers/databases`. Veja a [referência de modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

        É útil obter alguma compreensão básica do modelo antes de personalizá-lo.
1. Selecione **Guardar**  >  **Ficheiros Para** guardar uma cópia do ficheiro para o computador local com o nome *azuredeploy.json*.

## <a name="edit-the-template"></a>Editar o modelo

1. Adicione mais dois parâmetros na extremidade da secção de **parâmetros** para definir a chave da conta de armazenamento e o URL BACPAC.

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

    Adicione uma vírem depois de **administradorPassword**. Para formatar o ficheiro JSON do Visual Studio Code, selecione Shift+Alt+F.

    Para obter estes dois valores, consulte [Prepare um ficheiro BACPAC](#prepare-a-bacpac-file).

1. Adicione dois recursos adicionais ao modelo.

    * Para permitir que a extensão da Base de Dados SQL importe ficheiros BACPAC, tem de permitir o tráfego dos serviços Azure. Adicione a seguinte definição de regra de firewall sob a definição do servidor:

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

        Para compreender a definição do recurso, veja a [referência de extensão de Base de Dados SQL](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). Seguem alguns elementos importantes:

        * **dependsOn**: O recurso de extensão tem de ser criado após a criação da base de dados SQL.
        * **armazenamentoChaveType**: Especifique o tipo da chave de armazenamento a utilizar. O valor pode ser `StorageAccessKey` ou `SharedAccessKey`. Use `StorageAccessKey` neste tutorial.
        * **armaçõesChave:** Especifique a chave para a conta de armazenamento onde o ficheiro BACPAC está armazenado. Se o tipo de chave de armazenamento `SharedAccessKey` for, deve ser precedido com um "?".
        * **armazenamentoUri**: Especifique o URL do ficheiro BACPAC armazenado numa conta de armazenamento.
        * **administratorLoginPassword**: A palavra-passe do administrador do SQL. Use uma senha gerada. Veja [Pré-requisitos](#prerequisites).

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

Considere usar o mesmo nome de projeto que usou quando preparou o ficheiro BACPAC para que todos os recursos sejam armazenados dentro do mesmo grupo de recursos. Desta forma, é mais fácil gerir tarefas de recursos, como limpar os recursos. Se utilizar o mesmo nome de projeto, pode remover o `New-AzResourceGroup` comando do script ou responder sim (y) ou não (n) quando lhe perguntarem se pretende atualizar o grupo de recursos existente.

Use uma senha gerada. Veja [Pré-requisitos](#prerequisites).

## <a name="verify-the-deployment"></a>Verificar a implementação

Para aceder ao servidor a partir do seu computador cliente, precisa adicionar uma regra adicional de firewall. Para mais informações, consulte [Criar e gerir as regras de firewall IP](../../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules).

No portal Azure, selecione a base de dados SQL do grupo de recursos recém-implantado. Selecione **Editor de consultas (pré-visualização)** e, em seguida, introduza as credenciais de administrador. Verá duas mesas importadas na base de dados.

![Editor de consulta (pré-visualização)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal Azure, selecione **Grupo Recurso** a partir do menu esquerdo.
1. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
1. Selecione o nome do grupo de recursos. Verá um total de seis recursos no grupo de recursos.
1. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Próximos passos

Neste tutorial, implementou um servidor e uma base de dados e importou um ficheiro BACPAC. Para aprender a resolver a implantação do modelo, consulte:

> [!div class="nextstepaction"]
> [Implantações do modelo ARM de resolução de problemas](./template-tutorial-troubleshoot.md)
