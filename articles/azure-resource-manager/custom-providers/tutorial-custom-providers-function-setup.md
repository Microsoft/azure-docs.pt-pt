---
title: Configurar as Funções do Azure
description: Este tutorial é sobre como criar uma app de função Azure e conecê-la para trabalhar com a Azure Custom Providers
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 55554678047faeedd16b78dea61a42d50fd59491
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737324"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Configurar funções Azure para fornecedores personalizados Azure

Um fornecedor personalizado é um contrato entre a Azure e um ponto final. Com fornecedores personalizados, pode alterar fluxos de trabalho em Azure. Este tutorial mostra como configurar uma aplicação de função Azure para funcionar como um ponto final personalizado do fornecedor.

## <a name="create-the-azure-function-app"></a>Criar a app de função Azure

> [!NOTE]
> Neste tutorial, cria-se um ponto final de serviço simples que utiliza uma aplicação de função Azure. No entanto, um fornecedor personalizado pode usar qualquer ponto final acessível ao público. Alternativas incluem Aplicações Lógicas Azure, Gestão API Azure e a funcionalidade de Aplicações Web do Azure App Service.

Para iniciar este tutorial, deve seguir primeiro o tutorial [Crie a sua primeira aplicação de função Azure no portal Azure.](../../azure-functions/functions-get-started.md) Este tutorial cria uma função webhook .NET core que pode ser modificada no portal Azure. É também a base para o atual tutorial.

## <a name="install-azure-table-storage-bindings"></a>Instale encadernações de armazenamento de mesa Azure

Para instalar as ligações de armazenamento da Tabela Azure:

1. Aceda ao **separador Integração** para o HttpTrigger.
1. Selecione **+ Entrada Nova**.
1. Selecione **armazenamento de mesa Azure**.
1. Instale a extensão Microsoft.Azure.WebJobs.Extensions.Storage se ainda não estiver instalada.
1. Na caixa de **nome do parâmetro quadro,** **introduza a tabelaSStorage**.
1. Na caixa de **nome de tabela,** **insira myCustomResources**.
1. **Selecione Guardar** para guardar o parâmetro de entrada atualizado.

![Visão geral do fornecedor personalizado mostrando encadernações de mesa](./media/create-custom-provider/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Atualizar métodos HTTP RESTful

Para configurar a função Azure para incluir os métodos de pedido RESTful do fornecedor personalizado:

1. Aceda ao **separador Integração** para o HttpTrigger.
1. Nos **métodos HTTP selecionados**, selecione **GET,** **POST,** **DELETE** e **PUT**.

![Visão geral do fornecedor personalizado mostrando métodos HTTP](./media/create-custom-provider/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Adicionar pacotes NuGet do Gestor de Recursos Azure

> [!NOTE]
> Se o seu ficheiro de projeto C# faltar no diretório do projeto, pode adicioná-lo manualmente. Ou aparecerá depois da extensão Microsoft.Azure.WebJobs.Extensions.Storage ser instalada na aplicação de função.

Em seguida, atualize o ficheiro do projeto C# para incluir bibliotecas nuGet úteis. Estas bibliotecas facilitam a análise dos pedidos de entrada de fornecedores personalizados. Siga os [passos](../../azure-functions/functions-bindings-register.md) para adicionar extensões do portal e atualizar o ficheiro do projeto C# para incluir as seguintes referências de pacote:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

O seguinte elemento XML é um ficheiro de projeto exemplo C#:

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

Neste tutorial, criou uma aplicação de função Azure para funcionar como um ponto final de fornecedor personalizado Azure.

Para aprender a autoria de um ponto final de fornecedor personalizado RESTful, consulte [Tutorial: Autoria de um ponto final de fornecedor personalizado RESTful](./tutorial-custom-providers-function-authoring.md).