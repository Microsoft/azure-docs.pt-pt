---
title: Registar as extensões de vinculação de funções do Azure
description: Saiba como registar uma extensão de enlace de funções do Azure com base no seu ambiente.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 802e177b6f3844abe4d24c26b7ea2d0d4fb1688c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697001"
---
# <a name="register-azure-functions-binding-extensions"></a>Registar as extensões de vinculação de funções do Azure

A partir da versão de funções do Azure 2.x, [enlaces](./functions-triggers-bindings.md) estão disponíveis como pacotes separados do Runtime de funções. Enquanto as funções de .NET acessar ligações através de pacotes NuGet, os pacotes de extensão permitem acesso outras funções para todas as ligações através de uma definição de configuração.

Considere os seguintes itens relativas a extensões de enlace:

- Extensões de enlace explicitamente não são registadas nas funções 1.x, exceto quando [criar um C# biblioteca de classes com o Visual Studio 2017](#local-csharp).

- Os acionadores de HTTP e timer são suportados por predefinição e não necessitam de uma extensão.

A tabela seguinte indica quando e como se registrar enlaces.

| Ambiente de programação |Registo<br/> nas funções 1.x  |Registo<br/> nas funções 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Portal do Azure|Automático|Automático|
|Linguagens .NET não ou local de desenvolvimento de ferramentas de núcleo do Azure|Automático|[Utilizar ferramentas de núcleo de funções do Azure e os pacotes de extensão](#local-development-with-azure-functions-core-tools-and-extension-bundles)|
|Biblioteca de classes do c# com o Visual Studio 2017|[Utilizar as ferramentas do NuGet](#c-class-library-with-visual-studio-2017)|[Utilizar as ferramentas do NuGet](#c-class-library-with-visual-studio-2017)|
|Biblioteca de classes do c# com o Visual Studio Code|N/A|[Utilizar o .NET Core CLI](#c-class-library-with-visual-studio-code)|

## <a name="local-development-with-azure-functions-core-tools-and-extension-bundles"></a>Local de desenvolvimento com ferramentas de núcleo de funções do Azure e os pacotes de extensão

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>Biblioteca de classes c# com o Visual Studio 2017

Na **Visual Studio 2017**, pode instalar pacotes a partir da consola do Gestor de pacotes utilizando o [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) de comando, conforme mostrado no exemplo a seguir:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

O nome do pacote utilizado para uma determinada associação é fornecido no artigo de referência para esse enlace. Por exemplo, veja a [secção do artigo de referência de ligação do Service Bus de pacotes](functions-bindings-service-bus.md#packages---functions-1x).

Substitua `<TARGET_VERSION>` no exemplo com uma versão específica do pacote, tais como `3.0.0-beta5`. Versões válidas estão listadas nas páginas individuais do pacote na [NuGet.org](https://nuget.org). As versões principais que correspondem ao runtime das funções 1.x ou 2.x são especificados no artigo de referência para o enlace.

## <a name="c-class-library-with-visual-studio-code"></a>Biblioteca de classes c# com o Visual Studio Code

Na **Visual Studio Code**, pode instalar pacotes a partir da linha de comandos com o [dotnet Adicionar pacote](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) comando na CLI do núcleo do .NET, conforme mostrado no exemplo a seguir:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

Só pode ser utilizada a CLI do .NET Core para o desenvolvimento de 2.x as funções do Azure.

O nome do pacote para utilizar para uma determinada associação é fornecido no artigo de referência para esse enlace. Por exemplo, veja a [secção do artigo de referência de ligação do Service Bus de pacotes](functions-bindings-service-bus.md#packages---functions-1x).

Substitua `<TARGET_VERSION>` no exemplo com uma versão específica do pacote, tais como `3.0.0-beta5`. Versões válidas estão listadas nas páginas individuais do pacote na [NuGet.org](https://nuget.org). As versões principais que correspondem ao runtime das funções 1.x ou 2.x são especificados no artigo de referência para o enlace.

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Exemplo do Azure de Acionador e associação de função](./functions-bindings-example.md)

