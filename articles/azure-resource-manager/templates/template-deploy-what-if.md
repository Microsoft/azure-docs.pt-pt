---
title: Implantação do modelo e se (Pré-visualização)
description: Determine quais as alterações que irão acontecer aos seus recursos antes de implementar um modelo de Gestor de Recursos Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: tomfitz
ms.openlocfilehash: 27efe1e03b8a0d373d566106a53a41007731973e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87810076"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>Implantação do modelo ARM e se operação (Pré-visualização)

Antes de implementar um modelo de Gestor de Recursos Azure (modelo ARM), pode visualizar as alterações que irão ocorrer. O Azure Resource Manager fornece o e-se a operação para que você veja como os recursos vão mudar se implementar o modelo. A operação "e se" não altera os recursos existentes. Em vez disso, prevê as alterações se o modelo especificado for implantado.

> [!NOTE]
> A operação "e se" está atualmente em pré-visualização. Como um lançamento de pré-visualização, os resultados podem por vezes mostrar que um recurso vai mudar quando realmente não acontecerá nenhuma alteração. Estamos a trabalhar para reduzir estes problemas, mas precisamos da sua ajuda. Por favor, informe estas questões em [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .

Pode utilizar a operação "e se" com as operações Azure PowerShell, Azure CLI ou REST API. E se for suportado para implantações de grupo de recursos, subscrição, grupo de gestão e nível de inquilino.

## <a name="install-azure-powershell-module"></a>Instalar módulo Azure PowerShell

Para utilizar o que se em PowerShell, tem de ter a versão **4.2 ou posterior do módulo Az**.

Mas, antes de instalar o módulo necessário, certifique-se de que tem PowerShell Core (6.x ou 7.x). Se tiver PowerShell 5.x ou mais cedo, [atualize a sua versão do PowerShell](/powershell/scripting/install/installing-powershell). Não é possível instalar o módulo necessário no PowerShell 5.x ou mais cedo.

### <a name="install-latest-version"></a>Instalar a versão mais recente

Para instalar o módulo, utilize:

```powershell
Install-Module -Name Az -Force
```

Para obter mais informações sobre a instalação de módulos, consulte [instalar a Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="uninstall-alpha-version"></a>Desinstalar a versão alfa

Se já instalou uma versão alfa do módulo "e se", desinstale esse módulo. A versão alfa apenas estava disponível para utilizadores que se inscreveram para uma pré-visualização antecipada. Se não instalou a pré-visualização, pode saltar esta secção.

1. Execute o PowerShell como administrador
1. Verifique as versões instaladas do módulo Az.Resources.

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. Se tiver uma versão instalada com um número de versão no formato **2.x.x-alpha,** desinstale esta versão.

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. Desagregar o repositório que usou para instalar a pré-visualização.

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

Estás pronto para usar o "e se".

## <a name="install-azure-cli-module"></a>Instalar módulo CLI Azure

Para utilizar o "e se" em Azure CLI, tem de ter Azure CLI 2.5.0 ou mais tarde. Se necessário, [instale a versão mais recente do Azure CLI](/cli/azure/install-azure-cli).

## <a name="see-results"></a>Ver resultados

Quando utiliza o "what-if" em PowerShell ou Azure CLI, a saída inclui resultados codificados por cores que o ajudam a ver os diferentes tipos de alterações.

![Implementação do modelo do gestor de recursos, se a operação fullresourcepayload e os tipos de alteração](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

A saída do texto é:

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

> [!NOTE]
> O funcionamento do "e se" não pode resolver a [função de referência](template-functions-resource.md#reference). Cada vez que define uma propriedade para uma expressão de modelo que inclui a função de referência, e se reportar que a propriedade mudará. Este comportamento acontece porque o que-se compara o valor atual da propriedade (como `true` ou por um valor `false` booleano) com a expressão do modelo não resolvido. Obviamente, estes valores não vão corresponder. Quando implementar o modelo, a propriedade só mudará quando a expressão do modelo se resolver para um valor diferente.

## <a name="what-if-commands"></a>E se comandos

### <a name="azure-powershell"></a>Azure PowerShell

Para pré-visualizar alterações antes de implementar um modelo, utilize [o New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) ou [o New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment). Adicione o `-Whatif` parâmetro do interruptor ao comando de implantação.

* `New-AzResourceGroupDeployment -Whatif` para implementações de grupos de recursos
* `New-AzSubscriptionDeployment -Whatif` e `New-AzDeployment -Whatif` para implementações de nível de subscrição

Pode utilizar o `-Confirm` parâmetro do switch para visualizar as alterações e ser solicitado para continuar com a implementação.

* `New-AzResourceGroupDeployment -Confirm` para implementações de grupos de recursos
* `New-AzSubscriptionDeployment -Confirm` e `New-AzDeployment -Confirm` para implementações de nível de subscrição

Os comandos anteriores devolvem um resumo de texto que pode inspecionar manualmente. Para obter um objeto que possa inspecionar programaticamente as alterações, utilize [o Get-AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) ou [Get-AzSubscriptionDeploymentWhatIfResult](/powershell/module/az.resources/get-azdeploymentwhatifresult).

* `$results = Get-AzResourceGroupDeploymentWhatIfResult` para implementações de grupos de recursos
* `$results = Get-AzSubscriptionDeploymentWhatIfResult` ou `$results = Get-AzDeploymentWhatIfResult` para implementações de nível de subscrição

### <a name="azure-cli"></a>CLI do Azure

Para pré-visualizar alterações antes de implementar um modelo, use:

* [grupo de implantação az e se](/cli/azure/deployment/group#az-deployment-group-what-if) para implantações de grupos de recursos
* [az implantação sub-e-se](/cli/azure/deployment/sub#az-deployment-sub-what-if) para implementações de nível de subscrição
* [az implantação mg e se](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-what-if) para implantações de grupos de gestão
* [az inquilino de implantação o que se](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-what-if) para implantações de inquilinos

Pode utilizar o `--confirm-with-what-if` interruptor (ou a sua forma `-c` curta) para visualizar as alterações e ser solicitado para continuar com a implementação. Adicione este interruptor a:

* [az grupo de implementação criar](/cli/azure/deployment/group#az-deployment-group-create)
* [sub de implantação az criar](/cli/azure/deployment/sub#az-deployment-sub-create).
* [mg de implantação az criar](/cli/azure/deployment/mg#az-deployment-mg-create)
* [inquilino de implantação az criar](/cli/azure/deployment/tenant#az-deployment-tenant-create)

Por exemplo, utilização `az deployment group create --confirm-with-what-if` ou `-c` para implantações de grupos de recursos.

Os comandos anteriores devolvem um resumo de texto que pode inspecionar manualmente. Para obter um objeto JSON que possa inspecionar programaticamente as alterações, utilize o `--no-pretty-print` interruptor. Por exemplo, utilizar `az deployment group what-if --no-pretty-print` para implantações de grupos de recursos.

Se pretender devolver os resultados sem cores, abra o seu ficheiro [de configuração Azure CLI.](/cli/azure/azure-cli-configuration) Desateia **no_color** para **sim.**

### <a name="azure-rest-api"></a>API REST do Azure

Para REST API, utilize:

* [Implementações - E se](/rest/api/resources/deployments/whatif) para implantações de grupos de recursos
* [Implementações - E se no âmbito de subscrição](/rest/api/resources/deployments/whatifatsubscriptionscope) para implementações de subscrição
* [Implementações - E se estiver em gestão de âmbito do grupo](/rest/api/resources/deployments/whatifatmanagementgroupscope) para implantações de grupos de gestão
* [Implantações - E se no âmbito do arrendatário](/rest/api/resources/deployments/whatifattenantscope) para implantações de inquilinos.

## <a name="change-types"></a>Tipos de alteração

A operação "e se" enumera seis tipos diferentes de alterações:

- **Criar:** O recurso não existe atualmente, mas está definido no modelo. O recurso será criado.

- **Excluir**: Este tipo de alteração só se aplica quando se utiliza [o modo completo](deployment-modes.md) de implantação. O recurso existe, mas não está definido no modelo. Com o modo completo, o recurso será eliminado. Apenas os recursos que [suportam a eliminação completa do modo](complete-mode-deletion.md) estão incluídos neste tipo de alteração.

- **Ignorar:** O recurso existe, mas não está definido no modelo. O recurso não será implantado ou modificado.

- **NoChange**: O recurso existe e é definido no modelo. O recurso será redistribuído, mas as propriedades do recurso não mudarão. Este tipo de alteração é devolvido quando [o ResultadoFormat](#result-format) está definido para `FullResourcePayloads` , que é o valor padrão.

- **Modificar:** O recurso existe e é definido no modelo. O recurso será redistribuído e as propriedades do recurso serão alteradas. Este tipo de alteração é devolvido quando [o ResultadoFormat](#result-format) está definido para `FullResourcePayloads` , que é o valor padrão.

- **Implementar**: O recurso existe e é definido no modelo. O recurso será redistribuído. As propriedades do recurso podem ou não mudar. A operação devolve este tipo de alteração quando não tem informações suficientes para determinar se alguma propriedade irá mudar. Só se vê esta condição quando [o ResultadoFormat](#result-format) está definido para `ResourceIdOnly` .

## <a name="result-format"></a>Formato de resultados

Controla o nível de detalhe que é devolvido sobre as mudanças previstas. Tem duas opções:

* **FullResourcePayloads** - devolve uma lista de recursos que mudarão e detalhes sobre as propriedades que vão mudar
* **ResourceIdOnly** - devolve uma lista de recursos que vão mudar

O valor predefinido é **FullResourcePayloads**.

Para os comandos de implantação PowerShell, utilize o `-WhatIfResultFormat` parâmetro. Nos comandos programáticos do objeto, utilize o `ResultFormat` parâmetro.

Para Azure CLI, utilize o `--result-format` parâmetro.
 
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

- Apenas ID de recursos

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Executar o que se operar

### <a name="set-up-environment"></a>Configurar ambiente

Para ver como funciona, vamos fazer alguns testes. Primeiro, implemente um [modelo que cria uma rede virtual.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json) Você usará esta rede virtual para testar como as mudanças são reportadas pelo e-se.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>Modificação do teste

Depois da colocação concluída, está pronto para testar a operação "e se". Desta vez implementa um [modelo que altera a rede virtual.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json) Falta-lhe uma das etiquetas originais, uma sub-rede foi removida, e o prefixo do endereço mudou.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

A saída do "e se" parece semelhante a:

![Implementação do modelo do gestor de recursos, se a saída de operação](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

A saída do texto é:

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

Note na parte superior da saída que as cores são definidas para indicar o tipo de alterações.

Na parte inferior da saída, mostra que a etiqueta Proprietário foi eliminada. O prefixo do endereço passou de 10.0.0.0/16 para 10.0.0.0/15. A sub-rede denominada sub-rede001 foi eliminada. Lembre-se que estas mudanças não foram implementadas. Você vê uma pré-visualização das alterações que irão acontecer se implementar o modelo.

Algumas das propriedades que estão listadas como eliminadas não vão realmente mudar. As propriedades podem ser incorretamente reportadas como eliminadas quando não estão no modelo, mas são automaticamente definidas durante a implementação como valores predefinidos. Este resultado é considerado "ruído" na resposta "e se". O recurso implantado final terá os valores definidos para as propriedades. À medida que a operação se amadurecer, estas propriedades serão filtradas do resultado.

## <a name="programmatically-evaluate-what-if-results"></a>Avaliar programáticamente os resultados do que se

Agora, vamos avaliar programáticamente os resultados do "e se", definindo o comando para uma variável.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>Confirmar eliminação

O e-se funcionamento suporta o [modo de implantação](deployment-modes.md). Quando definido para o modo completo, os recursos que não estão no modelo são eliminados. O exemplo a seguir implementa um [modelo que não tem recursos definidos](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) em modo completo.

Para visualizar alterações antes de implementar um modelo, utilize o parâmetro de comutador de confirmação com o comando de implementação. Se as alterações forem como esperava, reconheça que deseja que a implementação esteja concluída.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

Como nenhum recursos é definido no modelo e o modo de implementação está definido para ser concluído, a rede virtual será eliminada.

![Implementação do modelo do gestor de recursos, se o modo de implementação da saída de operação estiver concluído](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

A saída do texto é:

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

Vê as alterações esperadas e pode confirmar que pretende que a implementação seja executada.

## <a name="sdks"></a>SDKs

Pode utilizar a operação "e se" através dos Azure SDKs.

* Para Python, use [o e se.](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations?view=azure-python#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-)

* Para Java, utilize [a classe DeploymentWhatIf](/java/api/com.microsoft.azure.management.resources.deploymentwhatif?view=azure-java-stable).

* Para .NET, utilize [a classe DeploymentWhatIf](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif?view=azure-dotnet).

## <a name="next-steps"></a>Passos seguintes

- Se notar resultados incorretos a partir da pré-visualização do "e se", por favor reporte as questões em [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .
- Para implementar modelos com Azure PowerShell, consulte [implementar recursos com modelos ARM e Azure PowerShell](deploy-powershell.md).
- Para implementar modelos com Azure CLI, consulte [recursos de implantação com modelos ARM e Azure CLI](deploy-cli.md).
- Para implementar modelos com REST, consulte [implementar recursos com modelos ARM e API do Gestor de Recursos](deploy-rest.md)REST .
