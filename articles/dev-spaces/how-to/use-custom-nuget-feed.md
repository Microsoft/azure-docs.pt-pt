---
title: Utilize um feed personalizado do NuGet
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Utilize um feed NuGet personalizado para aceder e utilizar pacotes NuGet num Espaço Azure Dev.
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contentores Azure, contentores
manager: gwallace
ms.openlocfilehash: 39984a3b3a1be64a497fb8088559ccfcdee4f1c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325731"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Use um feed nuGet personalizado com espaços Azure Dev

Um feed NuGet fornece uma maneira conveniente de incluir fontes de pacote num projeto. A Azure Dev Spaces precisa de aceder a este feed para que as dependências sejam corretamente instaladas no recipiente Docker.

## <a name="set-up-a-nuget-feed"></a>Configurar um feed NuGet

Adicione uma referência de [pacote](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) `*.csproj` para a `PackageReference` sua dependência no ficheiro sob o nó. Por exemplo:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Crie um ficheiro [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) na `packageSources` pasta `packageSourceCredentials` do projeto e detete as secções e secções para o seu feed NuGet. A `packageSources` secção contém o seu url de alimentação, que deve ser acessível a partir do seu cluster AKS. São `packageSourceCredentials` as credenciais para aceder ao feed. Por exemplo:

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

Atualize os seus `NuGet.Config` Ficheiros Docker para copiar o ficheiro para a imagem. Por exemplo:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> No `NuGet.Config`Windows, `Nuget.Config`e `nuget.config` todos funcionam como nomes de ficheiros válidos. No Linux, `NuGet.Config` apenas é um nome de ficheiro válido para este ficheiro. Uma vez que a Azure Dev Spaces utiliza `NuGet.Config`o Docker e o Linux, este ficheiro deve ser nomeado. Pode fixar o nome manualmente `dotnet restore --configfile nuget.config`ou executando .


Se estiver a utilizar o Git, não deve ter as credenciais para o seu feed NuGet no controlo da versão. Adicione `NuGet.Config` ao `.gitignore` para o seu `NuGet.Config` projeto para que o ficheiro não seja adicionado ao controlo da versão. A Azure Dev Spaces necessitará deste ficheiro durante o processo de construção `.gitignore` `.dockerignore` de imagem do contentor, mas por padrão, respeita as regras definidas dentro e durante a sincronização. Para alterar o padrão e permitir que a Azure Dev Spaces sincronize o `NuGet.Config` ficheiro, atualize o `azds.yaml` ficheiro:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Se não estiver a usar o Git, pode saltar este passo.

Da próxima vez `azds up` que `F5` executar ou acertar no Visual Studio Code ou Visual `NuGet.Config` Studio, a Azure Dev Spaces sincronizará o ficheiro para instalar dependências de pacotes.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o NuGet e como funciona.](https://docs.microsoft.com/nuget/what-is-nuget)