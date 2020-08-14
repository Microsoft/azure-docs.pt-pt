---
title: Registar extensões de ligação funções Azure Funções
description: Aprenda a registar uma extensão de ligação Azure Functions com base no seu ambiente.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 43bc278ea3cbd14690f1a9ac9263872536b5b174
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224786"
---
# <a name="register-azure-functions-binding-extensions"></a>Registar extensões de ligação funções Azure Funções

A partir da versão 2.x do Azure Functions, estão disponíveis [encadernações](./functions-triggers-bindings.md) como pacotes separados do tempo de execução das funções. Enquanto as funções .NET acedem a ligações através de pacotes NuGet, os pacotes de extensão permitem a outras funções o acesso a todas as ligações através de uma configuração.

Considere os seguintes itens relacionados com extensões vinculativas:

- As extensões de encadernação não estão explicitamente registadas nas Funções 1.x, exceto quando [se cria uma biblioteca de classe C# utilizando o Visual Studio](#local-csharp).

- HTTP e os gatilhos do temporizador são suportados por padrão e não requerem uma extensão.

A tabela seguinte indica quando e como regista as encadernações.

| Ambiente de desenvolvimento |Registo<br/> em Funções 1.x  |Registo<br/> em Funções 3.x/2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Portal do Azure|Automático|Automático<sup>*</sup>|
|Non-.NET línguas ou desenvolvimento local de Ferramentas Core Azure|Automático|[Use ferramentas principais de funções Azure e pacotes de extensão](#extension-bundles)|
|Biblioteca de classe C# usando Visual Studio|[Use ferramentas NuGet](#vs)|[Use ferramentas NuGet](#vs)|
|Biblioteca de classe C# usando Código de Estúdio Visual|N/D|[Use .NET Core CLI](#vs-code)|

<sup>*</sup> Portal usa pacotes de extensão.

## <a name="extension-bundles"></a><a name="extension-bundles"></a>Pacotes de extensão

Os pacotes de extensão são uma forma de adicionar um conjunto compatível de extensões de ligação de funções à sua aplicação de função. Ao utilizar pacotes, um conjunto de extensões predefinidos é adicionado quando constrói a sua aplicação. Os pacotes de extensão definidos num pacote são verificados como compatíveis uns com os outros, o que ajuda a evitar conflitos entre pacotes. Os pacotes de extensão permitem evitar ter de publicar o código de projeto .NET com um projeto de funções non-.NET. Você ativa pacotes de extensão no host.jsda aplicação em arquivo.  

Pode utilizar pacotes de extensão com versões 2.x e versões posteriores do tempo de execução das Funções. 

Utilize pacotes de extensões para o desenvolvimento local utilizando ferramentas principais de funções Azure, Código do Estúdio Visual e quando construir remotamente. Ao desenvolver localmente, certifique-se de que está a utilizar a versão mais recente das [Ferramentas Principais de Funções Azure.](functions-run-local.md#v2) Os pacotes de extensão também são usados no desenvolvimento de funções no portal Azure. 

Se não utilizar pacotes de extensão, tem de instalar o .NET Core 2.x SDK no computador local antes [de instalar explicitamente quaisquer extensões de ligação](#explicitly-install-extensions). Um ficheiro extensions.csproj, que define explicitamente as extensões necessárias, é adicionado ao seu projeto. Os pacotes de extensão removem estes requisitos para o desenvolvimento local. 

Para utilizar pacotes de extensão, atualize o *host.jsno* ficheiro para incluir a seguinte entrada `extensionBundle` para:
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="explicitly-install-extensions"></a>Instalar explicitamente extensões

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="nuget-packages"></a><a name="local-csharp"></a>Pacotes NuGet

Para um projeto de funções baseadas em bibliotecas C#, deve instalar as extensões diretamente. Os pacotes de extensão são projetados especificamente para projetos que não são baseados em bibliotecas de classe C.

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a> Biblioteca de classe C \# com Estúdio Visual

No **Visual Studio,** pode instalar pacotes a partir da consola Package Manager utilizando o comando [Install-Package,](/nuget/tools/ps-ref-install-package) como mostra o seguinte exemplo:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

O nome da embalagem utilizada para uma determinada ligação está previsto no artigo de referência para essa encadernação. Por exemplo, consulte a [secção pacotes do artigo de referência de ligação service bus](functions-bindings-service-bus.md#functions-1x).

Substitua `<TARGET_VERSION>` no exemplo por uma versão específica do pacote, tal como `3.0.0-beta5` . As versões válidas estão listadas nas páginas de pacote individuais [NuGet.org](https://nuget.org). As principais versões que correspondem ao tempo de execução das funções 1.x ou 2.x estão especificadas no artigo de referência para a encadernação.

Se utilizar `Install-Package` para fazer referência a uma ligação, não precisa de utilizar [pacotes de extensão](#extension-bundles). Esta abordagem é específica para bibliotecas de classes construídas no Visual Studio.

## <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> Biblioteca de classe C# com Código de Estúdio Visual

No **Código do Estúdio Visual,** instale pacotes para um projeto de biblioteca de classe C# a partir do pedido de comando utilizando o comando do pacote de adicionar [dotnet](/dotnet/core/tools/dotnet-add-package) no .NET Core CLI. O exemplo a seguir demonstra como se adiciona uma ligação:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

O CLI do núcleo .NET só pode ser utilizado para o desenvolvimento de Funções Azure 2.x.

`<BINDING_TYPE_NAME>`Substitua-o pelo nome da embalagem que contém a ligação necessária. Pode encontrar o artigo de referência vinculativo pretendido na [lista de encadernações apoiadas.](./functions-triggers-bindings.md#supported-bindings)

Substitua `<TARGET_VERSION>` no exemplo por uma versão específica do pacote, tal como `3.0.0-beta5` . As versões válidas estão listadas nas páginas de pacote individuais [NuGet.org](https://nuget.org). As principais versões que correspondem ao tempo de execução das funções 1.x ou 2.x estão especificadas no artigo de referência para a encadernação.

## <a name="next-steps"></a>Próximos passos
> [!div class="nextstepaction"]
> [Gatilho da função Azure e exemplo de ligação](./functions-bindings-example.md)
