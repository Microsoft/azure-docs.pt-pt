---
title: Utilize um feed personalizado do NuGet
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Use um feed do NuGet personalizado para acessar e usar pacotes NuGet em um espaço de desenvolvimento do Azure.
keywords: Docker, kubernetes, Azure, AKS, serviço de contêiner do Azure, contêineres
manager: gwallace
ms.openlocfilehash: 39984a3b3a1be64a497fb8088559ccfcdee4f1c6
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325731"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Usar um feed do NuGet personalizado com o Azure Dev Spaces

Um feed do NuGet fornece uma maneira conveniente de incluir fontes de pacote em um projeto do. Azure Dev Spaces precisa acessar esse feed para que as dependências sejam instaladas corretamente no contêiner do Docker.

## <a name="set-up-a-nuget-feed"></a>Configurar um feed do NuGet

Adicione uma [referência de pacote](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) para sua dependência no arquivo de `*.csproj` sob o nó `PackageReference`. Por exemplo:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Crie um arquivo [NuGet. config](https://docs.microsoft.com/nuget/reference/nuget-config-file) na pasta do projeto e defina as seções `packageSources` e `packageSourceCredentials` para o feed do NuGet. A seção `packageSources` contém a URL do feed, que deve ser acessível do seu cluster AKS. As `packageSourceCredentials` são as credenciais para acessar o feed. Por exemplo:

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

Atualize seu Dockerfiles para copiar o arquivo de `NuGet.Config` para a imagem. Por exemplo:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> No Windows, `NuGet.Config`, `Nuget.Config`e `nuget.config` funciona como nomes de arquivo válidos. No Linux, somente `NuGet.Config` é um nome de arquivo válido para esse arquivo. Como o Azure Dev Spaces usa o Docker e o Linux, esse arquivo deve ser nomeado `NuGet.Config`. Você pode corrigir a nomenclatura manualmente ou executando `dotnet restore --configfile nuget.config`.


Se você estiver usando o Git, não deverá ter as credenciais para o feed do NuGet no controle de versão. Adicione `NuGet.Config` ao `.gitignore` do seu projeto para que o arquivo de `NuGet.Config` não seja adicionado ao controle de versão. Azure Dev Spaces precisará desse arquivo durante o processo de criação da imagem de contêiner, mas, por padrão, ele respeita as regras definidas em `.gitignore` e `.dockerignore` durante a sincronização. Para alterar o padrão e permitir que Azure Dev Spaces Sincronize o arquivo de `NuGet.Config`, atualize o arquivo `azds.yaml`:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Se você não estiver usando o Git, poderá ignorar esta etapa.

Na próxima vez que você executar `azds up` ou pressionar `F5` no Visual Studio Code ou no Visual Studio, Azure Dev Spaces sincronizará o arquivo de `NuGet.Config` usá-lo para instalar as dependências do pacote.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o NuGet e como ele funciona](https://docs.microsoft.com/nuget/what-is-nuget).