---
title: Como usar um feed do NuGet personalizado no Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Use um feed do NuGet personalizado para acessar e usar pacotes NuGet em um espaço de desenvolvimento do Azure.
keywords: Docker, kubernetes, Azure, AKS, serviço de contêiner do Azure, contêineres
manager: gwallace
ms.openlocfilehash: 9df095011c1ff66ff0c85993c7c85dffe62623b8
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873250"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Usar um feed do NuGet personalizado em um espaço de desenvolvimento do Azure

Um feed do NuGet fornece uma maneira conveniente de incluir fontes de pacote em um projeto do. Azure Dev Spaces precisa acessar esse feed para que as dependências sejam instaladas corretamente no contêiner do Docker.

## <a name="set-up-a-nuget-feed"></a>Configurar um feed do NuGet

Adicione uma [referência de pacote](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) para sua dependência no `*.csproj` arquivo sob o `PackageReference` nó. Por exemplo:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Crie um arquivo [NuGet. config](https://docs.microsoft.com/nuget/reference/nuget-config-file) na pasta do projeto e defina as `packageSources` seções `packageSourceCredentials` e para o feed do NuGet. A `packageSources` seção contém a URL do feed, que deve ser acessível do seu cluster AKs. O `packageSourceCredentials` são as credenciais para acessar o feed. Por exemplo:

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

Atualize seu Dockerfiles para copiar o `NuGet.Config` arquivo para a imagem. Por exemplo:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> No Windows, `NuGet.Config`, `Nuget.Config`e `nuget.config` todos funcionam como nomes de arquivo válidos. No Linux, apenas `NuGet.Config` um nome de arquivo válido para esse arquivo. Como o Azure Dev Spaces usa o Docker e o Linux, esse arquivo `NuGet.Config`deve ser nomeado. Você pode corrigir a nomenclatura manualmente ou executando `dotnet restore --configfile nuget.config`.


Se você estiver usando o Git, não deverá ter as credenciais para o feed do NuGet no controle de versão. Adicione `NuGet.Config` `NuGet.Config` ao para o seu projeto para que o arquivo não seja adicionado ao controle de versão. `.gitignore` Azure dev Spaces precisará desse arquivo durante o processo de criação da imagem de contêiner, mas, por padrão, ele respeita `.gitignore` as `.dockerignore` regras definidas no e durante a sincronização. Para alterar o padrão e permitir que Azure dev Spaces Sincronize o `NuGet.Config` arquivo, atualize o `azds.yaml` arquivo:

```yaml
build:
useGitIgnore: true
ignore:
- “!NuGet.Config”
```

Se você não estiver usando o Git, poderá ignorar esta etapa.

Na próxima vez que você `azds up` executar ou `F5` acessar o Visual Studio Code ou o Visual Studio, Azure dev Spaces sincronizará o arquivo e o usará para instalar as dependências do `NuGet.Config` pacote.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [o NuGet e como ele funciona](https://docs.microsoft.com/nuget/what-is-nuget).