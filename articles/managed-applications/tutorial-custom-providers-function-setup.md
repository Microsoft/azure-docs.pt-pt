---
title: Configurar Azure Functions para provedores personalizados do Azure
description: Este tutorial vai sobre como criar um aplicativo de funções do Azure e configurá-lo para trabalhar com os provedores personalizados do Azure
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 6b5ab6948d382a9925c9ced91e04f360ecf51a0e
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173022"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Configurar Azure Functions para provedores personalizados do Azure

Um provedor personalizado é um contrato entre o Azure e um ponto de extremidade. Com provedores personalizados, você pode alterar os fluxos de trabalho no Azure. Este tutorial mostra como configurar um aplicativo de funções do Azure para funcionar como um ponto de extremidade de provedor personalizado.

## <a name="create-the-azure-function-app"></a>Criar o aplicativo de funções do Azure

> [!NOTE]
> Neste tutorial, você cria um ponto de extremidade de serviço simples que usa um aplicativo de funções do Azure. No entanto, um provedor personalizado pode usar qualquer ponto de extremidade publicamente acessível. As alternativas incluem aplicativos lógicos do Azure, gerenciamento de API do Azure e o recurso de aplicativos Web do serviço de Azure App.

Para iniciar este tutorial, primeiro você deve seguir o tutorial [criar seu primeiro aplicativo de funções do Azure no portal do Azure](../azure-functions/functions-create-first-azure-function.md). Esse tutorial cria uma função de webhook do .NET Core que pode ser modificada no portal do Azure. Também é a base para o tutorial atual.

## <a name="install-azure-table-storage-bindings"></a>Instalar associações de armazenamento de tabelas do Azure

Para instalar as associações de armazenamento de tabela do Azure:

1. Vá para a guia **integrar** para o HttpTrigger.
1. Selecione **+ nova entrada**.
1. Selecione **armazenamento de tabelas do Azure**.
1. Instale a extensão Microsoft. Azure. webjobs. Extensions. Storage se ela ainda não estiver instalada.
1. Na caixa **nome do parâmetro de tabela** , digite **tableStorage**.
1. Na caixa **nome da tabela** , digite **myCustomResources**.
1. Selecione **salvar** para salvar o parâmetro de entrada atualizado.

![Visão geral do provedor personalizado mostrando associações de tabela](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Atualizar métodos de HTTP RESTful

Para configurar a função do Azure para incluir os métodos de solicitação RESTful de provedor personalizado:

1. Vá para a guia **integrar** para o HttpTrigger.
1. Em **métodos http selecionados**, selecione **obter**, **postar**, **excluir**e **colocar**.

![Visão geral do provedor personalizado mostrando métodos HTTP](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Adicionar Azure Resource Manager pacotes NuGet

> [!NOTE]
> Se o C# arquivo de projeto estiver ausente no diretório do projeto, você poderá adicioná-lo manualmente. Ou ele será exibido depois que a extensão Microsoft. Azure. webjobs. Extensions. Storage estiver instalada no aplicativo de funções.

Em seguida, atualize C# o arquivo de projeto para incluir bibliotecas de NuGet úteis. Essas bibliotecas facilitam a análise de solicitações de entrada de provedores personalizados. Siga as etapas para [adicionar extensões do portal](../azure-functions/install-update-binding-extensions-manual.md) e atualizar o C# arquivo de projeto para incluir as seguintes referências de pacote:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

O seguinte elemento XML é um exemplo C# de arquivo de projeto:

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

Neste tutorial, você configura um aplicativo de funções do Azure para trabalhar como um ponto de extremidade do provedor personalizado do Azure.

Para saber como criar um ponto de extremidade do provedor personalizado RESTful [, consulte o tutorial: Criação de um ponto de extremidade](./tutorial-custom-providers-function-authoring.md)do provedor personalizado RESTful.

