---
title: Como direcionar Azure Functions versões de tempo de execução
description: O Azure Functions dá suporte a várias versões do tempo de execução. Saiba como especificar a versão de tempo de execução de um aplicativo de funções hospedado no Azure.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: glenga
ms.openlocfilehash: c30dbad9e2d433920ade6890eabd85f083f9d968
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596839"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Como direcionar Azure Functions versões de tempo de execução

Um aplicativo de funções é executado em uma versão específica do tempo de execução de Azure Functions. Há duas versões principais: [1. x e 2. x](functions-versions.md), com a versão 3. x na visualização. Por padrão, os aplicativos de funções que são criados versão 2. x do tempo de execução. Este artigo explica como configurar um aplicativo de funções no Azure para ser executado na versão que você escolher. Para obter informações sobre como configurar um ambiente de desenvolvimento local para uma versão específica, consulte [código e teste Azure Functions localmente](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Atualizações automáticas e manuais de versão

Azure Functions permite que você direcione uma versão específica do tempo de execução usando a configuração de aplicativo `FUNCTIONS_EXTENSION_VERSION` em um aplicativo de funções. O aplicativo de funções é mantido na versão principal especificada até que você escolha explicitamente mover para uma nova versão.

Se você especificar apenas a versão principal, o aplicativo de funções será atualizado automaticamente para novas versões secundárias do tempo de execução quando eles forem disponibilizados. As novas versões secundárias não introduzem alterações significativas. Se você especificar uma versão secundária (por exemplo, "2.0.12345"), o aplicativo de funções será fixado nessa versão específica até que você a altere explicitamente.

> [!NOTE]
> Se você fixar em uma versão específica do Azure Functions e tentar publicar no Azure usando o Visual Studio, uma janela de diálogo será exibida solicitando que você atualize para a versão mais recente ou cancele a publicação. Para evitar isso, adicione a propriedade `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` em seu arquivo de `.csproj`.

Quando uma nova versão estiver disponível publicamente, um prompt no portal lhe dará a chance de se mover para essa versão. Depois de mover para uma nova versão, você sempre pode usar a configuração `FUNCTIONS_EXTENSION_VERSION` aplicativo para voltar para uma versão anterior.

A tabela a seguir mostra os valores de `FUNCTIONS_EXTENSION_VERSION` para cada versão principal para habilitar as atualizações automáticas:

| Versão principal | valor `FUNCTIONS_EXTENSION_VERSION` |
| ------------- | ----------------------------------- |
| 3. x (visualização) | `~3` |
| 2.x  | `~2` |
| 1.x | `~1` |

Uma alteração na versão de tempo de execução faz com que um aplicativo de funções seja reiniciado.

## <a name="view-and-update-the-current-runtime-version"></a>Exibir e atualizar a versão de tempo de execução atual

Você pode alterar a versão de tempo de execução usada pelo seu aplicativo de funções. Devido ao potencial de alterações significativas, você só pode alterar a versão de tempo de execução antes de criar qualquer função em seu aplicativo de funções. 

> [!IMPORTANT]
> Embora a versão de tempo de execução seja determinada pela configuração de `FUNCTIONS_EXTENSION_VERSION`, você deve fazer essa alteração na portal do Azure e não alterando a configuração diretamente. Isso ocorre porque o portal valida as alterações e faz outras alterações relacionadas, conforme necessário.

### <a name="from-the-azure-portal"></a>da portal do Azure

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Usando o portal do Azure, você não pode alterar a versão de tempo de execução de um aplicativo de funções que já contém funções.

### <a name="view-and-update-the-runtime-version-using-azure-cli"></a>Da CLI do Azure

Você também pode exibir e definir o `FUNCTIONS_EXTENSION_VERSION` do CLI do Azure.

>[!NOTE]
>Como outras configurações podem ser afetadas pela versão de tempo de execução, você deve alterar a versão no Portal. O portal automaticamente faz as outras atualizações necessárias, como a versão do node. js e a pilha de tempo de execução, quando você altera as versões de tempo de execução.  

Usando o CLI do Azure, exiba a versão de tempo de execução atual com o comando [AZ functionapp config appSettings Set](/cli/azure/functionapp/config/appsettings) .

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Nesse código, substitua `<function_app>` pelo nome do seu aplicativo de funções. Substitua também `<my_resource_group>` pelo nome do grupo de recursos para seu aplicativo de funções. 

Você verá a `FUNCTIONS_EXTENSION_VERSION` na seguinte saída, que foi truncada para maior clareza:

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

Você pode atualizar a configuração de `FUNCTIONS_EXTENSION_VERSION` no aplicativo de funções com o comando [AZ functionapp config appSettings Set](/cli/azure/functionapp/config/appsettings) .

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Substitua `<function_app>` pelo nome do seu aplicativo de funções. Substitua também `<my_resource_group>` pelo nome do grupo de recursos para seu aplicativo de funções. Além disso, substitua `<version>` por uma versão válida do tempo de execução 1. x ou `~2` para a versão 2. x.

Você pode executar esse comando no [Azure cloud Shell](../cloud-shell/overview.md) escolhendo **experimentar** no exemplo de código anterior. Você também pode usar o [CLI do Azure localmente](/cli/azure/install-azure-cli) para executar esse comando depois de executar [AZ login](/cli/azure/reference-index#az-login) para entrar.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Direcione o tempo de execução de 2,0 em seu ambiente de desenvolvimento local](functions-run-local.md)

> [!div class="nextstepaction"]
> [Consulte notas de versão para versões de tempo de execução](https://github.com/Azure/azure-webjobs-sdk-script/releases)
