---
title: Use a adoçada de chave Azure em modelos
description: Aprenda a usar o Cofre da Chave Azure para passar valores de parâmetros seguros durante a implementação do modelo do Gestor de Recursos Azure (modelo ARM).
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 75eb977559573b72883de3ddbc27391c7e299a6f
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929321"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-arm-template-deployment"></a>Tutorial: Integrar o Azure Key Vault na implementação de modelos do Resource Manager

Aprenda a recuperar segredos a partir de um cofre de chave Azure e passe os segredos como parâmetros quando implementar um modelo de Gestor de Recursos Azure (modelo ARM). O valor do parâmetro nunca é exposto, porque refere apenas o seu ID do cofre chave. Pode fazer referência ao segredo do cofre de chaves utilizando um ID estático ou um ID dinâmico. Este tutorial usa uma identificação estática. Com a abordagem estática de ID, você referencia o cofre chave no arquivo do parâmetro do modelo, não o ficheiro do modelo. Para obter mais informações sobre ambas as abordagens, consulte [use Azure Key Vault para passar o valor do parâmetro seguro durante a implementação](./key-vault-parameter.md).

No tutorial de [ordem de implementação de recursos Definido,](./template-tutorial-create-templates-with-dependent-resources.md) cria-se uma máquina virtual (VM). Tem de fornecer o nome de utilizador e a palavra-passe do administrador VM. Em vez de fornecer a palavra-passe, pode pré-armazenar a palavra-passe num cofre de chave Azure e, em seguida, personalizar o modelo para recuperar a palavra-passe do cofre chave durante a implementação.

![Diagrama que mostra a integração de um modelo de Gestor de Recursos com um cofre chave](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Prepare um cofre chave
> * Abrir um modelo de início rápido
> * Editar o ficheiro de parâmetros
> * Implementar o modelo
> * Validar a implementação
> * Limpar os recursos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code com extensão Ferramentas do Resource Manager. Consulte [Quickstart: Crie modelos ARM com Código de Estúdio Visual](quickstart-create-templates-use-visual-studio-code.md).
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador VM. Aqui está uma amostra para gerar uma senha:

    ```console
    openssl rand -base64 32
    ```
    Verifique se a palavra-passe gerada satisfaz os requisitos de senha VM. Cada serviço do Azure tem requisitos de palavra-passe específicos. Para os requisitos de senha VM, consulte [quais são os requisitos de senha quando criar um VM?](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)

## <a name="prepare-a-key-vault"></a>Prepare um cofre chave

Nesta secção, você cria um cofre chave e adiciona-lhe um segredo, para que possa recuperar o segredo quando implementar o seu modelo. Há muitas maneiras de criar um cofre chave. Neste tutorial, utilize a Azure PowerShell para implantar um [modelo ARM](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Este modelo faz duas coisas:

* Cria um cofre chave com a `enabledForTemplateDeployment` propriedade ativada. Esta propriedade deve ser *verdadeira* antes que o processo de implementação do modelo possa aceder aos segredos que são definidos no cofre da chave.
* Acrescenta um segredo ao cofre das chaves. O segredo armazena a senha do administrador VM.

> [!NOTE]
> Como utilizador que está a implementar o modelo de máquina virtual, se não for o Proprietário ou um Contribuinte para o cofre de chaves, o Proprietário ou um Contribuinte deve conceder-lhe acesso à *microsoft.KeyVault/vaults/deploy/action* permission para o cofre de chaves. Para obter mais informações, consulte [use Azure Key Vault para passar um valor de parâmetro seguro durante a implementação](./key-vault-parameter.md).

Para executar o seguinte script Azure PowerShell, selecione **Experimente-o** para abrir a Azure Cloud Shell. Para colar o script, clique com o botão direito da vidraça e, em seguida, **selecione Pasta**.

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

Write-Host "Press [ENTER] to continue ..."
```

> [!IMPORTANT]
> * O nome do grupo de recursos é o nome do projeto, mas com **rg** anexado a ele. Para facilitar a [limpeza dos recursos que criou neste tutorial,](#clean-up-resources)use o mesmo nome de projeto e nome de grupo de recursos quando implementar o próximo [modelo](#deploy-the-template).
> * O nome padrão para o segredo é **vmAdminPassword**. É codificado no modelo.
> * Para ativar o modelo para recuperar o segredo, tem de ativar uma política de acesso chamada **Enable access to Azure Resource Manager para a implementação** do modelo para o cofre de chaves. Esta política está ativada no modelo. Para obter mais informações sobre a política de acesso, consulte [a Deploy key vaults and secrets](./key-vault-parameter.md#deploy-key-vaults-and-secrets).

O modelo tem um valor de saída, chamado *keyVaultId*. Você usará esta identificação juntamente com o nome secreto para recuperar o valor secreto mais tarde no tutorial. O formato de ID de recursos é:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Quando copiar e colar o ID, pode ser dividido em várias linhas. Misture as linhas e corte os espaços extras.

Para validar a colocação, executar o seguinte comando PowerShell no mesmo painel de concha para recuperar o segredo em texto claro. O comando funciona apenas na mesma sessão de concha, porque utiliza a variável *$keyVaultName*, que é definida no script PowerShell anterior.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Agora preparaste um cofre e um segredo. As seguintes secções mostram-lhe como personalizar um modelo existente para recuperar o segredo durante a implementação.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de início rápido

Azure Quickstart Templates é um repositório para modelos ARM. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo que é usado neste tutorial chama-se [Implementar um Simples Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. No Código do Estúdio Visual, selecione Ficheiro Aberto **de**  >  **Ficheiros**.

1. Na caixa **de nomes do Ficheiro,** cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Selecione **Abrir** para abrir o ficheiro. O cenário é o mesmo que o usado no [Tutorial: Criar modelos ARM com recursos dependentes.](./template-tutorial-create-templates-with-dependent-resources.md)
   O modelo define seis recursos:

   * [**Microsoft.Storage/storageAcounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

   É útil ter alguma compreensão básica do modelo antes de personalizá-lo.

1. Selecione **File**  >  **'Guardar ficheiros',** e, em seguida, guarde uma cópia do ficheiro para o computador local com o nome *azuredeploy.jsligado*.

1. Repita os passos 1-3 para abrir o seguinte URL e, em seguida, guarde o ficheiro à medida *queazuredeploy.parameters.js.*

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Editar o ficheiro de parâmetros

Ao utilizar o método de ID estático, não precisa de fazer alterações no ficheiro do modelo. A recuperação do valor secreto é feita configurando o ficheiro de parâmetro do modelo.

1. No Visual Studio Code, abra *azuredeploy.parameters.js* se ainda não estiver aberto.
1. Atualize o `adminPassword` parâmetro para:

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
    > Substitua o valor **do id** pelo ID de recurso do cofre chave que criou no procedimento anterior. O nome secreto é codificado como **vmAdminPassword**.  Ver [Preparar um cofre de chaves](#prepare-a-key-vault).

    ![Integrar o cofre chave e o ficheiro de parâmetros de implementação de modelos de modelo de gestão de modelos de gestão de modelos de gestão de recursos](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Atualizar os seguintes valores:

    * **adminUsername**: O nome da conta de administrador de máquina virtual.
    * **dnsLabelPrefix**: Nomeie o valor dnsLabelPrefix.

    Por exemplo, veja a imagem anterior.

1. Guarde as alterações.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Inscreva-se na [Azure Cloud Shell](https://shell.azure.com)

1. Escolha o seu ambiente preferido selecionando **PowerShell** ou **Bash** (para CLI) no canto superior esquerdo.  É necessário reiniciar o Shell quando mudar.

    ![Arquivo de upload do portal Azure Cloud Shell](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selecione **Carregar/transferir ficheiros** e, em seguida, selecione **Carregar**. Faça *o upload de ambos osazuredeploy.js* e *azuredeploy.parameters.js* para a Cloud Shell. Depois de carregar o ficheiro, pode utilizar o comando **ls** e o comando do **gato** para verificar se o ficheiro é carregado com sucesso.

1. Executar o seguinte script PowerShell para implementar o modelo.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
    $location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$HOME/azuredeploy.json" `
        -TemplateParameterFile "$HOME/azuredeploy.parameters.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    Quando implementar o modelo, utilize o mesmo grupo de recursos que utilizou no cofre da chave. Esta abordagem facilita a limpeza dos recursos, pois precisa de eliminar apenas um grupo de recursos em vez de dois.

## <a name="validate-the-deployment"></a>Validar a implementação

Depois de ter implantado com sucesso a máquina virtual, teste as credenciais de entrada utilizando a palavra-passe que está armazenada no cofre da chave.

1. Abra o [portal do Azure](https://portal.azure.com).

1. Selecione **grupos de recursos**  >  **\<*YourResourceGroupName*>**  >  **simplesWinVM**.
1. Selecione **ligar** na parte superior.
1. Selecione **Baixar Ficheiro RDP** e, em seguida, siga as instruções para iniciar sing na máquina virtual utilizando a palavra-passe que está armazenada no cofre da chave.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos seus recursos Azure, limpe os recursos que implementou eliminando o grupo de recursos.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, recuperaste um segredo do teu cofre de chaves Azure. Em seguida, usou o segredo na sua implementação do modelo. Para aprender a utilizar extensões de máquinas virtuais para executar tarefas de implantação pós-implantação, consulte:

> [!div class="nextstepaction"]
> [Implementar extensões de máquinas virtuais](./template-tutorial-deploy-vm-extensions.md)
