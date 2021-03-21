---
title: Azure Cloud Services (suporte alargado) Definição Schema (ficheiro csdef) | Microsoft Docs
description: Informações relacionadas com o esquema de definição (csdef) para serviços em nuvem (suporte alargado)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: d9bf1b54f1bfeebacbb406a50c8496817857204c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507573"
---
# <a name="azure-cloud-services-extended-support-definition-schema-csdef-file"></a>Esquema de definição Azure Cloud Services (suporte alargado) (ficheiro csdef)

O ficheiro de definição de serviço define o modelo de serviço para uma aplicação. O ficheiro contém as definições para as funções que estão disponíveis num Serviço de Cloud, especifica os pontos finais do serviço e estabelece definições de configuração para o serviço. Os valores de definição de configuração são definidos no ficheiro de configuração de serviço, conforme descrito pelo Esquema de Configuração do [Serviço de Nuvem (suporte alargado).](schema-cscfg-file.md)

Por predefinição, o ficheiro de configuração Azure Diagnostics é instalado no `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` diretório. `<version>`Substitua-a pela versão instalada do [Azure SDK](https://www.windowsazure.com/develop/downloads/).

A extensão padrão para o ficheiro de definição de serviço é csdef.

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
| upgradeDomainCount      | Opcional. Especifica o número de domínios de atualização em que as funções neste serviço são atribuídas. As instâncias de função são atribuídas a um domínio de atualização quando o serviço é implantado. Para obter mais informações, consulte [Atualizar uma função ou implementação do Serviço cloud](sample-update-cloud-service.md) e Gerir a disponibilidade de [máquinas virtuais](../virtual-machines/availability.md) Pode especificar até 20 domínios de atualização. Se não for especificado, o número predefinido dos domínios de atualização é 5.|

## <a name="see-also"></a>Ver também

[Azure Cloud Services (suporte alargado) config schema (cscfg File)](schema-cscfg-file.md).