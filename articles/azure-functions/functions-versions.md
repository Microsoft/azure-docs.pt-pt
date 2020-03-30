---
title: Visão geral das versões de tempo de funcionamento do Azure Functions
description: A Azure Functions suporta várias versões do tempo de funcionamento. Aprende as diferenças entre eles e como escolher a que é certa para ti.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 21a7b25087efd5d4adf2154c935636c263df9afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276559"
---
# <a name="azure-functions-runtime-versions-overview"></a>Visão geral das versões de tempo de funcionamento do Azure Functions

As principais versões do tempo de funcionamento das Funções Azure estão relacionadas com a versão de .NET em que se baseia o tempo de execução. O quadro seguinte indica a versão atual do tempo de execução, o nível de lançamento e a versão .NET relacionada. 

| Versão runtime | Nível de lançamento<sup>1</sup> | versão .NET | 
| --------------- | ------------- | ------------ |
| 3.x | GA | .NET Core 3.1 | 
| 2.x | GA | .NET Core 2.2 |
| 1.x | GA<sup>2</sup> | .Quadro líquido 4.6<sup>3</sup> |

<sup>1</sup> Lançamentos de GA são suportados para cenários de produção.   
<sup>2</sup> A versão 1.x encontra-se no modo de manutenção. As melhorias só são fornecidas em versões posteriores.   
<sup>3</sup> Apenas suporta o desenvolvimento no portal Azure ou localmente em computadores Windows.

Este artigo detalha algumas das diferenças entre as várias versões, como pode criar cada versão e como alterar versões.

## <a name="languages"></a>Linguagens

Começando pela versão 2.x, o tempo de execução utiliza um modelo de extensibility linguística, e todas as funções numa aplicação de função devem partilhar o mesmo idioma. O idioma das funções numa aplicação de funções é escolhido na criação da app e mantém-se na definição DE TEMPO DE EXECUÇÃO DO [TRABALHADOR\_\_FUNÇÕES.](functions-app-settings.md#functions_worker_runtime) 

As funções azure 1.x as línguas experimentais não podem usar o novo modelo, por isso não são suportadas em 2.x. O quadro seguinte indica quais as línguas de programação que são atualmente suportadas em cada versão de tempo de execução.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Para obter mais informações, veja [Idiomas suportados](supported-languages.md).

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>Executar numa versão específica

Por padrão, as aplicações de função criadas no portal Azure e pelo Azure CLI estão definidas para a versão 3.x. Pode modificar esta versão conforme necessário. Só pode alterar a versão de tempo de execução para 1.x depois de criar a sua aplicação de função, mas antes de adicionar qualquer função.  Mover-se entre 2.x e 3.x é permitido mesmo com apps que têm funções, mas ainda é recomendado testar numa nova aplicação primeiro.

## <a name="migrating-from-1x-to-later-versions"></a>Migração de 1.x para versões posteriores

Pode optar por migrar uma aplicação existente escrita para utilizar o tempo de execução da versão 1.x para, em vez disso, utilizar uma versão mais recente. A maioria das alterações que precisa de fazer estão relacionadas com alterações no tempo de execução da linguagem, tais como alterações de C# API entre .NET Framework 4.7 e .NET Core. Também terá de se certificar de que o seu código e bibliotecas são compatíveis com o tempo de execução da linguagem que escolher. Por fim, certifique-se de que quaisquer alterações no gatilho, encadernações e funcionalidades destacadas abaixo. Para obter os melhores resultados de migração, deverá criar uma nova aplicação de funções numa nova versão e de portar o código de função da versão 1.x existente para a nova aplicação.  

Embora seja possível fazer uma atualização "in-place" atualizando manualmente a configuração da aplicação, passar de 1.x para uma versão mais alta inclui algumas alterações de quebra. Por exemplo, em C#, o objeto `TraceWriter` de `ILogger`depuração é alterado de . Ao criar um novo projeto versão 3.x, começa com funções atualizadas com base nos modelos mais recentes da versão 3.x.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Alterações nos gatilhos e encadernações após a versão 1.x

A partir da versão 2.x, tem de instalar as extensões para gatilhos e encadernações específicas utilizados pelas funções da sua aplicação. A única exceção para este HTTP e tensões temporizadores, que não requerem uma extensão.  Para mais informações, consulte [O Registo e instale extensões de encadernação.](./functions-bindings-register.md)

Há também algumas alterações na *função.json* ou atributos da função entre versões. Por exemplo, a `path` propriedade `eventHubName`do Event Hub é agora. Consulte a [tabela de encadernação existente](#bindings) para ligações à documentação para cada encadernação.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Alterações nas funcionalidades e funcionalidadeapós a versão 1.x

Algumas funcionalidades foram removidas, atualizadas ou substituídas após a versão 1.x. Esta secção detalha as alterações que vê em versões posteriores depois de ter usado a versão 1.x.

Na versão 2.x, foram feitas as seguintes alterações:

* As chaves para chamar pontos finais HTTP são sempre armazenadas encriptadas no armazenamento do Azure Blob. Na versão 1.x, as chaves foram armazenadas no armazenamento do Ficheiro Azure por padrão. Ao atualizar uma aplicação da versão 1.x para a versão 2.x, os segredos existentes que estão no armazenamento de ficheiros são redefinidos.

* O tempo de execução da versão 2.x não inclui suporte incorporado para fornecedores de webhook. Esta mudança foi feita para melhorar o desempenho. Ainda pode utilizar os gatilhos HTTP como pontos finais para webhooks.

* O ficheiro de configuração do anfitrião (host.json) deve estar vazio ou ter a corda `"version": "2.0"`.

* Para melhorar a monitorização, o painel WebJobs [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) no portal, que utilizou a [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) definição, é substituído por Insights de Aplicação Azure, que utiliza a definição. Para mais informações, consulte as [Funções Monitor Azure](functions-monitoring.md).

* Todas as funções numa aplicação de função devem partilhar o mesmo idioma. Quando cria uma aplicação de função, tem de escolher uma stack de tempo de funcionamento para a aplicação. A pilha de tempo de [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) execução é especificada pelo valor nas definições da aplicação. Este requisito foi adicionado para melhorar a pegada e o tempo de arranque. Ao desenvolver-se localmente, também deve incluir esta definição no [ficheiro local.settings.json](functions-run-local.md#local-settings-file).

* O tempo de paragem padrão para funções num plano de Serviço de Aplicações é alterado para 30 minutos. Pode alterar manualmente o tempo de volta para ilimitado utilizando a definição [de timeout](functions-host-json.md#functiontimeout) de função em host.json.

* Os aceleradores de condivisões HTTP são implementados por padrão para funções do plano de consumo, com um padrão de 100 pedidos simultâneos por exemplo. Pode alterar isto [`maxConcurrentRequests`](functions-host-json.md#http) na definição do ficheiro host.json.

* Devido às [limitações do Núcleo .NET,](https://github.com/Azure/azure-functions-host/issues/3414)o suporte para funções de script F# (.fsx) foi removido. As funções F# compiladas (.fs) ainda são suportadas.

* O formato URL dos webhooks do `https://{app}/runtime/webhooks/{triggerName}`gatilho da Rede de Eventos foi alterado para .

## <a name="migrating-from-2x-to-3x"></a>Migração de 2.x a 3.x

A versão 3.x do Azure Functions é altamente compatível com a versão 2.x.  Muitas aplicações devem ser capazes de fazer upgrade com segurança para 3.x sem alterações de código.  Ao mover-se para o 3.x é encorajado, certifique-se de executar testes extensivos antes de alterar a versão principal em aplicações de produção.

### <a name="breaking-changes-between-2x-and-3x"></a>Quebrar alterações entre 2.x e 3.x

Seguem-se as alterações a ter em conta antes de atualizar uma aplicação 2.x para 3.x.

#### <a name="javascript"></a>Javascript

* As encadernações de saída atribuídas `context.done` através `context.bindings`ou valores de retorno comportam-se agora da mesma forma que a fixação em .

* Objeto de gatilho temporizador é cameloCase em vez de PascalCase

* O Event Hub desencadeou funções com `dataType` `binary` binário `string`receberá uma série de em vez de .

* A carga útil do pedido HTTP `context.bindingData.req`já não pode ser acedida via .  Ainda pode ser acedido como um `context.req`parâmetro de `context.bindings`entrada, e em .

* O nójs 8 já não é suportado e não executará em funções 3.x.

#### <a name="net"></a>.NET

* [As operações sincronizadas do servidor são desativadas por defeito](https://docs.microsoft.com/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

### <a name="changing-version-of-apps-in-azure"></a>Alteração da versão das aplicações no Azure

A versão do tempo de funcionamento das Funções utilizada [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) pelas aplicações publicadas no Azure é ditada pela definição de aplicação. Os seguintes principais valores da versão runtime são suportados:

| Valor | Alvo de tempo de execução |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> Não altere arbitrariamente esta definição, pois podem ser necessárias alterações e alterações no código de funcionamento.

### <a name="locally-developed-application-versions"></a>Versões de aplicação desenvolvidas localmente

Pode efazer as seguintes atualizações para funcionar aplicações para alterar localmente as versões direcionadas.

#### <a name="visual-studio-runtime-versions"></a>Versões de tempo de execução do Estúdio Visual

No Visual Studio, selecione a versão runtime quando criar um projeto. As ferramentas Azure Functions para o Estúdio Visual suportam as três principais versões de tempo de execução. A versão correta é usada na depuração e publicação com base nas definições do projeto. As definições da `.csproj` versão são definidas no ficheiro nas seguintes propriedades:

##### <a name="version-1x"></a>Versão 1.x

```xml
<TargetFramework>net461</TargetFramework>
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
> As funções Azure 3.x e `Microsoft.NET.Sdk.Functions` .NET `3.0.0`exigem que a extensão seja pelo menos .

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>Atualizar aplicativos 2.x para 3.x no Estúdio Visual

Pode abrir uma função existente direcionada para 2.x e `.csproj` passar para 3.x editando o ficheiro e atualizando os valores acima.  O Visual Studio gere automaticamente versões de tempo de execução para si com base em metadados de projetos.  No entanto, é possível que nunca tenha criado uma aplicação 3.x antes que o Visual Studio ainda não tenha os modelos e o tempo de execução para 3.x na sua máquina.  Isto pode apresentar-se com um erro como "no functions runtime disponível que corresponda à versão especificada no projeto."  Para obter os modelos mais recentes e tempo de execução, passe pela experiência para criar um novo projeto de função.  Quando chegar à versão e modelo selecionado, espere que o Visual Studio complete a busca dos modelos mais recentes.  Uma vez que os mais recentes modelos .NET Core 3 estejam disponíveis e exibidos, você deve ser capaz de executar e desmarcar qualquer projeto configurado para a versão 3.x.

> [!IMPORTANT]
> As funções da versão 3.x só podem ser desenvolvidas no Visual Studio se utilizarem a versão 16.4 do Estúdio Visual ou mais recentes.

#### <a name="vs-code-and-azure-functions-core-tools"></a>Vs Código e Funções Azure Ferramentas Nucleares

[As Ferramentas Nucleares das Funções Azure](functions-run-local.md) são utilizadas para o desenvolvimento da linha de comando e também pela extensão de [funções Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para código de estúdio visual. Para desenvolver contra a versão 3.x, instale a versão 3.x das Ferramentas Core. O desenvolvimento da versão 2.x requer a versão 2.x das Ferramentas Core, e assim por diante. Para mais informações, consulte [Instalar as Ferramentas Core funções Do Azure](functions-run-local.md#install-the-azure-functions-core-tools).

Para o desenvolvimento do Código do Estúdio Visual, `azureFunctions.projectRuntime` poderá também ser necessário atualizar a definição do utilizador para que a versão corresponda à versão das ferramentas instaladas.  Esta definição também atualiza os modelos e idiomas utilizados durante a criação de aplicações de função.  Para criar `~3` aplicações em `azureFunctions.projectRuntime` si `~3`atualizaria a definição do utilizador para .

![Definição de tempo de extensão de funções azure](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Aplicativos Maven e Java

Pode migrar as aplicações Java da versão 2.x para 3.x [instalando a versão 3.x das ferramentas centrais necessárias](functions-run-local.md#install-the-azure-functions-core-tools) para funcionar localmente.  Depois de verificar se a sua aplicação funciona corretamente na `POM.xml` versão 3.x, atualize o ficheiro da aplicação para modificar a `FUNCTIONS_EXTENSION_VERSION` definição para, `~3`como no exemplo seguinte:

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

Começando pela versão 2.x, o tempo de execução utiliza um novo modelo de [extensibility de ligação](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) que oferece estas vantagens:

* Apoio a extensões vinculativas de terceiros.

* Dissociação do tempo de execução e encadernações. Esta alteração permite que as extensões vinculativas sejam versonizadas e lançadas de forma independente. Pode, por exemplo, optar por fazer upgrade para uma versão de uma extensão que se baseie numa versão mais recente de um SDK subjacente.

* Um ambiente de execução mais leve, onde apenas as encadernações em uso são conhecidas e carregadas pelo tempo de execução.

Com exceção dos gatilhos HTTP e Temporizador, todas as encadernações devem ser explicitamente adicionadas ao projeto da aplicação de funções, ou registadas no portal. Para mais informações, consulte O Registo de [extensões vinculativas](./functions-bindings-expressions-patterns.md).

A tabela que se segue mostra quais as encadernações suportadas em cada versão de tempo de execução.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte os seguintes recursos:

* [Criar código e testar as Funções do Azure localmente](functions-run-local.md)
* [Como direcionar as versões de tempo de execução das Funções Azure](set-runtime-version.md)
* [Notas de versão](https://github.com/Azure/azure-functions-host/releases)
