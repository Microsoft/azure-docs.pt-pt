---
title: Implementação do modelo o que-se (Pré-visualização)
description: Determine quais as mudanças que acontecerão aos seus recursos antes de implementar um modelo de Gestor de Recursos Azure.
author: mumian
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: jgao
ms.openlocfilehash: b9d4150779842614a5dc284a2b3a489593fabfe1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388507"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Implementação do modelo do Gestor de Recursos que-se operação (Pré-visualização)

Antes de implementar um modelo, é melhor visualizar as alterações que irão acontecer. O Azure Resource Manager fornece a operação "what-if" para que veja como os recursos vão mudar se implementar o modelo. A operação "e se" não faz alterações nos recursos existentes. Em vez disso, prevê as alterações se o modelo especificado for implantado.

> [!NOTE]
> A operação "e se" está atualmente em pré-visualização. Para usá-lo, deve [inscrever-se para a pré-visualização](https://aka.ms/armtemplatepreviews). Como um lançamento de pré-visualização, os resultados podem por vezes mostrar que um recurso mudará quando, na verdade, nenhuma mudança acontecerá. Estamos a trabalhar para reduzir estes problemas, mas precisamos da sua ajuda. Por favor, informe estas questões em [https://aka.ms/whatifissues. ](https://aka.ms/whatifissues)

Pode utilizar a operação "what-if" com os comandos PowerShell ou as operações REST API.

No PowerShell, a saída inclui resultados codificados por cores que o ajudam a ver os diferentes tipos de alterações.

![Implementação do modelo do Gestor de Recursos o que-se operação fullresourcepayload e tipos de mudança](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

O texto é:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

## <a name="what-if-commands"></a>E se comandos

Pode utilizar a API Azure PowerShell ou Azure REST para a operação "e se".

### <a name="azure-powershell"></a>Azure PowerShell

Para ver uma pré-visualização das alterações antes de implementar um modelo, adicione o parâmetro de comutação `-Whatif` ao comando de implantação.

* `New-AzResourceGroupDeployment -Whatif` para implantações de grupos de recursos
* `New-AzSubscriptionDeployment -Whatif` e `New-AzDeployment -Whatif` para implementações de nível de subscrição

Ou, pode utilizar o parâmetro `-Confirm` mudar para pré-visualizar as alterações e ser solicitado para continuar com a implementação.

* `New-AzResourceGroupDeployment -Confirm` para implantações de grupos de recursos
* `New-AzSubscriptionDeployment -Confirm` e `New-AzDeployment -Confirm` para implementações de nível de subscrição

Os comandos anteriores devolvem um resumo de texto que pode inspecionar manualmente. Para obter um objeto que possa inspecionar programáticamente para obter alterações, utilize:

* `$results = Get-AzResourceGroupDeploymentWhatIf` para implantações de grupos de recursos
* `$results = Get-AzSubscriptionDeploymentWhatIf` ou `$results = Get-AzDeploymentWhatIf` para implementações de nível de subscrição

> [!NOTE]
> Antes do lançamento da versão 2.0.1-alpha5, usou o comando `New-AzDeploymentWhatIf`. Este comando foi substituído pelos comandos `Get-AzDeploymentWhatIf`, `Get-AzResourceGroupDeploymentWhatIf`e `Get-AzSubscriptionDeploymentWhatIf`. Se usou uma versão anterior, precisa atualizar essa sintaxe. O parâmetro `-ScopeType` foi removido.

### <a name="azure-rest-api"></a>API de DESCANSO Azul

Para a API REST, utilize:

* [Implementações - E se](/rest/api/resources/deployments/whatif) para implementações de grupos de recursos
* [Implementações - E se no âmbito de subscrição](/rest/api/resources/deployments/whatifatsubscriptionscope) para implementações de nível de subscrição

## <a name="change-types"></a>Alterar tipos

A operação "e se" enumera seis tipos diferentes de alterações:

- **Criar**: O recurso não existe atualmente, mas está definido no modelo. O recurso será criado.

- **Eliminar**: Este tipo de alteração só se aplica quando se utiliza [o modo completo](deployment-modes.md) de implantação. O recurso existe, mas não está definido no modelo. Com o modo completo, o recurso será eliminado. Apenas os recursos que suportam a [eliminação completa](complete-mode-deletion.md) do modo estão incluídos neste tipo de alteração.

- **Ignore**: O recurso existe, mas não está definido no modelo. O recurso não será implantado ou modificado.

- **NoChange**: O recurso existe e é definido no modelo. O recurso será redistribuído, mas as propriedades do recurso não mudarão. Este tipo de alteração é devolvido quando o [ResultFormat](#result-format) está definido para `FullResourcePayloads`, que é o valor padrão.

- **Modificar**: O recurso existe e é definido no modelo. O recurso será redistribuído e as propriedades do recurso mudarão. Este tipo de alteração é devolvido quando o [ResultFormat](#result-format) está definido para `FullResourcePayloads`, que é o valor padrão.

- **Implementação**: O recurso existe e é definido no modelo. O recurso será redistribuído. As propriedades do recurso podem ou não mudar. A operação devolve este tipo de alteração quando não tem informação suficiente para determinar se alguma propriedade irá mudar. Só vê esta condição quando o [Formato de Resultados](#result-format) está definido para `ResourceIdOnly`.

## <a name="result-format"></a>Formato resultado

Pode controlar o nível de detalhe que é devolvido sobre as alterações previstas. Nos comandos de implantação (`New-Az*Deployment`), utilize o parâmetro **-WhatIfResultFormat.** Nos comandos programáticos do objeto (`Get-Az*DeploymentWhatIf`), utilize o parâmetro **ResultFormat.**

Defina o parâmetro de formato para **FullResourcePayloads** para obter uma lista de recursos que irão alterar e detalhes sobre as propriedades que irão mudar. Defina o parâmetro de formato para **O RecursoIdOnly** para obter uma lista de recursos que irão mudar. O valor predefinido é **FullResourcePayloads**.  

Os seguintes resultados mostram os dois formatos de saída diferentes:

- Cargas completas de recursos

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- ID de recursos apenas

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Executar o que-se operação

### <a name="set-up-environment"></a>Configurar o ambiente

Para ver como funciona, vamos fazer alguns testes. Primeiro, implemente um [modelo que crie uma rede virtual.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json) Utilizará esta rede virtual para testar como as mudanças são reportadas pelo "e se".

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>Modificação do teste

Depois da implementação terminar, está pronto para testar a operação "e se". Desta vez implemente um [modelo que altera a rede virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Falta-lhe uma das etiquetas originais, uma sub-rede foi removida e o prefixo de endereço mudou.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

A saída do que-se parece semelhante a:

![Implementação do modelo do Gestor de Recursos o que-se a saída de operação](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

A saída de texto é:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Note no topo da saída que as cores são definidas para indicar o tipo de alterações.

Na parte inferior da saída, mostra que a etiqueta Proprietário foi eliminada. O prefixo do endereço passou de 10.0.0.0/16 para 10.0.0.0.0/15. A sub-rede chamada subnet001 foi eliminada. Lembre-se que estas mudanças não foram realmente implementadas. Você vê uma pré-visualização das mudanças que acontecerão se implementar o modelo.

Algumas das propriedades que estão listadas como eliminadas não vão realmente mudar. As propriedades podem ser incorretamente reportadas como eliminadas quando não estão no modelo, mas são automaticamente definidas durante a implementação como valores predefinidos. Este resultado é considerado "ruído" na resposta "e se". O recurso implantado final terá os valores definidos para as propriedades. À medida que a operação se amadurece, estas propriedades serão filtradas fora do resultado.

## <a name="programmatically-evaluate-what-if-results"></a>Avaliar programáticamente os resultados do que se

Agora, vamos avaliar programáticamente os resultados do que se, definindo o comando para uma variável.

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIf `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Pode ver um resumo de cada mudança.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>Confirmar eliminação

A operação "e se" suporta a utilização do modo de [implantação](deployment-modes.md). Quando definido para completar o modo, os recursos não no modelo são eliminados. O exemplo seguinte implementa um [modelo que não tem recursos definidos](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) em modo completo.

Para pré-visualizar alterações antes de implementar um modelo, utilize o parâmetro de comutação `-Confirm` com o comando de implantação. Se as alterações forem como esperava, confirme que pretende que a implementação esteja concluída.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Como não são definidos recursos no modelo e o modo de implementação está definido para ser concluído, a rede virtual será eliminada.

![Modo de implementação do modelo do Gestor de Recursos o que-se o modo de implementação de saída de operação completo](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

A saída de texto é:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

Vê as alterações esperadas e pode confirmar que quer que a implantação seja executada.

## <a name="next-steps"></a>Passos seguintes

- Se notar resultados incorretos a partir da versão prévia do que-se, por favor reporte os problemas em [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- Para implementar modelos com O PowerShell Azure, consulte [a implantação de recursos com modelos](deploy-powershell.md)de Gestor de Recursos e PowerShell Azure .
- Para implementar modelos com REST, consulte implementar recursos com modelos de Gestor de [Recursos e Gestor de Recursos REST API](deploy-rest.md).
