---
title: Executar sua primeira consulta usando Azure PowerShell
description: Este artigo explica os passos para ativar o módulo do Resource Graph para o Azure PowerShell e executar a primeira consulta.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 6c58528aba29cbbc81694112815534a8fc525b8a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565391"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Início rápido: Executar a primeira consulta do Resource Graph com o Azure PowerShell

O primeiro passo para utilizar o Azure Resource Graph é garantir que o módulo do Azure PowerShell está instalado. Este início rápido explica-lhe o processo para adicionar o módulo à instalação do Azure PowerShell.

No final deste processo, terá adicionado o módulo à instalação do Azure PowerShell à escolha e executado a sua primeira consulta do Resource Graph.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="add-the-resource-graph-module"></a>Adicionar o módulo do Resource Graph

Para ativar o Azure PowerShell para consultar o Azure Resource Graph, tem de adicionar o módulo. Esse módulo pode ser usado com o PowerShell instalado localmente, com [Azure cloud Shell](https://shell.azure.com)ou com a [imagem Azure PowerShell Docker](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Requisitos de base

O módulo do Azure Resource Graph necessita do seguinte software:

- Azure PowerShell 1.0.0 ou superior. Se não estiver ainda instalado, siga [estas instruções](/powershell/azure/install-az-ps).

- PowerShellGet 2.0.1 ou superior. Se não estiver ainda instalado ou atualizado, siga [estas instruções](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Instalar o módulo

O módulo do grafo de recursos para o PowerShell é **AZ. ResourceGraph**.

1. Em um prompt **administrativo** do PowerShell, execute o seguinte comando:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Valide se o módulo foi importado e se é a versão correta (0.7.3):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta do Resource Graph

Com o módulo Azure PowerShell adicionado ao seu ambiente escolhido, é altura de experimentar uma consulta simples do Resource Graph. A consulta devolverá os cinco primeiros recursos do Azure com o **Nome** e o **Tipo de Recurso** de cada recurso.

1. Execute a primeira consulta do Azure Resource Graph com o cmdlet `Search-AzGraph`:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Como este exemplo de consulta não fornece um modificador de ordenação como `order by`, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido.

1. Atualize a consulta para `order by` propriedade **Nome**:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Assim como na primeira consulta, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido. A ordem dos comandos da consulta é importante. Neste exemplo, `order by` vem depois de `limit`, ou seja, primeiro limita os resultados da consulta e, em seguida, ordena-os.

1. Atualize a consulta para primeiro `order by` propriedade **Nome** e, em seguida, `limit` os cinco resultados principais:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'project name, type | order by name asc | limit 5'
   ```

Quando a consulta final é executada várias vezes, partindo do princípio de que nada no seu ambiente está a mudar, os resultados devolvidos serão consistentes e conforme o esperado – ordenados pela propriedade **Nome**, mas continuam a ser limitados aos cinco resultados principais.

> [!NOTE]
> Se a consulta não retornar resultados de uma assinatura à qual você já tem acesso, observe que `Search-AzGraph` o cmdlet usa como padrão as assinaturas no contexto padrão. Para ver a lista de IDs de assinatura que fazem parte do contexto padrão, execute `(Get-AzContext).Account.ExtendedProperties.Subscriptions` -a se desejar pesquisar em todas as assinaturas às quais você tem acesso, uma pode definir o PSDefaultParameterValues para `Search-AzGraph` o cmdlet executando`$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
## <a name="clean-up-resources"></a>Limpar recursos

Se quiser remover o módulo Resource Graph do seu ambiente do Azure PowerShell, poderá fazê-lo com o seguinte comando:

```azurepowershell-interactive
# Remove the Resource Graph module from the Azure PowerShell environment
Remove-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Este comando não elimina o ficheiro do módulo que transferiu anteriormente. Apenas o remove da sessão do PowerShell.

## <a name="next-steps"></a>Passos Seguintes

- Obter mais informações sobre a [linguagem de consulta](./concepts/query-language.md)
- Aprender a [explorar recursos](./concepts/explore-resources.md)
- Executar a primeira consulta com a [CLI do Azure](first-query-azurecli.md)
- Ver exemplos de [Consultas de introdução](./samples/starter.md)
- Ver exemplos de [Consultas avançadas](./samples/advanced.md)
- Enviar comentários sobre o [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)
