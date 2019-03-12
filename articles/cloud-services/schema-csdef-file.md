---
title: Esquema de definição (ficheiro. csdef) dos serviços Cloud do Azure | Documentos da Microsoft
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: b7735dbf-8e91-4d1b-89f7-2f17e9302469
caps.latest.revision: 42
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 5f1084090561d3233d0587cf426581ab6ef0ab94
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533005"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Esquema de definição de serviços Cloud do Azure (ficheiro. csdef)
O ficheiro de definição de serviço define o modelo de serviço para uma aplicação. O ficheiro contém as definições para as funções que estão disponíveis para um serviço em nuvem, especifica os pontos de extremidade de serviço e estabelece as definições de configuração para o serviço. Valores de definição de configuração são definidos no ficheiro de configuração do serviço, conforme descrito pelos [esquema de configuração do serviço em nuvem (clássico)](https://msdn.microsoft.com/library/b1ae68cd-cc95-48cb-a4a4-da91dc708a35).

Por predefinição, o ficheiro de esquema de configuração de diagnósticos do Azure está instalado para o `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` diretório. Substitua `<version>` com a versão instalada do [SDK do Azure](https://www.windowsazure.com/develop/downloads/).

A extensão de padrão para o ficheiro de definição de serviço é. csdef.

## <a name="basic-service-definition-schema"></a>Esquema de definição de serviço básico
O ficheiro de definição de serviço tem de conter um `ServiceDefinition` elemento. A definição de serviço tem de conter pelo menos uma função (`WebRole` ou `WorkerRole`) elemento. Pode conter até 25 funções definidas numa única definição e pode misturar tipos de função. A definição de serviço também contém o opcional `NetworkTrafficRules` elemento que restringe o que funções pode comunicar com pontos finais internos especificados. A definição de serviço também contém o opcional `LoadBalancerProbes` elemento que contém o cliente definido sondas de estado de funcionamento dos pontos de extremidade.

Segue-se o formato básico do ficheiro de definição de serviço.

```xml
<ServiceDefinition name="<service-name>" topologyChangeDiscovery="<change-type>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" upgradeDomainCount="<number-of-upgrade-domains>" schemaVersion="<version>">
  
  <LoadBalancerProbes>
         …
  </LoadBalancerProbes>
  
  <WebRole …>
         …
  </WebRole>
  
  <WorkerRole …>
         …
  </WorkerRole>
  
  <NetworkTrafficRules>
         …
  </NetworkTrafficRules>

</ServiceDefinition>
```

## <a name="schema-definitions"></a>Definições de esquema
Os tópicos seguintes descrevem o esquema:

- [Esquema de LoadBalancerProbe](schema-csdef-loadbalancerprobe.md)
- [Esquema de WebRole](schema-csdef-webrole.md)
- [Esquema de WorkerRole](schema-csdef-workerrole.md)
- [Esquema de NetworkTrafficRules](schema-csdef-networktrafficrules.md)

##  <a name="ServiceDefinition"></a> ServiceDefinition elemento
O `ServiceDefinition` elemento é o elemento de nível superior do ficheiro de definição de serviço.

A tabela seguinte descreve os atributos do `ServiceDefinition` elemento.

| Atributo               | Descrição |
| ----------------------- | ----------- |
| nome                    |Necessário. O nome do serviço. O nome tem de ser exclusivo dentro da conta de serviço.|
| topologyChangeDiscovery | Opcional. Especifica o tipo de notificação de alteração de topologia. Os valores possíveis são:<br /><br /> -   `Blast` -Envia a atualização logo que possível para todas as instâncias de função. Se escolher a opção, a função deve ser capaz de lidar com a atualização de topologia sem ser reiniciado.<br />-   `UpgradeDomainWalk` – Envia a atualização para cada instância de função de forma seqüencial depois da instância anterior com êxito aceitou a atualização.|
| schemaVersion           | Opcional. Especifica a versão do esquema de definição de serviço. A versão do esquema permite o Visual Studio selecionar as ferramentas SDK corretas para utilizar para a validação de esquema mais de uma versão do SDK é instalada lado a lado.|
| upgradeDomainCount      | Opcional. Especifica o número de domínios de atualização em que são atribuídas a funções neste serviço. Instâncias de função são alocadas a um domínio de atualização quando o serviço é implementado. Para obter mais informações, consulte [atualizar uma função de serviço na nuvem ou implementação](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment).<br /><br /> Pode especificar até 20 domínios de atualização. Se não for especificado, o número predefinido de domínios de atualização é 5.|