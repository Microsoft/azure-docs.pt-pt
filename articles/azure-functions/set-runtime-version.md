---
title: Como direcionar Azure Functions versões de tempo de execução
description: A Azure Functions suporta várias versões do tempo de funcionamento. Saiba como especificar a versão de tempo de execução de um aplicativo de funções hospedado no Azure.
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 5a71338b1b9735d7e7494dc2667bd7addf5d4a53
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77151960"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Como direcionar Azure Functions versões de tempo de execução

Um aplicativo de funções é executado em uma versão específica do tempo de execução de Azure Functions. Existem três versões principais: [1.x, 2.x e 3.x](functions-versions.md). Por padrão, os aplicativos de funções são criados na versão 2. x do tempo de execução. Este artigo explica como configurar um aplicativo de funções no Azure para ser executado na versão que você escolher. Para obter informações sobre como configurar um ambiente de desenvolvimento local para uma versão específica, consulte [Código e teste funções Azure localmente](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Atualizações automáticas e manuais de versão

As Funções Azure permitem-lhe direcionar uma versão específica do tempo de funcionamento utilizando a definição de aplicação `FUNCTIONS_EXTENSION_VERSION` numa aplicação de função. O aplicativo de funções é mantido na versão principal especificada até que você escolha explicitamente mover para uma nova versão.

Se você especificar apenas a versão principal, o aplicativo de funções será atualizado automaticamente para novas versões secundárias do tempo de execução quando eles forem disponibilizados. Novas versões menores não devem introduzir mudanças de rutura. Se você especificar uma versão secundária (por exemplo, "2.0.12345"), o aplicativo de funções será fixado nessa versão específica até que você a altere explicitamente.

> [!NOTE]
> Se você fixar em uma versão específica do Azure Functions e tentar publicar no Azure usando o Visual Studio, uma janela de diálogo será exibida solicitando que você atualize para a versão mais recente ou cancele a publicação. Para evitar isso, adicione a propriedade `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` no seu ficheiro `.csproj`.

Quando uma nova versão estiver disponível publicamente, um prompt no portal lhe dará a chance de se mover para essa versão. Depois de passar para uma nova versão, pode sempre utilizar a definição de aplicação `FUNCTIONS_EXTENSION_VERSION` para voltar a uma versão anterior.

O quadro seguinte mostra os valores `FUNCTIONS_EXTENSION_VERSION` para cada versão principal para permitir atualizações automáticas:

| Versão principal | `FUNCTIONS_EXTENSION_VERSION` valor |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

Uma alteração na versão de tempo de execução faz com que um aplicativo de funções seja reiniciado.

## <a name="view-and-update-the-current-runtime-version"></a>Exibir e atualizar a versão de tempo de execução atual

Você pode alterar a versão de tempo de execução usada pelo seu aplicativo de funções. Devido ao potencial de alterações significativas, você só pode alterar a versão de tempo de execução antes de criar qualquer função em seu aplicativo de funções. 

> [!IMPORTANT]
> Embora a versão de tempo de execução seja determinada pela definição `FUNCTIONS_EXTENSION_VERSION`, deverá efazer esta alteração no portal Azure e não alterando diretamente a definição. Isso ocorre porque o portal valida as alterações e faz outras alterações relacionadas, conforme necessário.

### <a name="from-the-azure-portal"></a>No portal do Azure

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Usando o portal do Azure, você não pode alterar a versão de tempo de execução de um aplicativo de funções que já contém funções.

### <a name="view-and-update-the-runtime-version-using-azure-cli"></a>Do Azure CLI

Também pode ver e definir o `FUNCTIONS_EXTENSION_VERSION` do Azure CLI.

>[!NOTE]
>Como outras configurações podem ser afetadas pela versão de tempo de execução, você deve alterar a versão no Portal. O portal automaticamente faz as outras atualizações necessárias, como a versão do node. js e a pilha de tempo de execução, quando você altera as versões de tempo de execução.  

Utilizando o Azure CLI, veja a versão atual do tempo de execução com o comando de definição de definições de definição de definições de definição de definições de definição de definição de definição de definições de definição de definição de definição de definição de definições de definição de definição de definição de definições de [definição](/cli/azure/functionapp/config/appsettings) de

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Neste código, substitua `<function_app>` pelo nome da sua aplicação de funções. Substitua também `<my_resource_group>` com o nome do grupo de recursos para a sua aplicação de função. 

Vê-se o `FUNCTIONS_EXTENSION_VERSION` na seguinte saída, que foi truncada para clareza:

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

Pode atualizar a definição `FUNCTIONS_EXTENSION_VERSION` na aplicação de funções com o comando de definição de definições de definição de definições de definição de definições de definição de definição de definição de definição de definição de definição de definição de definição de definição de definição de definição de [definição](/cli/azure/functionapp/config/appsettings) de defini

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Substitua `<function_app>` pelo nome da sua aplicação de funções. Substitua também `<my_resource_group>` com o nome do grupo de recursos para a sua aplicação de função. Além disso, substitua `<version>` por uma versão válida do tempo de execução 1.x ou `~2` para a versão 2.x.

Pode executar este comando a partir da Casca de [Nuvem Azure,](../cloud-shell/overview.md) escolhendo **experimentá-lo** na amostra de código anterior. Também pode utilizar o [Azure CLI localmente](/cli/azure/install-azure-cli) para executar este comando depois de executar [az login](/cli/azure/reference-index#az-login) para iniciar sessão.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Direcione o tempo de execução 2.0 no seu ambiente de desenvolvimento local](functions-run-local.md)

> [!div class="nextstepaction"]
> [Ver notas de lançamento para versões de tempo de execução](https://github.com/Azure/azure-webjobs-sdk-script/releases)
