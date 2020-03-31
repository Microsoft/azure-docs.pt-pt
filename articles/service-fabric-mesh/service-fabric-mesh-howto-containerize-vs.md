---
title: Colocar uma aplicação .NET existente para o Service Fabric Mesh em contentores
description: Adicione suporte de orquestração de recipientes de malha de tecido de serviço aos projetos ASP.NET e consola que utilizam a estrutura completa .NET.
author: dkkapur
ms.author: dekapur
ms.date: 11/08/2018
ms.topic: conceptual
ms.openlocfilehash: d67ea5bb7df5910ec87e69adf3c414c303bf0182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462044"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Colocar uma aplicação .NET existente para o Service Fabric Mesh em contentores

Este artigo mostra-lhe como adicionar suporte de orquestração de recipientes de malha de tecido de serviço a uma aplicação existente .NET.

No Visual Studio 2017, pode adicionar suporte de contentorização a projetos ASP.NET e Consolaque sustem a estrutura completa .NET.

> [!NOTE]
> Os projetos .NET **Core** não são atualmente suportados.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Certifique-se de que criou o seu ambiente de [desenvolvimento.](service-fabric-mesh-howto-setup-developer-environment-sdk.md) Isto inclui a instalação do tempo de execução do Tecido de Serviço, SDK, Docker, Visual Studio 2017, e a criação de um cluster local.

## <a name="open-an-existing-net-app"></a>Abra uma aplicação .NET existente

Abra a aplicação à qual pretende adicionar suporte à orquestração de contentores.

Se quiser experimentar um exemplo, pode utilizar a amostra de código [eShop.](https://github.com/MikkelHegn/ContainersSFLab) O resto deste artigo vai assumir que estamos a usar esse projeto, embora possa aplicar estes passos no seu próprio projeto.

Obtenha uma cópia do projeto **eShop:**

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

Uma vez descarregado, no Visual Studio 2017 abre **containersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln**.

## <a name="add-container-support"></a>Adicionar suporte de contentores
 
Adicione suporte de orquestração de recipientes a um projeto de ASP.NET ou consola existente utilizando as ferramentas de malha de tecido de serviço da seguinte forma:

No explorador de soluções Visual Studio, clique no nome do projeto (no exemplo, **eShopLegacyWebForms**) e, em seguida, escolha **Adicionar** > **Suporte orquestrador**de recipientes .
Aparece o diálogo de suporte do **orquestrador de recipientes adicionais.**

![Visual Studio adiciona diálogo orquestrador de recipientes](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

Escolha **a malha** de tecido de serviço a partir da queda e, em seguida, clique **OK**.

A ferramenta verifica então que o Docker está instalado, adiciona um Dockerfile ao seu projeto e puxa uma imagem de estivador para o seu projeto.  
Um projeto de aplicação de malha de tecido de serviço é adicionado à sua solução. Contém os perfis de publicação da Malha e ficheiros de configuração. O nome do projeto é o mesmo que o nome do seu projeto, com 'Aplicação' concatenada até ao fim, por exemplo, **eShopLegacyWebFormsApplication**. 

No novo projeto Mesh você verá duas pastas que você deve estar ciente:
- **Recursos de Aplicações** que contém ficheiros YAML que descrevem recursos adicionais de malha, como a rede.
- **Recursos de Serviço** que contém um ficheiro service.yaml que descreve como a sua aplicação deve ser executada quando implementada.

Assim que o suporte à orquestração de contentores for adicionado à sua aplicação, pode premir **F5** para desinbugar a sua aplicação .NET no seu cluster local de malha de tecido de serviço. Aqui está a aplicação eShop ASP.NET em execução num cluster de malha de tecido de serviço: 

![eShop app](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

Agora pode publicar a aplicação à Azure Service Fabric Mesh.

## <a name="next-steps"></a>Passos seguintes

Veja como publicar uma aplicação para service Fabric Mesh: [Tutorial- Implementar uma aplicação](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md) de malha de tecido de serviço