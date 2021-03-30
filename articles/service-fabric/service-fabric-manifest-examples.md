---
title: Aplicação Azure Service Fabric manifestam exemplos
description: Saiba como configurar as configurações de aplicação e manifesto de serviço para uma aplicação de Tecido de Serviço.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 6f44d2ddaac11545a3a3dc52fd20e02263c6ad67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "75451647"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Aplicação de tecido de serviço e exemplos manifestos de serviço
Esta secção contém exemplos de manifestos de aplicação e serviço. Estes exemplos não se destinam a mostrar cenários importantes, mas a mostrar as diferentes configurações que estão disponíveis e como usá-las. 

Segue-se um índice das características apresentadas e o exemplo manifesto(s) em que fazem parte.

|Funcionalidade|Manifesto|
|---|---|
|[Gestão de recursos](service-fabric-resource-governance.md)|[Manifesto de Aplicação de Serviços Fiáveis,](service-fabric-manifest-example-reliable-services-app.md#application-manifest) [Manifesto de Aplicação de Contentores](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Executar um serviço como uma conta de administração local](service-fabric-application-runas-security.md)|[Manifesto de aplicação de serviços fiáveis](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Aplicar uma política padrão a todos os pacotes de código de serviço](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Manifesto de aplicação de serviços fiáveis](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Criar principais de utilizador e grupo](service-fabric-application-runas-security.md)|[Manifesto de aplicação de serviços fiáveis](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|partilhar um pacote de dados entre instâncias de serviço|[Manifesto de aplicação de serviços fiáveis](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Sobrepor pontos finais de serviço](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Manifesto de aplicação de serviços fiáveis](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Executar um script na startup de serviço](service-fabric-run-script-at-service-startup.md)|[Manifesto de serviço votingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Definir um ponto final HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[Manifesto de serviço votingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Declare um pacote config](service-fabric-application-and-service-manifests.md)|[Manifesto de serviço de votoData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Declarar um pacote de dados](service-fabric-application-and-service-manifests.md)|[Manifesto de serviço de votoData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Sobrepor variáveis ambientais](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifesto de Aplicação de Contentores](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Configure o mapeamento porta-a-hospedeiro do contentor](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Manifesto de Aplicação de Contentores](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Configurar a autenticação do registo de contentor](service-fabric-get-started-containers.md#configure-container-repository-authentication)|[Manifesto de Aplicação de Contentores](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Definir o modo de isolamento](service-fabric-get-started-containers.md#configure-isolation-mode)|[Manifesto de Aplicação de Contentores](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Especifique imagens de contentores específicas da construção de SO](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Manifesto de Aplicação de Contentores](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Definir variáveis de ambiente](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifesto de serviço FrontEndService de contentores](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [manifesto de serviço de backEndService de contentores](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Configure um ponto final](service-fabric-get-started-containers.md#configure-communication)|[Manifesto de serviço De Serviço FrontEndService de Contentores](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [Manifesto de serviço de BackEndService de Contentores,](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)Manifesto de serviço [voteData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|passar comandos para o recipiente|[Manifesto de serviço FrontEndService de contentores](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Importar um certificado num contentor](service-fabric-securing-containers.md)|[Manifesto de serviço FrontEndService de contentores](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Controlador de volume de configuração](service-fabric-containers-volume-logging-drivers.md)|[Manifesto de serviço de BackEndService de contentores](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

