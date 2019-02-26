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
ms.date: 02/25/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: d4dae65f3e88bbc210fea4bf9dbed391c1e9ba69
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822194"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Tutorial: Integrar o Azure Key Vault na implementação de modelo do Resource Manager

Saiba como obter segredos a partir do Azure Key Vault e transmitir os segredos como parâmetros durante a implementação do Resource Manager. O valor nunca está exposto porque só fazem referência a sua ID de Cofre de chaves. Para obter mais informações, veja [Utilizar o Azure Key Vault para transmitir valores de parâmetros seguros durante a implementação](./resource-manager-keyvault-parameter.md)

No tutorial [Definir a ordem de implementação de recursos](./resource-manager-tutorial-create-templates-with-dependent-resources.md), cria uma máquina virtual, uma rede virtual e alguns outros recursos dependentes. Neste tutorial, personalizar o modelo para recuperar a palavra-passe de administrador de máquina virtual de um cofre de chaves.

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

Nesta secção, vai utilizar um modelo do Resource Manager para criar um cofre de chaves e um segredo. Este modelo:

* Criar um cofre de chaves com o `enabledForTemplateDeployment` ativa de propriedade. Esta propriedade tem de ser verdadeira antes do processo de implementação do modelo pode acessar os segredos definidos neste Cofre de chaves.
* Adicione um segredo ao Cofre de chaves.  O segredo armazena a palavra-passe de administrador da máquina virtual.

Se (como o utilizador para implementar o modelo de máquina virtual) não for o proprietário ou contribuinte do Cofre de chaves, o proprietário ou contribuinte do Cofre de chaves deve conceder-lhe o acesso à permissão Microsoft.KeyVault/vaults/deploy/action para o Cofre de chaves. Para obter mais informações, veja [Utilizar o Azure Key Vault para transmitir valores de parâmetros seguros durante a implementação](./resource-manager-keyvault-parameter.md)

O modelo precisa do ID de objeto de utilizador do Azure AD para configurar permissões. O procedimento seguinte obtém o objeto de ID (GUID).

1. Execute o comando seguinte do Azure PowerShell ou da CLI do Azure.  

    ```azurecli-interactive
    echo "Enter your email address that is associated with your Azure subscription):" &&
    read upn &&
    az ad user show --upn-or-object-id $upn --query "objectId" &&
    ```
    ```azurepowershell-interactive
    $upn = Read-Host -Prompt "Input your user principal name (email address) used to sign in to Azure"
    (Get-AzADUser -UserPrincipalName $upn).Id
    ```
2. Anote o ID de objeto. Precisar dele mais tarde no tutorial.

Para criar um cofre de chaves:

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria um cofre de chaves e um segredo.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Farmtutorials.blob.core.windows.net%2Fcreatekeyvault%2FCreateKeyVault.json"><img src="./media/resource-manager-tutorial-use-key-vault/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Selecione ou introduza os seguintes valores.  Não selecione **Comprar** depois de introduzir os valores.

    ![Portal de implementação de integração de Cofres de Chaves de modelos do Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-key-vault-portal.png)

    * **Subscrição**: selecione uma subscrição do Azure.
    * **Grupo de recursos**: atribua um nome exclusivo. Anote este nome, uma vez que vai utilizar o mesmo grupo de recursos para implementar a máquina virtual na próxima sessão. Colocar o Cofre de chaves e a máquina virtual no mesmo grupo de recursos facilita limpar o recurso no final do tutorial.
    * **Localização**: selecione uma localização.  A localização predefinida é **E.U.A. Central**.
    * **Nome do Cofre de Chaves**: atribua um nome exclusivo. 
    * **ID do inquilino**: a função de modelo obtém automaticamente o ID do inquilino.  Não altere o valor predefinido
    * **ID de utilizador do AD**: introduza o ID de objeto de utilizador do Azure AD obtido no último procedimento.
    * **Nome do segredo**: O nome predefinido é **vmAdminPassword**. Se alterar o nome do segredo aqui, tem de atualizá-lo quando implementar a máquina virtual.
    * **Valor secreto**: Introduza o seu segredo.  O segredo é a palavra-passe utilizada para iniciar sessão na máquina virtual. É recomendado utilizar a palavra-passe gerada que criou no último procedimento.
    * **Eu concordo com o estado de termos e condições acima**: Selecione.
3. Selecione **Editar parâmetros** na parte superior para ver o modelo.
4. Navegue para a linha 28 do ficheiro JSON do modelo. Esta é a definição do recurso de Cofre de chaves.
5. Navegue para a linha 35:

    ```json
    "enabledForTemplateDeployment": true,
    ```
    `enabledForTemplateDeployment` é uma propriedade do Cofre de Chaves. Esta propriedade tem de ser verdadeira antes de pode obter os segredos a partir deste cofre de chaves durante a implementação.
6. Navegue para a linha 89. Esta é a definição do segredo do Cofre de Chaves.
7. Selecione **Eliminar** na parte inferior da página. Não efetue alterações.
8. Verifique se forneceu todos os valores, conforme mostrado na captura de ecrã anterior, e clique em **Comprar** na parte inferior da página.
9. Selecione o ícone de campainha (notificação) na parte superior da página para abrir o painel **Notificações**. Aguarde até o recurso ser implementado com êxito.
10. Selecione **Ir para o grupo de recursos** no painel **Notificações**. 
11. Selecione o nome do Cofre de chaves para abri-lo.
12. Selecione **segredos** no painel à esquerda. **vmAdminPassword** listada aqui.
13. Selecione **Políticas de acesso** no painel esquerdo. O seu nome (Active Directory) deve ser apresentado, caso contrário, não tem permissão para aceder ao cofre de chaves.
14. Selecione **Clicar para mostrar as políticas de acesso avançadas**. Verifique se a opção **Ativar o acesso ao Azure Resource Manager para implementação de modelos** está selecionada. Esta definição é outra condição tornar a integração do Cofre de chaves para trabalhar.

    ![Políticas de acesso de integração de Cofres de Chaves de modelos do Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-key-vault-access-policies.png)
15. Selecione **Propriedades** no painel esquerdo.
16. Faça uma cópia do **ID do Recurso**. Vai precisar deste ID quando implementar a máquina virtual.  O formato do ID do Recurso é:

    ```json
    /subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
    ```

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

    Substitua a **id** com o ID de recurso do seu Cofre de chaves criado no último procedimento.  

    ![Integrar o cofre de chaves e o ficheiro de parâmetros de implementação de máquinas virtuais de modelos do Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Atribua valores para:

    * **adminUsername**: nome da conta de administrador da máquina virtual.
    * **dnsLabelPrefix**: dê um nome a dnsLabelPrefix.
4. Guarde as alterações.

## <a name="deploy-the-template"></a>Implementar o modelo

Siga as instruções em [Implementar o modelo](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) para implementar o modelo. Tem de carregar **azuredeploy.json** e **azuredeploy.parameters.json** para o Cloud Shell e, em seguida, utilize o seguinte script do PowerShell para implementar o modelo:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Quando implementar o modelo, utilize o mesmo grupo de recursos como o Cofre de chaves. Facilita a limpeza dos recursos. Apenas tem de eliminar um grupo de recursos em vez de dois.

## <a name="valid-the-deployment"></a>Validar a implementação

Depois de ter implementado com êxito a máquina virtual, teste o início de sessão com a palavra-passe armazenada no Cofre de chaves.

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Selecione **Grupos de recursos**/**NomedoGrupodeRecursos>**/**simpleWinVM**
3. Selecione **ligar** na parte superior.
4. Selecione **baixar arquivo RDP** e, em seguida, siga as instruções para iniciar sessão na máquina virtual utilizando a palavra-passe armazenada no Cofre de chaves.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.  Verá um total de seis recursos no grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, obteve um segredo do Azure Key Vault e utilizou-o na sua implementação do modelo.  Para saber como criar modelos ligados, veja:

> [!div class="nextstepaction"]
> [Criar modelos ligados](./resource-manager-tutorial-create-linked-templates.md)
