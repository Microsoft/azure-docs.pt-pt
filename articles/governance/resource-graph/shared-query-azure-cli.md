---
title: 'Quickstart: Criar uma consulta partilhada com o Azure CLI'
description: Neste arranque rápido, siga os passos para ativar a extensão do Gráfico de Recurso para O Azure CLI e crie uma consulta partilhada.
ms.date: 06/29/2020
ms.topic: quickstart
ms.openlocfilehash: c888cee5899c45747db1775ffdfbc0d0c78e00c7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91667661"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-cli"></a>Quickstart: Criar uma consulta partilhada de gráficos de recurso usando O Azure CLI

O primeiro passo para a utilização do Azure Resource Graph com [O Azure CLI](/cli/azure/) é verificar se a extensão está instalada. Este início rápido explica-lhe o processo para adicionar a extensão à instalação da CLI do Azure. Pode utilizar a extensão com a CLI do Azure instalada localmente ou através do [Azure Cloud Shell](https://shell.azure.com).

No final deste processo, terá adicionado a extensão à sua instalação de eleição Azure CLI e criará uma consulta partilhada de Gráfico de Recurso.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>Adicionar a extensão do Resource Graph

Para permitir que o Azure CLI trabalhe com o Azure Resource Graph, a extensão deve ser adicionada. Esta extensão funciona onde quer que a CLI do Azure possa ser utilizada, incluindo [bash no Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (autónomo e no portal), a [imagem do Docker da CLI do Azure](https://hub.docker.com/_/microsoft-azure-cli), ou instalada localmente.

1. Verifique se o último CLI do Azure está instalado (pelo menos **2.8.0**). Se não estiver ainda instalado, siga [estas instruções](/cli/azure/install-azure-cli-windows).

1. No seu ambiente de eleição Azure CLI, use [a extensão az adicionar](/cli/azure/extension#az-extension-add) para importar a extensão de Gráfico de Recurso com o seguinte comando:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Validar que a extensão foi instalada e é a versão prevista (pelo menos **1.1.0**) com [a lista de extensões az](/cli/azure/extension#az-extension-list):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="create-a-resource-graph-shared-query"></a>Criar uma consulta partilhada de gráficos de recurso

Com a extensão Azure CLI adicionada ao seu ambiente de eleição, é hora de uma consulta partilhada do Gráfico de Recurso. A consulta partilhada é um objeto Azure Resource Manager que pode conceder permissão ou executar no Azure Resource Graph Explorer. A consulta resume a contagem de todos os recursos agrupados por _localização._

1. Crie um grupo de recursos com [o grupo Az criar](/cli/azure/group#az-group-create) para armazenar a consulta partilhada do Azure Resource Graph. Este grupo de recursos foi nomeado `resource-graph-queries` e a localização é `westus2` .

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Create the resource group
   az group create --name 'resource-graph-queries' --location 'westus2'
   ```

1. Crie a consulta partilhada Azure Resource Graph utilizando o comando de partilha de extensão e gráfico `graph` [az gráfico:](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-create)

   ```azurecli-interactive
   # Create the Azure Resource Graph shared query
   az graph shared-query create --name 'Summarize resources by location' \
      --description 'This shared query summarizes resources by location for a pinnable map graphic.' \
      --graph-query 'Resources | summarize count() by location' \
      --resource-group 'resource-graph-queries'
   ```

1. Listar as consultas partilhadas no novo grupo de recursos. O comando [da lista de consultas partilhadas az gráfico](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-list) devolve uma série de valores.

   ```azurecli-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   az graph shared-query list --resource-group 'resource-graph-queries'
   ```

1. Para obter apenas um único resultado de consulta partilhada, use o comando [de show de consulta partilhada de gráficos az.](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-show)

   ```azurecli-interactive
   # Show a specific Azure Resource Graph shared query
   az graph shared-query show --resource-group 'resource-graph-queries' \
      --name 'Summarize resources by location'
   ```

1. Executar a consulta partilhada em Azure CLI com a `{{shared-query-uri}}` sintaxe num comando [de consulta de gráfico az.](/cli/azure/ext/resource-graph/graph#ext-resource-graph-az-graph-query)
   Primeiro, copie o `id` campo do resultado do comando `show` acima. Substitua `shared-query-uri` o texto no exemplo pelo valor do `id` campo, mas deixe o ambiente e os `{{` `}}` caracteres.

   ```azurecli-interactive
   # Run a Azure Resource Graph shared query
   az graph query --graph-query "{{shared-query-uri}}"
   ```

   > [!NOTE]
   > A `{{shared-query-uri}}` sintaxe é uma **funcionalidade de pré-visualização.**

Outra forma de encontrar consultas partilhadas do Gráfico de Recurso é através do portal Azure. No portal, utilize a barra de pesquisa para procurar "consultas de gráficos de recurso". Selecione a consulta partilhada. Na página **'Vista Geral',** o separador **'Consulta'** apresenta a consulta guardada. O botão **Editar** abre-o no [Explorador de Gráficos de Recurso.](./first-query-portal.md)

## <a name="clean-up-resources"></a>Limpar recursos

Se desejar remover a consulta partilhada do Gráfico de Recurso, o grupo de recursos e a extensão do seu ambiente Azure CLI, pode fazê-lo utilizando os seguintes comandos:

- [az gráfico compartilhado-consulta excluir](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-delete)
- [az group delete](/cli/azure/group#az-group-delete)
- [extensão az remover](/cli/azure/extension#az-extension-remove)

```azurecli-interactive
# Delete the Azure Resource Graph shared query
az graph shared-query delete --resource-group 'resource-graph-queries' \
   --name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group without prompting for confirmation
az group delete --resource-group 'resource-graph-queries' --yes

# Remove the Azure Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, adicionou a extensão do Gráfico de Recursos ao seu ambiente Azure CLI e criou uma consulta partilhada. Para saber mais sobre a linguagem Resource Graph, continue na página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obtenha mais informações sobre o idioma de consulta](./concepts/query-language.md)