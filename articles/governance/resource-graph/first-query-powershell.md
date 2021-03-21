---
title: 'Quickstart: A sua primeira consulta PowerShell'
description: Neste arranque rápido, siga os passos para ativar o módulo Gráfico de Recursos para Azure PowerShell e executar a sua primeira consulta.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 131bed4fe60035682a317e186f11561bc005b298
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98917678"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Quickstart: Executar a sua primeira consulta de Gráfico de Recurso usando a Azure PowerShell

O primeiro passo para utilizar o Azure Resource Graph é garantir que o módulo do Azure PowerShell está instalado. Este início rápido explica-lhe o processo para adicionar o módulo à instalação do Azure PowerShell.

No final deste processo, terá adicionado o módulo à instalação do Azure PowerShell à escolha e executado a sua primeira consulta do Resource Graph.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-module"></a>Adicionar o módulo do Resource Graph

Para ativar o Azure PowerShell para consultar o Azure Resource Graph, tem de adicionar o módulo. Este módulo pode ser utilizado com PowerShell instalado localmente, com [Azure Cloud Shell,](https://shell.azure.com)ou com a [imagem PowerShell Docker](https://hub.docker.com/_/microsoft-powershell).

### <a name="base-requirements"></a>Requisitos de base

O módulo do Azure Resource Graph necessita do seguinte software:

- Azure PowerShell 1.0.0 ou superior. Se não estiver ainda instalado, siga [estas instruções](/powershell/azure/install-az-ps).

- PowerShellGet 2.0.1 ou superior. Se não estiver ainda instalado ou atualizado, siga [estas instruções](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>Instale o módulo

O módulo gráfico de recurso para powerShell é **Az.ResourceGraph**.

1. A partir de um pedido **administrativo** powerShell, executar o seguinte comando:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Validar que o módulo foi importado e é a versão mais recente (0.7.5):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta do Resource Graph

Com o módulo Azure PowerShell adicionado ao seu ambiente escolhido, é altura de experimentar uma consulta simples do Resource Graph. A consulta devolve os primeiros cinco recursos Azure com o **Nome** e Tipo de **Recurso** de cada recurso.

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
   > Assim como na primeira consulta, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido. A ordem dos comandos da consulta é importante. Neste exemplo, `order by` vem depois de `limit`, Esta ordem de comando limita primeiro os resultados da consulta e depois ordena-os.

1. Atualize a consulta para primeiro `order by` propriedade **Nome** e, em seguida, `limit` os cinco resultados principais:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'Resources | project name, type | order by name asc | limit 5'
   ```

Quando a consulta final é executada várias vezes, assumindo que nada no seu ambiente está a mudar, os resultados devolvidos são consistentes e encomendados pela propriedade **Name,** mas ainda limitados aos cinco melhores resultados.

> [!NOTE]
> Se a consulta não devolver os resultados de uma subscrição a que já tem acesso, então note que `Search-AzGraph` o CMDlet predefini às subscrições no contexto padrão. Para ver a lista de IDs de subscrição que fazem parte do contexto padrão executar isto `(Get-AzContext).Account.ExtendedProperties.Subscriptions` Se pretender pesquisar em todas as subscrições a que tem acesso, pode-se definir o PSDefaultParameterValues para `Search-AzGraph` cmdlet executando `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser remover o módulo Resource Graph do seu ambiente do Azure PowerShell, poderá fazê-lo com o seguinte comando:

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Isto não elimina o ficheiro do módulo descarregado anteriormente. Apenas o remove da sessão do PowerShell.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, adicionou o módulo Gráfico de Recursos ao seu ambiente Azure PowerShell e executou a sua primeira consulta. Para saber mais sobre a linguagem Resource Graph, continue na página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obtenha mais informações sobre o idioma de consulta](./concepts/query-language.md)