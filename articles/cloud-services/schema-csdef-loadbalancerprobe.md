---
title: Azure Cloud Services Def. LoadBalancerProbe Schema Microsoft Docs
description: O cliente definido LoadBalancerProbe é uma sonda de saúde de pontos finais em instâncias de funções. Combina com funções web ou trabalhador num ficheiro de definição de serviço.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 14
author: georgewallace
ms.author: tagore
ms.openlocfilehash: 6d0e84b6724d9df4162d4be3e06a9952087a53a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537351"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Definição de serviços de nuvem azure LoadBalancerProbe Schema
A sonda balancer de carga é uma sonda de saúde definida pelo cliente de pontos finais uDP e pontos finais em instâncias de papéis. O `LoadBalancerProbe` não é um elemento autónomo; é combinado com o papel web ou o papel do trabalhador num ficheiro de definição de serviço. A `LoadBalancerProbe` pode ser usado por mais de um papel.

A extensão predefinida para o ficheiro de definição de serviço é .csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>A função de uma sonda de equilíbrio de carga
O Azure Load Balancer é responsável por encaminhar o tráfego para as suas instâncias de papel. O equilibrador de carga determina quais os casos que podem receber tráfego sondando regularmente cada instância, a fim de determinar a saúde desse caso. O equilibrador de carga sonda cada instância várias vezes por minuto. Existem duas opções diferentes para fornecer saúde de exemplo ao equilibrador de carga – a sonda de equilíbrio de carga padrão, ou uma sonda de equilíbrio de carga personalizada, que é implementada através da definição do LoadBalancerProbe no ficheiro .csdef.

A sonda de equilíbrio de carga predefinida utiliza o Agente Convidado dentro da máquina virtual, que ouve e responde com uma resposta HTTP 200 OK apenas quando a instância está no estado Ready (como quando a instância não está nos estados ocupados, reciclando, parando, etc.). Se o Agente Convidado não responder com HTTP 200 OK, o Balancer de Carga Azure marca a ocorrência como sem resposta e impede o envio de tráfego para essa instância. O Balancer de Carga Azure continua a dar a conhecer a instância, e se o Agente Convidado responder com um HTTP 200, o Balancer de Carga Azure envia o tráfego para essa instância novamente. Ao utilizar uma função web, o seu código de website normalmente funciona em w3wp.exe que não é monitorizado pelo tecido Azure ou agente convidado, o que significa falhas em w3wp.exe (por exemplo. HTTP 500 respostas) não são reportadas ao agente convidado e o equilibrista de carga não sabe tirar essa instância da rotação.

A sonda de equilíbrio de carga personalizada sobrepõe-se à sonda padrão do agente convidado e permite criar a sua própria lógica personalizada para determinar a saúde da instância de função. O equilibrador de carga sonda regularmente o seu ponto final (a cada 15 segundos, por defeito) e a instância é considerada em rotação se responder com um TCP ACK ou HTTP 200 no prazo de tempo (padrão de 31 segundos). Isto pode ser útil para implementar a sua própria lógica para remover instâncias da rotação do equilíbrio de carga, por exemplo, devolvendo um estatuto não-200 se a instância estiver acima de 90% de CPU. Para funções web usando w3wp.exe, isto também significa que você obtém monitorização automática do seu website, uma vez que falhas no código do seu site devolvem um estado não-200 à sonda de equilíbrio de carga. Se não definir um LoadBalancerProbe no ficheiro .csdef, então o comportamento do equilíbrio de carga predefinido (como descrito anteriormente) é utilizado.

Se utilizar uma sonda de equilíbrio de carga personalizada, deve certificar-se de que a sua lógica leva em consideração o método RoleEnvironment.OnStop. Ao utilizar a sonda de equilíbrio de carga predefinida, a instância é retirada da rotação antes de ser chamada onStop, mas uma sonda de equilíbrio de carga personalizada pode continuar a devolver um 200 OK durante o evento OnStop. Se estiver a utilizar o evento OnStop para limpar cache, parar o serviço ou fazer alterações que possam afetar o comportamento do tempo de execução do seu serviço, então precisa de garantir que a lógica da sonda de balanço de carga personalizada remove a instância da rotação.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Esquema de definição de serviço básico para uma sonda de equilíbrio de carga
 O formato básico de um ficheiro de definição de serviço contendo uma sonda de equilíbrio de carga é o seguinte.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementos de esquema
O `LoadBalancerProbes` elemento do ficheiro de definição de serviço inclui os seguintes elementos:

- [Elemento LoadBalancerProbes](#LoadBalancerProbes)
- [Elemento LoadBalancerProbe](#LoadBalancerProbe)

##  <a name="loadbalancerprobes-element"></a><a name="LoadBalancerProbes"></a>Elemento LoadBalancerProbes
O `LoadBalancerProbes` elemento descreve a recolha de sondas de equilíbrio de carga. Este elemento é o elemento-mãe do [elemento LoadBalancerProbe](#LoadBalancerProbe). 

##  <a name="loadbalancerprobe-element"></a><a name="LoadBalancerProbe"></a>Elemento LoadBalancerProbe
O `LoadBalancerProbe` elemento define a sonda de saúde para um modelo. Pode definir várias sondas de equilíbrio de carga. 

A tabela seguinte descreve `LoadBalancerProbe` os atributos do elemento:

|Atributo|Tipo|Descrição|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Necessário. O nome da sonda de equilíbrio de carga. O nome deve ser único.|
| `protocol`          | `string` | Necessário. Especifica o protocolo do ponto final. Os valores possíveis são `http` ou `tcp`. Se `tcp` for especificado, é necessário um ACK recebido para que a sonda tenha sucesso. Se `http` for especificado, é necessária uma resposta de 200 OK do URI especificado para que a sonda tenha sucesso.|
| `path`              | `string` | O URI utilizado para solicitar o estado de saúde do VM. `path`é necessário `protocol` se estiver `http`definido para . Caso contrário, não é permitido.<br /><br /> Não há valor predefinido.|
| `port`              | `integer` | Opcional. A porta para comunicar a sonda. Isto é opcional para qualquer ponto final, uma vez que a mesma porta será então utilizada para a sonda. Também pode configurar uma porta diferente para a sua sondagem. Os valores possíveis variam de 1 a 65535, inclusive.<br /><br /> O valor predefinido é definido pelo ponto final.|
| `intervalInSeconds` | `integer` | Opcional. O intervalo, em segundos, para a frequência de sondar o ponto final para o estado de saúde. Tipicamente, o intervalo é ligeiramente menos de metade do período de tempo atribuído (em segundos) que permite duas sondas completas antes de tirar a instância da rotação.<br /><br /> O valor padrão é de 15, o valor mínimo é 5.|
| `timeoutInSeconds`  | `integer` | Opcional. O período de tempo, em segundos, aplicado à sonda onde nenhuma resposta resultará em impedir que o tráfego seja entregue até ao ponto final. Este valor permite que os pontos finais sejam retirados da rotação mais rapidamente ou mais lentamente do que os tempos típicos utilizados em Azure (que são os defeitos).<br /><br /> O valor padrão é 31, o valor mínimo é 11.|

## <a name="see-also"></a>Veja também
[Serviço de Nuvem (clássico) Definição Schema](schema-csdef-file.md)