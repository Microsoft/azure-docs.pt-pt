---
title: Criar um balanceador de carga com acesso à Internet para serviços cloud do Azure
titlesuffix: Azure Load Balancer
description: Saiba como criar um balanceador de carga com acesso à Internet no modelo de implementação clássica para serviços em nuvem
services: load-balancer
documentationcenter: na
author: genlin
manager: cshepard
tags: azure-service-management
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: 66c978a7eb151ce9df939a11e2e3c0016c8e7c9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532519"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>Introdução à criação de um balanceador de carga com acesso à Internet para serviços em nuvem

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Antes de trabalhar com recursos do Azure, é importante compreender que o Azure tem atualmente dois modelos de implementação: O Azure Resource Manager e clássica. Confirme que compreende os [modelos e ferramentas de implementação](../azure-classic-rm.md) antes de trabalhar com qualquer recurso do Azure. Pode ver a documentação de diversas ferramentas clicando nos separadores na parte superior deste artigo. Este artigo abrange o modelo de implementação clássica. Também pode [saber como criar um balanceador de carga com acesso à Internet com o Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

Os serviços em nuvem são configurados automaticamente com um balanceador de carga e podem ser personalizados através do modelo de serviço.

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>Criar um balanceador de carga com o ficheiro de definição do serviço

Pode aproveitar o Azure SDK para .NET 2.5 para atualizar o seu serviço em nuvem. As definições de ponto final dos serviços cloud são efetuadas no ficheiro .csdef de [definição do serviço](https://msdn.microsoft.com/library/azure/gg557553.aspx).

O exemplo seguinte mostra como um ficheiro servicedefinition.csdef para uma implementação em nuvem está configurado:

Ao verificar o fragmento do ficheiro .csdef gerado por uma implementação na cloud, pode ver o ponto final externo configurado para utilizar as portas HTTP na porta 10000, 10001 e 10002.

```xml
<ServiceDefinition name="Tenant">
    <WorkerRole name="FERole" vmsize="Small">
        <Endpoints>
            <InputEndpoint name="FE_External_Http" protocol="http" port="10000" />
            <InputEndpoint name="FE_External_Tcp"  protocol="tcp"  port="10001" />
            <InputEndpoint name="FE_External_Udp"  protocol="udp"  port="10002" />

            <InputEndpoint name="HTTP_Probe" protocol="http" port="80" loadBalancerProbe="MyProbe" />

            <InstanceInputEndpoint name="InstanceEP" protocol="tcp" localPort="80">
                <AllocatePublicPortFrom>
                    <FixedPortRange min="10110" max="10120"  />
                </AllocatePublicPortFrom>
            </InstanceInputEndpoint>
            <InternalEndpoint name="FE_InternalEP_Tcp" protocol="tcp" />
        </Endpoints>
    </WorkerRole>
</ServiceDefinition>
```

## <a name="check-load-balancer-health-status-for-cloud-services"></a>Verificar o estado de funcionamento do balanceador de carga para serviços em nuvem

Segue-se um exemplo de uma sonda de estado de funcionamento:

```xml
<LoadBalancerProbes>
    <LoadBalancerProbe name="MyProbe" protocol="http" path="Probe.aspx" intervalInSeconds="5" timeoutInSeconds="100" />
</LoadBalancerProbes>
```

O balanceador de carga combina as informações do ponto final e da sonda para criar um URL com o formato `http://{DIP of VM}:80/Probe.aspx`, que pode ser utilizado para consultar o estado de funcionamento do serviço.

O serviço deteta as sondas periódicas do mesmo endereço IP. Este é o pedido de sonda do estado de funcionamento proveniente do anfitrião do nó no qual a máquina virtual está a ser executada. O serviço tem de responder com um código de estado HTTP 200 para o balanceador de carga para assumir que o serviço está em bom estado. Qualquer outro código de estado HTTP (por exemplo, 503) retira diretamente a máquina virtual da rotação.

A definição de sonda também controla a frequência da sonda. No nosso caso acima, o balanceador de carga está a sondar o ponto final a cada 5 segundos. Se não for recebida nenhuma resposta positiva durante 10 segundos (dois intervalos de sonda), presume-se que a sonda está inativa e a máquina virtual é retirada da rotação. Do mesmo modo, se o serviço estiver fora da rotação e for recebida uma resposta positiva, o serviço volta a ser colocado em rotação de imediato. Se o serviço estiver a flutuar entre mau e bom estado de funcionamento, o balanceador de carga pode optar por atrasar a reintrodução do serviço na rotação até ter estado em bom estado de funcionamento para várias sondas.

Verifique o esquema de definição do serviço para a [sonda de estado de funcionamento](https://msdn.microsoft.com/library/azure/jj151530.aspx) para obter mais informações.

## <a name="next-steps"></a>Passos Seguintes

[Começar a configurar um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição de balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

