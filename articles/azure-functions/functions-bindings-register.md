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
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 5534086d5754691f650370e465fa2c63210e0dc7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56739764"
---
# <a name="register-azure-functions-binding-extensions"></a>Registar as extensões de vinculação de funções do Azure

As funções do Azure oferece suporte a HTTP e o temporizador de imediato. Para trabalhar com outros serviços, tem de instalar ou *registar* um [enlace](./functions-triggers-bindings.md) extensão. Extensões de ligação são fornecidas por meio de pacotes de ferramentas de núcleo do Azure ou o NuGet. 

A tabela seguinte indica quando e como se registrar enlaces.

| Ambiente de programação |Registo<br/> nas funções 1.x  |Registo<br/> nas funções 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Portal do Azure|Automático|[Automático com linha de comandos](#azure-portal-development)|
|Linguagens .NET não ou local de desenvolvimento de ferramentas de núcleo do Azure|Automático|[Utilizar comandos da CLI de ferramentas de núcleo](#local-development-azure-functions-core-tools)|
|Biblioteca de classes do c# com o Visual Studio 2017|[Utilizar as ferramentas do NuGet](#c-class-library-with-visual-studio-2017)|[Utilizar as ferramentas do NuGet](#c-class-library-with-visual-studio-2017)|
|Biblioteca de classes do c# com o Visual Studio Code|N/A|[Utilizar o .NET Core CLI](#c-class-library-with-visual-studio-code)|

Os seguintes tipos de ligação são exceções que não exigem Registro explícito, porque eles são automaticamente registrados em todas as versões e ambientes: HTTP e temporizador.

> [!IMPORTANT]
> Este conteúdo para o restante deste artigo aplicam-se apenas ao funciona 2.x. Extensões de enlace explicitamente não são registadas nas funções 1.x, exceto quando [criar um C# biblioteca de classes com o Visual Studio 2017](#local-csharp).

## <a name="azure-portal-development"></a>Desenvolvimento de portal do Azure

Quando criar uma função ou adicionar um enlace, é solicitado quando a extensão para o acionador ou enlace requer o registo. Responder à solicitação clicando **instalar** para registar a extensão. A instalação pode demorar até 10 minutos num plano de consumo.

Precisa apenas instalar cada extensão de uma vez para uma aplicação de função especificada. Para ligações suportadas que não estão disponíveis no portal ou para atualizar a uma extensão instalada, também pode [manualmente instalar ou atualizar as funções do Azure enlace extensões a partir do portal do](install-update-binding-extensions-manual.md).  

## <a name="local-development-azure-functions-core-tools"></a>Local de desenvolvimento de ferramentas de núcleo de funções do Azure

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>Biblioteca de classes c# com o Visual Studio 2017

Na **Visual Studio 2017**, pode instalar pacotes a partir da consola do Gestor de pacotes utilizando o [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) de comando, conforme mostrado no exemplo a seguir:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <target_version>
```

O nome do pacote para utilizar para uma determinada associação é fornecido no artigo de referência para esse enlace. Por exemplo, veja a [secção do artigo de referência de ligação do Service Bus de pacotes](functions-bindings-service-bus.md#packages---functions-1x).

Substitua `<target_version>` no exemplo com uma versão específica do pacote, tais como `3.0.0-beta5`. Versões válidas estão listadas nas páginas individuais do pacote na [NuGet.org](https://nuget.org). As versões principais que correspondem ao runtime das funções 1.x ou 2.x são especificados no artigo de referência para o enlace.

## <a name="c-class-library-with-visual-studio-code"></a>Biblioteca de classes c# com o Visual Studio Code

Na **Visual Studio Code**, pode instalar pacotes a partir da linha de comandos com o [dotnet Adicionar pacote](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) comando na CLI do núcleo do .NET, conforme mostrado no exemplo a seguir:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <target_version>
```

Só pode ser utilizada a CLI do .NET Core para o desenvolvimento de 2.x as funções do Azure.

O nome do pacote para utilizar para uma determinada associação é fornecido no artigo de referência para esse enlace. Por exemplo, veja a [secção do artigo de referência de ligação do Service Bus de pacotes](functions-bindings-service-bus.md#packages---functions-1x).

Substitua `<target_version>` no exemplo com uma versão específica do pacote, tais como `3.0.0-beta5`. Versões válidas estão listadas nas páginas individuais do pacote na [NuGet.org](https://nuget.org). As versões principais que correspondem ao runtime das funções 1.x ou 2.x são especificados no artigo de referência para o enlace.

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Exemplo do Azure de Acionador e associação de função](./functions-bindings-example.md)

