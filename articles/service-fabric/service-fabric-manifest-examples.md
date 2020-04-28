---
title: Exemplos manifestos de aplicação azure service Fabric
description: Saiba configurar as definições de formulário de aplicação e de serviço para uma aplicação Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 6f44d2ddaac11545a3a3dc52fd20e02263c6ad67
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75451647"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Service Fabric aplicação e exemplos manifesto de serviço
Esta secção contém exemplos de manifestos de aplicação e de serviço. Estes exemplos não são para mostrar cenários importantes, mas para mostrar as diferentes configurações que estão disponíveis e como usá-los. 

Segue-se um índice das características mostradas e o manifesto de exemplo dos qual fazem parte.

|Funcionalidade|Manifesto|
|---|---|
|[Governação de recursos](service-fabric-resource-governance.md)|[Manifesto de aplicação de serviços fiáveis,](service-fabric-manifest-example-reliable-services-app.md#application-manifest) [manifesto de aplicação de contentores](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Executar um serviço como uma conta de administração local](service-fabric-application-runas-security.md)|[Manifesto de aplicação de serviços fiáveis](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Aplicar uma política predefinida a todos os pacotes de código de serviço](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Manifesto de aplicação de serviços fiáveis](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Criar os diretores de utilizadores e grupos](service-fabric-application-runas-security.md)|[Manifesto de aplicação de serviços fiáveis](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|partilhar um pacote de dados entre instâncias de serviço|[Manifesto de aplicação de serviços fiáveis](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Pontos finais de serviço de substituição](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Manifesto de aplicação de serviços fiáveis](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Executar um script na startup de serviço](service-fabric-run-script-at-service-startup.md)|[Manifesto de serviço votingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Defina um ponto final HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[Manifesto de serviço votingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Declare um pacote de config](service-fabric-application-and-service-manifests.md)|[Manifesto de serviço de Data de Voto](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Declarar um pacote de dados](service-fabric-application-and-service-manifests.md)|[Manifesto de serviço de Data de Voto](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Sobrepor variáveis ambientais](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifesto de aplicação de contentores](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Configurar mapeamento porta-a-anfitrião de contentores](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Manifesto de aplicação de contentores](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Configurar a autenticação do registo de contentor](service-fabric-get-started-containers.md#configure-container-repository-authentication)|[Manifesto de aplicação de contentores](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Definir modo de isolamento](service-fabric-get-started-containers.md#configure-isolation-mode)|[Manifesto de aplicação de contentores](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Especificar imagens de contentores específicos da construção de OS](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Manifesto de aplicação de contentores](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Definir variáveis de ambiente](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifesto de serviço FrontEndService de contentores,](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)manifesto de [serviço do Serviço BackEndService](service-fabric-manifest-example-container-app.md#backendservice-service-manifest) do contentor|
|[Configurar um ponto final](service-fabric-get-started-containers.md#configure-communication)|[Manifesto de serviço FrontEndService de contentores,](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest) [manifesto de serviço de serviço de BackEndService de contentores,](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)manifesto de serviço [VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|passar comandos para o recipiente|[Manifesto de serviço FrontEndService de contentores](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Importar um certificado para um contentor](service-fabric-securing-containers.md)|[Manifesto de serviço FrontEndService de contentores](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Configure o controlador de volume](service-fabric-containers-volume-logging-drivers.md)|[Manifesto de serviço BackEndService de contentores](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

