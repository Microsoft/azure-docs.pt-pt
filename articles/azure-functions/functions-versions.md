---
title: Visão geral das versões do Azure Functions Runtime
description: O Azure Functions dá suporte a várias versões do tempo de execução. Aprenda as diferenças entre elas e como escolher a mais adequada para você.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 0138af15c89a4792b9ccb73a8b761577fad36c5e
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290381"
---
# <a name="azure-functions-runtime-versions-overview"></a>Visão geral das versões do Azure Functions Runtime

As versões principais do tempo de execução do Azure Functions estão relacionadas à versão do .NET na qual o tempo de execução se baseia. A tabela a seguir indica a versão atual do tempo de execução, o nível de versão e a versão do .NET relacionada. 

| Versão de tempo de execução | Nível de liberação<sup>1</sup> | Versão do .NET | 
| --------------- | ------------- | ------------ |
| Win | GA (Geographic Availability, Disponibilidade Geográfica) | .NET Core 3,1 | 
| 2.x | GA (Geographic Availability, Disponibilidade Geográfica) | .NET Core 2.2 |
| 1.x | GA<sup>2</sup> | .NET Framework 4,6<sup>3</sup> |

<sup>1</sup> as versões de GA têm suporte para cenários de produção.   
<sup>2</sup> a versão 1. x está no modo de manutenção. Os aprimoramentos são fornecidos somente em versões posteriores.   
<sup>3</sup> dá suporte apenas ao desenvolvimento no portal do Azure ou localmente em computadores com Windows.

Este artigo detalha algumas das diferenças entre as várias versões, como você pode criar cada versão e como alterar versões.

## <a name="languages"></a>Idiomas

A partir da versão 2. x, o tempo de execução usa um modelo de extensibilidade de linguagem e todas as funções em um aplicativo de funções devem compartilhar o mesmo idioma. O idioma das funções em um aplicativo de funções é escolhido durante a criação do aplicativo e é mantido na configuração do [\_WORKER\_tempo de execução](functions-app-settings.md#functions_worker_runtime) . 

As linguagens experimentais Azure Functions 1. x não podem usar o novo modelo, portanto, não há suporte no 2. x. A tabela a seguir indica quais linguagens de programação têm suporte no momento em cada versão de tempo de execução.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Para obter mais informações, veja [Idiomas suportados](supported-languages.md).

## <a name="creating-1x-apps"></a>Executar em uma versão específica

Por padrão, os aplicativos de funções criados no portal do Azure e pelo CLI do Azure são definidos como a versão 2. x. Você pode modificar essa versão conforme necessário. Você só pode alterar a versão de tempo de execução para 1. x depois de criar seu aplicativo de funções, mas antes de adicionar qualquer função.  A movimentação entre 2. x e 3. x é permitida mesmo com aplicativos que têm funções, mas ainda é recomendável testar em um novo aplicativo primeiro.

## <a name="migrating-from-1x-to-later-versions"></a>Migrando do 1. x para versões posteriores

Você pode optar por migrar um aplicativo existente gravado para usar o tempo de execução da versão 1. x para, em vez disso, usar uma versão mais recente. A maioria das alterações que você precisa fazer está relacionada a alterações no tempo de execução de linguagem, C# como alterações de API entre o .NET Framework 4,7 e o .NET Core. Você também precisará certificar-se de que seu código e suas bibliotecas são compatíveis com o Language Runtime escolhido. Por fim, lembre-se de anotar as alterações em gatilho, associações e recursos realçados abaixo. Para obter os melhores resultados de migração, você deve criar um novo aplicativo de funções em uma nova versão e portar seu código de função existente da versão 1. x para o novo aplicativo.  

Embora seja possível fazer uma atualização "in-loco" atualizando manualmente a configuração do aplicativo, passar de 1. x para uma versão superior inclui algumas alterações significativas. Por exemplo, no C#, o objeto de depuração é alterado de `TraceWriter` para `ILogger`. Ao criar um novo projeto de versão 3. x, você começa com as funções atualizadas com base nos modelos de versão 3. x mais recentes.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Alterações em gatilhos e associações após a versão 1. x

A partir da versão 2. x, você deve instalar as extensões para gatilhos e associações específicas usadas pelas funções em seu aplicativo. A única exceção para esses gatilhos HTTP e Timer, que não exigem uma extensão.  Para obter mais informações, consulte [registrar e instalar extensões de associação](./functions-bindings-register.md).

Também há algumas alterações no *Function. JSON* ou atributos da função entre versões. Por exemplo, a propriedade `path` do hub de eventos agora é `eventHubName`. Consulte a [tabela de associação existente](#bindings) para obter links para a documentação de cada associação.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Alterações nos recursos e funcionalidade após a versão 1. x

Alguns recursos foram removidos, atualizados ou substituídos após a versão 1. x. Esta seção detalha as alterações que você vê em versões posteriores depois de ter usado a versão 1. x.

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

## <a name="migrating-from-2x-to-3x"></a>Migrando de 2. x para 3. x

Azure Functions versão 3. x é compatível com versões anteriores à versão 2. x.  Muitos aplicativos devem ser capazes de atualizar com segurança para 3. x sem nenhuma alteração de código.  Embora a mudança para 3. x seja incentivada, execute testes extensivos antes de alterar a versão principal em aplicativos de produção.

### <a name="breaking-changes-between-2x-and-3x"></a>Alterações significativas entre 2. x e 3. x

A seguir estão as alterações a serem observadas antes de atualizar um aplicativo 2. x para 3. x.

#### <a name="javascript"></a>Javascript

* As associações de saída atribuídas por meio de `context.done` ou valores de retorno agora se comportam da mesma forma que a configuração em `context.bindings`.

* O objeto de gatilho de temporizador é camelCase em vez de PascalCase

* As funções disparadas pelo hub de eventos com `dataType` binário receberão uma matriz de `binary` em vez de `string`.

* A carga de solicitação HTTP não pode mais ser acessada via `context.bindingData.req`.  Ele ainda pode ser acessado como um parâmetro de entrada, `context.req`e em `context.bindings`.

* Não há mais suporte para node. js 8 e ele não será executado em funções 3. x.

#### <a name="net"></a>.NET

* [As operações de servidor síncronas são desabilitadas por padrão](https://docs.microsoft.com/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

### <a name="changing-version-of-apps-in-azure"></a>Alterando a versão dos aplicativos no Azure

A versão do tempo de execução do Functions usada por aplicativos publicados no Azure é ditada pela configuração do aplicativo [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) . Há suporte para os seguintes valores de versão de tempo de execução principais:

| Valor | Destino de tempo de execução |
| ------ | -------- |
| `~3` | Win |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> Não altere arbitrariamente essa configuração, pois outras alterações de configuração de aplicativo e alterações no código de função podem ser necessárias.

### <a name="locally-developed-application-versions"></a>Versões do aplicativo desenvolvidas localmente

Você pode fazer as seguintes atualizações para que os aplicativos funcionem para alterar localmente as versões de destino.

#### <a name="visual-studio-runtime-versions"></a>Versões de tempo de execução do Visual Studio

No Visual Studio, você seleciona a versão de tempo de execução ao criar um projeto. O Azure Functions Tools para Visual Studio dá suporte às três principais versões de tempo de execução. A versão correta é usada ao depurar e publicar com base nas configurações do projeto. As configurações de versão são definidas no arquivo de `.csproj` nas seguintes propriedades:

##### <a name="version-1x"></a>Versão 1. x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Versão 2. x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>Versão 3. x

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Azure Functions 3. x e .NET requer que a extensão `Microsoft.NET.Sdk.Functions` seja pelo menos `3.0.0`.

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>Atualizando aplicativos 2. x para 3. x no Visual Studio

Você pode abrir uma função existente com destino 2. x e mover para 3. x editando o arquivo de `.csproj` e atualizando os valores acima.  O Visual Studio gerencia as versões de tempo de execução automaticamente com base nos metadados do projeto.  No entanto, é possível se você nunca tiver criado um aplicativo 3. x antes que o Visual Studio ainda não tenha os modelos e o tempo de execução para 3. x em seu computador.  Isso pode se apresentar com um erro como "nenhum tempo de execução do Functions disponível que corresponde à versão especificada no projeto".  Para buscar os modelos e o tempo de execução mais recentes, percorra a experiência para criar um novo projeto de função.  Quando chegar à tela de seleção de versão e modelo, aguarde até que o Visual Studio conclua a busca dos modelos mais recentes.  Depois que os modelos do .NET Core 3 mais recentes estiverem disponíveis e forem exibidos, você deverá ser capaz de executar e depurar qualquer projeto configurado para a versão 3. x.

> [!IMPORTANT]
> As funções da versão 3. x só podem ser desenvolvidas no Visual Studio se você estiver usando o Visual Studio versão 16,4 ou mais recente.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code e Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) é usado para o desenvolvimento de linha de comando e também pela [extensão de Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code. Para desenvolver na versão 3. x, instale a versão 3. x das principais ferramentas. O desenvolvimento da versão 2. x requer a versão 2. x das principais ferramentas e assim por diante. Para obter mais informações, consulte [instalar o Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

Para o desenvolvimento de Visual Studio Code, talvez você também precise atualizar a configuração de usuário do `azureFunctions.projectRuntime` para corresponder à versão das ferramentas instaladas.  Essa configuração também atualiza os modelos e os idiomas usados durante a criação do aplicativo de funções.  Para criar aplicativos no `~3` você atualizaria a configuração de `azureFunctions.projectRuntime` usuário para `~3`.

![Configuração do tempo de execução de Azure Functions extensão](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Aplicativos Maven e Java

Você pode migrar aplicativos Java da versão 2. x para 3. x [instalando a versão 3. x das ferramentas principais](functions-run-local.md#install-the-azure-functions-core-tools) necessárias para executar localmente.  Depois de verificar se seu aplicativo funciona corretamente em execução localmente na versão 3. x, atualize o arquivo de `POM.xml` do aplicativo para modificar a configuração de `FUNCTIONS_EXTENSION_VERSION` para `~3`, como no exemplo a seguir:

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

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
