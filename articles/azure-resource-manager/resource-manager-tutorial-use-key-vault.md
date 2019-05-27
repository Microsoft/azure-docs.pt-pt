---
title: Integrar o Azure Key Vault na implementação de modelos do Resource Manager | Microsoft Docs
description: Saiba como utilizar o Azure Key Vault para transmitir valores de parâmetros seguros durante a implementação de modelos do Resource Manager
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 0d78e6eaca708073c3a216507b320fe8783a25b6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239247"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Tutorial: Integrar o Azure Key Vault na implementação de modelo do Resource Manager

Saiba como obter segredos a partir do Azure Key Vault e transmitir os segredos como parâmetros durante a implementação do Resource Manager. O valor nunca está exposto porque só fazem referência a sua ID de Cofre de chaves. Para obter mais informações, veja [Utilizar o Azure Key Vault para transmitir valores de parâmetros seguros durante a implementação](./resource-manager-keyvault-parameter.md)

Na [definir a ordem de implementação de recursos](./resource-manager-tutorial-create-templates-with-dependent-resources.md) tutorial, vai criar uma máquina virtual. Tem de fornecer o nome de utilizador de administrador de máquina virtual e a palavra-passe. Em vez de fornecer a palavra-passe, pode armazenar previamente a palavra-passe num Azure Key Vault e, em seguida, personalizar o modelo para recuperar a palavra-passe do Cofre de chaves durante a implantação.

![Diagrama de integração de Cofre de chaves de modelo do Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um cofre de chaves
> * Abrir um modelo de Início rápido
> * Editar o ficheiro de parâmetros
> * Implementar o modelo
> * Validar a implementação
> * Limpar recursos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* [Visual Studio Code](https://code.visualstudio.com/) com [extensão Ferramentas do Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Eis um exemplo para gerar uma palavra-passe:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Verifique se a palavra-passe gerada cumpre os requisitos de palavra-passe da máquina virtual. Cada serviço do Azure tem requisitos de palavra-passe específicos. Para obter os requisitos de palavra-passe de VM, veja [Quais são os requisitos de palavra-passe quando criar uma VM?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Preparar um cofre de chaves

Nesta secção, criar um cofre de chaves e adicionar um segredo ao Cofre de chaves, para que possa obter o segredo quando implementar o seu modelo. Existem várias formas de criar um cofre de chaves. Neste tutorial, vai utilizar do Azure PowerShell para implementar um [modelo do Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Este modelo:

* Criar um cofre de chaves com o `enabledForTemplateDeployment` ativa de propriedade. Esta propriedade tem de ser verdadeira antes do processo de implementação do modelo pode acessar os segredos definidos neste Cofre de chaves.
* Adicione um segredo ao Cofre de chaves.  O segredo armazena a palavra-passe de administrador da máquina virtual.

> [!NOTE]
> Se (como o utilizador para implementar o modelo de máquina virtual) não for o proprietário ou contribuinte do Cofre de chaves, o proprietário ou contribuinte do Cofre de chaves deve conceder-lhe o acesso à permissão Microsoft.KeyVault/vaults/deploy/action para o Cofre de chaves. Para obter mais informações, veja [Utilizar o Azure Key Vault para transmitir valores de parâmetros seguros durante a implementação](./resource-manager-keyvault-parameter.md)

Para executar o seguinte script do PowerShell, selecione **experimente** para abrir o Cloud shell. Colar o script, clique com o botão direito do painel de shell e, em seguida, selecione **colar**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue
```

Algumas partes de informações importantes:

* O nome de grupo de recursos é o nome do projeto com **rg** anexado. Para que seja mais fácil [limpe os recursos criados neste tutorial](#clean-up-resources), utilize o mesmo grupo de recursos e o nome de projeto nome quando [implementar o modelo seguinte](#deploy-the-template).
* É o nome predefinido para o nome secreto **vmAdminPassword**. É codificado no modelo.
* Para poder-se de que para o modelo recuperar o segredo, tem de ativar uma política de acesso chamada **ativar o acesso ao Azure Resource Manager para a implementação de modelo** para o Cofre de chaves. Esta política está ativada no modelo. Para obter mais informações sobre esta política de acesso, consulte [implementar cofres de chaves e segredos](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

O modelo tem um valor de saída chamado **keyVaultId**. Anote o valor. Vai precisar deste ID quando implementar a máquina virtual. O formato do ID do Recurso é:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Quando copiar e colar o ID, o ID pode ser dividido em várias linhas. Tem de intercalar as linhas e cortar os espaços extras.

Para validar a implementação, execute o seguinte comando do PowerShell no mesmo painel de shell para obter o segredo em texto não criptografado. O comando só funciona na mesma sessão de shell, porque utiliza uma variável $keyVaultName definido no script de PowerShell anterior.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Agora preparou um cofre de chaves e um segredo, as secções seguintes mostram-lhe como personalizar um modelo existente para recuperar o segredo durante a implantação.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Os Modelos de Início Rápido do Azure são um repositório de modelos do Resource Manager. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo utilizado neste tutorial é denominado [Implementar uma VM do Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. No Visual Studio Code, selecione **Ficheiro**>**Abrir Ficheiro**.
2. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Selecione **Abrir** para abrir o ficheiro. Trata-se do mesmo cenário utilizado no [Tutorial: Criar modelos do Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
4. Existem cinco recursos definidos pelo modelo:

   * `Microsoft.Storage/storageAccounts`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     É útil ter alguma compreensão básica do modelo antes de personalizá-lo.
5. Selecione **Ficheiro**>**Guardar Como** para guardar uma cópia do ficheiro no computador local, com o nome **azuredeploy.json**.
6. Repita os passos 1 a 4 para abrir o URL seguinte e, em seguida, guarde o ficheiro como **azuredeploy.parameters.json**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Editar o ficheiro de parâmetros

Não precisa de fazer quaisquer alterações ao ficheiro de modelo.

1. Abra **azuredeploy.parameters.json** no Visual Studio Code, se não estiver aberto.
2. Atualize o parâmetro **adminPassword** para:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > Substitua o valor de **id** com o ID de recurso do seu Cofre de chaves criado no último procedimento.

    ![Integrar o cofre de chaves e o ficheiro de parâmetros de implementação de máquinas virtuais de modelos do Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Atribua valores para:

    * **adminUsername**: nome da conta de administrador da máquina virtual.
    * **dnsLabelPrefix**: dê um nome a dnsLabelPrefix.

    Ver um exemplo na captura de ecrã anterior.

4. Guarde as alterações.

## <a name="deploy-the-template"></a>Implementar o modelo

Siga as instruções em [Implementar o modelo](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) para implementar o modelo. Tem de carregar ambos **azuredeploy. JSON** e **azuredeploy** para o Cloud shell e, em seguida, utilize o seguinte script do PowerShell para implementar o modelo:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Quando implementar o modelo, utilize o mesmo grupo de recursos como o Cofre de chaves. Facilita a limpeza dos recursos. Apenas tem de eliminar um grupo de recursos em vez de dois.

## <a name="validate-the-deployment"></a>Validar a implementação

Depois de ter implementado com êxito a máquina virtual, teste o início de sessão com a palavra-passe armazenada no Cofre de chaves.

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Selecione **Grupos de recursos**/**NomedoGrupodeRecursos>** /**simpleWinVM**
3. Selecione **ligar** na parte superior.
4. Selecione **baixar arquivo RDP** e, em seguida, siga as instruções para iniciar sessão na máquina virtual utilizando a palavra-passe armazenada no Cofre de chaves.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, obteve um segredo do Azure Key Vault e utilizou-o na sua implementação do modelo.  Para saber como criar modelos ligados, veja:

> [!div class="nextstepaction"]
> [Criar modelos ligados](./resource-manager-tutorial-create-linked-templates.md)
