---
title: Exemplos do Azure Service Fabric contentor aplicação manifestos | Documentos da Microsoft
description: Saiba como configurar a aplicação e serviço manifestos definições para uma aplicação do Service Fabric.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: ryanwi
ms.openlocfilehash: 8336f0a63f74aa0db176adbb3baf462c903ba2ec
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47095584"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Exemplos de manifesto de aplicação do Service Fabric e serviço
Esta seção contém exemplos de manifestos de aplicações e serviços. Estes exemplos não se destinam a mostrar cenários importantes, mas para mostrar as diferentes definições que estão disponíveis e como utilizá-los. 

Segue-se um índice dos recursos mostrados e o manifest(s) de exemplo são uma parte.

|Funcionalidade|Manifesto|
|---|---|
|[Governação de recursos](service-fabric-resource-governance.md)|[Manifesto da aplicação de serviços fiável](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [manifesto da aplicação de contentor](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Executar um serviço como uma conta de administrador local](service-fabric-application-runas-security.md)|[Manifesto da aplicação de serviços fiável](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Aplicar uma política predefinida para todos os pacotes de código de serviço](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Manifesto da aplicação de serviços fiável](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Criar principais de utilizador e grupo](service-fabric-application-runas-security.md)|[Manifesto da aplicação de serviços fiável](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|partilhar um pacote de dados entre instâncias de serviço|[Manifesto da aplicação de serviços fiável](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Substituir os pontos finais de serviço](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Manifesto da aplicação de serviços fiável](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Executar um script na inicialização do serviço](service-fabric-run-script-at-service-startup.md)|[Manifesto do serviço VotingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Definir um ponto de final HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[Manifesto do serviço VotingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Declarar um pacote de configuração](service-fabric-application-and-service-manifests.md)|[Manifesto do serviço de VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Declarar um pacote de dados](service-fabric-application-and-service-manifests.md)|[Manifesto do serviço de VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Substituir as variáveis de ambiente](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifesto de aplicação de contentor](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Configurar o mapeamento de porta do contentor-a-anfitrião](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Manifesto de aplicação de contentor](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Configurar a autenticação de registo de contentor](service-fabric-get-started-containers.md#configure-container-registry-authentication)|[Manifesto de aplicação de contentor](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Definir o modo de isolamento](service-fabric-get-started-containers.md#configure-isolation-mode)|[Manifesto de aplicação de contentor](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Especifique as imagens de contentor específicas de compilação do SO](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Manifesto de aplicação de contentor](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Definir variáveis de ambiente](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifesto de serviço do contentor FrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [manifesto do serviço de contentor BackEndService](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Configurar um ponto final](service-fabric-get-started-containers.md#configure-communication)|[Manifesto de serviço do contentor FrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [manifesto do serviço de contentor BackEndService](service-fabric-manifest-example-container-app.md#backendservice-service-manifest), [VotingData manifesto de serviço](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|passar comandos para o contentor|[Manifesto de serviço do contentor FrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Importar um certificado para um contentor](service-fabric-securing-containers.md)|[Manifesto de serviço do contentor FrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Configurar controladores de volume](service-fabric-containers-volume-logging-drivers.md)|[Manifesto de serviço do contentor BackEndService](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

