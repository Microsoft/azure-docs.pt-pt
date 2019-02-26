---
title: Crie contentores uma aplicação .NET existente para a malha do Service Fabric | Documentos da Microsoft
description: Adicionar suporte a malha a uma aplicação .NET existente
services: service-fabric-mesh
keywords: Crie contentores de malha do service fabric
author: dkkapur
ms.author: dekapur
ms.date: 11/08/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: bb4aacafaa90f16370bc097b5feab7791def4006
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806951"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Crie contentores uma aplicação .NET existente para a malha do Service Fabric

Este artigo mostra-lhe como adicionar suporte de orquestração do contentor de malha do Service Fabric a uma aplicação .NET existente.

No Visual Studio 2017, pode adicionar suporte de contentores para projetos de consola e do ASP.NET que usam o .NET framework completo.

> [!NOTE]
> .NET **core** projetos não são atualmente suportados.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Certifique-se de que [configurar o ambiente de desenvolvimento](service-fabric-mesh-howto-setup-developer-environment-sdk.md). Isto inclui o tempo de execução do Service Fabric, SDK, o Docker, o Visual Studio 2017, a instalar e criar um cluster local.

## <a name="open-an-existing-net-app"></a>Abrir uma aplicação .NET existente

Abra a aplicação à qual pretende adicionar o suporte de orquestração do contentor.

Se quiser experimentar um exemplo, pode utilizar o [eShop](https://github.com/MikkelHegn/ContainersSFLab) exemplo de código. O restante deste artigo será partem do princípio de que estamos a utilizar esse projeto, embora possa aplicar estas etapas para seu próprio projeto.

Obtenha uma cópia do **eShop** projeto:

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

Uma vez que transferiu, no Visual Studio 2017 open **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln**.

## <a name="add-container-support"></a>Adicionar suporte para contentores
 
Adicione suporte de orquestração do contentor para um projeto do ASP.NET ou a consola existente, usando as ferramentas de malha de recursos de infraestrutura do serviço da seguinte forma:

No Explorador de soluções do Visual Studio, clique com botão direito no nome do projeto (no exemplo, **eShopLegacyWebForms**) e, em seguida, escolha **Add** > **suporte de Orchestrator de contentor** .
O **adicionar suporte de Orchestrator de contentor** é apresentada a caixa de diálogo.

![Visual Studio adicionar caixa de diálogo de orchestrator de contentor](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

Escolher **malha do Service Fabric** da lista pendente e, em seguida, clique em **OK**.

A ferramenta verifica, em seguida, o que o Docker está instalado, adiciona um Dockerfile ao seu projeto e extrai uma imagem do docker para o seu projeto.  
Um projeto de aplicativo de malha do Service Fabric é adicionado à sua solução. Ele contém sua malha publicar perfis e ficheiros de configuração. O nome do projeto é o mesmo que o nome do projeto, com 'Application' concatenado no final, por exemplo, **eShopLegacyWebFormsApplication**. 

O novo projeto de malha verá duas pastas, deve estar atento:
- **Recursos de aplicação** que contém ficheiros YAML que descrevem os recursos de malha adicionais, como a rede.
- **Recursos de serviço** que contém um ficheiro de service.yaml que descreve como seu aplicativo deve ser executado quando implementada.

Assim que o suporte de orquestração do contentor é adicionado à sua aplicação, pode premir **F5** para depurar a sua aplicação .NET no seu cluster de malha do Service Fabric local. Segue-se a aplicação ASP.NET eShop em execução num cluster do Service Fabric em malha: 

![aplicação de eShop](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

Agora pode publicar a aplicação para o modo de malha do Azure Service Fabric.

## <a name="next-steps"></a>Passos Seguintes

Veja como publicar uma aplicação para a malha de recursos de infraestrutura do serviço: [Tutorial-implementar uma aplicação de malha do Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)