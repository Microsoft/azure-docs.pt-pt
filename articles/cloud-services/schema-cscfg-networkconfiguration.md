---
title: Azure Cloud Services NetworkConfiguration Schema [ Microsoft Docs
description: Conheça os elementos infantis do elemento Configuração da Rede do ficheiro de configuração do serviço, que especifica os valores da Rede Virtual e dNS.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 28
author: tgore03
ms.author: tagore
ms.openlocfilehash: 695ba3acfd5af8797de6e6f7454e493d7863627c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79529293"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Azure Cloud Services Config NetworkConfiguration Schema

O `NetworkConfiguration` elemento do ficheiro de configuração do serviço especifica os valores da Rede Virtual e dNS. Estas configurações são opcionais para serviços na nuvem.

Pode utilizar o seguinte recurso para saber mais sobre redes virtuais e os esquemas associados:

- [Cloud Service (clássico) Configuração Schema](schema-cscfg-file.md)
- [Serviço de Nuvem (clássico) Definição Schema](schema-csdef-file.md)
- [Criar uma Rede Virtual (clássica)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>Elemento de configuração de rede
O exemplo que `NetworkConfiguration` se segue mostra o elemento e os elementos infantis.

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

A tabela seguinte descreve os `NetworkConfiguration` elementos infantis do elemento.

| Elemento       | Descrição |
| ------------- | ----------- |
| Controlo de Acesso | Opcional. Especifica as regras de acesso a pontos finais num serviço na nuvem. O nome de controlo de `name` acesso é definido por uma corda para atributo. O `AccessControl` elemento contém `Rule` um ou mais elementos. Mais do `AccessControl` que um elemento pode ser definido.|
| Regra | Opcional. Especifica as medidas que devem ser tomadas para uma gama de endereços IP especificados. A ordem da regra é definida por `order` um valor de cadeia para o atributo. Quanto menor o número da regra, maior a prioridade. Por exemplo, as regras poderiam ser especificadas com números de encomendas de 100, 200 e 300. A regra com o número de encomenda de 100 prevalece sobre a regra que tem uma ordem de 200.<br /><br /> A ação para a regra é `action` definida por uma corda para o atributo. Os valores possíveis são:<br /><br /> -   `permit`– Especifica que apenas os pacotes da gama de sub-redes especificada podem comunicar com o ponto final.<br />-   `deny`– Especifica que o acesso é negado aos pontos finais na gama de sub-redes especificada.<br /><br /> A gama de sub-redes de endereços IP que são `remoteSubnet` afetados pela regra são definidas por uma cadeia para o atributo. A descrição da regra é definida `description` por uma corda para o atributo.|
| EndpointAcl | Opcional. Especifica a atribuição das regras de controlo de acesso a um ponto final. O nome do papel que contém o ponto final `role` é definido por uma corda para o atributo. O nome do ponto final é definido `endpoint` por uma corda para o atributo. O nome do `AccessControl` conjunto de regras que devem ser aplicados ao `accessControl` ponto final são definidos numa corda para o atributo. Mais do `EndpointAcl` que um elemento pode ser definido.|
| DnsServer | Opcional. Especifica as definições para um servidor DNS. Pode especificar definições para servidores DNS sem rede virtual. O nome do servidor DNS é definido `name` por uma corda para o atributo. O endereço IP do servidor DNS é `IPAddress` definido por uma cadeia para o atributo. O endereço IP deve ser um endereço IPv4 válido.|
| Site virtualnetwork | Opcional. Especifica o nome do site da Rede Virtual no qual pretende implementar o seu serviço na nuvem. Esta definição não cria um Site de Rede Virtual. Refere-se a um site que já foi previamente definido no ficheiro de rede para a sua Rede Virtual. Um serviço na nuvem só pode ser membro de uma Rede Virtual. Se não especificar esta definição, o serviço de nuvem não será implantado numa Rede Virtual. O nome do site da Rede Virtual `name` é definido por uma corda para o atributo.|
| Endereço de instância | Opcional. Especifica a associação de uma função a uma subneta ou a um conjunto de subredes na Rede Virtual. Quando associar um nome de papel a um endereço de instância, pode especificar as subredes às quais pretende que este papel seja associado. O `InstanceAddress` contém um elemento Subnets. O nome do papel que está associado à sub-rede ou sub-redes é definido por uma corda para o `roleName` atributo.|
| Subrede | Opcional. Especifica a sub-rede que corresponde ao nome da sub-rede no ficheiro de configuração da rede. O nome da sub-rede é definido `name` por uma corda para o atributo.|
| IP reservado | Opcional. Especifica o endereço IP reservado que deve ser associado à implantação. Tem de utilizar o Create Reserved IP Address para criar o endereço IP reservado. Cada implantação num serviço de nuvem pode ser associada a um endereço IP reservado. O nome do endereço IP reservado é `name` definido por uma corda para o atributo.|

## <a name="see-also"></a>Veja também
[Cloud Service (clássico) Configuração Schema](schema-cscfg-file.md)
