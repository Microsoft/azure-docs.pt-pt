---
title: Sondas Azure Service Fabric
description: Como modelar uma sonda de vivacidade no Tecido de Serviço Azure utilizando ficheiros de manifesto de aplicação e serviço.
ms.topic: conceptual
author: tugup
ms.author: tugup
ms.date: 3/12/2020
ms.openlocfilehash: 07a1b836ca7ea79244e303f54654dfcaa6e5fcb9
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137591"
---
# <a name="liveness-probe"></a>Sonda de vivacidade
A partir da versão 7.1, o Azure Service Fabric suporta um mecanismo de sonda de vivacidade para aplicações [contentorizadas.][containers-introduction-link] Uma sonda de vivacidade ajuda a relatar a vivacidade de uma aplicação contentorizada, que recomeçará se não responder rapidamente.
Este artigo fornece uma visão geral de como definir uma sonda de vivacidade utilizando ficheiros manifestos.

Antes de avançar com este artigo, familiarize-se com o modelo de [aplicação Service Fabric][application-model-link] e o modelo de [hospedagem do Tecido de Serviço.][hosting-model-link]

> [!NOTE]
> A sonda liveness é suportada apenas para recipientes no modo de rede NAT.

## <a name="semantics"></a>Semântica
Pode especificar apenas uma sonda de vivacidade por recipiente e pode controlar o seu comportamento utilizando estes campos:

* `initialDelaySeconds`: O atraso inicial em segundos para começar a executar a sonda após o início do contentor. O valor suportado é **int**. O padrão é 0 e o mínimo é 0.

* `timeoutSeconds`: O período de segundos após o qual consideramos a sonda como falhada, se não tiver terminado com sucesso. O valor suportado é **int**. O padrão é 1 e o mínimo é 1.

* `periodSeconds`: O período em segundos para especificar a frequência da sonda. O valor suportado é **int**. O padrão é 10 e o mínimo é 1.

* `failureThreshold`: Quando atingirmos este valor, o recipiente recomeçará. O valor suportado é **int**. O padrão é 3 e o mínimo é 1.

* `successThreshold`: No caso de não ter, para que a sonda seja considerada bem sucedida, tem de ser executada com sucesso por este valor. O valor suportado é **int**. O padrão é 1 e o mínimo é 1.

Pode haver, no máximo, uma sonda para um contentor a qualquer momento. Se a sonda não terminar no tempo definido no **tempo limiteSeconds**, espere e conte o tempo para o **falhaThreshold**. 

Além disso, o Service Fabric irá levantar os seguintes [relatórios][health-introduction-link] de saúde da sonda sobre **o DeployedServicePackage:**

* `OK`: A sonda tem sucesso pelo valor definido no **sucessoThreshold**.

* `Error`: Falha **da sondaFalhaGemLimiar,** ==  **failureThreshold**antes do reinício do recipiente.

* `Warning`: 
    * A sonda falha e **falhaFalhaO** < **FalhaThreshold**. Este relatório de saúde permanece até **que a falhaCount** atinja o valor definido em **falhaLimiar** ou **sucessoThreshold**.
    * Sobre o sucesso após o fracasso, o aviso permanece, mas com sucessos consecutivos atualizados.

## <a name="specifying-a-liveness-probe"></a>Especificando uma sonda de vivacidade

Pode especificar uma sonda no ficheiro ApplicationManifest.xml no **âmbito do ServiceManifestImport**.

A sonda pode ser para qualquer um dos seguintes:

* HTTP
* TCP
* Executivo 

### <a name="http-probe"></a>Sonda HTTP

Para uma sonda HTTP, o Service Fabric enviará um pedido HTTP para a porta e caminho que especifica. Um código de retorno que é maior ou igual a 200, e menos de 400, indica sucesso.

Aqui está um exemplo de como especificar uma sonda HTTP:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

A sonda HTTP tem propriedades adicionais que pode definir:

* `path`: O caminho a utilizar no pedido HTTP.

* `port`: A porta a utilizar para as sondas. Esta propriedade é obrigatória. O intervalo é de 1 a 65535.

* `scheme`: O regime a utilizar para a ligação à embalagem de código. Se esta propriedade for definida para HTTPS, a verificação do certificado é ignorada. A definição predefinida é HTTP.

* `httpHeader`: Os cabeçalhos a fixar no pedido. Pode especificar vários cabeçalhos.

* `host`: O endereço IP do anfitrião a que se ligar.

### <a name="tcp-probe"></a>Sonda TCP

Para uma sonda TCP, o Tecido de Serviço tentará abrir uma tomada no recipiente utilizando a porta especificada. Se conseguir estabelecer uma ligação, a sonda é considerada bem sucedida. Aqui está um exemplo de como especificar uma sonda que usa uma tomada TCP:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

### <a name="exec-probe"></a>Sonda executiva

Esta sonda emitirá um comando **executivo** no contentor e esperará que o comando termine.

> [!NOTE]
> **O** comando executivo leva uma corda separada de vírina. O comando no exemplo seguinte funcionará para um recipiente Linux.
> Se estiver a tentar sondar um recipiente Windows, utilize **cmd**.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>Passos seguintes
Consulte o seguinte artigo para obter informações relacionadas:
* [Tecido de serviço e recipientes][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

