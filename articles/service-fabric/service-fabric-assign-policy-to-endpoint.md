---
title: Atribuir políticas de acesso a pontos finais de serviço do Azure Service Fabric | Documentos da Microsoft
description: Saiba como atribuir segurança políticas de acesso a pontos finais HTTP ou HTTPS no seu serviço do Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: 3e892e443f5e3309add48f939f26ba14eaf5a51b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60614194"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Atribuir uma política de acesso de segurança para pontos finais HTTP e HTTPS
Se aplicar uma política de Run e o manifesto do serviço declara os recursos de ponto final HTTP, tem de especificar um **SecurityAccessPolicy**.  **SecurityAccessPolicy** garante que as portas alocadas para estes pontos finais são restritos corretamente a conta de utilizador que o serviço é executado como. Caso contrário, **HTTP. sys** não tem acesso ao serviço e obter falhas com chamadas do cliente. O exemplo seguinte aplica-se a conta de Customer1 para um ponto de extremidade chamado **EndpointName**, que concede direitos de acesso completo.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Para um ponto final HTTPS, também indica o nome do certificado para devolver ao cliente. Referencia o certificado através de **EndpointBindingPolicy**.  O certificado está definido no **certificados** secção de manifesto do aplicativo.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies>
```

> [!WARNING] 
> Ao utilizar o HTTPS, não utilize a mesma porta e certificado para as instâncias de serviço diferentes (independentemente do aplicativo) implementado ao mesmo nó. A atualizar dois serviços diferentes, utilizar a mesma porta em diferentes instâncias da aplicação irá resultar numa falha de atualização. Para obter mais informações, consulte [atualizar vários aplicativos com pontos finais de HTTPS ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Para obter passos seguintes, leia os artigos seguintes:
* [Compreender o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos num manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
