---
ms.openlocfilehash: c3c5b8ef94b507cad433e587c9ebfc2ec16c0ff9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91440412"
---
## <a name="add-secret-manager"></a>Adicionar Gerente Secreto

Uma ferramenta chamada Secret Manager armazena dados sensíveis para trabalhos de desenvolvimento fora da sua árvore do projeto. Esta abordagem ajuda a evitar a partilha acidental de segredos de aplicações no código fonte. Complete os seguintes passos para permitir a utilização de Gestor Secreto no projeto ASP.NET Core:

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
