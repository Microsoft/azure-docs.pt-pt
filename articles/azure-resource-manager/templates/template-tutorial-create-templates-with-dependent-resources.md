---
title: Modelo com recursos dependentes
description: Saiba como criar um modelo do Azure Resource Manager com vários recursos e como implementá-lo com o portal do Azure
author: mumian
ms.date: 04/10/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: bbe973f5c701f55705fe197f56f5f8ab1d9e8c68
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260790"
---
# <a name="tutorial-create-arm-templates-with-dependent-resources"></a>Tutorial: Criar modelos ARM com recursos dependentes

Aprenda a criar um modelo de Gestor de Recursos Azure (ARM) para implementar vários recursos e configurar a ordem de implementação. Depois de criar o modelo, implementa o modelo utilizando a Cloud Shell do portal Azure.

Neste tutorial, vai criar uma conta de armazenamento, uma máquina virtual, uma rede virtual e mais alguns recursos dependentes. Alguns dos recursos não podem ser implementados enquanto existir outro recurso. Por exemplo, não é possível criar a máquina virtual enquanto as respetivas conta de armazenamento e interface de rede existirem. Defina esta relação fazendo com que um recurso seja dependente dos outros recursos. O Resource Manager avalia as dependências entre os recursos e implementa-os por ordem dependente. Quando os recursos não são dependentes entre si, o Resource Manager implementa-os em paralelo. Para mais informações, consulte [Definir a ordem de implantação de recursos em modelos ARM](./define-resource-dependency.md).

![Diagrama de ordem de implementação de recursos dependentes do gestor de recursos de recursos](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início rápido
> * Explorar o modelo
> * Implementar o modelo

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code com extensão Ferramentas do Resource Manager. Consulte [o Use Visual Studio Code para criar modelos ARM](use-vs-code-to-create-template.md).
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Eis um exemplo para gerar uma palavra-passe:

    ```console
    openssl rand -base64 32
    ```

    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para mais informações, consulte [Tutorial: Integre o Cofre chave Azure na implementação](./template-tutorial-use-key-vault.md)do modelo ARM . Também recomendamos que atualize a palavra-passe a cada três meses.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Os modelos Azure QuickStart são um repositório para modelos ARM. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo utilizado neste tutorial é denominado [Implementar uma VM do Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. A partir do Código do Estúdio Visual, selecione **File**>**Open File**.
2. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Selecione **Abrir** para abrir o ficheiro.
4. Selecione **Guardar Ficheiros**>**Para** guardar uma cópia do ficheiro para o computador local com o nome **azuredeploy.json**.

## <a name="explore-the-template"></a>Explorar o modelo

Ao explorar o modelo nesta secção, tente responder a estas perguntas:

* Número de recursos do Azure definidos neste modelo?
* Um dos recursos é uma conta de armazenamento do Azure.  A definição aparenta ser como a utilizada no último tutorial?
* Pode encontrar as referências de modelo para os recursos definidos neste modelo?
* Pode encontrar as dependências dos recursos?

1. No Visual Studio Code, feche os elementos até ver apenas os elementos de primeiro nível e os elementos de segundo nível dentro de **recursos**:

    ![Modelos do Azure Resource Manager no Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Existem seis recursos definidos pelo modelo:

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresss**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     É útil rever a referência do modelo antes de personalizar um modelo.

1. Expanda o primeiro recurso. É uma conta de armazenamento. Compare a definição de recurso com a referência do [modelo](/azure/templates/Microsoft.Storage/storageAccounts).

    ![Definição da conta de armazenamento de modelos do Azure Resource Manager no Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

1. Expanda o segundo recurso. O tipo de recurso é `Microsoft.Network/publicIPAddresses`. Compare a definição de recurso com a referência do [modelo](/azure/templates/microsoft.network/publicipaddresses).

    ![Definição do endereço IP público de modelos do Azure Resource Manager no Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)

1. Expanda o terceiro recurso. O tipo de recurso é `Microsoft.Network/networkSecurityGroups`. Compare a definição de recurso com a referência do [modelo](/azure/templates/microsoft.network/networksecuritygroups).

    ![Visual Studio Code Azure Resource Manager modelos definição de grupo de segurança de rede](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-network-security-group-definition.png)

1. Expanda o quarto recurso. O tipo de recurso é `Microsoft.Network/virtualNetworks`:

    ![Modelos de gestor de recursos do Estúdio Visual Azure gestor de recursos dependem da rede virtual](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-virtual-network-definition.png)

    O elemento dependsOn permite-lhe definir um recurso como sendo dependente de um ou mais recursos. O elemento dependsOn permite-lhe definir um recurso como sendo dependente de um ou mais recursos.  Este recurso depende de um outro recurso:

    * `Microsoft.Network/networkSecurityGroups`

1. Expanda o recurso de cinquenta. O tipo de recurso é `Microsoft.Network/networkInterfaces`. O recurso depende de dois outros recursos:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

1. Expanda o sexto recurso. Este recurso é uma máquina virtual. Depende de dois outros recursos:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

O diagrama seguinte ilustra os recursos e as informações de dependência para este modelo:

![Diagrama de dependência de modelos do Azure Resource Manager no Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Ao especificar as dependências, o Resource Manager implementa a solução de forma eficiente. Implementa a conta de armazenamento, o endereço IP público e a rede virtual em paralelo porque não têm dependências. Depois de o endereço IP público e a rede virtual serem implementados, a interface de rede é criada. Quando todos os outros recursos estiverem implementados, o Resource Manager implementa a máquina virtual.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Siga as instruções em [Desdobrar o modelo](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) para abrir a Cloud Shell e carregar o modelo revisto.

1. Executar o seguinte script PowerShell para implementar o modelo.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

1. Execute o seguinte comando do PowerShell para listar a máquina virtual recentemente criada:

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $resourceGroupName = "${projectName}rg"
    $vmName = "SimpleWinVM"

    Get-AzVM -Name $vmName -ResourceGroupName $resourceGroupName
    
    Write-Host "Press [ENTER] to continue ..."
    ```

    O nome da máquina virtual está hard-coded como **SimpleWinVM** dentro do modelo.

1. Faça o RDP à máquina virtual para verificar se a máquina virtual foi criada com êxito.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.  Verá um total de seis recursos no grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, desenvolveu e implementou um modelo para criar uma máquina virtual, uma rede virtual e os recursos dependentes. Para aprender a utilizar scripts de implantação para realizar operações de implantação pré/pós, consulte:

> [!div class="nextstepaction"]
> [Utilize o script de implementação](./template-tutorial-deployment-script.md)
