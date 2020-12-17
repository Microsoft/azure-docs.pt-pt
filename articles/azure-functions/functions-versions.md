---
title: Visão geral das versões de tempo de execução do Azure Functions
description: O Azure Functions suporta várias versões do tempo de execução. Aprende as diferenças entre eles e como escolher o que é certo para ti.
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 12/09/2019
ms.openlocfilehash: 935291c461e275902cb6905c4440fe4d289f0c16
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653355"
---
# <a name="azure-functions-runtime-versions-overview"></a>Visão geral das versões de tempo de execução do Azure Functions

A Azure Functions suporta atualmente três versões do hospedeiro de tempo de execução: 1.x, 2.x e 3.x. As três versões são apoiadas para cenários de produção.  

> [!IMPORTANT]
> A versão 1.x encontra-se em modo de manutenção e só suporta o desenvolvimento no portal Azure Stack Hub ou localmente em computadores Windows. As melhorias são fornecidas apenas em versões posteriores. 

Este artigo detalha algumas das diferenças entre as várias versões, como pode criar cada versão e como alterar versões.

## <a name="languages"></a>Idiomas

Começando pela versão 2.x, o tempo de execução utiliza um modelo de extensibilidade linguística, e todas as funções numa aplicação de função devem partilhar o mesmo idioma. O idioma das funções numa aplicação de função é escolhido ao criar a app e é mantido na definição [de \_ TRABALHO DE \_ EXECUÇÃO DO TRABALHADOR DE FUNÇÕES.](functions-app-settings.md#functions_worker_runtime) 

A tabela seguinte indica quais as linguagens de programação que são atualmente suportadas em cada versão de tempo de execução.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>Executar em uma versão específica

Por predefinição, as aplicações de função criadas no portal Azure e pelo CLI Azure estão definidas para a versão 3.x. Pode modificar esta versão conforme necessário. Só pode alterar a versão de tempo de execução para 1.x depois de criar a sua aplicação de função, mas antes de adicionar quaisquer funções.  Mover-se entre 2.x e 3.x é permitido mesmo com apps que tenham funções, mas ainda é recomendado testar em uma nova aplicação primeiro.

## <a name="migrating-from-1x-to-later-versions"></a>Migrando de 1.x para versões posteriores

Pode optar por migrar uma aplicação existente escrita para utilizar a versão 1.x para, em vez disso, utilizar uma versão mais recente. A maioria das alterações que precisa de fazer estão relacionadas com alterações no tempo de execução da linguagem, tais como alterações de API C# entre .NET Framework 4.7 e .NET Core. Também terá de se certificar de que o seu código e bibliotecas são compatíveis com o tempo de execução do idioma que escolher. Por fim, certifique-se de notar quaisquer alterações no gatilho, encadernações e funcionalidades realçadas abaixo. Para obter os melhores resultados de migração, deverá criar uma nova aplicação de função numa nova versão e apresentar o código de função 1.x existente para a nova aplicação.  

Embora seja possível fazer uma atualização "no local", atualizando manualmente a configuração da aplicação, passar de 1.x para uma versão superior inclui algumas alterações de rutura. Por exemplo, em C#, o objeto de depurar é alterado de `TraceWriter` `ILogger` . Ao criar um novo projeto de versão 3.x, começa com funções atualizadas com base nos modelos mais recentes da versão 3.x.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Alterações nos gatilhos e encadernações após a versão 1.x

A partir da versão 2.x, tem de instalar as extensões para gatilhos e encadernações específicos utilizados pelas funções da sua aplicação. A única exceção para este HTTP e gatilhos temporizadores, que não requerem uma extensão.  Para obter mais informações, consulte [registar-se e instalar extensões de ligação.](./functions-bindings-register.md)

Há também algumas alterações no *function.jsou* atributos da função entre versões. Por exemplo, a propriedade Event Hub `path` é agora `eventHubName` . Consulte a [tabela de ligação existente](#bindings) para obter links para a documentação de cada encadernação.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Alterações nas funcionalidades e funcionalidades após a versão 1.x

Algumas funcionalidades foram removidas, atualizadas ou substituídas após a versão 1.x. Esta secção detalha as alterações que vê nas versões posteriores depois de ter usado a versão 1.x.

Na versão 2.x, foram efetuadas as seguintes alterações:

* As chaves para chamar pontos finais HTTP são sempre armazenadas encriptadas no armazenamento Azure Blob. Na versão 1.x, as chaves foram armazenadas no armazenamento do Ficheiro Azure por padrão. Ao atualizar uma aplicação da versão 1.x para a versão 2.x, os segredos existentes que estão no armazenamento de ficheiros são reiniciados.

* A versão 2.x runtime não inclui suporte incorporado para fornecedores de webhook. Esta alteração foi feita para melhorar o desempenho. Ainda pode utilizar os gatilhos HTTP como pontos finais para webhooks.

* O ficheiro de configuração do anfitrião (host.jsligado) deve estar vazio ou ter a corda `"version": "2.0"` .

* Para melhorar a monitorização, o painel WebJobs no portal, que utilizou a [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) definição, é substituído por Azure Application Insights, que utiliza a [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) definição. Para obter mais informações, consulte [as Funções Do Monitor Azure](functions-monitoring.md).

* Todas as funções de uma aplicação de função devem partilhar o mesmo idioma. Quando criar uma aplicação de função, deve escolher uma pilha de tempo de execução para a aplicação. A pilha de tempo de execução é especificada pelo [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) valor nas definições de aplicação. Este requisito foi adicionado para melhorar a pegada e o tempo de arranque. Ao desenvolver-se localmente, deve também incluir esta definição no [local.settings.jsficheiro](functions-run-local.md#local-settings-file).

* O tempo limite padrão para funções num plano de Serviço de Aplicações é alterado para 30 minutos. Pode alterar manualmente o tempo limite de tempo para ilimitado utilizando a [definição de funçãoTimeout](functions-host-json.md#functiontimeout) host.jsligado.

* Os aceleradores de concuência HTTP são implementados por padrão para funções de plano de consumo, com um padrão de 100 pedidos simultâneos por exemplo. Pode alterar isto na [`maxConcurrentRequests`](functions-host-json.md#http) definição do host.jsno ficheiro.

* Devido às [limitações de núcleo .NET,](https://github.com/Azure/azure-functions-host/issues/3414)o suporte para funções de script F# (.fsx) foi removido. As funções F# compiladas (.fs) ainda estão suportadas.

* O formato URL dos webhooks de gatilho de 'Grade de Eventos' foi alterado para `https://{app}/runtime/webhooks/{triggerName}` .

## <a name="migrating-from-2x-to-3x"></a>Migrando de 2.x para 3.x

A versão 3.x do Azure Functions é altamente compatível com a versão 2.x.  Muitas aplicações devem ser capazes de fazer um upgrade com segurança para 3.x sem alterações de código.  Enquanto se move para 3.x é encorajado, certifique-se de realizar testes extensivos antes de mudar a versão principal em apps de produção.

### <a name="breaking-changes-between-2x-and-3x"></a>Quebrando alterações entre 2.x e 3.x

Seguem-se as alterações a ter em conta antes de atualizar uma aplicação de 2.x para 3.x.

#### <a name="javascript"></a>JavaScript

* As ligações de saída atribuídas através `context.done` ou valores de devolução comportam-se agora da mesma forma que a definição em `context.bindings` .

* Objeto de gatilho temporizador é camelCase em vez de PascalCase

* O Event Hub desencadeou funções com `dataType` binário receberá uma matriz de em vez de `binary` `string` .

* A carga útil do pedido HTTP já não pode ser acedida através de `context.bindingData.req` .  Ainda pode ser acedido como um parâmetro de entrada, `context.req` e em `context.bindings` .

* Node.js 8 já não está suportado e não será executado em funções 3.x.

#### <a name="net"></a>.NET

* [As operações do servidor sincronizado são desativadas por defeito](/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

### <a name="changing-version-of-apps-in-azure"></a>Mudar versão de apps em Azure

A versão do tempo de execução de Funções utilizada pelas aplicações publicadas no Azure é ditada pela definição da [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) aplicação. São suportados os seguintes principais valores da versão de execução:

| Valor | Alvo de tempo de execução |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> Não altere arbitrariamente esta definição, porque podem ser necessárias outras alterações e alterações no código de função.

### <a name="locally-developed-application-versions"></a>Versões de aplicações desenvolvidas localmente

Pode fazer as seguintes atualizações para funcionar apps para alterar localmente as versões direcionadas.

#### <a name="visual-studio-runtime-versions"></a>Versões de tempo de execução do Estúdio Visual

No Visual Studio, seleciona a versão de tempo de execução quando cria um projeto. As ferramentas Azure Functions para Visual Studio suportam as três principais versões de tempo de execução. A versão correta é utilizada ao depurar e publicar com base nas definições do projeto. As definições da versão são definidas no `.csproj` ficheiro nas seguintes propriedades:

##### <a name="version-1x"></a>Versão 1.x

```xml
<TargetFramework>net472</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Versão 2.x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>Versão 3.x

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> As funções Azure 3.x e .NET exigem que a `Microsoft.NET.Sdk.Functions` extensão seja pelo menos `3.0.0` .

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>Atualizar aplicativos 2.x para 3.x em Visual Studio

Pode abrir uma função existente direcionando 2.x e passar para 3.x editando o `.csproj` ficheiro e atualizando os valores acima.  O Visual Studio gere as versões de tempo de execução automaticamente para si com base em metadados de projeto.  No entanto, é possível que nunca tenha criado uma aplicação 3.x antes que o Visual Studio ainda não tenha os modelos e tempo de execução para 3.x na sua máquina.  Isto pode apresentar-se com um erro como "nenhum tempo de execução de Funções disponível que corresponda à versão especificada no projeto".  Para obter os modelos mais recentes e tempo de execução, passe pela experiência para criar um novo projeto de função.  Quando chegar à versão e ao ecrã selecionado por modelos, aguarde que o Visual Studio complete a procura dos modelos mais recentes.  Uma vez que os modelos mais recentes .NET Core 3 estejam disponíveis e exibidos, você deve ser capaz de executar e depurar qualquer projeto configurado para a versão 3.x.

> [!IMPORTANT]
> As funções da versão 3.x só podem ser desenvolvidas no Visual Studio se utilizar a versão 16.4 ou mais recente do Visual Studio.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Código e Funções Azure Funções Ferramentas Principais

[As ferramentas principais do Azure Functions](functions-run-local.md) são utilizadas para o desenvolvimento da linha de comando e também pela extensão de [Funções Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Código do Estúdio Visual. Para se desenvolver contra a versão 3.x, instale a versão 3.x das Ferramentas Core. O desenvolvimento da versão 2.x requer a versão 2.x das Ferramentas Core, e assim por diante. Para obter mais informações, consulte [instalar as Ferramentas Centrais de Funções Azure](functions-run-local.md#install-the-azure-functions-core-tools).

Para o desenvolvimento do Código do Estúdio Visual, também poderá necessitar de atualizar a definição do utilizador `azureFunctions.projectRuntime` para corresponder à versão das ferramentas instaladas.  Esta definição também atualiza os modelos e idiomas utilizados durante a criação de aplicações de função.  Para criar aplicações em `~3` você atualizaria a definição do `azureFunctions.projectRuntime` utilizador para `~3` .

![Definição de tempo de execução de extensão de funções Azure Functions](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Aplicativos Maven e Java

Pode migrar as aplicações Java da versão 2.x para 3.x [instalando a versão 3.x das ferramentas centrais necessárias](functions-run-local.md#install-the-azure-functions-core-tools) para ser executada localmente.  Depois de verificar se a sua aplicação funciona corretamente na versão 3.x, atualize o ficheiro da aplicação `POM.xml` para modificar a `FUNCTIONS_EXTENSION_VERSION` definição para `~3` , como no exemplo seguinte:

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

Começando pela versão 2.x, o tempo de execução utiliza um novo [modelo de extensibilidade de ligação](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) que oferece estas vantagens:

* Apoio a extensões vinculativas de terceiros.

* Dissociação de tempo de execução e encadernações. Esta alteração permite que as extensões vinculativas sejam versadas e libertadas de forma independente. Pode, por exemplo, optar por atualizar para uma versão de uma extensão que se baseia numa versão mais recente de um SDK subjacente.

* Um ambiente de execução mais leve, onde apenas as ligações em uso são conhecidas e carregadas pelo tempo de execução.

Com exceção do HTTP e dos gatilhos do temporizador, todas as ligações devem ser explicitamente adicionadas ao projeto da aplicação de funções, ou registadas no portal. Para obter mais informações, consulte [as extensões de ligação do Registo.](./functions-bindings-expressions-patterns.md)

A tabela a seguir mostra quais as ligações suportadas em cada versão de tempo de execução.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os seguintes recursos:

* [Criar código e testar as Funções do Azure localmente](functions-run-local.md)
* [Como direcionar as versões de tempo de execução do Azure Functions](set-runtime-version.md)
* [Notas de versão](https://github.com/Azure/azure-functions-host/releases)
