---
title: Azure Cloud Services NetworkConfiguration Schema Microsoft Docs
description: Saiba mais sobre os elementos infantis do elemento De Configuração de Rede do ficheiro de configuração de serviço, que especifica os valores de Rede Virtual e DNS.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 28
author: tgore03
ms.author: tagore
ms.openlocfilehash: 695ba3acfd5af8797de6e6f7454e493d7863627c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79529293"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Azure Cloud Services Config NetworkConfiguration Schema

O `NetworkConfiguration` elemento do ficheiro de configuração de serviço especifica os valores de Rede Virtual e DNS. Estas configurações são opcionais para serviços na nuvem.

Pode utilizar o seguinte recurso para saber mais sobre redes virtuais e os esquemas associados:

- [Esquema de configuração do Serviço de Nuvem (clássico)](schema-cscfg-file.md)
- [Esquema de Definição de Cloud Service (clássico)](schema-csdef-file.md)
- [Criar uma Rede Virtual (clássica)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>Elemento deConfiguação de Rede
O exemplo a seguir mostra o `NetworkConfiguration` elemento e os elementos da criança.

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

A tabela seguinte descreve os elementos da criança do `NetworkConfiguration` elemento.

| Elemento       | Description |
| ------------- | ----------- |
| AcessoControl | Opcional. Especifica as regras de acesso aos pontos finais num serviço de nuvem. O nome do controlo de acesso é definido por uma cadeia para `name` atributo. O `AccessControl` elemento contém um ou mais `Rule` elementos. Mais de um `AccessControl` elemento pode ser definido.|
| Regra | Opcional. Especifica a ação que deve ser tomada para uma gama de sub-redes especificada de endereços IP. A ordem da regra é definida por um valor de cadeia para o `order` atributo. Quanto menor for o número de regra, maior é a prioridade. Por exemplo, as regras poderiam ser especificadas com números de encomendas de 100, 200 e 300. A regra com o número de encomenda de 100 tem precedência sobre a regra que tem uma ordem de 200.<br /><br /> A ação para a regra é definida por uma cadeia para o `action` atributo. Os valores possíveis são:<br /><br /> -   `permit`– Especifica que apenas os pacotes da gama de sub-redes especificada podem comunicar com o ponto final.<br />-   `deny`– Especifica que o acesso é negado aos pontos finais na gama de sub-redes especificada.<br /><br /> A gama de endereços IP que são afetados pela regra é definida por um fio para o `remoteSubnet` atributo. A descrição da regra é definida por uma cadeia para o `description` atributo.|
| EndpointAcl | Opcional. Especifica a atribuição das regras de controlo de acesso a um ponto final. O nome do papel que contém o ponto final é definido por uma cadeia para o `role` atributo. O nome do ponto final é definido por uma cadeia para o `endpoint` atributo. O nome do conjunto de `AccessControl` regras que deve ser aplicado ao ponto final é definido numa cadeia para o `accessControl` atributo. Mais de um `EndpointAcl` elemento pode ser definido.|
| DnsServer | Opcional. Especifica as definições para um servidor DNS. Pode especificar as definições para servidores DNS sem rede virtual. O nome do servidor DNS é definido por uma cadeia para o `name` atributo. O endereço IP do servidor DNS é definido por uma cadeia para o `IPAddress` atributo. O endereço IP deve ser um endereço IPv4 válido.|
| VirtualNetworkSite | Opcional. Especifica o nome do site da Rede Virtual no qual pretende implementar o seu serviço na nuvem. Esta definição não cria um Site de Rede Virtual. Refere-se a um site previamente definido no ficheiro de rede para a sua Rede Virtual. Um serviço de nuvem só pode ser membro de uma Rede Virtual. Se não especificar esta definição, o serviço de nuvem não será implantado numa Rede Virtual. O nome do site da Rede Virtual é definido por uma cadeia para o `name` atributo.|
| CasoAddress | Opcional. Especifica a associação de uma função a uma sub-rede ou conjunto de sub-redes na Rede Virtual. Quando associar um nome de função a um endereço de exemplo, pode especificar as sub-redes às quais deseja que esta função seja associada. Contém `InstanceAddress` um elemento sub-redes. O nome do papel que está associado à sub-rede ou sub-redes é definido por uma cadeia para o `roleName` atributo.|
| Subrede | Opcional. Especifica a sub-rede que corresponde ao nome da sub-rede no ficheiro de configuração da rede. O nome da sub-rede é definido por uma cadeia para o `name` atributo.|
| Reservado | Opcional. Especifica o endereço IP reservado que deve ser associado à implantação. Tem de utilizar o Endereço IP Reservado para criar o endereço IP reservado. Cada implantação num serviço de nuvem pode ser associada a um endereço IP reservado. O nome do endereço IP reservado é definido por um string para o `name` atributo.|

## <a name="see-also"></a>Consulte também
[Esquema de configuração do Serviço de Nuvem (clássico)](schema-cscfg-file.md)
