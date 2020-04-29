---
title: Definição de Serviços azure Cloud Schema (.csdef File) [ Arquivo Azure Cloud) [ Microsoft Docs
description: Um ficheiro de definição de serviço (.csdef) define um modelo de serviço para uma aplicação, contendo funções disponíveis, pontos finais e valores de configuração para o serviço.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 42
author: tgore03
ms.author: tagore
ms.openlocfilehash: dadb50bd0663f47e6a1bf3d58b5187c8b466964d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79528375"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Definição de Serviços azure Cloud Schema (arquivo.csdef)
O ficheiro de definição de serviço define o modelo de serviço para uma aplicação. O ficheiro contém as definições para as funções disponíveis para um serviço na nuvem, especifica os pontos finais do serviço e estabelece as definições de configuração para o serviço. Os valores de configuração de configuração são definidos no ficheiro de configuração do serviço, conforme descrito pelo [Cloud Service (clássico) Configuração Schema](/previous-versions/azure/reference/ee758710(v=azure.100)).

Por predefinição, o ficheiro esquema de configuração `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` do Azure Diagnostics está instalado no diretório. Substitua-a `<version>` pela versão instalada do [Azure SDK](https://www.windowsazure.com/develop/downloads/).

A extensão predefinida para o ficheiro de definição de serviço é .csdef.

## <a name="basic-service-definition-schema"></a>Esquema de definição de serviço básico
O ficheiro de definição de serviço deve conter um `ServiceDefinition` elemento. A definição de serviço deve`WebRole` `WorkerRole`conter pelo menos uma função ( ou ) elemento. Pode conter até 25 funções definidas numa única definição e pode misturar tipos de papéis. A definição de `NetworkTrafficRules` serviço também contém o elemento opcional que restringe quais as funções que podem comunicar a pontos finais internos especificados. A definição de `LoadBalancerProbes` serviço também contém o elemento opcional que contém sondas de saúde definidas pelo cliente de pontos finais.

O formato básico do ficheiro de definição de serviço é o seguinte.

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
Os seguintes tópicos descrevem o esquema:

- [Esquema de LoadBalancerProbe](schema-csdef-loadbalancerprobe.md)
- [Esquema de WebRole](schema-csdef-webrole.md)
- [Esquema de WorkerRole](schema-csdef-workerrole.md)
- [Esquema de NetworkTrafficRules](schema-csdef-networktrafficrules.md)

##  <a name="servicedefinition-element"></a><a name="ServiceDefinition"></a>Elemento de Definição de Serviço
O `ServiceDefinition` elemento é o elemento de nível superior do ficheiro de definição de serviço.

A tabela seguinte descreve `ServiceDefinition` os atributos do elemento.

| Atributo               | Descrição |
| ----------------------- | ----------- |
| nome                    |Necessário. O nome do serviço. O nome deve ser único dentro da conta de serviço.|
| topologiaChangeDiscovery | Opcional. Especifica o tipo de notificação de alteração de topologia. Os valores possíveis são:<br /><br /> -   `Blast`- Envia a atualização o mais rapidamente possível para todas as instâncias de função. Se escolher a opção, a função deve ser capaz de lidar com a atualização de topologia sem ser reiniciada.<br />-   `UpgradeDomainWalk`– Envia a atualização para cada instância de funções de forma sequencial após a instância anterior ter aceitado com sucesso a atualização.|
| schemaVersion           | Opcional. Especifica a versão do esquema de definição de serviço. A versão schema permite ao Visual Studio selecionar as ferramentas SDK corretas para a validação de esquemas se mais de uma versão do SDK for instalada lado a lado.|
| upgradeDomainCount      | Opcional. Especifica o número de domínios de atualização em que as funções neste serviço são atribuídas. As instâncias de função são atribuídas a um domínio de atualização quando o serviço é implantado. Para mais informações, consulte [Atualizar uma função de serviço na nuvem ou implementação](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment), [Gerencie a disponibilidade de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) e o que é um Modelo de Serviço [cloud](https://docs.microsoft.com/azure/cloud-services/cloud-services-model-and-package).<br /><br /> Pode especificar até 20 domínios de atualização. Se não especificado, o número padrão de domínios de atualização é de 5.|
