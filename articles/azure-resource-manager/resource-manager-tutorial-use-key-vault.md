---
title: Usar Azure Key Vault no modelo do Resource Manager
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
ms.openlocfilehash: 5dfc186e5d047de76e16ec145f5f0afe94b8d2f4
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533608"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-resource-manager-template-deployment"></a>Tutorial: integrar Azure Key Vault em sua implantação de modelo do Resource Manager

Saiba como recuperar segredos de um cofre de chaves do Azure e passar os segredos como parâmetros ao implantar Azure Resource Manager. O valor do parâmetro nunca é exposto, pois você faz referência somente à ID do cofre de chaves. Para obter mais informações, consulte [usar Azure Key Vault para passar o valor do parâmetro seguro durante a implantação](./resource-manager-keyvault-parameter.md).

No tutorial [definir ordem de implantação de recursos](./resource-manager-tutorial-create-templates-with-dependent-resources.md) , você cria uma VM (máquina virtual). Você precisa fornecer o nome de usuário e a senha do administrador da VM. Em vez de fornecer a senha, você pode armazenar previamente a senha em um cofre de chaves do Azure e, em seguida, personalizar o modelo para recuperar a senha do cofre de chaves durante a implantação.

![Diagrama exibindo a integração de um modelo do Resource Manager com um cofre de chaves](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um cofre de chaves
> * Abrir um modelo de início rápido
> * Editar o ficheiro de parâmetros
> * Implementar o modelo
> * Validar a implementação
> * Limpar recursos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* [Visual Studio Code](https://code.visualstudio.com/) com a [extensão de ferramentas do Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Para aumentar a segurança, use uma senha gerada para a conta de administrador da VM. Aqui está um exemplo para gerar uma senha:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Verifique se a senha gerada atende aos requisitos de senha da VM. Cada serviço do Azure tem requisitos de palavra-passe específicos. Para os requisitos de senha da VM, consulte [quais são os requisitos de senha ao criar uma VM?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Preparar um cofre de chaves

Nesta seção, você cria um cofre de chaves e adiciona um segredo a ele, para que você possa recuperar o segredo ao implantar seu modelo. Há várias maneiras de criar um cofre de chaves. Neste tutorial, você usará Azure PowerShell para implantar um [modelo do Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Este modelo faz o seguinte:

* Cria um cofre de chaves com a propriedade `enabledForTemplateDeployment` habilitada. Essa propriedade deve ser *verdadeira* antes que o processo de implantação de modelo possa acessar os segredos definidos no cofre de chaves.
* Adiciona um segredo ao cofre de chaves. O segredo armazena a senha de administrador da VM.

> [!NOTE]
> Como o usuário que está implantando o modelo de máquina virtual, se você não for o proprietário ou um colaborador para o cofre de chaves, o proprietário ou um colaborador deverá conceder acesso à permissão *Microsoft. keyvault/Vaults/Deployment/Action* para o cofre de chaves. Para obter mais informações, consulte [usar Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](./resource-manager-keyvault-parameter.md).

Para executar o script de Azure PowerShell a seguir, selecione **tentar** abrir Azure cloud Shell. Para colar o script, clique com o botão direito do mouse no painel Shell e selecione **colar**.

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

> [!IMPORTANT]
> * O nome do grupo de recursos é o nome do projeto, mas com o **RG** acrescentado a ele. Para facilitar a [limpeza dos recursos que você criou neste tutorial](#clean-up-resources), use o mesmo nome do projeto e o nome do grupo de recursos ao [implantar o próximo modelo](#deploy-the-template).
> * O nome padrão para o segredo é **vmAdminPassword**. Ele é codificado no modelo.
> * Para habilitar o modelo para recuperar o segredo, você deve habilitar uma política de acesso chamada "habilitar o acesso ao Azure Resource Manager para implantação de modelo" para o cofre de chaves. Essa política está habilitada no modelo. Para obter mais informações sobre a política de acesso, consulte [implantar segredos e cofres de chaves](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

O modelo tem um valor de saída, chamado *Keyvaultid*. Anote o valor de ID para uso posterior, quando você implantar a máquina virtual. O formato da ID de recurso é:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Quando você copia e cola a ID, ela pode ser dividida em várias linhas. Mescle as linhas e corte os espaços extras.

Para validar a implantação, execute o seguinte comando do PowerShell no mesmo painel de Shell para recuperar o segredo em texto não criptografado. O comando funciona apenas na mesma sessão de Shell, pois ele usa a variável *$keyVaultName*, que é definida no script do PowerShell anterior.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Agora você preparou um cofre de chaves e um segredo. As seções a seguir mostram como personalizar um modelo existente para recuperar o segredo durante a implantação.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de início rápido

Os modelos de início rápido do Azure são um repositório para modelos do Resource Manager. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo usado neste tutorial é chamado [implantar uma VM simples do Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. Em Visual Studio Code, selecione **arquivo** > **Abrir arquivo**.

1. Na caixa **nome do arquivo** , Cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Selecione **Abrir** para abrir o ficheiro. O cenário é o mesmo usado no [tutorial: criar modelos de Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
   O modelo define cinco recursos:

   * `Microsoft.Storage/storageAccounts`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

   É útil ter alguma compreensão básica do modelo antes de personalizá-lo.

1. Selecione **arquivo**  > **salvar como**e salve uma cópia do arquivo em seu computador local com o nome *azuredeploy. JSON*.

1. Repita as etapas 1-3 para abrir a URL a seguir e salve o arquivo como *azuredeploy. Parameters. JSON*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Editar o ficheiro de parâmetros

Não precisa de fazer quaisquer alterações ao ficheiro de modelo.

1. No Visual Studio Code, abra *azuredeploy. Parameters. JSON* se ele ainda não estiver aberto.
1. Atualize o parâmetro `adminPassword` para:

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
    > Substitua o valor de **ID** pela ID de recurso do cofre de chaves que você criou no procedimento anterior.

    ![Integrar o cofre de chaves e o arquivo de parâmetros de implantação de máquina virtual do modelo do Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Atualize os seguintes valores:

    * **adminUsername**: o nome da conta de administrador da máquina virtual.
    * **dnsLabelPrefix**: nomeie o valor de dnsLabelPrefix.

    Para obter exemplos de nomes, consulte a imagem anterior.

1. Guarde as alterações.

## <a name="deploy-the-template"></a>Implementar o modelo

Siga as instruções em [implantar o modelo](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Carregue *azuredeploy. JSON* e *azuredeploy. Parameters. JSON* para Cloud Shell e, em seguida, use o seguinte script do PowerShell para implantar o modelo:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Ao implantar o modelo, use o mesmo grupo de recursos que você usou no cofre de chaves. Essa abordagem facilita a limpeza dos recursos, pois você precisa excluir apenas um grupo de recursos em vez de dois.

## <a name="validate-the-deployment"></a>Validar a implementação

Depois de implantar com êxito a máquina virtual, teste as credenciais de entrada usando a senha armazenada no cofre de chaves.

1. Abra o [Portal do Azure](https://portal.azure.com).

1. Selecione **grupos de recursos**  >  **\<*YourResourceGroupName* >**  > **simpleWinVM**.
1. Selecione **conectar** na parte superior.
1. Selecione **baixar arquivo RDP**e siga as instruções para entrar na máquina virtual usando a senha armazenada no cofre de chaves.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais dos recursos do Azure, limpe os recursos implantados excluindo o grupo de recursos.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você recuperou um segredo do seu Azure Key Vault. Em seguida, você usou o segredo em sua implantação de modelo. Para saber como criar modelos ligados, veja:

> [!div class="nextstepaction"]
> [Criar modelos ligados](./resource-manager-tutorial-create-linked-templates.md)
