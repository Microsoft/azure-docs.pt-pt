---
title: Criar modelo - Código de Estúdio Visual
description: Utilize o Visual Studio Code e a extensão Azure Resource Manager Tools para trabalhar em modelos do Resource Manager.
author: neilpeterson
ms.date: 04/17/2020
ms.topic: quickstart
ms.author: nepeters
ms.openlocfilehash: cd107db5220a96d75092a94736e060ae46672926
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686619"
---
# <a name="quickstart-create-azure-resource-manager-templates-with-visual-studio-code"></a>Quickstart: Crie modelos de Gestor de Recursos Azure com Código de Estúdio Visual

As ferramentas do Gestor de Recursos Azure para o Código do Estúdio Visual fornecem suporte linguístico, fragmentos de recursos e auto-conclusão de recursos. Estas ferramentas ajudam a criar e validar os modelos do Gestor de Recursos Azure. Neste arranque rápido, você usa a extensão para criar um modelo de Gestor de Recursos Azure do zero. Ao fazê-lo, experimente as capacidades de extensões tais como snippets de modelo ARM, validação, conclusão e suporte de ficheiros de parâmetros.

Para completar este arranque rápido, precisa do [Código do Estúdio Visual,](https://code.visualstudio.com/)com a extensão das [ferramentas Do Gestor de Recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) instalada. Também precisa do [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) ou do [módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.7.0) instalado e autenticado.

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-arm-template"></a>Criar um modelo ARM

Crie e abra com o Visual Studio Code um novo ficheiro chamado *azuredeploy.json*. Introduza `arm` no editor de código, que inicia os cortes do Gestor de Recursos Azure para andaimes de um modelo ARM.

Selecione `arm!` para criar um modelo de mira para uma implantação do grupo de recursos Azure.

![Imagem mostrando andaimes do Gestor de Recursos Azure](./media/quickstart-create-templates-use-visual-studio-code/1.png)

Este corte cria os blocos básicos de construção para um modelo ARM.

![Imagem mostrando um modelo ARM totalmente andaime](./media/quickstart-create-templates-use-visual-studio-code/2.png)

Note que o modo de linguagem Visual Studio Code mudou de *JSON* para Modelo de Gestor de *Recursos Azure*. A extensão inclui um servidor de idiomas específico dos modelos ARM que fornece validação, conclusão e outros serviços linguísticos específicos do modelo ARM.

![Imagem mostrando Azure Resource Manager como o modo de linguagem código de estúdio visual](./media/quickstart-create-templates-use-visual-studio-code/3.png)

## <a name="add-an-azure-resource"></a>Adicione um recurso Azure

A extensão inclui cortes para muitos recursos Azure. Estes cortes podem ser usados para adicionar facilmente recursos à implementação do seu modelo.

Coloque o cursor **resources** no bloco de `storage`recursos do modelo, escreva e selecione o corte *de armazenamento de braços.*

![Imagem mostrando um recurso sendo adicionado ao modelo ARM](./media/quickstart-create-templates-use-visual-studio-code/4.png)

Esta ação adiciona um recurso de armazenamento ao modelo.

![Imagem mostrando um recurso de armazenamento azure em um modelo ARM](./media/quickstart-create-templates-use-visual-studio-code/5.png)

A tecla **de separador** pode ser utilizada para abasquete através de propriedades configuráveis na conta de armazenamento.

![Imagem mostrando como a tecla de separador pode ser usada para navegar através da configuração do recurso](./media/quickstart-create-templates-use-visual-studio-code/6.png)

## <a name="completion-and-validation"></a>Conclusão e validação

Uma das capacidades mais poderosas da extensão é a sua integração com os schemas azure. Os schemas azure fornecem à extensão capacidades de validação e de conclusão conscientes dos recursos. Vamos modificar a conta de armazenamento para ver validação e conclusão em ação. 

Em primeiro lugar, atualize o tipo `megaStorage`de conta de armazenamento para um valor inválido, como . Note que esta ação produz `megaStorage` um aviso indicando que não é um valor válido.

![Imagem mostrando uma configuração de armazenamento inválida](./media/quickstart-create-templates-use-visual-studio-code/7.png)

Para utilizar as capacidades `megaStorage`de conclusão, remova , coloque o `ctrl`  +  `space`cursor dentro das cotações duplas e prima . Esta ação apresenta uma lista de valores válidos.

![Imagem mostrando a conclusão automática da extensão](./media/quickstart-create-templates-use-visual-studio-code/8.png)

## <a name="add-template-parameters"></a>Adicionar parâmetros de modelo

Agora crie e use um parâmetro para especificar o nome da conta de armazenamento.

Coloque o cursor no bloco de parâmetros, `par`adicione uma `arm-param-value` devolução de transporte, escreva, e, em seguida, selecione o corte. Esta ação adiciona um parâmetro genérico ao modelo.

![Imagem mostrando um parâmetro sendo adicionado ao modelo ARM](./media/quickstart-create-templates-use-visual-studio-code/9.png)

Atualize o nome do `storageAccountName` parâmetro e `Storage Account Name`a descrição para .

![Imagem mostrando o parâmetro completo em um modelo ARM](./media/quickstart-create-templates-use-visual-studio-code/10.png)

Os nomes da conta de armazenamento azure têm um comprimento mínimo de 3 caracteres e um máximo de 24. Adicione `minLength` tanto `maxLength` e ao parâmetro e forneça valores adequados.

![Imagem mostrando minLength e maxLength sendo adicionado a um parâmetro de modelo ARM](./media/quickstart-create-templates-use-visual-studio-code/11.png)

Agora, no recurso de armazenamento, atualize a propriedade do nome para usar o parâmetro. Para isso, retire o nome atual. Introduza uma cotação dupla `[`e um suporte quadrado de abertura, que produz uma lista de funções de modelo ARM. Selecione *parâmetros* da lista. 

![Imagem mostrando a auto-conclusão ao usar parâmetros nos recursos do modelo ARM](./media/quickstart-create-templates-use-visual-studio-code/12.png)

A introdução de `'` uma única cotação dentro dos suportes redondos produz uma lista de todos os parâmetros definidos no modelo, neste caso, *storageAccountName*. Selecione o parâmetro.

![Imagem mostrando parâmetro completo em um recurso de modelo ARM](./media/quickstart-create-templates-use-visual-studio-code/13.png)

## <a name="create-a-parameter-file"></a>Criar um ficheiro de parâmetro

Um ficheiro de parâmetro de modelo ARM permite-lhe armazenar valores de parâmetros específicos do ambiente e passar estes valores como um grupo no momento da implantação. Por exemplo, pode ter um ficheiro de parâmetrocom valores específicos de um ambiente de teste e outro para um ambiente de produção.

A extensão facilita a criação de um ficheiro de parâmetros a partir dos seus modelos existentes. Para isso, clique no modelo no editor de `Select/Create Parameter File`código e selecione .

![Imagem mostrando o processo de clique direito para criar um ficheiro de parâmetro a partir de um modelo ARM](./media/quickstart-create-templates-use-visual-studio-code/14.png)

Selecione `New`  >  `All Parameters` > Selecione um nome e localização para o ficheiro parâmetro.

![Imagem mostrando o nome e guardar diálogo de ficheiro ao criar um ficheiro de parâmetros a partir de um modelo ARM](./media/quickstart-create-templates-use-visual-studio-code/15.png)

Esta ação cria um novo ficheiro de parâmetros e mapeia-o com o modelo a partir do qual foi criado. Pode ver e modificar o modelo/mapeamento de ficheiros de modelo/parâmetro atual na barra de estado do Código do Estúdio Visual enquanto o modelo é selecionado.

![](./media/quickstart-create-templates-use-visual-studio-code/16.png)

Agora que o ficheiro do parâmetro foi mapeado para o modelo, a extensão valida o modelo e o ficheiro parâmetro em conjunto. Para ver esta validação na prática, adicione `storageAccountName` um valor de dois caracteres ao parâmetro no ficheiro parâmetro e guarde o ficheiro.

![Imagem mostrando um modelo invalidado devido a problema de ficheiro de parâmetro](./media/quickstart-create-templates-use-visual-studio-code/17.png)

Navegue de volta ao modelo ARM e note que foi levantado um erro indicando que o valor não satisfaz os critérios do parâmetro.

![Imagem mostrando um modelo arm válido](./media/quickstart-create-templates-use-visual-studio-code/18.png)

Atualize o valor para algo apropriado, guarde o ficheiro e navegue de volta para o modelo. Note que o erro no parâmetro foi resolvido.

## <a name="deploy-the-template"></a>Implementar o modelo

Abra o terminal integrado de `ctrl`  +  ```` ` ```` Código do Estúdio Visual utilizando a combinação de chaves e utilize o módulo Azure CLI ou Azure PowerShell para implementar o modelo.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.json --parameters azuredeploy.parameters.json
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.json -TemplateParameterFile ./azuredeploy.parameters.json
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
> [Tutoriais para principiantes](./template-tutorial-create-first-template.md)
