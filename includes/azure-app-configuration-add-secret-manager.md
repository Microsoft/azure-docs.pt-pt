---
ms.openlocfilehash: b1be5e903994f0a2c4a3d457f0c2bb5572a889c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98663034"
---
## <a name="add-secret-manager"></a>Adicionar Gerente Secreto

Uma ferramenta chamada Secret Manager armazena dados sensíveis para trabalhos de desenvolvimento fora da sua árvore do projeto. Esta abordagem ajuda a evitar a partilha acidental de segredos de aplicações no código fonte. Complete os seguintes passos para permitir a utilização de Gestor Secreto no projeto ASP.NET Core:

#### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

Navegue para o diretório de raiz do projeto, e executar o seguinte comando para permitir o armazenamento de segredos no projeto:

```dotnetcli
dotnet user-secrets init
```

Um `UserSecretsId` elemento que contenha um GUID é adicionado ao ficheiro *.csproj:*

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>net5.0</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

Navegue para o diretório de raiz do projeto, e executar o seguinte comando para permitir o armazenamento de segredos no projeto:

```dotnetcli
dotnet user-secrets init
```

Um `UserSecretsId` elemento que contenha um GUID é adicionado ao ficheiro *.csproj:*

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

1. Abra o ficheiro *.csproj.*

1. Adicione um `UserSecretsId` elemento ao ficheiro *.csproj* como mostrado aqui. Pode usar o mesmo GUID, ou pode substituir este valor pelo seu.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>
    
    </Project>
    ```
    
1. Guarde o ficheiro *.csproj.*

---

> [!TIP]
> Para saber mais sobre o Secret Manager, consulte [o armazenamento seguro de segredos de aplicações em desenvolvimento em ASP.NET Core.](/aspnet/core/security/app-secrets)
