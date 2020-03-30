---
title: Como direcionar as versões de tempo de execução das Funções Azure
description: A Azure Functions suporta várias versões do tempo de funcionamento. Saiba como especificar a versão de tempo de execução de uma aplicação de função hospedada no Azure.
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 5a71338b1b9735d7e7494dc2667bd7addf5d4a53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77151960"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Como direcionar as versões de tempo de execução das Funções Azure

Uma aplicação de função funciona numa versão específica do tempo de funcionamento das Funções Azure. Existem três versões principais: [1.x, 2.x e 3.x](functions-versions.md). Por padrão, as aplicações de função são criadas na versão 2.x do tempo de execução. Este artigo explica como configurar uma aplicação de função no Azure para executar na versão que escolher. Para obter informações sobre como configurar um ambiente de desenvolvimento local para uma versão específica, consulte [Código e teste funções Azure localmente](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Atualizações de versão automática e manual

As Funções Azure permitem-lhe direcionar uma versão `FUNCTIONS_EXTENSION_VERSION` específica do tempo de funcionamento utilizando a definição de aplicação numa aplicação de função. A aplicação de funções é mantida na versão principal especificada até que escolha explicitamente mudar-se para uma nova versão.

Se especificar apenas a versão principal, a aplicação de funções é automaticamente atualizada para novas versões menores do tempo de funcionamento quando estiverem disponíveis. Novas versões menores não devem introduzir mudanças de rutura. Se especificar uma versão menor (por exemplo, "2.0.12345"), a aplicação de função está fixada nessa versão específica até que a altere explicitamente.

> [!NOTE]
> Se fixar numa versão específica das Funções Azure e tentar publicar no Azure usando o Visual Studio, surgirá uma janela de diálogo que o levará a atualizar para a versão mais recente ou cancelar a publicação. Para evitar isso, `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` adicione `.csproj` a propriedade no seu ficheiro.

Quando uma nova versão está disponível publicamente, um pedido no portal dá-lhe a oportunidade de avançar para essa versão. Depois de passar para uma nova `FUNCTIONS_EXTENSION_VERSION` versão, pode sempre utilizar a definição de aplicação para voltar a uma versão anterior.

O quadro seguinte `FUNCTIONS_EXTENSION_VERSION` mostra os valores de cada versão principal para permitir atualizações automáticas:

| Versão principal | `FUNCTIONS_EXTENSION_VERSION`valor |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

Uma alteração na versão tempo de execução faz com que uma aplicação de função reinicie.

## <a name="view-and-update-the-current-runtime-version"></a>Ver e atualizar a versão atual do tempo de execução

Pode alterar a versão de tempo de execução utilizada pela sua aplicação de função. Devido ao potencial de quebra de alterações, só pode alterar a versão tempo de execução antes de ter criado quaisquer funções na sua aplicação de função. 

> [!IMPORTANT]
> Embora a versão de tempo `FUNCTIONS_EXTENSION_VERSION` de execução seja determinada pela definição, deverá efazer esta alteração no portal Azure e não alterando diretamente a definição. Isto porque o portal valida as suas alterações e faz outras alterações relacionadas conforme necessário.

### <a name="from-the-azure-portal"></a>No portal do Azure

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Utilizando o portal Azure, não é possível alterar a versão de tempo de execução para uma aplicação de função que já contém funções.

### <a name="from-the-azure-cli"></a><a name="view-and-update-the-runtime-version-using-azure-cli"></a>Do Azure CLI

Também pode ver e `FUNCTIONS_EXTENSION_VERSION` definir o do Azure CLI.

>[!NOTE]
>Como outras definições podem ser impactadas pela versão tempo de execução, deve alterar a versão no portal. O portal faz automaticamente as outras atualizações necessárias, como a versão Node.js e a stack runtime, quando muda as versões de tempo de execução.  

Utilizando o Azure CLI, veja a versão atual do tempo de execução com o comando de definição de definições de definição de definições de definição de definições de definição de definição de definição de definições de definição de definição de definição de definição de definições de definição de definição de definição de definições de [definição](/cli/azure/functionapp/config/appsettings) de

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Neste código, `<function_app>` substitua-o pelo nome da sua aplicação de funções. Substitua `<my_resource_group>` também o nome do grupo de recursos para a sua aplicação de função. 

Veja a `FUNCTIONS_EXTENSION_VERSION` seguinte saída, que foi truncada para clareza:

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

Pode atualizar `FUNCTIONS_EXTENSION_VERSION` a definição na aplicação de funções com o comando de definição de definições de definição de definições de definição de definição de definição de definição de definição de definição de definição de definição de definição de definição de definição de definição de definição de [definição](/cli/azure/functionapp/config/appsettings)

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Substitua-o `<function_app>` pelo nome da sua aplicação de funções. Substitua `<my_resource_group>` também o nome do grupo de recursos para a sua aplicação de função. Além `<version>` disso, substitua-a por uma `~2` versão válida do tempo de execução 1.x ou para a versão 2.x.

Pode executar este comando a partir da Casca de [Nuvem Azure,](../cloud-shell/overview.md) escolhendo **experimentá-lo** na amostra de código anterior. Também pode utilizar o [Azure CLI localmente](/cli/azure/install-azure-cli) para executar este comando depois de executar [az login](/cli/azure/reference-index#az-login) para iniciar sessão.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Direcione o tempo de execução 2.0 no seu ambiente de desenvolvimento local](functions-run-local.md)

> [!div class="nextstepaction"]
> [Ver notas de lançamento para versões de tempo de execução](https://github.com/Azure/azure-webjobs-sdk-script/releases)
