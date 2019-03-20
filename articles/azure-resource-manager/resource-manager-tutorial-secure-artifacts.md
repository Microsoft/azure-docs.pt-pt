---
title: Proteger os artefactos em implementações de modelo do Azure Resource Manager | Documentos da Microsoft
description: Saiba como proteger os artefactos utilizados nos seus modelos do Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/25/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: f7f235ce709fd81c4bb4c367774b4a96cd920e13
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120351"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Tutorial: Artefactos seguros em implementações de modelo do Azure Resource Manager

Saiba como proteger os artefactos utilizados nos seus modelos do Azure Resource Manager com a conta de armazenamento do Azure com assinaturas de acesso partilhado (SAS). Artefatos de implementação são todos os ficheiros, além do ficheiro de modelo principal, que são necessárias para concluir uma implementação. Por exemplo, no [Tutorial: Importar ficheiros BACPAC do SQL com modelos Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md), o modelo principal cria uma base de dados do SQL do Azure; ele também chama um ficheiro BACPAC para criar tabelas e inserir dados. Ficheiro BACPAC é um artefato. O artefacto é armazenado numa conta de armazenamento do Azure com o acesso público. Neste tutorial, vai utilizar SAS a conceder acesso limitado para o ficheiro BACPAC em sua própria conta de armazenamento do Azure. Para obter mais informações sobre SAS, consulte [Using partilhado assinaturas de acesso (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Para saber como proteger o modelo ligado, veja [Tutorial: Criar modelos do Azure Resource Manager ligados](./resource-manager-tutorial-create-linked-templates.md).

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um ficheiro BACPAC
> * Abra um modelo existente
> * Editar o modelo
> * Implementar o modelo
> * Verificar a implementação

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* [Visual Studio Code](https://code.visualstudio.com/) com a extensão Ferramentas do Resource Manager. Veja [Instalar a extensão](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Revisão [Tutorial: Importar ficheiros BACPAC do SQL com modelos Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md). O modelo utilizado neste tutorial é a desenvolveu esse tutorial. Uma ligação de transferência do modelo concluída é fornecida neste artigo.
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador do SQL Server. Eis um exemplo para gerar uma palavra-passe:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para obter mais informações, consulte [Tutorial: Integrar o Azure Key Vault na implementação de modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md). Também recomendamos que atualize a palavra-passe a cada três meses.

## <a name="prepare-a-bacpac-file"></a>Preparar um ficheiro BACPAC

Nesta secção, prepare o ficheiro BACPAC para que o ficheiro está acessível em segurança quando implementar o modelo do Resource Manager. Existem cinco procedimentos nesta secção:

* Transfira o ficheiro BACPAC.
* Criar uma conta de Armazenamento do Azure.
* Crie um contentor de Blob da conta de armazenamento.
* Carregue o ficheiro BACPAC para o contentor.
* Obter o token SAS do ficheiro BACPAC.

Para automatizar estes passos com um script do PowerShell, consulte o script a partir [carregar o modelo ligado](./resource-manager-tutorial-create-linked-templates.md#upload-the-linked-template).

### <a name="download-the-bacpac-file"></a>Transfira o ficheiro BACPAC

Transfira o [ficheiro BACPAC](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac)e guarde o ficheiro no seu computador local com o mesmo nome, **SQLDatabaseExtension.bacpac**.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. Selecione a imagem seguinte para abrir um modelo do Resource Manager no portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json" target="_blank"><img src="./media/resource-manager-tutorial-secure-artifacts/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Introduza as seguintes propriedades:

    * **Subscrição**: Selecione a sua subscrição do Azure.
    * **Grupo de recursos**: Selecione **criar novo** e atribua um nome. Um grupo de recursos é um contentor para recursos do Azure para o efeito de gestão. Neste tutorial, pode utilizar o mesmo grupo de recursos para a conta de armazenamento e a base de dados do SQL do Azure. Anote este nome de grupo de recursos, irá precisar dele quando criar a base de dados do SQL do Azure mais tarde nos tutoriais.
    * **Localização**: Selecione uma região. Por exemplo, **E.U.A. Central**. 
    * **Tipo de conta de armazenamento**: Utilize o valor predefinido, que é **Standard_LRS**.
    * **Localização**: Utilize o valor predefinido, que é **[resourceGroup (). location]**. Isso significa que usar a localização do grupo de recursos para a conta de armazenamento.
    * **Eu concordo com os termos e condições a utilizar acima**: (selecionado)
3. Selecione **Comprar**.
4. Selecione o ícone de notificação (o ícone de sino) no canto superior direito do portal para ver o estado de implementação.

    ![Painel de notificações do portal tutorial do Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-portal-notifications-pane.png)
5. Depois da conta de armazenamento é implementada com êxito, selecione **vá para o grupo de recursos** partir do painel de notificação para abrir o grupo de recursos.

### <a name="create-a-blob-container"></a>Criar um contentor de BLOBs

Um contentor de BLOBs é necessária para poder carregar todos os ficheiros. 

1. Selecione a conta de armazenamento para abri-la. Deverá ver apenas uma conta de armazenamento listada no grupo de recursos. O nome da sua conta de armazenamento é diferente daquele mostrado na captura de ecrã seguinte.

    ![Conta de armazenamento de tutorial do Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-storage-account.png)

2. Selecione o **Blobs** mosaico.

    ![Blobs de tutorial do Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-blobs.png)
3. Selecione **+ contentor** da parte superior para criar um novo contentor.
4. Introduza os seguintes valores:

    * **Nome**: introduza **sqlbacpac**. 
    * **Nível de acesso público**: Utilize o valor predefinido, **privado (sem acesso anónimo)**.
5. Selecione **OK**.
6. Selecione **sqlbacpac** para abrir o contentor criado recentemente.

### <a name="upload-the-bacpac-file-to-the-container"></a>Carregue o ficheiro BACPAC para o contentor

1. Selecione **Upload**.
2. Introduza os seguintes valores:

    * **Ficheiros**: Seguindo as instruções para selecionar o ficheiro BACPAC que transferiu anteriormente. O nome predefinido é **SQLDatabaseExtension.bacpac**.
    * **Tipo de autenticação**: Selecione **SAS**.  *SAS* é o valor predefinido.
3. Selecione **Upload**.  Depois do ficheiro é carregado com êxito, o nome do ficheiro deve ser listado no contentor.

### <a name="a-namegenerate-a-sas-token-generate-a-sas-token"></a><a name="generate-a-sas-token" />Gerar um token SAS

1. Com o botão direito **SQLDatabaseExtension.bacpac** do contentor e, em seguida, selecione **gerar SAS**.
2. Introduza os seguintes valores:

    * **Permissão**: Utilize a predefinição **leitura**.
    * **Data/hora de início e de expiração**: O valor predefinido dá-lhe oito horas para usar o token SAS. Se precisar de mais tempo para concluir este tutorial, atualize **expiração**.
    * **Endereços IP permitidos**: Deixe este campo em branco.
    * **Permitidos protocolos**: Utilize o valor predefinido: **HTTPS**.
    * **Chave de assinatura**: Utilize o valor predefinido: **Chave 1**.
3. Selecione **gerar o token SAS do blob e o URL**.
4. Faça uma cópia deles **URL de SAS do Blob**. No meio o URL é o nome de ficheiro **SQLDatabaseExtension.bacpac**.  O nome de ficheiro divide o URL em três partes:

   - **Localização do artefacto**: https://xxxxxxxxxxxxxx.blob.core.windows.net/sqlbacpac/. Certifique-se de que a localização termina com uma "/".
   - **Nome de ficheiro BACPAC**: SQLDatabaseExtension.bacpac.
   - **Token SAS de localização de artefacto**: Certifique-se de que precede o token com um "?."

     Vai precisar destes três valores na [implementar o modelo](#deploy-the-template).

## <a name="open-an-existing-template"></a>Abra um modelo existente

Nesta sessão, é modificar o modelo que criou no [Tutorial: Importar ficheiros BACPAC do SQL com modelos Azure Resource Manager](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) para chamar o ficheiro BACPAC, com um token SAS.  O modelo que desenvolveu o tutorial de extensão SQL é partilhado na [ https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json ](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json).

1. No Visual Studio Code, selecione **Ficheiro**>**Abrir Ficheiro**.
2. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json
    ```
3. Selecione **Abrir** para abrir o ficheiro.

    Existem cinco recursos definidos no modelo:

   * `Microsoft.Sql/servers`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/securityalertpolicies).
   * `Microsoft.SQL/servers/filewallRules`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`.  Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`.  Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

     É útil ter alguma compreensão básica do modelo antes de personalizá-lo.
4. Selecione **Ficheiro**>**Guardar Como** para guardar uma cópia do ficheiro no computador local, com o nome **azuredeploy.json**.

## <a name="edit-the-template"></a>Editar o modelo

Adicione os seguintes parâmetros adicionais:

```json
"_artifactsLocation": {
    "type": "string",
    "metadata": {
        "description": "The base URI where artifacts required by this template are located."
    }
},
"_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
        "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
},
"bacpacFileName": {
    "type": "string",
    "defaultValue": "SQLDatabaseExtension.bacpac",
    "metadata": {
        "description": "The bacpac for configure the database and tables."
    }
}
```

![Parâmetros de artefactos de seguro de tutorial do Resource Manager](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

Atualize o valor dos dois seguintes elementos:

```json
"storageKey": "[parameters('_artifactsLocationSasToken')]",
"storageUri": "[uri(parameters('_artifactsLocation'), parameters('bacpacFileName'))]",
```

## <a name="deploy-the-template"></a>Implementar o modelo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Veja a secção [Implementar o modelo](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) para obter o procedimento de implementação. Em alternativa, utilize o seguinte script de implementação do PowerShell:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$artifactsLocation = Read-Host -Prompt "Enter the artifacts location"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString
$bacpacFileName = Read-Host -Prompt "Enter the BACPAC file name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocation $artifactsLocation `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacFileName $bacpacFileName `
    -TemplateFile "$HOME/azuredeploy.json"
```

Utilize uma palavra-passe gerada. Veja [Pré-requisitos](#prerequisites).
Para os valores de artifactslocation, artifactslocationsastoken e bacpacFileName, consulte [gerar um token SAS](#generate-a-sas-token).

## <a name="verify-the-deployment"></a>Verificar a implementação

No portal, selecione a base de dados SQL a partir do grupo de recursos recentemente implementado. Selecione **Editor de consultas (pré-visualização)** e, em seguida, introduza as credenciais de administrador. Deverá ver duas tabelas importadas para a base de dados:

![Azure Resource Manager deploy sql extensions BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.  Verá um total de seis recursos no grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, implementou um servidor de SQL, uma base de dados SQL e importar um ficheiro BACPAC com o SAS token. Para saber como implementar recursos do Azure em várias regiões e como utilizar práticas de implementação seguras, veja

> [!div class="nextstepaction"]
> [Utilizar o Azure Deployment Manager](./resource-manager-tutorial-deploy-vm-extensions.md)
