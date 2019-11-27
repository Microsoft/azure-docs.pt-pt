---
title: Implantação de modelo What-if (visualização)
description: Determine quais alterações ocorrerão para seus recursos antes de implantar um modelo de Azure Resource Manager.
author: mumian
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jgao
ms.openlocfilehash: 19cb674ca7a2dfefc11c7646b23427c722f6e671
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278304"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Operação What-If da implantação de modelo do Resource Manager (versão prévia)

Antes de implantar um modelo, talvez você queira Visualizar as alterações que ocorrerão. Azure Resource Manager fornece a operação What-If para permitir que você veja como os recursos serão alterados se você implantar o modelo. A operação What-If não faz nenhuma alteração nos recursos existentes. Em vez disso, ele prevê as alterações se o modelo especificado for implantado.

> [!NOTE]
> A operação What-If está atualmente em visualização. Para usá-lo, você deve [se inscrever para a versão prévia](https://aka.ms/armtemplatepreviews). Como uma versão de visualização, os resultados podem, às vezes, mostrar que um recurso será alterado quando, na verdade, nenhuma alteração ocorrerá. Estamos trabalhando para reduzir esses problemas, mas precisamos de sua ajuda. Informe esses problemas em [https://aka.ms/whatifissues](https://aka.ms/whatifissues).

Você pode usar a operação What-If com o `New-AzDeploymentWhatIf` comando do PowerShell ou as [implantações-What If](/rest/api/resources/deployments/whatif) operação REST.

No PowerShell, a saída é semelhante a:

![Implantação do modelo do Resource Manager e-se operação fullresourcepayload e tipos de alteração](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

## <a name="change-types"></a>Alterar tipos

A operação What-If lista seis tipos diferentes de alterações:

- **Criar**: o recurso não existe atualmente, mas está definido no modelo. O recurso será criado.

- **Excluir**: esse tipo de alteração se aplica somente ao usar o [modo completo](deployment-modes.md) para implantação. O recurso existe, mas não está definido no modelo. Com o modo completo, o recurso será excluído. Somente os recursos que [dão suporte à exclusão de modo completo](complete-mode-deletion.md) são incluídos nesse tipo de alteração.

- **Ignorar**: o recurso existe, mas não está definido no modelo. O recurso não será implantado ou modificado.

- **NoChange**: o recurso existe e é definido no modelo. O recurso será reimplantado, mas as propriedades do recurso não serão alteradas. Esse tipo de alteração é retornado quando [ResultFormat](#result-format) é definido como `FullResourcePayloads`, que é o valor padrão.

- **Modify**: o recurso existe e é definido no modelo. O recurso será reimplantado e as propriedades do recurso serão alteradas. Esse tipo de alteração é retornado quando [ResultFormat](#result-format) é definido como `FullResourcePayloads`, que é o valor padrão.

- **Implantar**: o recurso existe e é definido no modelo. O recurso será reimplantado. As propriedades do recurso podem ou não ser alteradas. A operação retorna esse tipo de alteração quando não tem informações suficientes para determinar se as propriedades serão alteradas. Você só verá essa condição quando [ResultFormat](#result-format) estiver definido como `ResourceIdOnly`.

## <a name="deployment-scope"></a>Escopo da implantação

Você pode usar a operação What-If para implantações no nível de assinatura ou grupo de recursos. Você define o escopo de implantação com o parâmetro `-ScopeType`. Os valores aceitos são `Subscription` e `ResourceGroup`. Este artigo demonstra implantações de grupo de recursos.

Para saber mais sobre implantações em nível de assinatura, confira [criar grupos de recursos e recursos no nível da assinatura](deploy-to-subscription.md#).

## <a name="result-format"></a>Formato do resultado

Você pode controlar o nível de detalhe que é retornado sobre as alterações previstas. Defina o parâmetro `ResultFormat` como `FullResourcePayloads` para obter uma lista de recursos que serão alterados e detalhes sobre as propriedades que serão alteradas. Defina o parâmetro `ResultFormat` como `ResourceIdOnly` para obter uma lista de recursos que serão alterados. O valor padrão é `FullResourcePayloads`.  

As capturas de tela a seguir mostram os dois formatos de saída diferentes:

- Cargas de recursos completas

    ![Implantação do modelo do Gerenciador de recursos e-se operação fullresourcepayloads saída](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-fullresourcepayload.png)

- Somente ID do recurso

    ![Implantação do modelo do Gerenciador de recursos e-se operação resourceidonly saída](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-resourceidonly.png)

## <a name="run-what-if-operation"></a>Executar a operação What-If

### <a name="set-up-environment"></a>Configurar o ambiente

Para ver como funciona o que acontece, vamos executar alguns testes. Primeiro, implante um modelo de [modelos de início rápido do Azure que cria uma conta de armazenamento](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json). O tipo de conta de armazenamento padrão é `Standard_LRS`. Você usará essa conta de armazenamento para testar como as alterações são relatadas por What-If.

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

### <a name="test-modification"></a>Modificação de teste

Após a conclusão da implantação, você estará pronto para testar a operação What-If. Execute o comando What-If, mas altere o tipo de conta de armazenamento para `Standard_GRS`.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" `
  -storageAccountType Standard_GRS
```

A saída What-If é semelhante a:

![Saída da operação What-If da implantação de modelo do Resource Manager](./media/template-deploy-what-if/resource-manager-deployment-whatif-output.png)

Observe na parte superior da saída que as cores são definidas para indicar o tipo de alterações.

Na parte inferior da saída, ele mostra que o nome do SKU (tipo de conta de armazenamento) será alterado de **Standard_LRS** para **Standard_GRS**.

Algumas das propriedades listadas como excluídas não serão realmente alteradas. Na imagem anterior, essas propriedades são accessTier, Encryption. KeySource e outras nessa seção. As propriedades podem ser relatadas incorretamente como excluídas quando não estão no modelo, mas são definidas automaticamente durante a implantação como valores padrão. Esse resultado é considerado "ruído" na resposta What-If. O recurso implantado final terá os valores definidos para as propriedades. Como a operação What-If amadurece, essas propriedades serão filtradas do resultado.

### <a name="test-deletion"></a>Exclusão de teste

A operação What-If dá suporte ao uso do [modo de implantação](deployment-modes.md). Quando definido para o modo completo, os recursos que não estão no modelo são excluídos. O exemplo a seguir implanta um [modelo que não tem recursos definidos](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) no modo completo.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Como nenhum recurso está definido no modelo e o modo de implantação está definido como concluído, a conta de armazenamento será excluída.

![Implantação do modelo do Resource Manager o modo de implantação de saída de operação de conclusão](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

É importante lembrar que, se não fizer nenhuma alteração real. A conta de armazenamento ainda existe em seu grupo de recursos.

## <a name="next-steps"></a>Passos Seguintes

- Se você observar resultados incorretos da versão de visualização de What-If, informe os problemas em [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- Para implantar modelos com Azure PowerShell, consulte [implantar recursos com modelos do Resource Manager e Azure PowerShell](resource-group-template-deploy.md).
- Para implantar modelos com REST, consulte [implantar recursos com modelos do Resource Manager e a API REST do Gerenciador de recursos](resource-group-template-deploy-rest.md).
- Para reverter para uma implantação bem-sucedida quando você receber um erro, consulte [reverter em caso de erro para a implantação bem-sucedida](rollback-on-error.md).
