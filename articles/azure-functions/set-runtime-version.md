---
title: Como direcionar as versões de tempo de execução do Azure Functions
description: O Azure Functions suporta várias versões do tempo de execução. Saiba como especificar a versão de tempo de execução de uma aplicação de função hospedada no Azure.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 46bf7849888033b2bbb7e9b9669ee3eae4de10e9
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916529"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Como direcionar as versões de tempo de execução do Azure Functions

Uma aplicação de função executa numa versão específica do tempo de execução das Funções Azure. Existem três versões principais: [1.x, 2.x e 3.x](functions-versions.md). Por predefinição, as aplicações de função são criadas na versão 3.x do tempo de execução. Este artigo explica como configurar uma aplicação de função no Azure para executar na versão que escolher. Para obter informações sobre como configurar um ambiente de desenvolvimento local para uma versão específica, consulte [Código e teste funções Azure localmente](functions-run-local.md).

A forma como direciona manualmente uma versão específica depende se está a executar o Windows ou o Linux.

## <a name="automatic-and-manual-version-updates"></a>Atualizações automáticas e manuais da versão

_Esta secção não se aplica ao executar a sua aplicação de função [no Linux](#manual-version-updates-on-linux)._

O Azure Functions permite-lhe direcionar uma versão específica do tempo de execução no Windows utilizando a definição de `FUNCTIONS_EXTENSION_VERSION` aplicação numa aplicação de função. A aplicação de função é mantida na versão principal especificada até que você explicitamente opte por mudar para uma nova versão. Se especificar apenas a versão principal, a aplicação de função é automaticamente atualizada para novas versões menores do tempo de execução quando ficam disponíveis. As novas versões menores não devem introduzir mudanças de rutura. 

Se especificar uma versão menor (por exemplo, "2.0.12345"), a aplicação de função é fixada a essa versão específica até que a altere explicitamente. Versões menores mais antigas são regularmente removidas do ambiente de produção. Depois de isto ocorrer, a sua aplicação de função é executado na versão mais recente em vez da versão definida em `FUNCTIONS_EXTENSION_VERSION` . Por isso, deve resolver rapidamente quaisquer problemas com a sua app de função que exijam uma versão menor específica, para que possa, em vez disso, direcionar a versão principal. As remoçãos de versão menores são anunciadas nos [anúncios do Serviço de Aplicações](https://github.com/Azure/app-service-announcements/issues).

> [!NOTE]
> Se se fixar numa versão específica e importante de Azure Functions e tentar publicar para o Azure usando o Visual Studio, uma janela de diálogo aparecerá levando-o a atualizar para a versão mais recente ou cancelar a publicação. Para evitar isto, adicione a `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` propriedade no seu `.csproj` arquivo.

Quando uma nova versão está disponível publicamente, um pedido no portal dá-lhe a oportunidade de avançar para essa versão. Depois de mudar para uma nova versão, pode sempre utilizar a definição de `FUNCTIONS_EXTENSION_VERSION` aplicação para voltar a uma versão anterior.

A tabela a seguir mostra os `FUNCTIONS_EXTENSION_VERSION` valores de cada versão principal para permitir atualizações automáticas:

| Versão principal | `FUNCTIONS_EXTENSION_VERSION` valor |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

Uma alteração na versão de tempo de execução faz com que uma aplicação de função reinicie.

## <a name="view-and-update-the-current-runtime-version"></a>Ver e atualizar a versão atual do tempo de execução

_Esta secção não se aplica ao executar a sua aplicação de função [no Linux](#manual-version-updates-on-linux)._

Pode alterar a versão de tempo de execução utilizada pela sua aplicação de função. Devido ao potencial de rutura de alterações, só pode alterar a versão de tempo de execução antes de ter criado quaisquer funções na sua aplicação de função. 

> [!IMPORTANT]
> Embora a versão de tempo de execução seja determinada pela `FUNCTIONS_EXTENSION_VERSION` definição, deve fazer esta alteração no portal Azure e não alterando a definição diretamente. Isto porque o portal valida as suas alterações e faz outras alterações relacionadas conforme necessário.

# <a name="portal"></a>[Portal](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Utilizando o portal Azure, não é possível alterar a versão de tempo de execução para uma aplicação de função que já contém funções.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)

Também pode ver e definir `FUNCTIONS_EXTENSION_VERSION` o Azure CLI.  

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
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

`<FUNCTION_APP>`Substitua-o pelo nome da sua aplicação de função. Substitua também `<RESOURCE_GROUP>` o nome do grupo de recursos para a sua aplicação de função. Além disso, `<VERSION>` substitua-a por uma versão específica, ou `~3` , ou `~2` `~1` .

Pode executar este comando a partir da [Azure Cloud Shell](../cloud-shell/overview.md) escolhendo-o na amostra de código anterior.  Também pode utilizar o [Azure CLI localmente](/cli/azure/install-azure-cli) para executar este comando depois de executar [o login az](/cli/azure/reference-index#az-login) para iniciar sessão.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar o tempo de funcionamento das funções Azure, utilize o seguinte cmdlet: 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

`<FUNCTION_APP>`Substitua-o pelo nome da sua aplicação de função e `<RESOURCE_GROUP>` . O valor atual da `FUNCTIONS_EXTENSION_VERSION` definição é devolvido na tabela de haxixe.

Utilize o seguinte script para alterar o tempo de execução das funções:

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

Como antes, `<FUNCTION_APP>` substitua-o pelo nome da sua aplicação de função e `<RESOURCE_GROUP>` pelo nome do grupo de recursos. Além disso, `<VERSION>` substitua-a pela versão específica ou pela versão principal, como `~2` ou `~3` . Pode verificar o valor atualizado da `FUNCTIONS_EXTENSION_VERSION` definição na tabela de haxixe devolvido. 

---

A aplicação de função reinicia após a alteração da definição da aplicação.

## <a name="manual-version-updates-on-linux"></a>Atualizações de versão manual no Linux

Para fixar uma aplicação de função Linux a uma versão específica do anfitrião, especifica o URL de imagem no campo 'LinuxFxVersion' no site config. Por exemplo: se quisermos fixar uma aplicação de função nó 10 para dizer a versão anfitriã 3.0.13142 -

Para **o serviço de aplicações linux/aplicativos premium elásticos** - Definir para `LinuxFxVersion` `DOCKER|mcr.microsoft.com/azure-functions/node:3.0.13142-node10-appservice` .

Para **aplicações de consumo de linux** - Definir `LinuxFxVersion` para `DOCKER|mcr.microsoft.com/azure-functions/mesh:3.0.13142-node10` .


# <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli-linux)

Pode ver e definir `LinuxFxVersion` o Azure CLI.  

Utilizando o CLI Azure, veja a versão atual do tempo de execução com o comando de exibição de [config do az functionapp.](/cli/azure/functionapp/config)

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group>
```

Neste código, `<function_app>` substitua-o pelo nome da sua aplicação de função. Substitua também `<my_resource_group>` o nome do grupo de recursos para a sua aplicação de função. 

Vê-se `linuxFxVersion` a seguinte saída, que foi truncada para clareza:

```output
{
  ...

  "kind": null,
  "limits": null,
  "linuxFxVersion": <LINUX_FX_VERSION>,
  "loadBalancing": "LeastRequests",
  "localMySqlEnabled": false,
  "location": "West US",
  "logsDirectorySizeLimit": 35,
   ...
}
```

Pode atualizar a `linuxFxVersion` definição na aplicação de função com o comando [de definição de config do az functionapp.](/cli/azure/functionapp/config)

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

`<FUNCTION_APP>`Substitua-o pelo nome da sua aplicação de função. Substitua também `<RESOURCE_GROUP>` o nome do grupo de recursos para a sua aplicação de função. Além disso, `<LINUX_FX_VERSION>` substitua-o pelos valores acima explicados.

Pode executar este comando a partir da [Azure Cloud Shell](../cloud-shell/overview.md) escolhendo-o na amostra de código anterior.  Também pode utilizar o [Azure CLI localmente](/cli/azure/install-azure-cli) para executar este comando depois de executar [o login az](/cli/azure/reference-index#az-login) para iniciar sessão.


Da mesma forma, a aplicação de função reinicia após a alteração ser feita para o site config.

> [!NOTE]
> Note que a definição `LinuxFxVersion` para url de imagem diretamente para aplicações de consumo irá optá-las fora de espaços reservados e outras otimizações de início a frio.

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Direcione o tempo de execução 2.0 no seu ambiente de desenvolvimento local](functions-run-local.md)

> [!div class="nextstepaction"]
> [Ver notas de lançamento para versões de tempo de execução](https://github.com/Azure/azure-webjobs-sdk-script/releases)
