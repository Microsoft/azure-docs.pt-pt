---
title: Sondas Azure Service Fabric
description: Como modelar a Liveness Probe em Tecido de Serviço Azure utilizando ficheiros de manifesto de aplicação e serviço.
ms.topic: conceptual
ms.date: 3/12/2020
ms.openlocfilehash: 38f3888a29bf505b723d40bc7cd08fb0c7e29eff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431218"
---
# <a name="liveness-probe"></a>Sonda Liveness
A partir do 7.1 O tecido de serviço suporta o mecanismo de sonda Liveness para aplicações [contentorizadas.][containers-introduction-link] A Liveness Probe ajuda a anunciar a vivacidade da aplicação contentorizada e quando não respondem em tempo útil, resultará num recomeço.
Este artigo fornece uma visão geral de como definir uma Sonda Liveness através de ficheiros manifestos.

Antes de prosseguir com este artigo, recomendamos que se familiarize com o modelo de [aplicação Service Fabric][application-model-link] e o modelo de [hospedagem do Tecido de Serviço.][hosting-model-link]

> [!NOTE]
> A Liveness Probe só é suportada para recipientes no modo de rede NAT.

## <a name="semantics"></a>Semântica
Pode especificar apenas 1 Sonda de Vivacidade por recipiente e pode controlar o seu comportamento com estes campos:

* `initialDelaySeconds`: O atraso inicial em segundos para começar a executar a sonda uma vez iniciado o recipiente. O valor suportado é int. O padrão é 0. O mínimo é 0.

* `timeoutSeconds`: Período em segundos após o qual consideramos a sonda como falhada se não tiver concluído com sucesso. O valor suportado é int. O padrão é 1. O mínimo é 1.

* `periodSeconds`: Período em segundos para especificar com que frequência sondamos. O valor suportado é int. O padrão é 10. O mínimo é 1.

* `failureThreshold`: Assim que chegarmos ao FailureThreshold, o recipiente reinicia. O valor suportado é int. O padrão é de 3. O mínimo é 1.

* `successThreshold`: No caso de não ser considerado sucesso, para que a sonda seja considerada um sucesso, tem de executar com sucesso para o SuccessThreshold. O valor suportado é int. O padrão é 1. O mínimo é 1.

Haverá no máximo 1 sonda para contentor a um momento. Se a sonda não estiver concluída no **tempo limite,** continuamos à espera e a contar para a **falhaThreshold**. 

Além disso, o ServiceFabric irá levantar os seguintes relatórios de [saúde][health-introduction-link] da sonda sobre o Pacote de Serviços Implantados:

* `Ok`: Se a sonda tiver **sucessoThreshold** então reportamos a saúde como Ok.

* `Error`: Se a falha da sondaCount == **falhaThreshold**, antes de reiniciar o recipiente reportamos Erro.

* `Warning`: 
    1. Se a sonda falhar e a falhaCount < **falhaThreshold** reportamos Aviso. Este relatório de saúde permanece até falhaCount atinge **falhaLimiar** ou **sucessoThreshold**.
    2. Sobre o sucesso pós-fracasso, ainda reportamos Aviso, mas com sucesso consecutivo atualizado.

## <a name="specifying-liveness-probe"></a>Especificando a sonda liveness

Pode especificar a sonda no ApplicationManifest.xml no serviceManifestImport:

A sonda pode qualquer um dos:

1. HTTP
2. TCP
3. Executivo 

## <a name="http-probe"></a>Sonda HTTP

Para a sonda HTTP, o Tecido de Serviço enviará um pedido HTTP para a porta e caminho especificado. O código de devolução superior ou igual a 200 e menos de 400 indica sucesso.

Aqui está um exemplo de como especificar a sonda HttpGet:

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

A sonda HttpGet tem propriedades adicionais que pode definir:

* `path`: Caminho para o acesso ao pedido HTTP.

* `port`: Porto de acesso a sondas. O alcance é de 1 a 65535. Obrigatório.

* `scheme`: Esquema a utilizar para a ligação ao pacote de códigos. Se definido para HTTPS, a verificação do certificado é ignorada. Incumprimentos em HTTP

* `httpHeader`: Cabeçalhos a definir no pedido. Pode especificar vários destes.

* `host`: Hospedeiro IP para ligar a.

## <a name="tcp-probe"></a>Sonda TCP

Para a sonda TCP, o Tecido de Serviço tentará abrir uma tomada no recipiente com a porta especificada. Se conseguir estabelecer uma ligação, a sonda é considerada um sucesso. Aqui está um exemplo de como especificar a sonda que utiliza a tomada TCP:

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

## <a name="exec-probe"></a>Sonda Exec

Esta sonda emitirá um executivo no contentor e aguardará que o comando esteja concluído.

> [!NOTE]
> O comando executivo toma uma corda compera sepera. O seguinte comando no exemplo funcionará para o contentor Linux.
> Se estiver a experimentar o recipiente das janelas, use <Command>cmd</Command>

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
Consulte os seguintes artigos para obter informações relacionadas.
* [Tecido de serviço e recipientes.][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

