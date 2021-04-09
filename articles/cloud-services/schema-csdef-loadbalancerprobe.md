---
title: Azure Cloud Services (clássico) Def. LoadBalancerProbe Schema | Microsoft Docs
description: O LoadBalancerProbe definido pelo cliente é uma sonda de saúde de pontos finais em instâncias de função. Combina com funções web ou trabalhadora num ficheiro de definição de serviço.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 3dca519f7fb4523ce9d9267f7629c1177cc5e3b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98739795"
---
# <a name="azure-cloud-services-classic-definition-loadbalancerprobe-schema"></a>Azure Cloud Services (clássico) Definição LoadBalancerProbe Schema

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

A sonda de balanço de carga é uma sonda de saúde definida pelo cliente de pontos finais da UDP e pontos finais em instâncias de função. O `LoadBalancerProbe` não é um elemento autónomo; é combinado com o papel web ou o papel do trabalhador num ficheiro de definição de serviço. A `LoadBalancerProbe` pode ser usada por mais de um papel.

A extensão por defeito para o ficheiro de definição de serviço é .csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>A função de uma sonda de balançador de carga
O Azure Load Balancer é responsável por encaminhar o tráfego de entrada para as suas funções. O equilibrador de carga determina quais as instâncias que podem receber o tráfego, sondando regularmente cada instância, a fim de determinar a saúde desse caso. O equilibrador de carga sonda cada instância várias vezes por minuto. Existem duas opções diferentes para fornecer saúde de caso ao balanceador de carga – a sonda de balançador de carga padrão, ou uma sonda de balançador de carga personalizada, que é implementada definindo o LoadBalancerProbe no ficheiro .csdef.

A sonda de balançador de carga predefinido utiliza o Agente Convidado dentro da máquina virtual, que ouve e responde com uma resposta HTTP 200 OK apenas quando a instância está no estado Ready (como quando a instância não está nos estados ocupados, reciclando, parando, etc. Se o Agente Convidado não responder com HTTP 200 OK, o Balançador de Carga Azure marca o caso como não respondendo e deixa de enviar tráfego para esse caso. O Azure Load Balancer continua a pingar o caso, e se o Agente Convidado responder com um HTTP 200, o Balançador de Carga Azure envia novamente o tráfego para esse caso. Ao utilizar uma função web, o seu código de site normalmente funciona em w3wp.exe que não é monitorizado pelo tecido Azure ou pelo agente convidado, o que significa falhas na w3wp.exe (por exemplo. HTTP 500 respostas) não é reportado ao agente convidado e o equilibrador de carga não sabe tirar essa instância da rotação.

A sonda de balançador de carga personalizada substitui a sonda de agente de hóspedes predefinido e permite-lhe criar a sua própria lógica personalizada para determinar a saúde da instância de função. O balançador de carga sonda regularmente o seu ponto final (a cada 15 segundos, por padrão) e a instância é considerada em rotação se responder com um TCP ACK ou HTTP 200 dentro do período de tempo limite (padrão de 31 segundos). Isto pode ser útil para implementar a sua própria lógica para remover instâncias da rotação do balanceador de carga, por exemplo devolvendo um estado não-200 se a instância for superior a 90% CPU. Para as funções web que utilizam w3wp.exe, isto também significa que obtém monitorização automática do seu website, uma vez que as falhas no código do seu site devolvem um estado não-200 à sonda de balançador de carga. Se não definir um LoadBalancerProbe no ficheiro .csdef, então o comportamento do balançador de carga predefinido (como descrito anteriormente) será utilizado.

Se utilizar uma sonda de balanço de carga personalizada, deve certificar-se de que a sua lógica tem em conta o método RoleEnvironment.OnStop. Ao utilizar a sonda de balanço de carga predefinitiva, a instância é retirada da rotação antes de o OnStop ser chamado, mas uma sonda de balançador de carga personalizada pode continuar a devolver um 200 OK durante o evento OnStop. Se estiver a utilizar o evento OnStop para limpar cache, parar o serviço ou fazer alterações que possam afetar o comportamento de tempo de execução do seu serviço, então tem de garantir que a lógica da sonda de balanço de carga personalizada remove a instância da rotação.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Esquema de definição de serviço básico para uma sonda de balançador de carga
 O formato básico de um ficheiro de definição de serviço que contém uma sonda de balançador de carga é o seguinte.

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

##  <a name="loadbalancerprobes-element"></a><a name="LoadBalancerProbes"></a> Elemento LoadBalancerProbes
O `LoadBalancerProbes` elemento descreve a coleção de sondas de balançadores de carga. Este elemento é o elemento-mãe do [Elemento LoadBalancerProbe](#LoadBalancerProbe). 

##  <a name="loadbalancerprobe-element"></a><a name="LoadBalancerProbe"></a> Elemento LoadBalancerProbe
O `LoadBalancerProbe` elemento define a sonda de saúde para um modelo. Pode definir várias sondas de balanço de carga. 

A tabela a seguir descreve os atributos do `LoadBalancerProbe` elemento:

|Atributo|Tipo|Description|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Obrigatório. O nome da sonda do balançador de carga. O nome tem de ser exclusivo.|
| `protocol`          | `string` | Obrigatório. Especifica o protocolo do ponto final. Os valores possíveis são `http` ou `tcp`. Se `tcp` for especificado, é necessário um ACK recebido para que a sonda tenha sucesso. Se `http` for especificado, é necessária uma resposta de 200 OK do URI especificado para que a sonda seja bem sucedida.|
| `path`              | `string` | O URI utilizado para solicitar o estado de saúde do VM. `path` é necessário se `protocol` estiver definido para `http` . Caso contrário, não é permitido.<br /><br /> Não há valor padrão.|
| `port`              | `integer` | Opcional. O porto para comunicar a sonda. Isto é opcional para qualquer ponto final, já que a mesma porta será então utilizada para a sonda. Você pode configurar um porto diferente para a sua sondagem, também. Os valores possíveis variam de 1 a 65535, inclusive.<br /><br /> O valor predefinido é definido pelo ponto final.|
| `intervalInSeconds` | `integer` | Opcional. O intervalo, em segundos, para a frequência com que sondar o ponto final para o estado de saúde. Normalmente, o intervalo é ligeiramente inferior a metade do período de tempo atribuído (em segundos) que permite duas sondas completas antes de tirar o exemplo da rotação.<br /><br /> O valor predefinido é 15, o valor mínimo é 5.|
| `timeoutInSeconds`  | `integer` | Opcional. O período de tempo limite, em segundos, aplicado à sonda onde nenhuma resposta resultará em impedir que o tráfego adicional seja entregue no ponto final. Este valor permite que os pontos finais sejam retirados da rotação mais rápido ou mais lento do que os tempos típicos utilizados em Azure (que são os padrão).<br /><br /> O valor predefinido é 31, o valor mínimo é 11.|

## <a name="see-also"></a>Consulte também
[Esquema de Definição de Cloud Service (clássico)](schema-csdef-file.md)