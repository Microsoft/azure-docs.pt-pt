---
title: Configurar as Funções do Azure
description: Este tutorial passa por cima de como criar uma aplicação de função Azure e configura-a para trabalhar com fornecedores personalizados Azure
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: b6984bfdf7ec078d02999bccf77f3592cfbcab27
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649989"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Configurar funções Azure para fornecedores personalizados Azure

Um fornecedor personalizado é um contrato entre o Azure e um ponto final. Com fornecedores personalizados, pode alterar fluxos de trabalho em Azure. Este tutorial mostra como configurar uma aplicação de função Azure para funcionar como um ponto final personalizado do fornecedor.

## <a name="create-the-azure-function-app"></a>Criar a aplicação de função Azure

> [!NOTE]
> Neste tutorial, cria-se um simples ponto final de serviço que utiliza uma aplicação de função Azure. No entanto, um fornecedor personalizado pode usar qualquer ponto final acessível ao público. Alternativas incluem Aplicações Lógicas Azure, Gestão API Azure, e a funcionalidade de Aplicações Web do Azure App Service.

Para iniciar este tutorial, deve primeiro seguir o tutorial Criar a [sua primeira aplicação de função Azure no portal Azure](../../azure-functions/functions-create-first-azure-function.md). Este tutorial cria uma função de webhook de núcleo .NET que pode ser modificada no portal Azure. É também a base para o tutorial atual.

## <a name="install-azure-table-storage-bindings"></a>Instalar encadernações de armazenamento de mesa azure

Para instalar as encadernações de armazenamento da mesa azul:

1. Vá ao separador **Integração** para o HttpTrigger.
1. Selecione **+ Nova Entrada**.
1. Selecione **Armazenamento de Mesa Azure**.
1. Instale a extensão Microsoft.Azure.WebJobs.Extensions.Storage se ainda não estiver instalada.
1. Na caixa de nome do **parâmetro de mesa,** introduza **o depósito de mesa**.
1. Na caixa de **nomes de tabela,** introduza **os meus Recursos Personalizados.**
1. Selecione **Guardar** para guardar o parâmetro de entrada atualizado.

![Visão geral do fornecedor personalizado mostrando encadernações de tabela](./media/create-custom-provider/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Atualizar métodos RESTful HTTP

Para configurar a função Azure para incluir os métodos de pedido RESTful do fornecedor personalizado:

1. Vá ao separador **Integração** para o HttpTrigger.
1. Sob **os métodos HTTP selecionados,** selecione **GET,** **POST,** **DELETE**, e **PUT**.

![Visão geral do fornecedor personalizado mostrando métodos HTTP](./media/create-custom-provider/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Adicione pacotes NuGet do Gestor de Recursos Azure

> [!NOTE]
> Se o seu ficheiro de projeto C# estiver ausente do diretório do projeto, pode adicioná-lo manualmente. Ou aparecerá depois da Microsoft.Azure.WebJobs.Extensions.Installed na aplicação de função.

Em seguida, atualize o ficheiro do projeto C# para incluir bibliotecas nuGet úteis. Estas bibliotecas facilitam a análise dos pedidos de entrada de fornecedores personalizados. Siga os passos para [adicionar extensões do portal](../../azure-functions/install-update-binding-extensions-manual.md) e atualizar o ficheiro do projeto C# para incluir as seguintes referências do pacote:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

O seguinte elemento XML é um exemplo C# ficheiro de projeto:

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

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configura uma aplicação de função Azure para funcionar como um ponto final de fornecedor personalizado Azure.

Para aprender a autorar um ponto final de fornecedor personalizado RESTful, consulte [Tutorial: Authoring a RESTful custom provider endpoint](./tutorial-custom-providers-function-authoring.md).

