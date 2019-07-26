---
title: Def. de serviços de nuvem do Azure. Esquema LoadBalancerProbe | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 14
author: georgewallace
ms.author: gwallace
ms.openlocfilehash: 6f82406772f650b4565f2c9240efe580545dcad9
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360603"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Esquema LoadBalancerProbe de definição de serviços de nuvem do Azure
A investigação do balanceador de carga é uma investigação de integridade definida pelo cliente de pontos de extremidade UDP e pontos de extremidade em instâncias de função. O `LoadBalancerProbe` não é um elemento autônomo; ele é combinado com a função Web ou função de trabalho em um arquivo de definição de serviço. Um `LoadBalancerProbe` pode ser usado por mais de uma função.

A extensão padrão para o arquivo de definição de serviço é. csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>A função de uma investigação do balanceador de carga
O Azure Load Balancer é responsável por rotear o tráfego de entrada para suas instâncias de função. O balanceador de carga determina quais instâncias podem receber tráfego investigando regularmente cada instância para determinar a integridade dessa instância. O balanceador de carga investiga todas as instâncias várias vezes por minuto. Há duas opções diferentes para fornecer a integridade da instância para o balanceador de carga – a investigação do balanceador de carga padrão ou uma investigação personalizada do balanceador de carga, que é implementada definindo o LoadBalancerProbe no arquivo. csdef.

A investigação padrão do balanceador de carga utiliza o agente convidado dentro da máquina virtual, que escuta e responde com uma resposta HTTP 200 OK somente quando a instância está no estado pronto (como quando a instância não está nos Estados ocupado, reciclando, parando, etc.). Se o agente convidado não responder com HTTP 200 OK, a Azure Load Balancer marcará a instância como sem resposta e interromperá o envio de tráfego para essa instância. O Azure Load Balancer continua a executar o ping na instância e, se o agente convidado responder com um HTTP 200, o Azure Load Balancer enviará o tráfego para essa instância novamente. Ao usar uma função Web, o código de seu site normalmente é executado em w3wp. exe, que não é monitorado pela malha do Azure ou pelo agente convidado, o que significa falhas no w3wp. exe (por exemplo, As respostas HTTP 500) não são relatadas ao agente convidado e o balanceador de carga não sabe retirar essa instância da rotação.

A investigação personalizada do balanceador de carga substitui a investigação do agente convidado padrão e permite que você crie sua própria lógica personalizada para determinar a integridade da instância de função. O balanceador de carga investiga regularmente seu ponto de extremidade (a cada 15 segundos, por padrão) e a instância será considerada em rotação se responder com um TCP ACK ou HTTP 200 dentro do período de tempo limite (padrão de 31 segundos). Isso pode ser útil para implementar sua própria lógica para remover instâncias da rotação do balanceador de carga, por exemplo, retornando um status diferente de 200 se a instância estiver acima de 90% de CPU. Para funções Web usando w3wp. exe, isso também significa que você obtém o monitoramento automático do seu site, pois as falhas no código do site retornam um status diferente de 200 para a investigação do balanceador de carga. Se você não definir um LoadBalancerProbe no arquivo. csdef, o comportamento padrão do balanceador de carga (conforme descrito anteriormente) será usado.

Se você usar uma investigação personalizada do balanceador de carga, deverá garantir que sua lógica leve em consideração o método RoleEnvironment. OnStop. Ao usar a investigação de balanceador de carga padrão, a instância é retirada da rotação antes de o OnStop ser chamado, mas uma investigação de balanceador de carga personalizada pode continuar retornando um 200 OK durante o evento OnStop. Se você estiver usando o evento OnStop para limpar o cache, parar o serviço ou, caso contrário, fazer alterações que possam afetar o comportamento do tempo de execução do serviço, você precisará garantir que a lógica de investigação personalizada do balanceador de carga remova a instância da rotação.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Esquema de definição de serviço básico para uma investigação de balanceador de carga
 O formato básico de um arquivo de definição de serviço que contém uma investigação de balanceador de carga é o seguinte.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementos de esquema
O `LoadBalancerProbes` elemento do arquivo de definição de serviço inclui os seguintes elementos:

- [Elemento LoadBalancerProbes](#LoadBalancerProbes)
- [Elemento LoadBalancerProbe](#LoadBalancerProbe)

##  <a name="LoadBalancerProbes"></a>Elemento LoadBalancerProbes
O `LoadBalancerProbes` elemento descreve a coleção de investigações do balanceador de carga. Esse é o elemento pai do [elemento LoadBalancerProbe](#LoadBalancerProbe). 

##  <a name="LoadBalancerProbe"></a>Elemento LoadBalancerProbe
O `LoadBalancerProbe` elemento define a investigação de integridade para um modelo. Você pode definir várias investigações do balanceador de carga. 

A tabela a seguir descreve os atributos do `LoadBalancerProbe` elemento:

|Atributo|Type|Descrição|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Necessário. O nome da investigação do balanceador de carga. O nome tem de ser exclusivo.|
| `protocol`          | `string` | Necessário. Especifica o protocolo do ponto de extremidade. Os valores possíveis são `http` ou `tcp`. Se `tcp` for especificado, uma confirmação recebida será necessária para que a investigação seja bem-sucedida. Se `http` for especificado, uma resposta 200 OK do URI especificado será necessária para que a investigação seja bem-sucedida.|
| `path`              | `string` | O URI usado para solicitar o status de integridade da VM. `path`será obrigatório se `protocol` for definido como `http`. Caso contrário, isso não é permitido.<br /><br /> Não há valor padrão.|
| `port`              | `integer` | Opcional. A porta para comunicar a investigação. Isso é opcional para qualquer ponto de extremidade, pois a mesma porta será usada para a investigação. Você também pode configurar uma porta diferente para sua investigação. Os valores possíveis variam de 1 a 65535, inclusive.<br /><br /> O valor padrão é definido pelo ponto de extremidade.|
| `intervalInSeconds` | `integer` | Opcional. O intervalo, em segundos, para a frequência de investigação do status de integridade do ponto de extremidade. Normalmente, o intervalo é um pouco menor que metade do período de tempo limite alocado (em segundos) que permite duas investigações completas antes de tirar a instância da rotação.<br /><br /> O valor padrão é 15, o valor mínimo é 5.|
| `timeoutInSeconds`  | `integer` | Opcional. O período de tempo limite, em segundos, aplicado à investigação em que nenhuma resposta resultará na interrupção do tráfego adicional de ser entregue ao ponto de extremidade. Esse valor permite que os pontos de extremidade sejam retirados da rotação de forma mais rápida ou mais lenta do que os horários típicos usados no Azure (que são os padrões).<br /><br /> O valor padrão é 31, o valor mínimo é 11.|

## <a name="see-also"></a>Consultar Também
[Esquema de definição do serviço de nuvem (clássico)](schema-csdef-file.md)