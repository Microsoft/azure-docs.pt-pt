---
title: Como direcionar as versões de tempo de execução do Azure Functions
description: O Azure Functions suporta várias versões do tempo de execução. Saiba como especificar a versão de tempo de execução de uma aplicação de função hospedada no Azure.
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 5a71338b1b9735d7e7494dc2667bd7addf5d4a53
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77151960"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Como direcionar as versões de tempo de execução do Azure Functions

Uma aplicação de função executa numa versão específica do tempo de execução das Funções Azure. Existem três versões principais: [1.x, 2.x e 3.x](functions-versions.md). Por predefinição, as aplicações de função são criadas na versão 2.x do tempo de execução. Este artigo explica como configurar uma aplicação de função no Azure para executar na versão que escolher. Para obter informações sobre como configurar um ambiente de desenvolvimento local para uma versão específica, consulte [Código e teste funções Azure localmente](functions-run-local.md).

## <a name="automatic-and-manual-version-updates"></a>Atualizações automáticas e manuais da versão

As Funções Azure permitem-lhe direcionar uma versão específica do tempo de execução utilizando a definição de `FUNCTIONS_EXTENSION_VERSION` aplicação numa aplicação de função. A aplicação de função é mantida na versão principal especificada até que você explicitamente opte por mudar para uma nova versão.

Se especificar apenas a versão principal, a aplicação de função é automaticamente atualizada para novas versões menores do tempo de execução quando ficam disponíveis. As novas versões menores não devem introduzir mudanças de rutura. Se especificar uma versão menor (por exemplo, "2.0.12345"), a aplicação de função é fixada a essa versão específica até que a altere explicitamente.

> [!NOTE]
> Se se fixar numa versão específica de Azure Functions e tentar publicar para a Azure usando o Visual Studio, uma janela de diálogo aparecerá levando-o a atualizar para a versão mais recente ou cancelar a publicação. Para evitar isto, adicione a `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` propriedade no seu `.csproj` arquivo.

Quando uma nova versão está disponível publicamente, um pedido no portal dá-lhe a oportunidade de avançar para essa versão. Depois de mudar para uma nova versão, pode sempre utilizar a definição de `FUNCTIONS_EXTENSION_VERSION` aplicação para voltar a uma versão anterior.

A tabela a seguir mostra os `FUNCTIONS_EXTENSION_VERSION` valores de cada versão principal para permitir atualizações automáticas:

| Versão principal | `FUNCTIONS_EXTENSION_VERSION`valor |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

Uma alteração na versão de tempo de execução faz com que uma aplicação de função reinicie.

## <a name="view-and-update-the-current-runtime-version"></a>Ver e atualizar a versão atual do tempo de execução

Pode alterar a versão de tempo de execução utilizada pela sua aplicação de função. Devido ao potencial de rutura de alterações, só pode alterar a versão de tempo de execução antes de ter criado quaisquer funções na sua aplicação de função. 

> [!IMPORTANT]
> Embora a versão de tempo de execução seja determinada pela `FUNCTIONS_EXTENSION_VERSION` definição, deve fazer esta alteração no portal Azure e não alterando a definição diretamente. Isto porque o portal valida as suas alterações e faz outras alterações relacionadas conforme necessário.

### <a name="from-the-azure-portal"></a>No portal do Azure

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Utilizando o portal Azure, não é possível alterar a versão de tempo de execução para uma aplicação de função que já contém funções.

### <a name="from-the-azure-cli"></a><a name="view-and-update-the-runtime-version-using-azure-cli"></a>Do Azure CLI

Também pode ver e definir `FUNCTIONS_EXTENSION_VERSION` o Azure CLI.

>[!NOTE]
>Uma vez que outras definições podem ser impactadas pela versão de tempo de execução, deve alterar a versão no portal. O portal faz automaticamente as outras atualizações necessárias, como Node.js versão e stack de tempo de execução, quando altera as versões de tempo de execução.  

Utilizando o CLI Azure, veja a versão atual do tempo de execução com o comando [de configuração de appsettings az functionapp.](/cli/azure/functionapp/config/appsettings)

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Neste código, `<function_app>` substitua-o pelo nome da sua aplicação de função. Substitua também `<my_resource_group>` o nome do grupo de recursos para a sua aplicação de função. 

Vê-se `FUNCTIONS_EXTENSION_VERSION` a seguinte saída, que foi truncada para clareza:

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

Pode atualizar a `FUNCTIONS_EXTENSION_VERSION` definição na aplicação de função com o comando de configuração de configuração de [appsettings az functionapp.](/cli/azure/functionapp/config/appsettings)

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

`<function_app>`Substitua-o pelo nome da sua aplicação de função. Substitua também `<my_resource_group>` o nome do grupo de recursos para a sua aplicação de função. Além disso, `<version>` substitua por uma versão válida do tempo de execução 1.x ou `~2` pela versão 2.x.

Pode executar este comando a partir da [Azure Cloud Shell](../cloud-shell/overview.md) escolhendo-o na amostra de código anterior. **Try it** Também pode utilizar o [Azure CLI localmente](/cli/azure/install-azure-cli) para executar este comando depois de executar [o login az](/cli/azure/reference-index#az-login) para iniciar sessão.



## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Direcione o tempo de execução 2.0 no seu ambiente de desenvolvimento local](functions-run-local.md)

> [!div class="nextstepaction"]
> [Ver notas de lançamento para versões de tempo de execução](https://github.com/Azure/azure-webjobs-sdk-script/releases)
