---
title: 'Quickstart: A sua primeira consulta powerShell'
description: Neste arranque rápido, siga os passos para ativar o módulo De recursos para o Azure PowerShell e execute a sua primeira consulta.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: dd96324671f46f98d5b6c8bae1839a5b02d38b23
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240661"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Quickstart: Execute a sua primeira consulta de gráfico de recursos usando o Azure PowerShell

O primeiro passo para utilizar o Azure Resource Graph é garantir que o módulo do Azure PowerShell está instalado. Este início rápido explica-lhe o processo para adicionar o módulo à instalação do Azure PowerShell.

No final deste processo, terá adicionado o módulo à instalação do Azure PowerShell à escolha e executado a sua primeira consulta do Resource Graph.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-module"></a>Adicionar o módulo do Resource Graph

Para ativar o Azure PowerShell para consultar o Azure Resource Graph, tem de adicionar o módulo. Este módulo pode ser utilizado com powerShell instalado localmente, com [Azure Cloud Shell,](https://shell.azure.com)ou com a [imagem PowerShell Docker](https://hub.docker.com/_/microsoft-powershell).

### <a name="base-requirements"></a>Requisitos de base

O módulo do Azure Resource Graph necessita do seguinte software:

- Azure PowerShell 1.0.0 ou superior. Se não estiver ainda instalado, siga [estas instruções](/powershell/azure/install-az-ps).

- PowerShellGet 2.0.1 ou superior. Se não estiver ainda instalado ou atualizado, siga [estas instruções](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>Instalar o módulo

O módulo De Recurso Graph para PowerShell é **Az.ResourceGraph**.

1. A partir de um pedido **administrativo** da PowerShell, executar o seguinte comando:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Valide que o módulo foi importado e é a versão mais recente (0.7.5):

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
> Se a consulta não devolver os resultados de uma subscrição a que já tem acesso, note que `Search-AzGraph` cmdlet predefinido sintetiza as subscrições no contexto predefinido. Para ver a lista de IDs de subscrição que fazem parte do contexto predefinido executar este `(Get-AzContext).Account.ExtendedProperties.Subscriptions` Se deseja pesquisar todas as subscrições a que tem acesso, pode definir os PSDefaultParâmetrometerValues para `Search-AzGraph` cmdlet, executando `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
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

Neste arranque rápido, adicionou o módulo De recursos gráficos ao seu ambiente Azure PowerShell e executa a sua primeira consulta. Para saber mais sobre a linguagem do gráfico de recursos, continue na página de detalhes da linguagem da consulta.

> [!div class="nextstepaction"]
> [Obtenha mais informações sobre a linguagem de consulta](./concepts/query-language.md)