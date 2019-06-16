---
title: Como as versões de tempo de execução de funções do Azure de destino
description: As funções do Azure suporta várias versões do tempo de execução. Saiba como especificar a versão de tempo de execução de uma aplicação de funções alojada no Azure.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: glenga
ms.openlocfilehash: 2047f11272c6154d4443e889eff24401c2f73afa
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067700"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Como as versões de tempo de execução de funções do Azure de destino

Uma aplicação de funções é executado numa versão específica do runtime das funções do Azure. Existem duas versões principais: [1.x e 2.x](functions-versions.md). Por padrão, funcionar aplicações que são criadas versão 2.x do runtime. Este artigo explica como configurar uma aplicação de funções no Azure para executar a versão que escolher. Para obter informações sobre como configurar um ambiente de desenvolvimento local para uma versão específica, consulte [codificar e testar as funções do Azure localmente](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Versão automática e manual de atualizações

As funções do Azure permite-lhe uma versão específica do runtime de destino utilizando o `FUNCTIONS_EXTENSION_VERSION` definição da aplicação na aplicação de funções. A aplicação de funções é mantida na versão principal especificada até explicitamente optar por mover para uma nova versão.

Se especificar apenas a versão principal ("~ 2" para 2.x ou "~ 1" para a versão 1.x), a aplicação de funções é atualizada automaticamente para novas versões secundárias do runtime logo que ficarem disponíveis. Novas versões secundárias não introduz alterações significativas. Se especificar uma versão secundária (por exemplo, "2.0.12345"), a aplicação de função está afixada a essa versão específica, até que altere explicitamente.

> [!NOTE]
> Se afixar para uma versão específica das funções do Azure e, em seguida, tentar publicar para o Azure com o Visual Studio, será apresentada uma janela de caixa de diálogo solicitando a atualizar para a versão mais recente ou cancelar a publicar. Para evitar isto, adicione a `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` propriedade no seu `.csproj` ficheiro.

Quando uma nova versão estiver disponível ao público, uma linha de comandos no portal do dá-lhe a oportunidade de se mover para cima para essa versão. Depois de mover para uma nova versão, pode sempre utilizar o `FUNCTIONS_EXTENSION_VERSION` definição de aplicação para mover de volta para uma versão anterior.

Uma alteração para a versão de tempo de execução faz com que uma aplicação de funções reiniciar.

Os valores podem ser definidos no `FUNCTIONS_EXTENSION_VERSION` definição para ativar as atualizações automáticas de aplicação atualmente é "~ 1" para o runtime 1.x e "~ 2" para 2.x.

## <a name="view-and-update-the-current-runtime-version"></a>Ver e atualizar a versão atual do tempo de execução

Pode alterar a versão de runtime utilizada pela sua aplicação de função. Devido à possibilidade de quebras de código, deve apenas alterar a versão de runtime antes de criar as funções na sua aplicação de função. Embora a versão de runtime é determinada pelo `FUNCTIONS_EXTENSION_VERSION` definição, deve fazer essa alteração no portal do Azure e não ao alterar a definição diretamente. Isto acontece porque o portal valida as suas alterações e faz outras alterações relacionadas, conforme necessário.

### <a name="from-the-azure-portal"></a>No portal do Azure

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Utilizar o portal do Azure, não é possível alterar a versão de runtime para uma aplicação de funções que já contém funções.

### <a name="view-and-update-the-runtime-version-using-azure-cli"></a>Partir da CLI do Azure

Também pode visualizar e definir o `FUNCTIONS_EXTENSION_VERSION` partir da CLI do Azure.

>[!NOTE]
>Porque outras definições, poderão ser afetadas pela versão do tempo de execução, deve alterar a versão no portal. O portal faz automaticamente as outras atualizações necessárias, tais como a pilha de versão e o runtime de node. js, quando altera as versões de tempo de execução.  

Com a CLI do Azure, ver a versão de runtime atual com o [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) comando.

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Nesse código, substitua `<function_app>` com o nome da sua aplicação de função. Substitua, também `<my_resource_group>` com o nome do grupo de recursos para a sua aplicação de função. 

Verá o `FUNCTIONS_EXTENSION_VERSION` na seguinte saída, que foi truncado por motivos de clareza:

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

Pode atualizar o `FUNCTIONS_EXTENSION_VERSION` definição na aplicação de funções com o [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) comando.

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Substitua `<function_app>` com o nome da sua aplicação de função. Substitua, também `<my_resource_group>` com o nome do grupo de recursos para a sua aplicação de função. Além disso, substitua `<version>` com uma versão válida do 1.x runtime ou `~2` para a versão 2.x.

Pode executar este comando a partir do [Azure Cloud Shell](../cloud-shell/overview.md) escolhendo **experimente** no exemplo de código anterior. Também pode utilizar o [localmente a CLI do Azure](/cli/azure/install-azure-cli) para executar este comando após a execução [início de sessão az](/cli/azure/reference-index#az-login) para iniciar sessão.



## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [O tempo de execução 2.0 no seu ambiente de desenvolvimento local de destino](functions-run-local.md)

> [!div class="nextstepaction"]
> [Consulte as notas de versão para versões de tempo de execução](https://github.com/Azure/azure-webjobs-sdk-script/releases)
