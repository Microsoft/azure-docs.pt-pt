---
title: Descrição Geral do Azure Service Fabric
description: Service Fabric é uma plataforma de sistemas distribuídos para a construção de microserviços escaláveis, fiáveis e facilmente geridos.
ms.topic: overview
ms.date: 09/22/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: b6a14ba3f905fbef3fc3796b616cd594941a3d60
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028636"
---
# <a name="overview-of-azure-service-fabric"></a>Descrição Geral do Azure Service Fabric

A Azure Service Fabric é uma [plataforma de sistemas distribuídos](#container-orchestration) que facilita a embalagem, implantação e gestão de microserviços e contentores escaláveis e fiáveis. O Service Fabric também aborda os desafios significativos no [desenvolvimento e gestão de](#application-lifecycle-management) aplicações nativas em nuvem.

Um diferenciador chave da Service Fabric é o seu forte foco na construção de serviços estatais. Pode utilizar o [modelo de programação](#stateless-and-stateful-microservices) do Service Fabric ou executar serviços estatais contentorizados escritos em qualquer idioma ou código. Pode criar [clusters de Tecido de Serviço](#any-os-any-cloud)em qualquer lugar, incluindo Windows Server e Linux nas instalações e outras nuvens públicas, além do Azure.

![A plataforma Service Fabric fornece gestão de ciclo de vida, disponibilidade, orquestração, modelos de programação, saúde e monitorização, ferramentas dev e ops, e autoscaling -in Azure, em instalações, em outras nuvens, e na sua máquina dev][Image1]

Atualmente, o Service Fabric está subjacente a muitos serviços da Microsoft, entre os quais a Base de Dados SQL do Azure, o Azure Cosmos DB, o Cortana, o Microsoft Power BI, o Microsoft Intune, os Hubs de Eventos do Azure, o Hub IoT do Azure, o Dynamics 365, o Skype para Empresas e muitos dos mais importantes serviços do Azure.

## <a name="container-orchestration"></a>Orquestração de contentores

Service Fabric é o orquestrador de [contentores](service-fabric-cluster-resource-manager-introduction.md) da Microsoft para implantar e gerir microserviços através de um conjunto de máquinas, beneficiando das lições aprendidas a gerir os serviços da Microsoft em larga escala. O Tecido de Serviço pode implementar aplicações em segundos, em alta densidade com centenas ou milhares de aplicações ou contentores por máquina. Com o Service Fabric, pode misturar ambos os serviços em processos e serviços em contentores na mesma aplicação.

Saiba mais sobre conceitos fundamentais [do Service Fabric,](service-fabric-content-roadmap.md) modelos de programação, ciclo de vida da aplicação, testes, clusters e monitorização de saúde.

## <a name="stateless-and-stateful-microservices"></a>Microserviços apátridas e imponentes

A Service Fabric proporciona um tempo de execução sofisticado e leve que suporta microserviços apátridas e imponentes. Um diferenciador chave da Service Fabric é o seu suporte robusto para a construção de serviços estatais, quer com [modelos de programação incorporados em](service-fabric-choose-framework.md) Tecido de Serviço ou serviços estatais contentorizados.

Saiba mais sobre [os cenários](service-fabric-application-scenarios.md) de aplicação que beneficiam dos serviços estatais da Service Fabric.

## <a name="application-lifecycle-management"></a>Gestão do ciclo de vida das aplicações

A Service Fabric fornece suporte para o ciclo de vida da aplicação completa e CI/CD de aplicações em nuvem, incluindo contentores: desenvolvimento através da implantação, monitorização diária, gestão e manutenção, para um eventual desmantelamento. O Service Fabric está integrado com ferramentas de CI/CD como o [Azure Pipelines](https://www.visualstudio.com/team-services/), o [Jenkins](https://jenkins.io/index.html) e o [Octopus Deploy](https://octopus.com/) e pode ser utilizado com qualquer outra ferramenta de CI/CD popular.

Para obter mais informações sobre a gestão do ciclo de vida das aplicações, leia [Application lifecycle](service-fabric-application-lifecycle.md) (Ciclo de vida das aplicações). Para implementar aplicações existentes no Service Fabric, consulte [implementar um hóspede executável.](service-fabric-deploy-existing-app.md)

## <a name="any-os-any-cloud"></a>Qualquer SO, qualquer cloud

Pode criar clusters para o Tecido de Serviço em muitos ambientes, incluindo [o Azure ou nas instalações](service-fabric-deploy-anywhere.md), no [Windows Server ou linux](service-fabric-linux-windows-differences.md). Pode ainda criar clusters noutras clouds públicas. O ambiente de desenvolvimento no Service Fabric SDK é idêntico ao ambiente de produção, sem emuladores envolvidos. Por outras palavras, o que funciona no seu cluster de desenvolvimento local é o que se desdobra para os seus aglomerados em outros ambientes.

Para [o desenvolvimento do Windows,](service-fabric-get-started.md)o Service Fabric .NET SDK está integrado com o Visual Studio e o PowerShell. Para o [desenvolvimento do Linux,](service-fabric-get-started-linux.md)o Service Fabric Java SDK está integrado com o Eclipse, e o Yeoman é utilizado para gerar modelos para java, .NET Core e aplicações de contentores.

## <a name="compliance"></a>Conformidade

O Azure Service Fabric Resource Provider está disponível em todas as regiões do Azure e está em conformidade com todas as certificações de conformidade Azure, incluindo: SOC, ISO, PCI DSS, HIPAA e RGPD. Para obter uma lista completa, consulte [as Ofertas de Conformidade da Microsoft.](https://www.microsoft.com/trustcenter/compliance/complianceofferings)

## <a name="next-steps"></a>Passos seguintes

Crie e implemente a sua primeira aplicação no Azure Service Fabric:

> [!div class="nextstepaction"]
> [Arranque rápido do tecido de serviço][sf-quickstart]

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
[sf-quickstart]: ./service-fabric-quickstart-dotnet.md
