---
title: Azure Cloud Services (clássico) Definição Schema (.csdef File) | Microsoft Docs
description: Um ficheiro de definição de serviço (.csdef) define um modelo de serviço para uma aplicação, contendo funções disponíveis, pontos finais e valores de configuração para o serviço.
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: f201bc05795fa6aece256f3d3b4bd650385fef48
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934143"
---
# <a name="azure-cloud-services-classic-definition-schema-csdef-file"></a>Azure Cloud Services (clássico) Definição Schema (.csdef File)

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

O ficheiro de definição de serviço define o modelo de serviço para uma aplicação. O ficheiro contém as definições para as funções que estão disponíveis para um serviço de nuvem, especifica os pontos finais de serviço e estabelece definições de configuração para o serviço. Os valores de definição de configuração são definidos no ficheiro de configuração de serviço, conforme descrito pelo Esquema de Configuração do [Serviço de Nuvem (clássico).](/previous-versions/azure/reference/ee758710(v=azure.100))

Por predefinição, o ficheiro de configuração Azure Diagnostics é instalado no `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` diretório. `<version>`Substitua-a pela versão instalada do [Azure SDK](https://www.windowsazure.com/develop/downloads/).

A extensão por defeito para o ficheiro de definição de serviço é .csdef.

## <a name="basic-service-definition-schema"></a>Esquema de definição de serviço básico
O ficheiro de definição de serviço deve conter um `ServiceDefinition` elemento. A definição de serviço deve conter pelo menos uma função `WebRole` `WorkerRole` (ou) elemento. Pode conter até 25 funções definidas numa única definição e pode misturar tipos de funções. A definição de serviço também contém o elemento opcional `NetworkTrafficRules` que restringe quais as funções que podem comunicar a pontos finais internos especificados. A definição de serviço também contém o elemento opcional `LoadBalancerProbes` que contém sondas de saúde definidas pelo cliente de pontos finais.

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

##  <a name="servicedefinition-element"></a><a name="ServiceDefinition"></a> Elemento dedefinição de serviço
O `ServiceDefinition` elemento é o elemento de nível superior do ficheiro de definição de serviço.

A tabela seguinte descreve os atributos do `ServiceDefinition` elemento.

| Atributo               | Descrição |
| ----------------------- | ----------- |
| name                    |Obrigatório. O nome do serviço. O nome deve ser único na conta de serviço.|
| topologiaChangeDiscovery | Opcional. Especifica o tipo de notificação de alteração de topologia. Os valores possíveis são:<br /><br /> -   `Blast` - Envia a atualização o mais rapidamente possível para todas as instâncias de função. Se escolher a opção, a função deve ser capaz de lidar com a atualização de topologia sem ser reiniciada.<br />-   `UpgradeDomainWalk` – Envia a atualização para cada instância de função de forma sequencial depois de a instância anterior ter aceitado com sucesso a atualização.|
| schemaVersão           | Opcional. Especifica a versão do esquema de definição de serviço. A versão de esquema permite ao Visual Studio selecionar as ferramentas SDK corretas para usar para validação de esquemas se mais de uma versão do SDK for instalada lado a lado.|
| upgradeDomainCount      | Opcional. Especifica o número de domínios de atualização em que as funções neste serviço são atribuídas. As instâncias de função são atribuídas a um domínio de atualização quando o serviço é implantado. Para obter mais informações, consulte [Atualizar uma função de serviço na nuvem ou implementação](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment), [Gerir a disponibilidade de máquinas virtuais](../virtual-machines/availability.md) e o que é um Modelo de Serviço de [Cloud](./cloud-services-model-and-package.md).<br /><br /> Pode especificar até 20 domínios de atualização. Se não for especificado, o número predefinido dos domínios de atualização é 5.|