---
title: Executar a sua primeira consulta com a CLI do Azure
description: Este artigo explica os passos para ativar a extensão do Resource Graph para a CLI do Azure e executar a sua primeira consulta.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/22/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 47f3c4bdf21d751cd3804c6a854e7f8706b0815a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767962"
---
# <a name="run-your-first-resource-graph-query-using-azure-cli"></a>Executar a primeira consulta do Resource Graph com a CLI do Azure

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

1. Confirme que a extensão foi instalada e é a versão esperada (no mínimo, a **0.1.7**):

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
   az graph query -q 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Como este exemplo de consulta não fornece um modificador de ordenação como `order by`, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido.

1. Atualize a consulta para `order by` propriedade **Nome**:

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Assim como na primeira consulta, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido. A ordem dos comandos da consulta é importante. Neste exemplo, `order by` vem depois de `limit`, ou seja, primeiro limita os resultados da consulta e, em seguida, ordena-os.

1. Atualize a consulta para primeiro `order by` propriedade **Nome** e, em seguida, `limit` os cinco resultados principais:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'project name, type | order by name asc | limit 5'
   ```

Quando a consulta final é executada várias vezes, partindo do princípio de que nada no seu ambiente está a mudar, os resultados devolvidos serão consistentes e conforme o esperado – ordenados pela propriedade **Nome**, mas continuam a ser limitados aos cinco resultados principais.

## <a name="cleanup"></a>Limpeza

Se pretender remover a extensão do Resource Graph do seu ambiente da CLI do Azure, poderá fazê-lo com o seguinte comando:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

> [!NOTE]
> Este procedimento de remoção não elimina o ficheiro de extensão que transferiu anteriormente. Apenas o remove do ambiente da CLI do Azure em execução.

## <a name="next-steps"></a>Passos Seguintes

- Obter mais informações sobre a [linguagem de consulta](./concepts/query-language.md)
- Aprender a [explorar recursos](./concepts/explore-resources.md)
- Executar a primeira consulta com o [Azure PowerShell](first-query-powershell.md)
- Ver exemplos de [Consultas de introdução](./samples/starter.md)
- Ver exemplos de [Consultas avançadas](./samples/advanced.md)
- Enviar comentários sobre o [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)