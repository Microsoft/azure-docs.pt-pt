---
title: Modelo com recursos dependentes
description: Saiba como criar um modelo de Gestor de Recursos Azure (modelo ARM) com múltiplos recursos e como implementá-lo usando o portal Azure
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a77f64e51a26e1f916f9f96704c55412a870a509
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673941"
---
# <a name="tutorial-create-arm-templates-with-dependent-resources"></a>Tutorial: Criar modelos do ARM com recursos dependentes

Saiba como criar um modelo de Gestor de Recursos Azure (modelo ARM) para implementar múltiplos recursos e configurar a ordem de implementação. Depois de criar o modelo, implementa o modelo utilizando a Cloud Shell a partir do portal Azure.

Neste tutorial, vai criar uma conta de armazenamento, uma máquina virtual, uma rede virtual e mais alguns recursos dependentes. Alguns dos recursos não podem ser implementados enquanto existir outro recurso. Por exemplo, não é possível criar a máquina virtual enquanto as respetivas conta de armazenamento e interface de rede existirem. Defina esta relação fazendo com que um recurso seja dependente dos outros recursos. O Resource Manager avalia as dependências entre os recursos e implementa-os por ordem dependente. Quando os recursos não são dependentes entre si, o Resource Manager implementa-os em paralelo. Para obter mais informações, consulte [Definir a ordem para a implantação de recursos em modelos ARM](./define-resource-dependency.md).

![Diagrama de ordem de implementação de recursos dependentes do gestor de recursos](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início Rápido
> * Explorar o modelo
> * Implementar o modelo

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Para um módulo Microsoft Learn que cubra as dependências de recursos, consulte [Gerir implementações complexas em nuvem utilizando funcionalidades avançadas do modelo ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code com extensão Ferramentas do Resource Manager. Consulte [Quickstart: Crie modelos ARM com Código de Estúdio Visual](quickstart-create-templates-use-visual-studio-code.md).
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Eis um exemplo para gerar uma palavra-passe:

    ```console
    openssl rand -base64 32
    ```

    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para obter mais informações, consulte [Tutorial: Integre o cofre da chave azul na implementação do modelo ARM](./template-tutorial-use-key-vault.md). Também recomendamos que atualize a palavra-passe a cada três meses.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Azure Quickstart Templates é um repositório para modelos ARM. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo utilizado neste tutorial é denominado [Implementar uma VM do Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. A partir do Código do Estúdio Visual, selecione Ficheiro Aberto **de**  >  **Ficheiros**.
2. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Selecione **Abrir** para abrir o ficheiro.
4. Selecione   >  **'Guardar ficheiros' para** guardar uma cópia do ficheiro no computador local com o nome _azuredeploy.jsligado_.

## <a name="explore-the-template"></a>Explorar o modelo

Ao explorar o modelo nesta secção, tente responder a estas perguntas:

* Número de recursos do Azure definidos neste modelo?
* Um dos recursos é uma conta de armazenamento do Azure. A definição aparenta ser como a utilizada no último tutorial?
* Pode encontrar as referências de modelo para os recursos definidos neste modelo?
* Pode encontrar as dependências dos recursos?

1. A partir do Código do Estúdio Visual, desabar os elementos até ver apenas os elementos de primeiro nível e os elementos de segundo nível no `resources` interior:

    ![Modelos de ARM de código de estúdio visual](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Existem seis recursos definidos pelo modelo:

   * [**Microsoft.Storage/storageAcounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     É útil rever a referência do modelo antes de personalizar um modelo.

1. Expanda o primeiro recurso. É uma conta de armazenamento. Compare a definição de recurso com a [referência do modelo.](/azure/templates/Microsoft.Storage/storageAccounts)

    ![Definição de conta de armazenamento de modelos de código de estúdio visual](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

1. Expanda o segundo recurso. O tipo de recurso é `Microsoft.Network/publicIPAddresses`. Compare a definição de recurso com a [referência do modelo.](/azure/templates/microsoft.network/publicipaddresses)

    ![Modelos de código de estúdio visual ARM definição de endereço IP público](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)

1. Expandir o terceiro recurso. O tipo de recurso é `Microsoft.Network/networkSecurityGroups`. Compare a definição de recurso com a [referência do modelo.](/azure/templates/microsoft.network/networksecuritygroups)

    ![Visual Studio Code ARM modelos definição de grupo de segurança de rede](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-network-security-group-definition.png)

1. Expanda o quarto recurso. O tipo de recurso é `Microsoft.Network/virtualNetworks`:

    ![Modelos de braço de código de estúdio visual dependem deon](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-virtual-network-definition.png)

    O `dependsOn` elemento permite definir um recurso como dependente de um ou mais recursos. Este recurso depende de um outro recurso:

    * `Microsoft.Network/networkSecurityGroups`

1. Expanda o quinto recurso. O tipo de recurso é `Microsoft.Network/networkInterfaces`. O recurso depende de dois outros recursos:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

1. Expandir o sexto recurso. Este recurso é uma máquina virtual. Depende de dois outros recursos:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

O diagrama seguinte ilustra os recursos e as informações de dependência para este modelo:

![Diagrama de dependência de modelos de código de estúdio visual](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Ao especificar as dependências, o Resource Manager implementa a solução de forma eficiente. Implementa a conta de armazenamento, o endereço IP público e a rede virtual em paralelo porque não têm dependências. Depois de o endereço IP público e a rede virtual serem implementados, a interface de rede é criada. Quando todos os outros recursos estiverem implementados, o Resource Manager implementa a máquina virtual.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Inscreva-se na [Azure Cloud Shell](https://shell.azure.com)

1. Escolha o seu ambiente preferido selecionando **PowerShell** ou **Bash** (para CLI) no canto superior esquerdo.  É necessário reiniciar o Shell quando mudar.

    ![Arquivo de upload do portal Azure Cloud Shell](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selecione **Carregar/transferir ficheiros** e, em seguida, selecione **Carregar**. Veja a captura de ecrã anterior. Selecione o ficheiro que guardou anteriormente. Depois de carregar o ficheiro, pode utilizar o `ls` comando e o comando para verificar se o ficheiro foi carregado com `cat` sucesso.

1. Executar o seguinte script PowerShell para implementar o modelo.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    echo "Enter the virtual machine admin username:" &&
    read adminUsername &&
    echo "Enter the DNS label prefix:" &&
    read dnsLabelPrefix &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location $location &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters adminUsername=$adminUsername dnsLabelPrefix=$dnsLabelPrefix
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

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

    ---

1. Faça o RDP à máquina virtual para verificar se a máquina virtual foi criada com êxito.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos. Verá um total de seis recursos no grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, desenvolveu e implementou um modelo para criar uma máquina virtual, uma rede virtual e os recursos dependentes. Para aprender a usar scripts de implementação para executar operações de implantação pré/post, consulte:

> [!div class="nextstepaction"]
> [Utilize o script de implementação](./template-tutorial-deployment-script.md)
