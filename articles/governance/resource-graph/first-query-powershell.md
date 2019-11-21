---
title: 'Quickstart: Your first PowerShell query'
description: In this quickstart, you follow the steps to enable the Resource Graph module for Azure PowerShell and run your first query.
ms.date: 10/18/2019
ms.topic: quickstart
ms.openlocfilehash: de0103ea5fe12ae26ac64d04a8efc50b9e708fc5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74216464"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Quickstart: Run your first Resource Graph query using Azure PowerShell

O primeiro passo para utilizar o Azure Resource Graph é garantir que o módulo do Azure PowerShell está instalado. Este início rápido explica-lhe o processo para adicionar o módulo à instalação do Azure PowerShell.

No final deste processo, terá adicionado o módulo à instalação do Azure PowerShell à escolha e executado a sua primeira consulta do Resource Graph.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="add-the-resource-graph-module"></a>Adicionar o módulo do Resource Graph

Para ativar o Azure PowerShell para consultar o Azure Resource Graph, tem de adicionar o módulo. This module can be used with locally installed PowerShell, with [Azure Cloud Shell](https://shell.azure.com), or with the [PowerShell Docker image](https://hub.docker.com/_/microsoft-powershell).

### <a name="base-requirements"></a>Requisitos de base

O módulo do Azure Resource Graph necessita do seguinte software:

- Azure PowerShell 1.0.0 or higher. Se não estiver ainda instalado, siga [estas instruções](/powershell/azure/install-az-ps).

- PowerShellGet 2.0.1 ou superior. Se não estiver ainda instalado ou atualizado, siga [estas instruções](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>Install the module

The Resource Graph module for PowerShell is **Az.ResourceGraph**.

1. From an **administrative** PowerShell prompt, run the following command:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Validate that the module has been imported and is the latest version (0.7.5):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta do Resource Graph

Com o módulo Azure PowerShell adicionado ao seu ambiente escolhido, é altura de experimentar uma consulta simples do Resource Graph. A consulta devolverá os cinco primeiros recursos do Azure com o **Nome** e **Tipo de Recurso** de cada recurso.

1. Execute a primeira consulta do Azure Resource Graph com o cmdlet `Search-AzGraph`:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Como este exemplo de consulta não fornece um modificador de ordenação como `order by`, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido.

1. Atualize a consulta para `order by` propriedade **Nome**:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Assim como na primeira consulta, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido. A ordem dos comandos da consulta é importante. Neste exemplo, `order by` vem depois de `limit`, ou seja, primeiro limita os resultados da consulta e, em seguida, ordena-os.

1. Atualize a consulta para primeiro `order by` propriedade **Nome** e, em seguida, `limit` os cinco resultados principais:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'Resources | project name, type | order by name asc | limit 5'
   ```

Quando a consulta final é executada várias vezes, partindo do princípio de que nada no seu ambiente está a mudar, os resultados devolvidos serão consistentes e conforme o esperado – ordenados pela propriedade **Nome**, mas continuam a ser limitados aos cinco resultados principais.

> [!NOTE]
> If the query does not return results from a subscription you already have access to, then note that `Search-AzGraph` cmdlet defaults to subscriptions in the default context. To see the list of subscription IDs which are part of the default context run this `(Get-AzContext).Account.ExtendedProperties.Subscriptions` If you wish to search across all the subscriptions you have access to, one can set the PSDefaultParameterValues for `Search-AzGraph` cmdlet by running `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
## <a name="clean-up-resources"></a>Limpar recursos

Se quiser remover o módulo Resource Graph do seu ambiente do Azure PowerShell, poderá fazê-lo com o seguinte comando:

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Este comando não elimina o ficheiro do módulo que transferiu anteriormente. Apenas o remove da sessão do PowerShell.

## <a name="next-steps"></a>Passos seguintes

- Get more information about the [query language](./concepts/query-language.md).
- Learn more about how to [explore resources](./concepts/explore-resources.md).
- Run your first query by using the [Azure portal](first-query-portal.md).
- Run your first query with [Azure CLI](first-query-azurecli.md).
- See samples of [Starter queries](./samples/starter.md).
- See samples of [Advanced queries](./samples/advanced.md).
- Provide feedback on [UserVoice](https://feedback.azure.com/forums/915958-azure-governance).