---
title: Registar extensões de ligação funções Azure Funções
description: Aprenda a registar uma extensão de ligação Azure Functions com base no seu ambiente.
author: craigshoemaker
ms.topic: reference
ms.date: 08/16/2020
ms.author: cshoe
ms.openlocfilehash: 942ca3229808b57894598c3477e9dc97e40e8c80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88689572"
---
# <a name="register-azure-functions-binding-extensions"></a>Registar extensões de ligação funções Azure Funções

Começando pela versão 2.x do Azure Functions, as funções apenas incluem HTTP e gatilhos do temporizador por predefinição. Outros [gatilhos e encadernações estão](./functions-triggers-bindings.md) disponíveis como pacotes separados.

As aplicações de funções de biblioteca de classe .NET utilizam encadernações que são instaladas no projeto como pacotes NuGet. Os pacotes de extensão permitem que non-.NET aplicações de funções utilizem as mesmas ligações sem terem de lidar com a infraestrutura .NET.

A tabela seguinte indica quando e como regista as encadernações.

| Ambiente de programação |Registo<br/> em Funções 1.x  |Registo<br/> em Funções 3.x/2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Portal do Azure|Automático|Automático<sup>*</sup>|
|línguas Non-.NET|Automático|Utilize [pacotes de extensão](#extension-bundles) (recomendado) ou [instale explicitamente extensões](#explicitly-install-extensions)|
|Biblioteca de classe C# usando Visual Studio|[Use ferramentas NuGet](#vs)|[Use ferramentas NuGet](#vs)|
|Biblioteca de classe C# usando Código de Estúdio Visual|N/D|[Use .NET Core CLI](#vs-code)|

<sup>*</sup> Portal usa pacotes de extensão.

## <a name="access-extensions-in-non-net-languages"></a>Extensões de acesso em línguas non-.NET

Para as aplicações de funções Java, JavaScript, PowerShell, Python e Custom Handler, recomendamos a utilização de pacotes de extensão para aceder a encadernações. Nos casos em que os pacotes de extensão não possam ser utilizados, pode instalar explicitamente extensões de ligação.

### <a name="extension-bundles"></a><a name="extension-bundles"></a>Pacotes de extensão

Os pacotes de extensão são uma forma de adicionar um conjunto compatível de extensões de ligação à sua aplicação de função. Você ativa pacotes de extensão nahost.jsda aplicação *em* arquivo.

Pode utilizar pacotes de extensão com versões 2.x e versões posteriores do tempo de execução das Funções.

Os pacotes de extensão são ver versão. Cada versão contém um conjunto específico de extensões de ligação que são verificadas para trabalhar em conjunto. Selecione uma versão bundle com base nas extensões que precisa na sua aplicação.

Para adicionar um pacote de extensão à sua aplicação de função, adicione a `extensionBundle` secção parahost.jsligado *.* Em muitos casos, o Código do Estúdio Visual e as Ferramentas Core de Funções Azure irão adicioná-lo automaticamente para si.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

A tabela que se segue lista as versões atualmente disponíveis do pacote padrão *Microsoft.Azure.Functions.ExtensionBundle* e liga-se às extensões que incluem.

| Versão bundle | Versão em host.jsem | Extensões incluídas |
| --- | --- | --- |
| 1.x | `[1.*, 2.0.0)` | Veja [extensions.js](https://github.com/Azure/azure-functions-extension-bundles/blob/v1.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) usado para gerar o pacote |
| 2.x | `[2.*, 3.0.0)` | Veja [extensions.js](https://github.com/Azure/azure-functions-extension-bundles/blob/v2.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) usado para gerar o pacote |

> [!NOTE]
> Embora possa especificar a gama de versão personalizada em host.js, recomendamos que utilize um valor de versão a partir desta tabela.

### <a name="explicitly-install-extensions"></a><a name="explicitly-install-extensions"></a>Instalar explicitamente extensões

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="install-extensions-from-nuget-in-net-languages"></a><a name="local-csharp"></a>Instalar extensões do NuGet em idiomas .NET

Para um projeto de funções baseadas em bibliotecas C#, deve instalar as extensões diretamente. Os pacotes de extensão são projetados especificamente para projetos que não são baseados em bibliotecas de classe C.

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a> Biblioteca de classe C \# com Estúdio Visual

No **Visual Studio,** pode instalar pacotes a partir da consola Package Manager utilizando o comando [Install-Package,](/nuget/tools/ps-ref-install-package) como mostra o seguinte exemplo:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

O nome da embalagem utilizada para uma determinada ligação está previsto no artigo de referência para essa encadernação. Por exemplo, consulte a [secção pacotes do artigo de referência de ligação service bus](functions-bindings-service-bus.md#functions-1x).

Substitua `<TARGET_VERSION>` no exemplo por uma versão específica do pacote, tal como `3.0.0-beta5` . As versões válidas estão listadas nas páginas de pacote individuais [NuGet.org](https://nuget.org). As principais versões que correspondem ao tempo de execução das funções 1.x ou 2.x estão especificadas no artigo de referência para a encadernação.

Se utilizar `Install-Package` para fazer referência a uma ligação, não precisa de utilizar [pacotes de extensão](#extension-bundles). Esta abordagem é específica para bibliotecas de classes construídas no Visual Studio.

### <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> Biblioteca de classe C# com Código de Estúdio Visual

No **Código do Estúdio Visual,** instale pacotes para um projeto de biblioteca de classe C# a partir do pedido de comando utilizando o comando do pacote de adicionar [dotnet](/dotnet/core/tools/dotnet-add-package) no .NET Core CLI. O exemplo a seguir demonstra como se adiciona uma ligação:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

O CLI do núcleo .NET só pode ser utilizado para o desenvolvimento de Funções Azure 2.x.

`<BINDING_TYPE_NAME>`Substitua-o pelo nome da embalagem que contém a ligação necessária. Pode encontrar o artigo de referência vinculativo pretendido na [lista de encadernações apoiadas.](./functions-triggers-bindings.md#supported-bindings)

Substitua `<TARGET_VERSION>` no exemplo por uma versão específica do pacote, tal como `3.0.0-beta5` . As versões válidas estão listadas nas páginas de pacote individuais [NuGet.org](https://nuget.org). As principais versões que correspondem ao tempo de execução das funções 1.x ou 2.x estão especificadas no artigo de referência para a encadernação.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Gatilho da função Azure e exemplo de ligação](./functions-bindings-example.md)
