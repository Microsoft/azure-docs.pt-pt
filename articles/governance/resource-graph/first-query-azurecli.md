---
title: 'Quickstart: A sua primeira consulta Azure CLI'
description: Neste arranque rápido, siga os passos para ativar a extensão do Gráfico de Recurso para O Azure CLI e executar a sua primeira consulta.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5111f59eb760afda4e206837ca5bdf8bcc201338
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917846"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>Quickstart: Executar a sua primeira consulta de Gráfico de Recursos usando O Azure CLI

O primeiro passo para utilizar o Azure Resource Graph é garantir que a extensão da [CLI do Azure](/cli/azure/) está instalada. Este início rápido explica-lhe o processo para adicionar a extensão à instalação da CLI do Azure. Pode utilizar a extensão com a CLI do Azure instalada localmente ou através do [Azure Cloud Shell](https://shell.azure.com).

No final deste processo, terá adicionado a extensão à instalação da CLI do Azure à escolha e executado a sua primeira consulta do Resource Graph.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>Adicionar a extensão do Resource Graph

Para ativar a CLI do Azure para consultar o Azure Resource Graph, tem de adicionar a extensão. Esta extensão funciona onde quer que a CLI do Azure possa ser utilizada, incluindo [bash no Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (autónomo e no portal), a [imagem do Docker da CLI do Azure](https://hub.docker.com/_/microsoft-azure-cli), ou instalada localmente.

1. Verifique se o último Azure CLI está instalado (pelo menos **2.0.76**). Se não estiver ainda instalado, siga [estas instruções](/cli/azure/install-azure-cli-windows).

1. No ambiente da CLI do Azure à escolha, importe-a com o seguinte comando:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Validar que a extensão foi instalada e é a versão prevista (pelo menos **1.0.0**):

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
   > Assim como na primeira consulta, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido. A ordem dos comandos da consulta é importante. Neste exemplo, `order by` vem depois de `limit`, Esta ordem de comando limita primeiro os resultados da consulta e depois ordena-os.

1. Atualize a consulta para primeiro `order by` propriedade **Nome** e, em seguida, `limit` os cinco resultados principais:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'Resources | project name, type | order by name asc | limit 5'
   ```

Quando a consulta final é executada várias vezes, assumindo que nada no seu ambiente está a mudar, os resultados devolvidos são consistentes e encomendados pela propriedade **Name,** mas ainda limitados aos cinco melhores resultados.

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender remover a extensão do Resource Graph do seu ambiente da CLI do Azure, poderá fazê-lo com o seguinte comando:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, adicionou a extensão do Gráfico de Recursos ao seu ambiente Azure CLI e executou a sua primeira consulta. Para saber mais sobre a linguagem Resource Graph, continue na página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obtenha mais informações sobre o idioma de consulta](./concepts/query-language.md)
