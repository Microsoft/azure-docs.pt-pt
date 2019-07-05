---
title: Registar as extensões de vinculação de funções do Azure
description: Saiba como registar uma extensão de enlace de funções do Azure com base no seu ambiente.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 88ffd6ec24ed19dd3b1e57277884c8759cdac1f9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480339"
---
# <a name="register-azure-functions-binding-extensions"></a>Registar as extensões de vinculação de funções do Azure

Na versão das funções do Azure 2.x, [enlaces](./functions-triggers-bindings.md) estão disponíveis como pacotes separados do Runtime de funções. Enquanto as funções de .NET acessar ligações através de pacotes NuGet, os pacotes de extensão permitem acesso outras funções para todas as ligações através de uma definição de configuração.

Considere os seguintes itens relacionados com extensões de enlace:

- Extensões de enlace explicitamente não são registadas nas funções 1.x, exceto quando [criar um C# biblioteca de classes com o Visual Studio](#local-csharp).

- Os acionadores de HTTP e timer são suportados por predefinição e não necessitam de uma extensão.

A tabela seguinte indica quando e como se registrar enlaces.

| Ambiente de programação |Registo<br/> nas funções 1.x  |Registo<br/> nas funções 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Portal do Azure|Automático|Automático|
|Linguagens .NET não ou local de desenvolvimento de ferramentas de núcleo do Azure|Automático|[Utilizar ferramentas de núcleo de funções do Azure e os pacotes de extensão](#extension-bundles)|
|C#biblioteca de classe com o Visual Studio 2019|[Utilizar as ferramentas do NuGet](#c-class-library-with-visual-studio-2019)|[Utilizar as ferramentas do NuGet](#c-class-library-with-visual-studio-2019)|
|Biblioteca de classes do c# com o Visual Studio Code|N/A|[Utilizar o .NET Core CLI](#c-class-library-with-visual-studio-code)|

## <a name="extension-bundles"></a>Pacotes de extensão para o desenvolvimento local

Os pacotes de extensão é uma tecnologia de desenvolvimento local para a versão 2.x do runtime que lhe permite adicionar um conjunto compatível de extensões de ligação ao seu projeto de aplicação de função de funções. Esses pacotes de extensão, em seguida, são incluídos no pacote de implementação quando implementar no Azure. Os pacotes faz com que todos os enlaces publicados pela Microsoft disponível através de uma definição no *Host. JSON* ficheiro. Pacotes de extensão definidos num pacote são compatíveis entre si, o que ajuda a evitar conflitos entre pacotes. Quando desenvolver localmente, certifique-se estiver a utilizar a versão mais recente do [ferramentas de núcleo de funções do Azure](functions-run-local.md#v2).

Utilize pacotes de extensão para todo o desenvolvimento local usando as ferramentas de núcleo de funções do Azure ou o Visual Studio Code.

Se não usar os pacotes de extensão, tem de instalar o .NET Core SDK 2.x no seu computador local antes de instalar quaisquer extensões de enlace. Os pacotes elimina essa exigência para o desenvolvimento local. 

Para utilizar os pacotes de extensão, atualize o *Host. JSON* arquivo para incluir a seguinte entrada para `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

As seguintes propriedades estão disponíveis no `extensionBundle`:

| Propriedade | Descrição |
| -------- | ----------- |
| **`id`** | O espaço de nomes para os pacotes de extensão de funções do Microsoft Azure. |
| **`version`** | A versão do pacote para instalar. O runtime das funções sempre escolhe a versão de permissível máxima definida pelo intervalo ou intervalo de versão. O valor de versão acima permite que todas as versões de pacote do 1.0.0 até, mas não incluindo 2.0.0. Para obter mais informações, consulte a [notação de intervalo para a especificação de intervalos de versão](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). |

Incremento de versões de pacote como pacotes na alteração de pacote. Alterações de versão principal ocorrerem quando os pacotes no pacote de incrementar por uma versão principal, que normalmente coincide com uma alteração na versão principal do runtime das funções.  

O conjunto atual de extensões instaladas, o pacote padrão são enumeradas nisso [extensions.json ficheiro](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio-2019"></a>C\# biblioteca de classes com o Visual Studio 2019

Na **Visual Studio 2019**, pode instalar pacotes a partir da consola do Gestor de pacotes utilizando o [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) de comando, conforme mostrado no exemplo a seguir:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

O nome do pacote utilizado para uma determinada associação é fornecido no artigo de referência para esse enlace. Por exemplo, veja a [secção do artigo de referência de ligação do Service Bus de pacotes](functions-bindings-service-bus.md#packages---functions-1x).

Substitua `<TARGET_VERSION>` no exemplo com uma versão específica do pacote, tais como `3.0.0-beta5`. Versões válidas estão listadas nas páginas individuais do pacote na [NuGet.org](https://nuget.org). As versões principais que correspondem ao runtime das funções 1.x ou 2.x são especificados no artigo de referência para o enlace.

## <a name="c-class-library-with-visual-studio-code"></a>Biblioteca de classes c# com o Visual Studio Code

> [!NOTE]
> Recomendamos que utilize [pacotes de extensão](#extension-bundles) ter funções instalar automaticamente um conjunto compatível de pacotes de extensão de enlace.

Na **Visual Studio Code**, instalar pacotes para um C# projeto de biblioteca de classes do prompt de comando com o [dotnet adicionar o pacote](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) comando na CLI do núcleo do .NET, conforme mostrado no exemplo a seguir:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

Só pode ser utilizada a CLI do .NET Core para o desenvolvimento de 2.x as funções do Azure.

O nome do pacote para utilizar para uma determinada associação é fornecido no artigo de referência para esse enlace. Por exemplo, veja a [secção do artigo de referência de ligação do Service Bus de pacotes](functions-bindings-service-bus.md#packages---functions-1x).

Substitua `<TARGET_VERSION>` no exemplo com uma versão específica do pacote, tais como `3.0.0-beta5`. Versões válidas estão listadas nas páginas individuais do pacote na [NuGet.org](https://nuget.org). As versões principais que correspondem ao runtime das funções 1.x ou 2.x são especificados no artigo de referência para o enlace.

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Exemplo do Azure de Acionador e associação de função](./functions-bindings-example.md)

