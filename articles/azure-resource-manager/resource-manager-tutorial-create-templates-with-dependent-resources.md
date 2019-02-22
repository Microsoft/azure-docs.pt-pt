---
title: Criar modelos do Azure Resource Manager com recursos dependentes | Microsoft Docs
description: Saiba como criar um modelo do Azure Resource Manager com vários recursos e como implementá-lo com o portal do Azure
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: efe5c3f92ae743cb975b5ddbbebdf7994bea62fa
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594156"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Tutorial: Criar modelos Azure Resource Manager com recursos dependentes

Saiba como criar um modelo do Azure Resource Manager para implementar vários recursos.  Depois de criar o modelo, implemente o modelo com o Cloud Shell do portal do Azure.

Neste tutorial, vai criar uma conta de armazenamento, uma máquina virtual, uma rede virtual e mais alguns recursos dependentes. Alguns dos recursos não podem ser implementados enquanto existir outro recurso. Por exemplo, não é possível criar a máquina virtual enquanto as respetivas conta de armazenamento e interface de rede existirem. Defina esta relação fazendo com que um recurso seja dependente dos outros recursos. O Resource Manager avalia as dependências entre os recursos e implementa-os por ordem dependente. Quando os recursos não são dependentes entre si, o Resource Manager implementa-os em paralelo. Para obter mais informações, veja [Definir a ordem para implementar recursos nos Modelos do Azure Resource Manager](./resource-group-define-dependencies.md).

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início rápido
> * Explorar o modelo
> * Implementar o modelo

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* [Visual Studio Code](https://code.visualstudio.com/) com a extensão Ferramentas do Resource Manager.  Veja [Instalar a extensão](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Eis um exemplo para gerar uma palavra-passe:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    O Azure Key Vault foi criado para salvaguardar chaves criptográficos e outros segredos. Para obter mais informações, consulte [Tutorial: Integrar o Azure Key Vault na implementação de modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md). Também recomendamos que atualize a palavra-passe a cada três meses.

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Os Modelos de Início Rápido do Azure são um repositório de modelos do Resource Manager. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo utilizado neste tutorial é denominado [Implementar uma VM do Windows simples](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. No Visual Studio Code, selecione **Ficheiro**>**Abrir Ficheiro**.
2. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Selecione **Abrir** para abrir o ficheiro.
4. Selecione **Ficheiro**>**Guardar Como** para guardar uma cópia do ficheiro no computador local, com o nome **azuredeploy.json**.

## <a name="explore-the-template"></a>Explorar o modelo

Ao explorar o modelo nesta secção, tente responder a estas perguntas:

* Número de recursos do Azure definidos neste modelo?
* Um dos recursos é uma conta de armazenamento do Azure.  A definição aparenta ser como a utilizada no último tutorial?
* Pode encontrar as referências de modelo para os recursos definidos neste modelo?
* Pode encontrar as dependências dos recursos?

1. No Visual Studio Code, feche os elementos até ver apenas os elementos de primeiro nível e os elementos de segundo nível dentro de **recursos**:

    ![Modelos do Azure Resource Manager no Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Existem cinco recursos definidos pelo modelo:

    * `Microsoft.Storage/storageAccounts`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
    * `Microsoft.Network/publicIPAddresses`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
    * `Microsoft.Network/virtualNetworks`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
    * `Microsoft.Network/networkInterfaces`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
    * `Microsoft.Compute/virtualMachines`. Veja a [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    É útil ter alguma compreensão básica do modelo antes de personalizá-lo.

2. Expanda o primeiro recurso. É uma conta de armazenamento. Compare a definição do recurso à [referência do modelo](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).

    ![Definição da conta de armazenamento de modelos do Azure Resource Manager no Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Expanda o segundo recurso. O tipo de recurso é `Microsoft.Network/publicIPAddresses`. Compare a definição do recurso à [referência do modelo](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).

    ![Definição do endereço IP público de modelos do Azure Resource Manager no Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Expanda o quarto recurso. O tipo de recurso é `Microsoft.Network/networkInterfaces`:  

    ![dependson de modelos do Azure Resource Manager no Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    O elemento dependsOn permite-lhe definir um recurso como sendo dependente de um ou mais recursos. O recurso depende de dois outros recursos:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

5. Expanda o quinto recurso. Este recurso é uma máquina virtual. Depende de dois outros recursos:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

O diagrama seguinte ilustra os recursos e as informações de dependência para este modelo:

![Diagrama de dependência de modelos do Azure Resource Manager no Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Ao especificar as dependências, o Resource Manager implementa a solução de forma eficiente. Implementa a conta de armazenamento, o endereço IP público e a rede virtual em paralelo porque não têm dependências. Depois de o endereço IP público e a rede virtual serem implementados, a interface de rede é criada. Quando todos os outros recursos estiverem implementados, o Resource Manager implementa a máquina virtual.

## <a name="deploy-the-template"></a>Implementar o modelo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Existem muitos métodos para implementar modelos.  Neste tutorial, vai utilizar o Cloud Shell do portal do Azure.

1. Inicie sessão no [Cloud Shell](https://shell.azure.com). 
2. Selecione **PowerShell** no canto superior esquerdo do Cloud Shell e, em seguida, selecione **Confirmar**.  Vai utilizar o PowerShell neste tutorial.
3. Selecione **Carregar ficheiro** do Cloud Shell:

    ![Carregar ficheiro Cloud Shell do Portal do Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. Selecione o modelo que guardou anteriormente no tutorial. O nome predefinido é **azuredeploy.json**.  Se tiver um ficheiro com o mesmo nome de ficheiro, o ficheiro antigo é substituído sem qualquer notificação.
5. No Cloud Shell, execute o seguinte comando para certificar-se de que o ficheiro foi carregado com êxito. 

    ```bash
    ls
    ```

    ![Ficheiro de lista de Cloud Shell do portal do Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    O nome do ficheiro mostrado na captura de ecrã é azuredeploy.json.

6. No Cloud Shell, execute o seguinte comando para verificar o conteúdo do ficheiro JSON:

    ```bash
    cat azuredeploy.json
    ```
7. No Cloud Shell, execute os seguintes comandos do PowerShell. Para aumentar a segurança, utilize uma palavra-passe gerada para a conta de administrador da máquina virtual. Veja [Pré-requisitos](#prerequisites).

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile azuredeploy.json
    ```

    > [!NOTE]
    > Há um problema de e/s com o Azure PowerShell no Cloud shell do arquivo.  A mensagem de erro é *não é possível obter os parâmetros dinâmicos para o cmdlet. Não é possível localizar o caminho 'Azure:/azuredeploy.json' porque não existe.*  Uma solução temporária é não incluem o **- TemplateFile** mudar no `New-AzResourceGroupDeploy` comando. O comando irá solicitar-lhe para introduzir o nome de ficheiro.

8. Execute o seguinte comando do PowerShell para listar a máquina virtual recentemente criada:

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    Get-AzVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    O nome da máquina virtual está hard-coded como **SimpleWinVM** dentro do modelo.

9. Faça o RDP à máquina virtual para verificar se a máquina virtual foi criada com êxito.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.  Verá um total de seis recursos no grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, desenvolveu e implementou um modelo para criar uma máquina virtual, uma rede virtual e os recursos dependentes. Para saber como implementar recursos do Azure com base em condições, veja:

> [!div class="nextstepaction"]
> [Condições de utilização](./resource-manager-tutorial-use-conditions.md)
