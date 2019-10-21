---
title: Visão geral das versões do Azure Functions Runtime
description: O Azure Functions dá suporte a várias versões do tempo de execução. Aprenda as diferenças entre elas e como escolher a mais adequada para você.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: glenga
ms.openlocfilehash: 9ca7006bb842cbe235d2e982e611613e1fd74ed9
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597393"
---
# <a name="azure-functions-runtime-versions-overview"></a>Visão geral das versões do Azure Functions Runtime

As versões principais do tempo de execução do Azure Functions estão relacionadas à versão do .NET na qual o tempo de execução se baseia. A tabela a seguir indica a versão atual do tempo de execução, o nível de versão e a versão do .NET relacionada. 

| Versão de tempo de execução | Nível de liberação<sup>1</sup> | Versão do .NET | 
| --------------- | ------------- | ------------ |
| Win  | pré-visualização | .NET Core 3. x | 
| 2.x | GA (Geographic Availability, Disponibilidade Geográfica) | .NET Core 2.2 |
| 1.x | GA<sup>2</sup> | .NET Framework 4,6<sup>3</sup> |

<sup>1</sup> As versões de GA têm suporte para cenários de produção.   
<sup>2</sup> A versão 1. x está no modo de manutenção. Os aprimoramentos são fornecidos somente em versões posteriores.   
<sup>3</sup> O só dá suporte ao desenvolvimento no portal do Azure ou localmente em computadores com Windows.

>[!NOTE]  
> A versão 3. x do tempo de execução do Functions está em versão prévia e não tem suporte para ambientes de produção. Para obter mais informações sobre como experimentar a versão 3. x, consulte [este comunicado](https://dev.to/azure/develop-azure-functions-using-net-core-3-0-gcm).

Este artigo detalha algumas das diferenças entre as várias versões, como você pode criar cada versão e como alterar versões.

## <a name="languages"></a>Idiomas

A partir da versão 2. x, o tempo de execução usa um modelo de extensibilidade de linguagem e todas as funções em um aplicativo de funções devem compartilhar o mesmo idioma. O idioma das funções em um aplicativo de funções é escolhido ao criar o aplicativo e é mantido na configuração [\_WORKER \_RUNTIME do Functions](functions-app-settings.md#functions_worker_runtime) . 

As linguagens experimentais Azure Functions 1. x não podem usar o novo modelo, portanto, não há suporte no 2. x. A tabela a seguir indica quais linguagens de programação têm suporte no momento em cada versão de tempo de execução.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Para obter mais informações, veja [Idiomas suportados](supported-languages.md).

## <a name="creating-1x-apps"></a>Executar em uma versão específica

Por padrão, os aplicativos de funções criados no portal do Azure e pelo CLI do Azure são definidos como a versão 2. x. Quando possível, você deve usar essa versão de tempo de execução. Se precisar, você ainda poderá executar um aplicativo de funções no tempo de execução da versão 1. x. Você só pode alterar a versão de tempo de execução depois de criar seu aplicativo de funções, mas antes de adicionar qualquer função. Para saber como fixar a versão de tempo de execução para 1. x, consulte [Exibir e atualizar a versão de tempo de execução atual](set-runtime-version.md#view-and-update-the-current-runtime-version).

Você também pode atualizar para a versão 3. x do tempo de execução, que está em visualização. Faça isso se você precisar ser capaz de executar suas funções no .NET Core 3. x. Para saber como atualizar para o 3. x, consulte [Exibir e atualizar a versão de tempo de execução atual](set-runtime-version.md#view-and-update-the-current-runtime-version).

## <a name="migrating-from-1x-to-later-versions"></a>Migrando do 1. x para versões posteriores

Você pode optar por migrar um aplicativo existente gravado para usar o tempo de execução da versão 1. x para, em vez disso, usar a versão 2. x. A maioria das alterações que você precisa fazer está relacionada a alterações no tempo de execução de linguagem, C# como alterações de API entre o .NET Framework 4,7 e o .NET Core 2. Você também precisará certificar-se de que seu código e suas bibliotecas são compatíveis com o Language Runtime escolhido. Por fim, lembre-se de anotar as alterações em gatilho, associações e recursos realçados abaixo. Para obter os melhores resultados de migração, você deve criar um novo aplicativo de funções para a versão 2. x e portar seu código de função existente da versão 1. x para o novo aplicativo.  

### <a name="changes-in-triggers-and-bindings"></a>Alterações em gatilhos e associações

A versão 2. x exige que você instale as extensões para gatilhos e associações específicas usados pelas funções em seu aplicativo. A única exceção para esses gatilhos HTTP e Timer, que não exigem uma extensão.  Para obter mais informações, consulte [registrar e instalar extensões de associação](./functions-bindings-register.md).

Também há algumas alterações no `function.json` ou atributos da função entre as versões. Por exemplo, a propriedade `path` do hub de eventos agora é `eventHubName`. Consulte a [tabela de associação existente](#bindings) para obter links para a documentação de cada associação.

### <a name="changes-in-features-and-functionality"></a>Alterações nos recursos e funcionalidades

Alguns recursos que também foram removidos, atualizados ou substituídos na nova versão. Esta seção detalha as alterações que você vê na versão 2. x após ter usado a versão 1. x.

Na versão 2. x, as seguintes alterações foram feitas:

* As chaves para chamar pontos de extremidade HTTP são sempre armazenadas criptografadas no armazenamento de BLOBs do Azure. Na versão 1. x, as chaves armazenadas no armazenamento de arquivos do Azure são padrão. Ao atualizar um aplicativo da versão 1. x para a versão 2. x, os segredos existentes que estão no armazenamento de arquivos são redefinidos.

* O tempo de execução da versão 2. x não inclui suporte interno para provedores de webhook. Essa alteração foi feita para melhorar o desempenho. Você ainda pode usar gatilhos HTTP como pontos de extremidade para WebHooks.

* O arquivo de configuração do host (host. JSON) deve estar vazio ou ter a cadeia de caracteres `"version": "2.0"`.

* Para melhorar o monitoramento, o painel trabalhos Web no portal, que usou a configuração [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) , é substituído por aplicativo Azure insights, que usa a configuração [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) . Para obter mais informações, consulte [monitorar Azure Functions](functions-monitoring.md).

* Todas as funções em um aplicativo de funções devem compartilhar o mesmo idioma. Ao criar um aplicativo de funções, você deve escolher uma pilha de tempo de execução para o aplicativo. A pilha de tempo de execução é especificada pelo valor [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) nas configurações do aplicativo. Esse requisito foi adicionado para melhorar a superfície e o tempo de inicialização. Ao desenvolver localmente, você também deve incluir essa configuração no [arquivo local. Settings. JSON](functions-run-local.md#local-settings-file).

* O tempo limite padrão para funções em um plano do serviço de aplicativo é alterado para 30 minutos. Você pode alterar manualmente o tempo limite de volta para ilimitado usando a configuração [functionTimeout](functions-host-json.md#functiontimeout) em host. JSON.

* As restrições de simultaneidade HTTP são implementadas por padrão para funções de plano de consumo, com um padrão de 100 solicitações simultâneas por instância. Você pode alterar isso na configuração de [`maxConcurrentRequests`](functions-host-json.md#http) no arquivo host. JSON.

* Devido a [limitações do .NET Core](https://github.com/Azure/azure-functions-host/issues/3414), o F# suporte para funções de script (. fsx) foi removido. As F# funções compiladas (. FS) ainda têm suporte.

* O formato da URL dos WebHooks do gatilho da grade de eventos foi alterado para `https://{app}/runtime/webhooks/{triggerName}`.

### <a name="migrating-a-locally-developed-application"></a>Migrando um aplicativo desenvolvido localmente

Você pode ter projetos de aplicativo de funções existentes que você desenvolveu localmente usando o tempo de execução da versão 1. x. Para atualizar para a versão 2. x, você deve criar um projeto de aplicativo de função local com a versão 2. x e portar seu código existente para o novo aplicativo. Você pode atualizar manualmente o projeto e o código existentes, um tipo de atualização "in-loco". No entanto, há uma série de outros aprimoramentos entre a versão 1. x e a versão 2. x que talvez você ainda precise fazer. Por exemplo, no C# objeto de depuração foi alterado de `TraceWriter` para `ILogger`. Ao criar um novo projeto de versão 2. x, você começa com as funções atualizadas com base nos modelos da versão 2. x mais recentes.

#### <a name="visual-studio-runtime-versions"></a>Versões de tempo de execução do Visual Studio

No Visual Studio, você seleciona a versão de tempo de execução ao criar um projeto. O Azure Functions Tools para Visual Studio dá suporte às principais versões de tempo de execução. A versão correta é usada ao depurar e publicar com base nas configurações do projeto. As configurações de versão são definidas no arquivo de `.csproj` nas seguintes propriedades:

##### <a name="version-1x"></a>Versão 1. x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Versão 2. x

```xml
<TargetFramework>netcoreapp2.2</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Quando você depura ou publica seu projeto, a versão correta do tempo de execução é usada.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code e Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) é usado para o desenvolvimento de linha de comando e também pela [extensão de Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code. Para desenvolver na versão 2. x, instale a versão 2. x das principais ferramentas. O desenvolvimento da versão 1. x requer a versão 1. x das principais ferramentas. Para obter mais informações, consulte [instalar o Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

Para o desenvolvimento de Visual Studio Code, talvez você também precise atualizar a configuração de usuário do `azureFunctions.projectRuntime` para corresponder à versão das ferramentas instaladas.  Essa configuração também atualiza os modelos e os idiomas usados durante a criação do aplicativo de funções.

### <a name="changing-version-of-apps-in-azure"></a>Alterando a versão dos aplicativos no Azure

A versão do tempo de execução do Functions usada por aplicativos publicados no Azure é ditada pela configuração do aplicativo [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) . Um valor de `~2` tem como alvo o tempo de execução da versão 2. x e `~1` tem como alvo o tempo de execução da versão 1. x. Não altere arbitrariamente essa configuração, pois é provável que outras alterações de configuração de aplicativo e alterações de código em suas funções sejam necessárias. Para saber mais sobre a maneira recomendada de migrar seu aplicativo de funções para uma versão de tempo de execução diferente, consulte [como direcionar Azure Functions versões de tempo de execução](set-runtime-version.md).

## <a name="bindings"></a>Enlaces

A partir da versão 2. x, o tempo de execução usa um novo [modelo de extensibilidade de associação](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) que oferece essas vantagens:

* Suporte para extensões de associação de terceiros.

* Desacoplamento de tempo de execução e associações. Essa alteração permite que as extensões de ligação tenham controle de versão e sejam liberadas de forma independente. Você pode, por exemplo, optar por atualizar para uma versão de uma extensão que dependa de uma versão mais recente de um SDK subjacente.

* Um ambiente de execução mais leve, em que apenas as associações em uso são conhecidas e carregadas pelo tempo de execução.

Com exceção dos gatilhos HTTP e Timer, todas as associações devem ser adicionadas explicitamente ao projeto de aplicativo de funções ou registradas no Portal. Para obter mais informações, consulte [registrar extensões de associação](./functions-bindings-expressions-patterns.md).

A tabela a seguir mostra quais associações têm suporte em cada versão de tempo de execução.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte os seguintes recursos:

* [Criar código e testar as Funções do Azure localmente](functions-run-local.md)
* [Como direcionar Azure Functions versões de tempo de execução](set-runtime-version.md)
* [Notas de versão](https://github.com/Azure/azure-functions-host/releases)
