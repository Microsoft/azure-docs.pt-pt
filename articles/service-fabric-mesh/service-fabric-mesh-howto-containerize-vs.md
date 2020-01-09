---
title: Colocar uma aplicação .NET existente para o Service Fabric Mesh em contentores
description: Adicione Service Fabric suporte de orquestração de contêiner de malha a projetos de console e ASP.NET que usam o .NET Framework completo.
author: dkkapur
ms.author: dekapur
ms.date: 11/08/2018
ms.topic: conceptual
ms.openlocfilehash: d67ea5bb7df5910ec87e69adf3c414c303bf0182
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462044"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Colocar uma aplicação .NET existente para o Service Fabric Mesh em contentores

Este artigo mostra como adicionar Service Fabric suporte à orquestração de contêiner de malha a um aplicativo .NET existente.

No Visual Studio 2017, você pode adicionar suporte de contêineres ao ASP.NET e projetos de console que usam o .NET Framework completo.

> [!NOTE]
> Atualmente, não há suporte para projetos do .NET **Core** .

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Certifique-se de que você [configurou seu ambiente de desenvolvimento](service-fabric-mesh-howto-setup-developer-environment-sdk.md). Isso inclui a instalação do Service Fabric Runtime, SDK, Docker, Visual Studio 2017 e criação de um cluster local.

## <a name="open-an-existing-net-app"></a>Abrir um aplicativo .NET existente

Abra o aplicativo ao qual você deseja adicionar suporte à orquestração de contêiner.

Se você quiser experimentar um exemplo, poderá usar o exemplo de código [eshop](https://github.com/MikkelHegn/ContainersSFLab) . O restante deste artigo irá pressupor que estamos usando esse projeto, embora você possa aplicar essas etapas ao seu próprio projeto.

Obtenha uma cópia do projeto **eshop** :

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

Depois de baixado, no Visual Studio 2017 abra **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln**.

## <a name="add-container-support"></a>Adicionar suporte a contêiner
 
Adicione suporte de orquestração de contêiner a um ASP.NET ou projeto de console existente usando as ferramentas de malha Service Fabric da seguinte maneira:

No Gerenciador de soluções do Visual Studio, clique com o botão direito do mouse no nome do projeto (no exemplo, **eShopLegacyWebForms**) e escolha **Adicionar** > **contêiner suporte ao orquestrador**.
A caixa de diálogo **Adicionar suporte ao orquestrador de contêiner** é exibida.

![Caixa de diálogo Adicionar orquestrador de contêiner do Visual Studio](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

Escolha **Service Fabric malha** na lista suspensa e clique em **OK**.

Em seguida, a ferramenta verifica se o Docker está instalado, adiciona um Dockerfile ao seu projeto e puxa uma imagem do Docker para o seu projeto.  
Um projeto de aplicativo de malha Service Fabric é adicionado à sua solução. Ele contém seus perfis de publicação de malha e arquivos de configuração. O nome do projeto é o mesmo que o nome do projeto, com ' Application ' concatenado ao final, por exemplo, **eShopLegacyWebFormsApplication**. 

No novo projeto de malha, você verá duas pastas das quais deve estar ciente:
- **Recursos de aplicativo** que contêm arquivos YAML que descrevem recursos de malha adicionais, como a rede.
- **Recursos de serviço** que contêm um arquivo Service. YAML que descreve como seu aplicativo deve ser executado quando implantado.

Depois que o suporte à orquestração de contêiner é adicionado ao seu aplicativo, você pode pressionar **F5** para depurar seu aplicativo .net no cluster de malha Service Fabric local. Aqui está o aplicativo eShop ASP.NET em execução em um cluster de malha Service Fabric: 

![aplicativo eShop](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

Agora você pode publicar o aplicativo na malha de Service Fabric do Azure.

## <a name="next-steps"></a>Passos seguintes

Consulte Como publicar um aplicativo no Service Fabric malha: [tutorial – implantar um aplicativo de malha Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)