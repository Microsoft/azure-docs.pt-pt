---
title: Configurar as funções do Azure para fornecedores personalizados do Azure
description: Este tutorial irá abordar como criar uma função do Azure e configurá-lo para trabalhar com fornecedores de personalizados do Azure
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7e4de43659db88bfd9aad40cc3b9f1753189bba
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799995"
---
# <a name="setup-azure-functions-for-azure-custom-providers"></a>Configurar as funções do Azure para fornecedores personalizados do Azure

Fornecedores personalizados permitem-lhe personalizar fluxos de trabalho no Azure. Um provedor personalizado é um contrato entre o Azure e um `endpoint`. Este tutorial irá percorrer o processo de configuração de uma função do Azure para funcionar como um provedor personalizado `endpoint`.

Este tutorial é dividido nos seguintes passos:

- Criar a função do Azure
- Instalar os enlaces de tabelas do Azure
- Atualizar os métodos RESTful HTTP
- Adicionar pacotes NuGet do Azure Resource Manager

Neste tutorial, irá criar nos tutoriais seguintes:

- [Criar a sua primeira função do Azure através do portal do Azure](../azure-functions/functions-create-first-azure-function.md)

## <a name="creating-the-azure-function"></a>Criar a função do Azure

> [!NOTE]
> Neste tutorial, vamos criar um ponto de extremidade de serviço simples usando uma função do Azure, mas um provedor personalizado, pode utilizar qualquer público acessível `endpoint`. O Azure Logic Apps, API Management do Azure e aplicações Web do Azure são algumas ótimas alternativas.

Para iniciar este tutorial, deve seguir o tutorial [criando sua primeira função do Azure no portal do Azure](../azure-functions/functions-create-first-azure-function.md). O tutorial irá criar uma função de webhook do .NET core que pode ser modificada no portal do Azure.

## <a name="install-azure-table-bindings"></a>Instalar os enlaces de tabelas do Azure

Nesta secção irá percorrer os passos rápidos para instalar os enlaces de armazenamento de tabelas do Azure.

1. Navegue para o `Integrate` separador para o HttpTrigger.
2. Clique no `+ New Input`.
3. Selecione `Azure Table Storage`.
4. Instalar o `Microsoft.Azure.WebJobs.Extensions.Storage` se não estiver já instalado.
5. Atualização do `Table parameter name` para "tableStorage" e o `Table name` para "myCustomResources".
6. Guarde o parâmetro de entrada atualizado.

![Descrição geral do fornecedor personalizado](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Atualizar os métodos RESTful HTTP

Esta secção irá percorrer passos rápidos para configurar a função do Azure incluir os métodos de pedido RESTful do fornecedor personalizado.

1. Navegue para o `Integrate` separador para o HttpTrigger.
2. Atualização do `Selected HTTP methods` para: OBTER, POST, DELETE e PUT.

![Descrição geral do fornecedor personalizado](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="modifying-the-csproj"></a>Modificar o csproj

> [!NOTE]
> Se o csproj está em falta do diretório, podem ser adicionado manualmente ou irá aparecer uma vez o `Microsoft.Azure.WebJobs.Extensions.Storage` extensão está instalada na função.

Em seguida, vamos atualizar o ficheiro csproj para incluir as bibliotecas NuGet útil que tornam mais fácil de analisar pedidos recebidos de fornecedores personalizados. Siga os passos indicados em [adicionar extensões a partir do portal do](../azure-functions/install-update-binding-extensions-manual.md) e atualizar o csproj para incluir as seguintes referências de pacote:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Ficheiro de exemplo csproj:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, vamos configurar uma função do Azure para funcionar como um provedor personalizado do Azure `endpoint`. Vá para o artigo seguinte para saber como criar um provedor personalizado RESTful `endpoint`.

- [Tutorial: Criação de um ponto de extremidade RESTful fornecedor personalizado](./tutorial-custom-providers-function-authoring.md)
