---
title: Esquema de definição de serviços de nuvem do Azure (arquivo. csdef) | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 42
author: tgore03
ms.author: tagore
ms.openlocfilehash: 11660fe4f76cad9577b66e72893670dd1b9b559d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449079"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Esquema de definição de serviços de nuvem do Azure (arquivo. csdef)
O arquivo de definição de serviço define o modelo de serviço para um aplicativo. O arquivo contém as definições para as funções que estão disponíveis para um serviço de nuvem, especifica os pontos de extremidade de serviço e estabelece as definições de configuração para o serviço. Os valores de definição de configuração são definidos no arquivo de configuração de serviço, conforme descrito pelo [esquema de configuração do serviço de nuvem (clássico)](/previous-versions/azure/reference/ee758710(v=azure.100)).

Por padrão, o arquivo de esquema de configuração Diagnóstico do Azure é instalado no diretório `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas`. Substitua `<version>` pela versão instalada do SDK do [Azure](https://www.windowsazure.com/develop/downloads/).

A extensão padrão para o arquivo de definição de serviço é. csdef.

## <a name="basic-service-definition-schema"></a>Esquema de definição de serviço básico
O arquivo de definição de serviço deve conter um elemento `ServiceDefinition`. A definição de serviço deve conter pelo menos um elemento de função (`WebRole` ou `WorkerRole`). Ele pode conter até 25 funções definidas em uma única definição e você pode misturar tipos de função. A definição de serviço também contém o elemento opcional `NetworkTrafficRules` que restringe quais funções podem se comunicar com pontos de extremidade internos especificados. A definição de serviço também contém o elemento opcional `LoadBalancerProbes` que contém investigações de integridade definidas pelo cliente de pontos de extremidade.

O formato básico do arquivo de definição de serviço é o seguinte.

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
Os tópicos a seguir descrevem o esquema:

- [Esquema de LoadBalancerProbe](schema-csdef-loadbalancerprobe.md)
- [Esquema de WebRole](schema-csdef-webrole.md)
- [Esquema de WorkerRole](schema-csdef-workerrole.md)
- [Esquema de NetworkTrafficRules](schema-csdef-networktrafficrules.md)

##  <a name="ServiceDefinition"></a>Elemento de netdefinition
O elemento `ServiceDefinition` é o elemento de nível superior do arquivo de definição de serviço.

A tabela a seguir descreve os atributos do elemento `ServiceDefinition`.

| Atributo               | Descrição |
| ----------------------- | ----------- |
| nome                    |Necessário. O nome do serviço. O nome deve ser exclusivo dentro da conta de serviço.|
| topologyChangeDiscovery | Opcional. Especifica o tipo de notificação de alteração de topologia. Os valores possíveis são:<br /><br /> -   `Blast` – envia a atualização assim que possível para todas as instâncias de função. Se você escolher a opção, a função deverá ser capaz de lidar com a atualização de topologia sem ser reiniciada.<br />-   `UpgradeDomainWalk` – envia a atualização para cada instância de função de maneira sequencial depois que a instância anterior tiver aceitado a atualização com êxito.|
| schemaVersion           | Opcional. Especifica a versão do esquema de definição de serviço. A versão do esquema permite que o Visual Studio selecione as ferramentas do SDK corretas a serem usadas para validação de esquema se mais de uma versão do SDK estiver instalada lado a lado.|
| upgradeDomainCount      | Opcional. Especifica o número de domínios de atualização entre os quais as funções neste serviço são alocadas. As instâncias de função são alocadas para um domínio de atualização quando o serviço é implantado. Para obter mais informações, consulte [atualizar uma função ou implantação de serviço de nuvem](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment), [gerenciar o disponibilidade de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) e [o que é um modelo de serviço de nuvem](https://docs.microsoft.com/azure/cloud-services/cloud-services-model-and-package).<br /><br /> Você pode especificar até 20 domínios de atualização. Se não for especificado, o número padrão de domínios de atualização será 5.|
