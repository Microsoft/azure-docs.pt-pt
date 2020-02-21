---
title: Registar extensões de ligação de funções Azure
description: Aprenda a registar uma extensão de ligação de Funções Azure com base no seu ambiente.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 1688fe848beb62731391bf4399a0dabec5265320
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484728"
---
# <a name="register-azure-functions-binding-extensions"></a>Registar extensões de ligação de funções Azure

Nas Funções Azure versão 2.x, as [encadernações](./functions-triggers-bindings.md) estão disponíveis como pacotes separados das funções de tempo de funcionamento. Enquanto as funções .NET acedem a ligações através de pacotes NuGet, os pacotes de extensão permitem o acesso a todas as ligações através de uma definição de configuração.

Considere os seguintes itens relacionados com extensões vinculativas:

- As extensões de ligação não estão explicitamente registadas nas Funções 1.x exceto na [criação C# ](#local-csharp)de uma biblioteca de classes utilizando o Visual Studio .

- OS gatilhos HTTP e Temporizador são suportados por predefinição e não requerem uma extensão.

A tabela seguinte indica quando e como regista encadernações.

| Ambiente de programação |Registo<br/> em Funções 1.x  |Registo<br/> em Funções 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Portal do Azure|Automático|Automático|
|Non-.NET línguas ou desenvolvimento local de Ferramentas Nucleares|Automático|[Utilize ferramentas nucleares e pacotes de extensões de funções Azure](#extension-bundles)|
|C#biblioteca de classes usando O Estúdio Visual|[Use ferramentas NuGet](#vs)|[Use ferramentas NuGet](#vs)|
|C#biblioteca de classes usando Código de Estúdio Visual|N/A|[Utilizar cli core .NET](#vs-code)|

## <a name="extension-bundles"></a>Pacotes de extensão para o desenvolvimento local

Os pacotes de extensão são uma tecnologia de implementação que permite adicionar um conjunto compatível de extensões de ligação de funções à sua aplicação de função. Um conjunto predefinido de extensões é adicionado quando você constrói a sua app. Os pacotes de extensão definidos num pacote são compatíveis entre si, o que ajuda a evitar conflitos entre pacotes. Ativa pacotes de extensão no ficheiro host.json da aplicação.  

Pode utilizar pacotes de extensão com versões 2.x e versões posteriores do tempo de funcionamento das Funções. Ao desenvolver-se localmente, certifique-se de que está a utilizar a versão mais recente das [Ferramentas Core funções do Azure](functions-run-local.md#v2).

Utilize pacotes de extensão para desenvolvimento local utilizando ferramentas nucleares de funções Azure, Código de Estúdio Visual e quando construir remotamente.

Se não utilizar pacotes de extensão, tem de instalar o .NET Core 2.x SDK no seu computador local antes de instalar quaisquer extensões de ligação. Os pacotes de extensão removem este requisito para o desenvolvimento local. 

Para utilizar pacotes de extensão, atualize o ficheiro *host.json* para incluir a seguinte entrada para `extensionBundle`:
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="vs"></a>C\# biblioteca de classe com Estúdio Visual

No **Estúdio Visual,** pode instalar pacotes a partir da Consola gestorde pacotes utilizando o comando [Install-Package,](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) como mostra o seguinte exemplo:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

O nome da embalagem utilizada para uma determinada encadernação é fornecido no artigo de referência para esse encadernação. Por exemplo, consulte a [secção pacotes do artigo de referência de ligação](functions-bindings-service-bus.md#functions-1x)do autocarro de serviço .

Substitua `<TARGET_VERSION>` no exemplo por uma versão específica da embalagem, como `3.0.0-beta5`. Versões válidas estão listadas nas páginas de pacotes individuais em [NuGet.org](https://nuget.org). As versões principais que correspondem às funções funcionais 1.x ou 2.x são especificadas no artigo de referência para a ligação.

Se utilizar `Install-Package` para fazer referência a uma ligação, não precisa de utilizar pacotes de [extensão](#extension-bundles). Esta abordagem é específica para bibliotecas de classes construídas em Estúdio Visual.

## <a name="vs-code"></a>C# biblioteca de classes com Código de Estúdio Visual

No **Visual Studio Code,** instale pacotes para um C# projeto de biblioteca de classes a partir do pedido de comando utilizando o comando de adição de [dotnet](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) no CLI .NET Core. O exemplo que se segue demonstra como se adiciona uma ligação:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

O CLI de núcleo .NET só pode ser utilizado para o desenvolvimento das Funções Azure 2.x.

Substitua `<BINDING_TYPE_NAME>` pelo nome da embalagem que contém a encadernação de que necessita. Pode encontrar o artigo de referência vinculativo desejado na [lista de encadernações suportadas](./functions-triggers-bindings.md#supported-bindings).

Substitua `<TARGET_VERSION>` no exemplo por uma versão específica da embalagem, como `3.0.0-beta5`. Versões válidas estão listadas nas páginas de pacotes individuais em [NuGet.org](https://nuget.org). As versões principais que correspondem às funções funcionais 1.x ou 2.x são especificadas no artigo de referência para a ligação.

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Gatilho de função Azure e exemplo de ligação](./functions-bindings-example.md)
