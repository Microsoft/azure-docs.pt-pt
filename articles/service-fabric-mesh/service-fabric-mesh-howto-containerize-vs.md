---
title: Colocar uma aplicação .NET existente para o Service Fabric Mesh em contentores
description: Adicione suporte de orquestração de recipientes de malha de tecido de serviço a projetos de ASP.NET e consola que utilizam a estrutura completa .NET.
author: georgewallace
ms.author: gwallace
ms.date: 11/08/2018
ms.topic: conceptual
ms.openlocfilehash: b9c5053a2a49c942cc89bd50c65e13f3a2f8d9d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625888"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Colocar uma aplicação .NET existente para o Service Fabric Mesh em contentores

> [!IMPORTANT]
> A pré-estreia da Malha de Tecido de Serviço Azure foi reformada. As novas implementações deixarão de ser permitidas através da API de malha de malha de tecido de serviço. O apoio às implementações existentes continuará até 28 de abril de 2021.
> 
> Para mais detalhes, consulte [a pré-reforma da malha de malha de malha de tecido de serviço Azure.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

Este artigo mostra-lhe como adicionar suporte de orquestração de recipientes de malha de tecido de serviço a uma aplicação existente .NET.

No Visual Studio 2017, pode adicionar suporte de contentorização a projetos de ASP.NET e Consola que utilizam a estrutura completa .NET.

> [!NOTE]
> .NET Os projetos **core** não são atualmente suportados.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Certifique-se de que [criou o seu ambiente de desenvolvimento.](service-fabric-mesh-howto-setup-developer-environment-sdk.md) Isto inclui a instalação do tempo de execução do Tecido de Serviço, SDK, Docker, Visual Studio 2017 e a criação de um cluster local.

## <a name="open-an-existing-net-app"></a>Abra uma aplicação .NET existente

Abra a aplicação à qual pretende adicionar suporte à orquestração de contentores.

Se quiser experimentar um exemplo, pode utilizar a amostra de código [eShop.](https://github.com/MikkelHegn/ContainersSFLab) O resto deste artigo vai assumir que estamos a usar esse projeto, embora possas aplicar estes passos ao teu próprio projeto.

Obtenha uma cópia do projeto **eShop:**

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

Uma vez descarregado, no Visual Studio 2017 abre **ContentoresSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln**.

## <a name="add-container-support"></a>Adicionar suporte ao contentor
 
Adicione suporte de orquestração de contentores a um projeto de ASP.NET ou Consola existente utilizando as ferramentas de malha de tecido de serviço da seguinte forma:

No explorador de soluções Visual Studio, clique com o nome do projeto à direita (no exemplo, **eShopLegacyWebForms)** e, em seguida, escolha **Adicionar**  >  **Suporte orquestrador de contentores**.
Aparece o diálogo **de apoio do orquestrador de recipientes add.**

![Visual Studio adicionar diálogo de orquestrador de contentores](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

Escolha a malha de tecido de **serviço** a partir do drop-down e, em seguida, clique **em OK**.


>[!NOTE]
> A partir de 2 de novembro de 2020, [os limites de taxa de descarregamento aplicam-se](https://docs.docker.com/docker-hub/download-rate-limit/) a pedidos anónimos e autenticados para Docker Hub a partir de contas do plano Docker Free e são aplicados por endereço IP. Para mais detalhes, consulte [Authenticate with Docker Hub](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub).
>
> Para evitar uma taxa limitada, certifique-se de que o padrão `FROM microsoft/aspnet:4.7.2-windowsservercore-1803 AS base` no seu Dockerfile é substituído por `FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-1803 AS base`

A ferramenta verifica então que o Docker está instalado, adiciona um Dockerfile ao seu projeto e puxa uma imagem de estivador para o seu projeto.  
Um projeto de aplicação de malha de tecido de serviço é adicionado à sua solução. Contém os perfis de publicação da Malha e ficheiros de configuração. O nome do projeto é o mesmo que o nome do seu projeto, com 'Aplicação' concatenado até ao fim, por exemplo, **eShopLegacyWebFormsApplication**. 

No novo projeto Mesh verá duas pastas que deve estar ciente:
- **Recursos de aplicações** que contêm ficheiros YAML que descrevem recursos adicionais de Malha, como a rede.
- **Recursos de Serviço** que contém um ficheiro service.yaml que descreve como a sua aplicação deve ser executada quando implementada.

Assim que o suporte à orquestração de contentores for adicionado à sua aplicação, pode premir **F5** para depurar a sua aplicação .NET no seu cluster de malha de tecido de serviço local. Aqui está a aplicação eShop ASP.NET em execução num cluster de malha de tecido de serviço: 

![app eShop](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

Pode agora publicar a aplicação para a Azure Service Fabric Mesh.

## <a name="next-steps"></a>Passos seguintes

Veja como publicar uma app para Service Fabric Mesh: [Tutorial- Implementar uma aplicação de malha de tecido de serviço](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)