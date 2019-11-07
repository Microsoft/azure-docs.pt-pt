---
title: Executar sua primeira consulta usando CLI do Azure
description: Este artigo explica os passos para ativar a extensão do Resource Graph para a CLI do Azure e executar a sua primeira consulta.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 0136b101de870ce669e89e67679719e233343330
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622590"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>Início rápido: executar sua primeira consulta de grafo de recursos usando CLI do Azure

O primeiro passo para utilizar o Azure Resource Graph é garantir que a extensão da [CLI do Azure](/cli/azure/) está instalada. Este início rápido explica-lhe o processo para adicionar a extensão à instalação da CLI do Azure. Pode utilizar a extensão com a CLI do Azure instalada localmente ou através do [Azure Cloud Shell](https://shell.azure.com).

No final deste processo, terá adicionado a extensão à instalação da CLI do Azure à escolha e executado a sua primeira consulta do Resource Graph.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="add-the-resource-graph-extension"></a>Adicionar a extensão do Resource Graph

Para ativar a CLI do Azure para consultar o Azure Resource Graph, tem de adicionar a extensão. Esta extensão funciona onde quer que a CLI do Azure possa ser utilizada, incluindo [bash no Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (autónomo e no portal), a [imagem do Docker da CLI do Azure](https://hub.docker.com/r/microsoft/azure-cli/), ou instalada localmente.

1. Certifique-se que está instalada a CLI do Azure mais recente (no mínimo, a **2.0.45**). Se não estiver ainda instalado, siga [estas instruções](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

1. No ambiente da CLI do Azure à escolha, importe-a com o seguinte comando:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Valide se a extensão foi instalada e se é a versão esperada (pelo menos **1.0.0**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta do Resource Graph

Com a extensão da CLI do Azure adicionada ao seu ambiente escolhido, é altura de experimentar uma consulta simples do Resource Graph. A consulta devolverá os cinco primeiros recursos do Azure com o **Nome** e o **Tipo de Recurso** de cada recurso.

1. Execute a primeira consulta do Azure Resource Graph com a extensão `graph` e o comando `query`:

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Como este exemplo de consulta não fornece um modificador de ordenação como `order by`, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido.

1. Atualize a consulta para `order by` propriedade **Nome**:

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Assim como na primeira consulta, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido. A ordem dos comandos da consulta é importante. Neste exemplo, `order by` vem depois de `limit`, ou seja, primeiro limita os resultados da consulta e, em seguida, ordena-os.

1. Atualize a consulta para primeiro `order by` propriedade **Nome** e, em seguida, `limit` os cinco resultados principais:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'Resources | project name, type | order by name asc | limit 5'
   ```

Quando a consulta final é executada várias vezes, partindo do princípio de que nada no seu ambiente está a mudar, os resultados devolvidos serão consistentes e conforme o esperado – ordenados pela propriedade **Nome**, mas continuam a ser limitados aos cinco resultados principais.

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender remover a extensão do Resource Graph do seu ambiente da CLI do Azure, poderá fazê-lo com o seguinte comando:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Passos seguintes

- Obtenha mais informações sobre a [linguagem de consulta](./concepts/query-language.md).
- Saiba mais sobre como [explorar recursos](./concepts/explore-resources.md).
- Execute sua primeira consulta usando o [portal do Azure](first-query-portal.md).
- Execute sua primeira consulta com [Azure PowerShell](first-query-powershell.md).
- Consulte exemplos de [consultas iniciais](./samples/starter.md).
- Consulte exemplos de [consultas avançadas](./samples/advanced.md).
- Forneça comentários sobre o [UserVoice](https://feedback.azure.com/forums/915958-azure-governance).