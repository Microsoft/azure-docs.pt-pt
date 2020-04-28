---
title: Use cofre de chave azure em modelos
description: Saiba como utilizar o Azure Key Vault para transmitir valores de parâmetros seguros durante a implementação de modelos do Resource Manager
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 7fd84fc2e98578772c806f358cb8d6c400e0d994
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82185018"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-arm-template-deployment"></a>Tutorial: Integrar o cofre de chaves azure na implementação do modelo ARM

Aprenda a recuperar segredos de um cofre de chaves Azure e passe os segredos como parâmetros quando implementar um modelo de Gestor de Recursos Azure (ARM). O valor do parâmetro nunca é exposto, porque refere apenas o seu ID chave do cofre. Pode fazer referência ao segredo do cofre chave utilizando uma identificação estática ou uma identificação dinâmica. Este tutorial usa uma identificação estática. Com a abordagem id estática, você referencia o cofre chave no ficheiro de parâmetro do modelo, não o ficheiro do modelo. Para obter mais informações sobre ambas as abordagens, consulte [o Cofre chave Azure para passar o valor seguro](./key-vault-parameter.md)do parâmetro durante a implementação .

No tutorial de encomenda de [recursos set,](./template-tutorial-create-templates-with-dependent-resources.md) cria-se uma máquina virtual (VM). Tem de fornecer o nome de utilizador e senha do administrador VM. Em vez de fornecer a palavra-passe, pode pré-armazenar a palavra-passe num cofre de chaves Azure e, em seguida, personalizar o modelo para recuperar a palavra-passe do cofre da chave durante a implementação.

![Diagrama mostrando a integração de um modelo de Gestor de Recursos com um cofre chave](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Prepare um cofre chave
> * Abrir um modelo de início rápido
> * Editar o ficheiro de parâmetros
> * Implementar o modelo
> * Validar a implementação
> * Limpar recursos

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code com extensão Ferramentas do Resource Manager. Consulte [o Use Visual Studio Code para criar modelos ARM](use-vs-code-to-create-template.md).
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador vM. Aqui está uma amostra para gerar uma senha:

    ```console
    openssl rand -base64 32
    ```
    Verifique se a palavra-passe gerada satisfaz os requisitos de senha vM. Cada serviço do Azure tem requisitos de palavra-passe específicos. Para os requisitos de senha VM, consulte [quais são os requisitos de senha quando cria um VM?](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)

## <a name="prepare-a-key-vault"></a>Prepare um cofre chave

Nesta secção, cria-se um cofre chave e adiciona-lhe um segredo, para que possa recuperar o segredo quando implementar o seu modelo. Há muitas maneiras de criar um cofre chave. Neste tutorial, utiliza-se o Azure PowerShell para implementar um [modelo ARM](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Este modelo faz duas coisas:

* Cria um cofre `enabledForTemplateDeployment` chave com a propriedade ativada. Esta propriedade deve ser *verdadeira* antes que o processo de implementação do modelo possa aceder aos segredos que são definidos no cofre chave.
* Acrescenta um segredo ao cofre da chave. O segredo armazena a senha do administrador vM.

> [!NOTE]
> Como utilizador que está a implementar o modelo de máquina virtual, se não for o Proprietário ou colaborador do cofre chave, o Proprietário ou um Colaborador deve conceder-lhe acesso ao *Microsoft.KeyVault/vaults/deploy/action* permission para o cofre chave. Para mais informações, consulte [O Cofre chave Azure para passar um valor de parâmetro seguro durante](./key-vault-parameter.md)a implementação .

Para executar o seguinte script Azure PowerShell, selecione **Experimente-o** para abrir a Azure Cloud Shell. Para colar o script, clique na vidraça da direita e, em seguida, **selecione Pasta**.

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
> * O nome predefinido para o segredo é **vmAdminPassword**. Está codificado no modelo.
> * Para permitir que o modelo recupere o segredo, deve ativar uma política de acesso chamada **Enable access to Azure Resource Manager para a implementação** do modelo para o cofre chave. Esta política está ativada no modelo. Para obter mais informações sobre a política de acesso, consulte [Implementar cofres e segredos chave.](./key-vault-parameter.md#deploy-key-vaults-and-secrets)

O modelo tem um valor de saída, chamado *keyVaultId*. Você usará esta identificação juntamente com o nome secreto para recuperar o valor secreto mais tarde no tutorial. O formato ID do recurso é:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Quando copias e colas a identificação, pode ser dividida em várias linhas. Fundir as linhas e aparar os espaços extras.

Para validar a implantação, execute o seguinte comando PowerShell no mesmo painel de conchas para recuperar o segredo em texto claro. O comando funciona apenas na mesma sessão de concha, porque utiliza a *variável $keyVaultName*, que é definida no script PowerShell anterior.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Agora preparou um cofre e um segredo. As seguintes secções mostram-lhe como personalizar um modelo existente para recuperar o segredo durante a implementação.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de início rápido

Os modelos Azure Quickstart é um repositório para modelos ARM. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo que é usado neste tutorial chama-se [Implementar um Simples VM do Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. No Código do Estúdio Visual, selecione **File** > **Open File**.

1. Na caixa de **nomes de ficheiros,** cola o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Selecione **Abrir** para abrir o ficheiro. O cenário é o mesmo que é usado no [Tutorial: Criar modelos ARM com recursos dependentes](./template-tutorial-create-templates-with-dependent-resources.md).
   O modelo define seis recursos:

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresss**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

   É útil ter alguma compreensão básica do modelo antes de personalizá-lo.

1. Selecione **Guardar** > **Ficheiros As**e, em seguida, guardar uma cópia do ficheiro para o seu computador local com o nome *azuredeploy.json*.

1. Repita os passos 1-3 para abrir o seguinte URL e, em seguida, guarde o ficheiro como *azuredeploy.parâmetros.json*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Editar o ficheiro de parâmetros

Ao utilizar o método de ID estático, não precisa de fazer alterações no ficheiro do modelo. Recuperar o valor secreto é feito configurando o ficheiro de parâmetro do modelo.

1. No Visual Studio Code, abra *azuredeploy.parameters.json* se ainda não estiver aberto.
1. Atualize `adminPassword` o parâmetro para:

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
    > Substitua o valor por **ID** com a identificação de recursos do cofre chave que criou no procedimento anterior. O secretName é codificado como **vmAdminPassword**.  Ver [Preparar um cofre de chaves.](#prepare-a-key-vault)

    ![Integrar o cofre chave e o modelo de dispositivode dispositivos de implementação de máquinas virtuais](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Atualizar os seguintes valores:

    * **adminUsername**: O nome da conta de administrador de máquina virtual.
    * **dnsLabelPrefix**: Nomeie o valor dnsLabelPrefix.

    Por exemplo, de nomes, veja a imagem anterior.

1. Guarde as alterações.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Inscreva-se na [Casca de Nuvem Azure](https://shell.azure.com)

1. Escolha o seu ambiente preferido selecionando **powerShell** ou **Bash** (para CLI) no canto superior esquerdo.  É necessário reiniciar o Shell quando mudar.

    ![Ficheiro de upload do portal Azure Cloud Shell](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selecione **Carregar/transferir ficheiros** e, em seguida, selecione **Carregar**. Faça o upload de *azuredeploy.json* e *azuredeploy.parameters.json* para Cloud Shell. Depois de fazer o upload do ficheiro, pode utilizar o comando **ls** e o comando do **gato** para verificar se o ficheiro é carregado com sucesso.

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

    Quando implementar o modelo, use o mesmo grupo de recursos que usou no cofre da chave. Esta abordagem facilita a limpeza dos recursos, porque é necessário eliminar apenas um grupo de recursos em vez de dois.

## <a name="validate-the-deployment"></a>Validar a implementação

Depois de ter implementado com sucesso a máquina virtual, teste as credenciais de entrada utilizando a senha que está armazenada no cofre da chave.

1. Abra o [portal Azure.](https://portal.azure.com)

1. Selecione **Grupos de** >  > **\<Recursos*YourResourceGroupName*>****simpleWinVM**.
1. Selecione **ligar** na parte superior.
1. Selecione **Download RDP File**, e, em seguida, siga as instruções para iniciar sessão na máquina virtual utilizando a palavra-passe armazenada no cofre da chave.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos seus recursos Azure, limpe os recursos que implementou ao apagar o grupo de recursos.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, recuperou um segredo do seu cofre de chaves Azure. Em seguida, usou o segredo na sua implantação do modelo. Para aprender a utilizar extensões de máquinas virtuais para executar tarefas de implantação pós-implantação, consulte:

> [!div class="nextstepaction"]
> [Implementar extensões de máquinavirtual](./template-tutorial-deploy-vm-extensions.md)
