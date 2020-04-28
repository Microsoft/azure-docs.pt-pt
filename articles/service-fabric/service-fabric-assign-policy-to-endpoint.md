---
title: Atribuir políticas de acesso aos pontos finais do serviço
description: Saiba como atribuir polícias de acesso de segurança a pontos finais HTTP ou HTTPS no seu serviço de Tecido de Serviço.
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: c7d30e85848f045b5724bb8bdc6e5c810102c044
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75614660"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Atribuir uma política de acesso à segurança para os pontos finais HTTP e HTTPS
Se aplicar uma política de execução e o manifesto de serviço declarar recursos de ponto final HTTP, deve especificar uma Política de **Acesso ao Segurança**.  **SecurityAccessPolicy** garante que as portas atribuídas a estes pontos finais estão corretamente restritas à conta de utilizador que o serviço funciona como. Caso contrário, **http.sys** não tem acesso ao serviço, e obtém falhas com chamadas do cliente. O exemplo seguinte aplica a conta Cliente1 a um ponto final chamado **EndpointName,** que lhe confere todos os direitos de acesso.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Para um ponto final HTTPS, indique também o nome do certificado para devolver ao cliente. Refere o certificado utilizando **endpointBindingPolicy**.  O certificado é definido na secção de **Certificados** do manifesto de candidatura.

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
> Quando utilizar https, não utilize a mesma porta e certificado para diferentes instâncias de serviço (independentes da aplicação) implantadas no mesmo nó. A atualização de dois serviços diferentes utilizando a mesma porta em diferentes instâncias de aplicação resultará numa falha de atualização. Para mais informações, consulte [Atualizar várias aplicações com pontos finais HTTPS ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Para os próximos passos, leia os seguintes artigos:
* [Compreender o modelo de aplicação](service-fabric-application-model.md)
* [Especificar recursos num manifesto de serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
