---
title: Utilize um feed personalizado do NuGet
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Utilize um feed NuGet personalizado para aceder e utilizar pacotes NuGet num Espaço Azure Dev.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, contentores
manager: gwallace
ms.openlocfilehash: d60d7142d9b9979be76eebb3d324a448bd76638f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91960223"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Use um feed NuGet personalizado com espaços Azure Dev

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Um feed NuGet fornece uma maneira conveniente de incluir fontes de pacote em um projeto. A Azure Dev Spaces precisa de aceder a este feed para que as dependências sejam corretamente instaladas no recipiente Docker.

## <a name="set-up-a-nuget-feed"></a>Configurar um feed NuGet

Adicione uma [referência de embalagem](/nuget/consume-packages/package-references-in-project-files) para a sua dependência no ficheiro sob o `*.csproj` `PackageReference` nó. Por exemplo:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Crie um ficheiro [NuGet.Config](/nuget/reference/nuget-config-file) na pasta do projeto e desave as `packageSources` secções e `packageSourceCredentials` secções para o seu feed NuGet. A `packageSources` secção contém o seu url de alimentação, que deve estar acessível a partir do seu cluster AKS. `packageSourceCredentials`São as credenciais para aceder ao feed. Por exemplo:

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

Atualize os seus Ficheiros Docker para copiar o `NuGet.Config` ficheiro para a imagem. Por exemplo:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> No `NuGet.Config` Windows, `Nuget.Config` , e tudo funciona `nuget.config` como nomes de ficheiros válidos. No Linux, `NuGet.Config` apenas é um nome de ficheiro válido para este ficheiro. Uma vez que a Azure Dev Spaces utiliza o Docker e o Linux, este ficheiro deve ser `NuGet.Config` nomeado. Pode fixar o nome manualmente ou executando `dotnet restore --configfile nuget.config` .


Se estiver a utilizar o Git, não deverá ter as credenciais para o seu feed NuGet no controlo da versão. Adicione `NuGet.Config` ao projeto para que o ficheiro não seja adicionado ao controlo da `.gitignore` `NuGet.Config` versão. A Azure Dev Spaces necessitará deste ficheiro durante o processo de construção de imagens de contentores, mas por padrão, respeita as regras definidas dentro `.gitignore` e `.dockerignore` durante a sincronização. Para alterar o predefinido e permitir que a Azure Dev Spaces sincronize o `NuGet.Config` ficheiro, atualize o `azds.yaml` ficheiro:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Se não estiveres a usar o Git, podes saltar este passo.

Da próxima vez que correr `azds up` ou bater no Visual Studio Code ou no Visual `F5` Studio, o Azure Dev Spaces sincronizará o `NuGet.Config` ficheiro para instalar dependências de pacotes.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o NuGet e como funciona.](/nuget/what-is-nuget)
