---
title: Criar modelo - Código de Estúdio Visual
description: Utilize o Código do Estúdio Visual e a extensão de ferramentas do Gestor de Recursos Azure para trabalhar nos modelos do Gestor de Recursos Azure (modelos ARM).
author: neilpeterson
ms.date: 08/09/2020
ms.topic: quickstart
ms.author: nepeters
ms.openlocfilehash: 3e41d4f6f8b8aeb2be376ae1385189fe02e9b897
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504662"
---
# <a name="quickstart-create-arm-templates-with-visual-studio-code"></a>Quickstart: Criar modelos ARM com Código de Estúdio Visual

As Ferramentas de Gestor de Recursos Azure para Código do Estúdio Visual fornecem suporte linguístico, snippets de recursos e auto-preconização de recursos. Estas ferramentas ajudam a criar e validar modelos de Gestor de Recursos Azure (modelos ARM). Neste arranque rápido, utiliza-se a extensão para criar um modelo ARM de raiz. Ao fazê-lo, experimenta as capacidades de extensões tais como snippets de modelo ARM, validação, conclusão e suporte de ficheiros de parâmetros.

Para completar este arranque rápido, precisa do [Código do Estúdio Visual,](https://code.visualstudio.com/)com a extensão de [ferramentas Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) instalada. Também precisa do [Azure CLI](/cli/azure/) ou do [módulo Azure PowerShell](/powershell/azure/new-azureps-module-az) instalado e autenticado.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-arm-template"></a>Criar um modelo ARM

Crie e abra com o Visual Studio Code um novo ficheiro nomeado *azuredeploy.jsem*. Introduza `arm` o editor de código, que inicia os cortes do Azure Resource Manager para andaimes com um modelo ARM.

Selecione `arm!` para criar um modelo traçado para uma implementação de grupo de recursos Azure.

![Imagem mostrando azure Resource Manager andaimes](./media/quickstart-create-templates-use-visual-studio-code/1.png)

Este corte cria os blocos básicos de construção para um modelo ARM.

![Imagem mostrando um modelo DE ARM totalmente andaime](./media/quickstart-create-templates-use-visual-studio-code/2.png)

Note que o modo de linguagem Visual Studio Code mudou de *JSON* para *Azure Resource Manager Template*. A extensão inclui um servidor de idioma específico para modelos ARM que fornece validação, conclusão e outros serviços linguísticos específicos da ARM.

![Imagem mostrando Azure Resource Manager como o modo de linguagem Visual Studio Code](./media/quickstart-create-templates-use-visual-studio-code/3.png)

## <a name="add-an-azure-resource"></a>Adicione um recurso Azure

A extensão inclui snippets para muitos recursos Azure. Estes snippets podem ser usados para adicionar facilmente recursos à sua implementação do modelo.

Coloque o cursor no bloco de **recursos** do gabarito, escreva `storage` e selecione o corte *de armazenamento de braço.*

![Imagem mostrando um recurso sendo adicionado ao modelo ARM](./media/quickstart-create-templates-use-visual-studio-code/4.png)

Esta ação adiciona um recurso de armazenamento ao modelo.

![Imagem mostrando um recurso de armazenamento Azure em um modelo ARM](./media/quickstart-create-templates-use-visual-studio-code/5.png)

A **tecla do separador** pode ser usada para abasar através de propriedades configuráveis na conta de armazenamento.

![Imagem mostrando como a tecla de separador pode ser usada para navegar através da configuração de recursos](./media/quickstart-create-templates-use-visual-studio-code/6.png)

## <a name="completion-and-validation"></a>Conclusão e validação

Uma das capacidades mais poderosas da extensão é a sua integração com os esquemas Azure. Os esquemas Azure fornecem a extensão com capacidades de validação e de conclusão conscientes do recurso. Vamos modificar a conta de armazenamento para ver validação e conclusão em ação.

Em primeiro lugar, atualize o tipo de conta de armazenamento para um valor inválido, como `megaStorage` . Note que esta ação produz um aviso indicando que `megaStorage` não é um valor válido.

![Imagem mostrando uma configuração de armazenamento inválida](./media/quickstart-create-templates-use-visual-studio-code/7.png)

Para utilizar as capacidades de conclusão, `megaStorage` remova, coloque o cursor dentro das cotações duplas e prima `ctrl`  +  `space` . Esta ação apresenta uma lista de conclusão de valores válidos.

![Imagem mostrando a extensão auto-conclusão](./media/quickstart-create-templates-use-visual-studio-code/8.png)

## <a name="add-template-parameters"></a>Adicionar parâmetros de modelo

Agora crie e use um parâmetro para especificar o nome da conta de armazenamento.

Coloque o cursor no bloco de parâmetros, adicione uma volta de transporte, `"` escreva e, em seguida, selecione o `new-parameter` corte. Esta ação adiciona um parâmetro genérico ao modelo.

![Imagem mostrando um parâmetro sendo adicionado ao modelo ARM](./media/quickstart-create-templates-use-visual-studio-code/9.png)

Atualize o nome do parâmetro `storageAccountName` para e a descrição para `Storage Account Name` .

![Imagem mostrando o parâmetro concluído em um modelo ARM](./media/quickstart-create-templates-use-visual-studio-code/10.png)

Os nomes da conta de armazenamento Azure têm um comprimento mínimo de 3 caracteres e um máximo de 24. Adicione ambos `minLength` e ao parâmetro e forneça os `maxLength` valores apropriados.

![Imagem mostrando minLength e maxLength sendo adicionados a um parâmetro do modelo ARM](./media/quickstart-create-templates-use-visual-studio-code/11.png)

Agora, no recurso de armazenamento, atualize a propriedade do nome para usar o parâmetro. Para tal, retire o nome atual. Introduza uma cotação dupla e um suporte quadrado de `[` abertura, que produz uma lista de funções do modelo ARM. Selecione *parâmetros* da lista.

![Imagem mostrando a conclusão automática ao usar parâmetros nos recursos do modelo ARM](./media/quickstart-create-templates-use-visual-studio-code/12.png)

A introdução de uma única cotação `'` no interior dos suportes redondos produz uma lista de todos os parâmetros definidos no modelo, neste caso, *armazenamentoAme de contagem de armazenamento*. Selecione o parâmetro.

![Imagem mostrando parâmetro completo em um recurso de modelo ARM](./media/quickstart-create-templates-use-visual-studio-code/13.png)

## <a name="create-a-parameter-file"></a>Criar um ficheiro de parâmetros

Um ficheiro de parâmetro do modelo ARM permite armazenar valores de parâmetros específicos do ambiente e passar estes valores em grupo no tempo de implementação. Por exemplo, pode ter um ficheiro de parâmetros com valores específicos de um ambiente de teste e outro para um ambiente de produção.

A extensão facilita a criação de um ficheiro de parâmetros a partir dos seus modelos existentes. Para tal, clique com o botão direito no modelo no editor de código e selecione `Select/Create Parameter File` .

![Imagem mostrando o processo de clique direito para criar um ficheiro de parâmetro a partir de um modelo ARM](./media/quickstart-create-templates-use-visual-studio-code/14.png)

Selecione `New`  >  `All Parameters` > Selecione um nome e localização para o ficheiro de parâmetros.

![Imagem mostrando o nome e guardar o diálogo de ficheiro ao criar um ficheiro de parâmetros a partir de um modelo ARM](./media/quickstart-create-templates-use-visual-studio-code/15.png)

Esta ação cria um novo arquivo de parâmetros e mapeia-o com o modelo a partir do qual foi criado. Pode ver e modificar o mapeamento de ficheiros do modelo/parâmetro atual na barra de estado do Código do Estúdio Visual enquanto o modelo é selecionado.

![Imagem mostrando o mapeamento de ficheiro de modelo/parâmetro na barra de estado do Código do Estúdio Visual.](./media/quickstart-create-templates-use-visual-studio-code/16.png)

Agora que o ficheiro de parâmetros foi mapeado para o modelo, a extensão valida tanto o modelo como o ficheiro de parâmetros juntos. Para ver esta validação na prática, adicione um valor de dois caracteres ao `storageAccountName` parâmetro no ficheiro do parâmetro e guarde o ficheiro.

![Imagem mostrando um modelo invalidado devido à questão do ficheiro do parâmetro](./media/quickstart-create-templates-use-visual-studio-code/17.png)

Volte ao modelo ARM e note que foi levantado um erro indicando que o valor não satisfaz os critérios de parâmetro.

![Imagem mostrando um modelo de ARM válido](./media/quickstart-create-templates-use-visual-studio-code/18.png)

Atualize o valor para algo apropriado, guarde o ficheiro e volte ao modelo. Note que o erro no parâmetro foi resolvido.

## <a name="deploy-the-template"></a>Implementar o modelo

Abra o terminal integrado do Código do Estúdio Visual utilizando a `ctrl`  +  ```` ` ```` combinação de chaves e utilize o módulo Azure CLI ou Azure PowerShell para implantar o modelo.

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
