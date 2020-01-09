---
title: Atribuir políticas de acesso a pontos de extremidade de serviço
description: Saiba como atribuir políticas de acesso de segurança a pontos de extremidade HTTP ou HTTPS em seu serviço de Service Fabric.
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: c7d30e85848f045b5724bb8bdc6e5c810102c044
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614660"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Atribuir uma política de acesso de segurança para pontos de extremidade HTTP e HTTPS
Se você aplicar uma política executar como e o manifesto do serviço declarar recursos de ponto de extremidade HTTP, você deverá especificar um **SecurityAccessPolicy**.  O **SecurityAccessPolicy** garante que as portas alocadas a esses pontos de extremidade sejam corretamente restritas à conta de usuário em que o serviço é executado. Caso contrário, o **http. sys** não tem acesso ao serviço e você obtém falhas com chamadas do cliente. O exemplo a seguir aplica a conta Customer1 a um ponto de extremidade chamado **EndpointName**, que concede a ele direitos de acesso completo.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Para um ponto de extremidade HTTPS, também indique o nome do certificado a ser retornado ao cliente. Você faz referência ao certificado usando **EndpointBindingPolicy**.  O certificado é definido na seção de **certificados** do manifesto do aplicativo.

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
> Ao usar HTTPS, não use a mesma porta e o mesmo certificado para diferentes instâncias de serviço (independente do aplicativo) implantadas no mesmo nó. A atualização de dois serviços diferentes usando a mesma porta em diferentes instâncias de aplicativo resultará em uma falha de atualização. Para obter mais informações, consulte [Atualizando vários aplicativos com pontos de extremidade https ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Para as próximas etapas, leia os seguintes artigos:
* [Entender o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
