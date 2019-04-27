---
title: Def de serviços Cloud do Azure LoadBalancerProbe Schema | Microsoft Docs
ms.custom: ''
origin.date: 04/14/2015
ms.date: 11/06/2017
ms.prod: azure
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 113374a8-8072-4994-9d99-de391a91e6ea
caps.latest.revision: 14
author: thraka
ms.author: v-yiso
manager: timlt
ms.openlocfilehash: de365de7bf93c0a612f102b3ec2b25c79d1c3d18
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613867"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Esquema de LoadBalancerProbe de definição dos serviços Cloud do Azure
A sonda de Balanceador de carga é uma sonda de estado de funcionamento definido de cliente de pontos de extremidade do UDP e pontos de extremidade nas instâncias de função. O `LoadBalancerProbe` não é um elemento de autônomo; ele é combinado com a função da web ou a função de trabalho num arquivo de definição de serviço. A `LoadBalancerProbe` pode ser utilizado por mais de uma função.

A extensão de padrão para o ficheiro de definição de serviço é. csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>A função de uma sonda de Balanceador de carga
O Balanceador de carga do Azure é responsável pelo encaminhamento de tráfego de entrada às instâncias de função. O Balanceador de carga determina quais as instâncias podem receber o tráfego por cada instância de pesquisa regularmente para determinar o estado de funcionamento dessa instância. O load balancer sonda todas as instâncias várias vezes por minuto. Existem duas opções diferentes para fornecer o estado de funcionamento da instância para o Balanceador de carga – a sonda de Balanceador de carga predefinido, ou um balanceador de carga personalizados probe, que é implementado, definindo o LoadBalancerProbe no ficheiro. csdef.

A sonda de Balanceador de carga predefinido utiliza o agente convidado dentro da máquina virtual, o que fica à escuta e responde com uma resposta HTTP 200 OK, apenas quando a instância está no estado pronto (como quando a instância não é a disponibilidade, reciclar, parar, Estados de etc.). Se o agente convidado não responder com HTTP 200 OK, o Azure Load Balancer marca a instância como não responsivo e deixará de enviar tráfego para essa instância. O Balanceador de carga do Azure continua a enviar um ping a instância e, se o agente convidado responde com um HTTP 200, o Balanceador de carga do Azure envia o tráfego para essa instância novamente. Quando utiliza uma função da web que seu código de site, normalmente, é executado num w3wp.exe que não é monitorizado por recursos de infraestrutura do Azure ou o agente de convidado, o que significa que falhas no w3wp.exe (ex. Respostas de HTTP 500) não será possível reportar para o agente convidado e a carga, balanceador não sabe fazer essa instância da rotação.

A sonda de Balanceador de carga personalizado substitui a sonda de agente de convidado predefinida e permite-lhe criar sua própria lógica personalizada para determinar o estado de funcionamento da instância de função. O Balanceador de carga regularmente sondas é o ponto final (a cada 15 segundos, por padrão) e a instância de ser considerados na rotação se ele responde com uma TCP ACK ou HTTP 200 dentro do período de tempo limite (predefinição de 31 segundos). Isso pode ser útil para implementar sua própria lógica para remover instâncias de rotação do Balanceador de carga, por exemplo um Estado que não 200 a devolver se a instância for superior a 90% da CPU. Para funções da web usando w3wp.exe, isso também significa que obtém automática de monitorização do seu Web site, uma vez que as falhas em seu código de site devolver um Estado que não 200 para a sonda de Balanceador de carga. Se não definir um LoadBalancerProbe no ficheiro. csdef, então o comportamento de Balanceador de carga padrão (como descrito anteriormente) é utilizado.

Se utilizar uma sonda de Balanceador de carga personalizado, certifique-se de que a lógica de leva em consideração, o método RoleEnvironment.OnStop. Ao utilizar a sonda de Balanceador de carga padrão, a instância é retirada da rotação antes OnStop a ser chamado, mas uma sonda de Balanceador de carga personalizados pode continuar a devolver um 200 OK durante o evento de OnStop. Se estiver a utilizar o evento de OnStop para limpar a cache, pare o serviço, ou caso contrário, fazer alterações que podem afetar o comportamento de tempo de execução do seu serviço, terá de se certificar de que a lógica de sonda de Balanceador de carga personalizados remove a instância da rotação.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Esquema de definição de serviço básico para uma sonda de Balanceador de carga
 Segue-se o formato básico de um ficheiro de definição de serviço que contém uma sonda de Balanceador de carga.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementos de esquema
O `LoadBalancerProbes` elemento do ficheiro de definição do serviço inclui os seguintes elementos:

- [Elemento de LoadBalancerProbes](#LoadBalancerProbes)
- [Elemento de LoadBalancerProbe](#LoadBalancerProbe)

##  <a name="LoadBalancerProbes"></a> Elemento de LoadBalancerProbes
O `LoadBalancerProbes` elemento descreve a coleção de sondas do Balanceador de carga. Este elemento é o elemento principal dos [LoadBalancerProbe elemento](#LoadBalancerProbe). 

##  <a name="LoadBalancerProbe"></a> Elemento de LoadBalancerProbe
O `LoadBalancerProbe` elemento define a sonda de estado de funcionamento de um modelo. Pode definir várias sondas do Balanceador de carga. 

A tabela seguinte descreve os atributos do `LoadBalancerProbe` elemento:

|Atributo|Type|Descrição|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Necessário. O nome da sonda de Balanceador de carga. O nome tem de ser exclusivo.|
| `protocol`          | `string` | Necessário. Especifica o protocolo do ponto final. Os valores possíveis são `http` ou `tcp`. Se `tcp` for especificado, um ACK recebido é necessário para a sonda seja concluída com êxito. Se `http` for especificado, uma resposta 200 OK do URI especificado é necessária para a sonda seja concluída com êxito.|
| `path`              | `string` | O URI utilizado para pedir o estado de funcionamento da VM. `path` é necessário se `protocol` está definido como `http`. Caso contrário, não é permitido.<br /><br /> Não existe nenhum valor predefinido.|
| `port`              | `integer` | Opcional. A porta para comunicação a sonda. Isto é opcional para qualquer ponto de extremidade, como a mesma porta, em seguida, será utilizada para a sonda. Pode configurar uma porta diferente para a sua pesquisa, também. Intervalo de valores possíveis de 1 a 65535, inclusive.<br /><br /> O valor predefinido é definido pelo ponto final.|
| `intervalInSeconds` | `integer` | Opcional. O intervalo, em segundos, para a frequência de sonda o ponto final do Estado de funcionamento. Normalmente, o intervalo é um pouco menos da metade o tempo limite alocado (em segundos) que permite dois sondas completas antes de colocar a instância da rotação.<br /><br /> O valor predefinido é 15, o valor mínimo é 5.|
| `timeoutInSeconds`  | `integer` | Opcional. O período de tempo limite, em segundos, são aplicadas para a sonda em que nenhuma resposta resultará interromper o tráfego sejam entregues para o ponto final. Este valor permite que os pontos finais ficar da rotação mais rápida ou mais lenta do que os tempos típicos utilizados no Azure (que são as predefinições).<br /><br /> O valor predefinido é 31, o valor mínimo é de 11.|

## <a name="see-also"></a>Consultar Também
[Esquema de definição do cloud Service (clássico)](schema-csdef-file.md)