---
title: Criar ficheiros Bicep - Código de Estúdio Visual
description: Utilize o Código do Estúdio Visual e a extensão Bicep aos ficheiros Bicep para implantar recursos Azure
author: mumian
ms.date: 03/02/2021
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: b959d471d290a58611787cf1916343c26abe8e69
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703583"
---
# <a name="quickstart-create-bicep-files-with-visual-studio-code"></a>Quickstart: Criar ficheiros Bicep com Código de Estúdio Visual

A extensão Bicep para Visual Studio Code fornece suporte linguístico e auto-preconção de recursos. Estas ferramentas ajudam a criar e validar ficheiros [Bicep.](./bicep-overview.md) Neste arranque rápido, você usa a extensão para criar um ficheiro Bicep do zero. Ao fazê-lo, experimenta as capacidades de extensões, tais como validação e conclusões.

Para completar este arranque rápido, precisa do [Código do Estúdio Visual,](https://code.visualstudio.com/)com a [extensão Bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) instalada. Também precisa do mais recente [Azure CLI](/cli/azure/?view=azure-cli-latest&preserve-view=true) ou do mais recente [módulo Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-3.7.0&preserve-view=true) instalado e autenticado.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-bicep-file"></a>Criar um ficheiro Bicep

Crie e abra com o Visual Studio Code um novo ficheiro chamado *azuredeploy.bicep*.

## <a name="add-an-azure-resource"></a>Adicione um recurso Azure

Adicione um recurso básico de conta de armazenamento ao ficheiro bicep.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'storageaccount1' // must be globally unique
  location: 'eastus'
  tags:{
    diplayName: 'storageaccount1'
  }
  sku: {
      name: 'Standard_LRS'
      tier: 'Standard'
  }
  kind: 'Storage'
}
```

A declaração de recursos tem quatro componentes:

- **recurso**: Palavra-chave.
- **nome simbólico** (stg): O nome simbólico é um identificador para fazer referência ao recurso em todo o seu ficheiro bicep. Não é o nome do recurso quando for implantado. O nome do recurso é definido pela propriedade **do nome.**  Veja o quarto componente desta lista.
- **tipo de recurso** Microsoft.Storage/storageAccounts@2019-06-01 (): É composto pelo fornecedor de recursos (Microsoft.Storage), tipo de recurso (armazenamentoCocounts) e apiVersion (2019-06-01). Cada fornecedor de recursos publica as suas próprias versões API, pelo que este valor é específico do tipo. Pode encontrar mais tipos e apiversões para vários recursos Azure a partir da [referência ao modelo ARM](/azure/templates/).
- **propriedades** (tudo dentro = {...}): Especificar as propriedades para o tipo de recurso. Cada recurso tem uma `name` propriedade. A maioria dos recursos também tem um `location` imóvel, que define a região onde o recurso é implantado. As outras propriedades variam de acordo com o tipo de recurso e a versão API.

## <a name="completion-and-validation"></a>Conclusão e validação

Uma das capacidades mais poderosas da extensão é a sua integração com os esquemas Azure. Os esquemas Azure fornecem a extensão com capacidades de validação e de conclusão conscientes do recurso. Vamos modificar a conta de armazenamento para ver validação e conclusão em ação.

Em primeiro lugar, atualize o tipo de conta de armazenamento para um valor inválido, como `megaStorage` . Note que esta ação produz um aviso indicando que `megaStorage` não é um valor válido.

![Imagem mostrando uma configuração de armazenamento inválida](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-validation.png)

Para utilizar as capacidades de conclusão, `megaStorage` remova, coloque o cursor dentro das cotações individuais e prima `ctrl`  +  `space` . Esta ação apresenta uma lista de conclusão de valores válidos.

![Imagem mostrando a extensão auto-conclusão](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-auto-completion.png)

## <a name="add-parameters"></a>Adicionar parâmetros

Agora crie e use um parâmetro para especificar o nome da conta de armazenamento.

Adicione o seguinte código ao início do ficheiro:

```bicep
@minLength(3)
@maxLength(24)
@description('Specify a storage account name.')
param storageAccountName string
```

Os nomes da conta de armazenamento Azure têm um comprimento mínimo de 3 caracteres e um máximo de 24. Utilizar `minLength` e `maxLength` fornecer valores adequados.

Agora, no recurso de armazenamento, atualize a propriedade do nome para usar o parâmetro. Para tal, remova o nome atual do recurso de armazenamento, incluindo as cotações únicas. pressionar `ctrl`  +  `space` . Selecione o **parâmetro de armazenamentoA contagem** de nome da lista. Note que os parâmetros podem ser referenciados diretamente usando os seus nomes em Bicep. Os modelos JSON requerem uma função de parâmetro.).

![Imagem mostrando a conclusão automática ao utilizar parâmetros em recursos Bicep](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-valid-param.png)

## <a name="deploy-the-bicep-file"></a>Implementar o ficheiro Bicep

Abra o terminal integrado do Código do Estúdio Visual utilizando a `ctrl`  +  ```` ` ```` combinação de chaves, altere o diretório atual para onde o ficheiro Bicep está localizado e, em seguida, utilize o módulo Azure CLI ou Azure PowerShell para implantar o ficheiro Bicep.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.bicep --parameters storageAccountName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.bicep -storageAccountName "{your-unique-name}"
```

---

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos Azure já não forem necessários, utilize o módulo Azure CLI ou Azure PowerShell para eliminar o grupo de recursos quickstart.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name arm-vscode
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name arm-vscode
```

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutoriais de Bicep iniciantes](./bicep-tutorial-create-first-bicep.md)
