---
title: As funções runtime versões descrição geral do Azure
description: As funções do Azure suporta várias versões do tempo de execução. Aprenda as diferenças entre eles e como escolher aquele que é ideal para.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: glenga
ms.openlocfilehash: cc19a2d7641b20a413bbc39efe0d1c5ede362e6d
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734460"
---
# <a name="azure-functions-runtime-versions-overview"></a>As funções runtime versões descrição geral do Azure

 Existem duas versões principais do runtime das funções do Azure: 1.x e 2.x. A versão atual, onde os projetos de novos recursos e melhorias estão sendo feitas é 2.x, embora ambos são suportadas para cenários de produção.  Os seguintes detalhes algumas das diferenças entre os dois, como pode criar cada versão e atualizar a partir do 1.x 2.x.

> [!NOTE]
> Este artigo refere-se ao serviço cloud, as funções do Azure. Para obter informações sobre o produto de pré-visualização permite-lhe executar as funções do Azure no local, consulte a [descrição geral de tempo de execução de funções do Azure](functions-runtime-overview.md).

## <a name="cross-platform-development"></a>Desenvolvimento entre plataformas

A versão 2.x do runtime é executado no .NET Core 2, que permite que seja executado em todas as plataformas suportadas pelo .NET Core, incluindo o macOS e Linux. Em execução no .NET Core permite o desenvolvimento entre plataformas e cenários de hospedagem.

Por comparação, o tempo de execução do versão 1.x só oferece suporte a desenvolvimento e hospedagem no portal do Azure ou em computadores Windows.

## <a name="languages"></a>Languages

A versão 2.x do runtime usa um novo modelo de extensibilidade do idioma. Na versão 2.x, todas as funções na aplicação de função têm de partilhar o mesmo idioma. O idioma das funções numa aplicação de funções é escolhido ao criar a aplicação.

Linguagens experimentais do Azure funções 1.x não serão atualizadas para usar o novo modelo, para que eles não têm suporte no 2.x. A tabela seguinte indica quais linguagens de programação são atualmente suportadas em cada versão de runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Para obter mais informações, veja [Idiomas suportados](supported-languages.md).

## <a name="creating-1x-apps"></a>É executado na versão 1.x

Por predefinição, as aplicações de funções criadas no portal do Azure estão definidas para a versão 2.x. Sempre que possível, deve utilizar esta versão do tempo de execução, em que estão sendo feitos novos investimentos de funcionalidade. Se for necessário, pode executar uma aplicação de funções o tempo de execução do versão 1.x. Só pode alterar a versão de tempo de execução depois de criar a sua aplicação de função, mas antes de adicionar quaisquer funções. Para saber como afixar a versão de runtime para 1.x, veja [veja e atualize a versão atual do tempo de execução](set-runtime-version.md#view-and-update-the-current-runtime-version).

## <a name="migrating-from-1x-to-2x"></a>Migrando do 1.x para o 2.x

Pode optar por migrar uma aplicação existente, escrita para utilizar o tempo de execução do versão 1.x para utilizar em vez disso, a versão 2.x. A maioria das alterações que precisa para está relacionada a alterações no tempo de execução do idioma, como alterações de API c# entre o .NET Framework 4.7 e .NET Core 2. Também precisará certificar-se de que seu código e bibliotecas são compatíveis com o runtime de linguagem que escolher. Por fim, certifique-se de que tenha em atenção a quaisquer alterações no acionador, ligações e recursos, realçados abaixo. Para obter melhores resultados de migração, deve criar uma nova aplicação de função para a versão 2.x e a porta para a nova aplicação de código de sua função de 1.x versão existente.  

### <a name="changes-in-triggers-and-bindings"></a>Alterações em acionadores e enlaces

Versão 2.x precisa que instale as extensões para específicos acionadores e enlaces utilizados pelas funções na sua aplicação. A única exceção para esta acionadores HTTP e timer, que não necessitam de uma extensão.  Para obter mais informações, consulte [Registre-se e instalar as extensões de enlace](./functions-bindings-expressions-patterns.md).

Também há algumas alterações no `function.json` ou atributos da função entre versões. Por exemplo, o Hub de eventos `path` propriedade é agora `eventHubName`. Consulte a [tabela de associação existente](#bindings) para obter ligações para documentação para cada ligação.

### <a name="changes-in-features-and-functionality"></a>Alterações em recursos e funcionalidades

Alguns recursos também foram removidos, atualizados ou substituídos na nova versão. Esta secção descreve as alterações que pode ver na versão 2.x depois de ter usado a versão 1.x.

Na versão 2.x, as seguintes alterações foram feitas:

* Chaves para chamar pontos finais HTTP são sempre armazenadas encriptados no armazenamento de Blobs do Azure. Na versão 1.x, as chaves foram armazenadas no armazenamento de ficheiros do Azure ser predefinido. Ao atualizar uma aplicação a partir da versão 1.x para a versão 2.x, segredos existentes que estão no armazenamento de ficheiros são repostos.

* A versão 2.x do runtime não inclui suporte incorporado para fornecedores de webhook. Esta alteração foi feita para melhorar o desempenho. Pode continuar a utilizar acionadores HTTP como pontos finais para webhooks.

* O ficheiro de configuração de anfitrião (Host. JSON) deve estar vazio ou tem a cadeia de caracteres `"version": "2.0"`.

* Para melhorar a monitorização, o dashboard de WebJobs no portal, o que é utilizado o [ `AzureWebJobsDashboard` ](functions-app-settings.md#azurewebjobsdashboard) definição é substituída pelo Azure Application Insights, que usa o [ `APPINSIGHTS_INSTRUMENTATIONKEY` ](functions-app-settings.md#appinsightsinstrumentationkey) definição. Para obter mais informações, consulte [as funções do Azure de Monitor](functions-monitoring.md).

* Todas as funções na aplicação de função têm de partilhar o mesmo idioma. Quando cria uma aplicação de funções, tem de escolher uma pilha de runtime para a aplicação. A pilha de tempo de execução é especificada pela [ `FUNCTIONS_WORKER_RUNTIME` ](functions-app-settings.md#functionsworkerruntime) valor existente nas definições de aplicação. Este requisito foi adicionado para melhorar o tempo de requisitos de espaço e de inicialização. Ao desenvolver localmente, esta definição no também tem de incluir o [Settings ficheiro](functions-run-local.md#local-settings-file).

* O tempo limite predefinido para funções num plano do serviço de aplicações é alterado para 30 minutos. Pode alterar manualmente o tempo limite para ilimitado, utilizando o [functionTimeout](functions-host-json.md#functiontimeout) definir no Host. JSON.

* Limitações de simultaneidade HTTP são implementadas por predefinição para as funções do plano de consumo, com uma predefinição de 100 pedidos em simultâneo por instância. Pode alterá-lo no [ `maxConcurrentRequests` ](functions-host-json.md#http) definição no ficheiro de Host. JSON.

* Devido [limitações do .NET core](https://github.com/Azure/azure-functions-host/issues/3414), o suporte para F# funções de script (.fsx) foi removido. Compilado F# funções (soubor) ainda são suportadas.

* O formato de URL de webhooks de Acionador do Event Grid foi alterado para `https://{app}/runtime/webhooks/{triggerName}`.

### <a name="migrating-a-locally-developed-application"></a>Migração de um aplicativo desenvolvido localmente

Pode ter existente funcionar os projetos de aplicativos que desenvolveu localmente com o tempo de execução do versão 1.x. Para atualizar para versão 2.x, deve criar um projeto de aplicação de função local na versão 2.x e a porta já existentes de código para a nova aplicação. Pode atualizar manualmente o projeto existente e código, uma espécie de "atualização no local". No entanto, existem vários outros aprimoramentos entre a versão 1.x e a versão 2.x que ainda poderá ter de fazer. Por exemplo, no c# o objeto de depuração foi alterado de `TraceWriter` para `ILogger`. Ao criar um novo projeto de 2.x de versão, começa com as funções atualizadas com base nos modelos de 2.x versão mais recente.

#### <a name="visual-studio-runtime-versions"></a>Versões de tempo de execução do Visual Studio

No Visual Studio, selecione a versão de runtime quando cria um projeto. Ferramentas de funções do Azure para Visual Studio oferece suporte a ambas as versões principais do tempo de execução. A versão correta é utilizada quando a depuração e publicação com base nas configurações de projeto. As definições de versão são definidas no `.csproj` ficheiro nas seguintes propriedades:

##### <a name="version-1x"></a>Versão 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Versão 2.x

```xml
<TargetFramework>netstandard2.0</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Para depurar ou publicar o seu projeto, é utilizada a versão correta do runtime.

#### <a name="vs-code-and-azure-functions-core-tools"></a>O VS Code e ferramentas de núcleo das funções do Azure

[As ferramentas de núcleo das funções do Azure](functions-run-local.md) é utilizado para o desenvolvimento de linha de comandos e também pela [extensão de funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code. Para desenvolver na versão 2.x, instalação versão 2.x das ferramentas de núcleo. Desenvolvimento do versão 1.x requer a versão 1.x das ferramentas de núcleo. Para obter mais informações, consulte [instalar as ferramentas de núcleo de funções do Azure](functions-run-local.md#install-the-azure-functions-core-tools).

Para o desenvolvimento do Visual Studio Code, também poderá ter de atualizar a definição de utilizador para o `azureFunctions.projectRuntime` para corresponder à versão das ferramentas instalado.  Esta definição também atualiza os modelos e linguagens utilizadas durante a criação de aplicações de função.

### <a name="changing-version-of-apps-in-azure"></a>Alterar versão das aplicações no Azure

A versão do runtime das funções utilizado por aplicações publicadas no Azure é ditada pelos [ `FUNCTIONS_EXTENSION_VERSION` ](functions-app-settings.md#functionsextensionversion) definição da aplicação. Um valor de `~2` destina-se a versão 2.x do runtime e `~1` destina-se o tempo de execução do versão 1.x. Arbitrariamente é não altere esta definição, porque outras alterações de definição de aplicação e as alterações de código nas suas funções são provável que necessário. Para saber mais sobre a forma recomendada para migrar a sua aplicação de funções para uma versão de runtime diferentes, veja [como versões de tempo de execução de funções do Azure de destino](set-runtime-version.md).

## <a name="bindings"></a>Enlaces

A versão 2.x do runtime usa um novo [modelo de extensibilidade de ligação](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) que oferece essas vantagens:

* Suporte para extensões de vinculação de terceiros.

* Desacoplamento do tempo de execução e enlaces. Esta alteração permite que as extensões de vinculação ser uma versão e lançado de forma independente. Pode, por exemplo, optar por atualizar para uma versão de uma extensão que se baseia numa versão mais recente de um SDK subjacente.

* Um mais leve ambiente de execução, em que apenas as associações em utilização são conhecidas e carregadas pelo tempo de execução.

Com exceção dos acionadores HTTP e timer, todos os enlaces tem de ser explicitamente adicionados ao projeto de aplicação de função ou registados no portal. Para obter mais informações, consulte [registar as extensões de vinculação](./functions-bindings-expressions-patterns.md).

A tabela seguinte mostra as ligações são suportadas em cada versão de runtime.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte os seguintes recursos:

* [Criar código e testar as Funções do Azure localmente](functions-run-local.md)
* [Como as versões de tempo de execução de funções do Azure de destino](set-runtime-version.md)
* [Notas de versão](https://github.com/Azure/azure-functions-host/releases)
