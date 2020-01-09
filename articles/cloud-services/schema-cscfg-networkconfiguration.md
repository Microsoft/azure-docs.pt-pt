---
title: Esquema NetworkConfiguration dos serviços de nuvem do Azure | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 28
author: tgore03
ms.author: tagore
ms.openlocfilehash: cacc8b1f2909965594fdf0d841963e792acf648c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75385429"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Esquema NetworkConfiguration de configuração dos serviços de nuvem do Azure

O elemento `NetworkConfiguration` do arquivo de configuração de serviço especifica os valores de rede virtual e DNS. Essas configurações são opcionais para serviços de nuvem.

Você pode usar o seguinte recurso para saber mais sobre redes virtuais e os esquemas associados:

- [Esquema de configuração do serviço de nuvem (clássico)](schema-cscfg-file.md)
- [Esquema de definição do serviço de nuvem (clássico)](schema-csdef-file.md)
- [Criar uma rede virtual (clássica)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>Elemento NetworkConfiguration
O exemplo a seguir mostra o elemento `NetworkConfiguration` e seus elementos filho.

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

A tabela a seguir descreve os elementos filho do elemento `NetworkConfiguration`.

| Elemento       | Descrição |
| ------------- | ----------- |
| AccessControl | Opcional. Especifica as regras de acesso a pontos de extremidade em um serviço de nuvem. O nome do controle de acesso é definido por uma cadeia de caracteres para `name` atributo. O elemento `AccessControl` contém um ou mais elementos `Rule`. Mais de um elemento de `AccessControl` pode ser definido.|
| Regra | Opcional. Especifica a ação que deve ser executada para um intervalo de sub-rede especificado de endereços IP. A ordem da regra é definida por um valor de cadeia de caracteres para o atributo `order`. Quanto menor o número da regra, maior a prioridade. Por exemplo, as regras podem ser especificadas com números de ordem de 100, 200 e 300. A regra com o número de pedido de 100 tem precedência sobre a regra que tem uma ordem de 200.<br /><br /> A ação da regra é definida por uma cadeia de caracteres para o atributo `action`. Os valores possíveis são:<br /><br /> -   `permit` – especifica que somente os pacotes do intervalo de sub-rede especificado podem se comunicar com o ponto de extremidade.<br />-   `deny` – especifica que o acesso é negado aos pontos de extremidade no intervalo de sub-rede especificado.<br /><br /> O intervalo de sub-rede de endereços IP que são afetados pela regra são definidos por uma cadeia de caracteres para o atributo `remoteSubnet`. A descrição da regra é definida por uma cadeia de caracteres para o atributo `description`.|
| Endpointacl só | Opcional. Especifica a atribuição de regras de controle de acesso a um ponto de extremidade. O nome da função que contém o ponto de extremidade é definido por uma cadeia de caracteres para o atributo `role`. O nome do ponto de extremidade é definido por uma cadeia de caracteres para o atributo `endpoint`. O nome do conjunto de regras de `AccessControl` que devem ser aplicadas ao ponto de extremidade é definido em uma cadeia de caracteres para o atributo `accessControl`. Mais de um `EndpointAcl` elementos podem ser definidos.|
| DnsServer | Opcional. Especifica as configurações para um servidor DNS. Você pode especificar configurações para servidores DNS sem uma rede virtual. O nome do servidor DNS é definido por uma cadeia de caracteres para o atributo `name`. O endereço IP do servidor DNS é definido por uma cadeia de caracteres para o atributo `IPAddress`. O endereço IP deve ser um endereço IPv4 válido.|
| VirtualNetworkSite | Opcional. Especifica o nome do site de rede virtual no qual você deseja implantar seu serviço de nuvem. Essa configuração não cria um site de rede virtual. Ele faz referência a um site que foi definido anteriormente no arquivo de rede para sua rede virtual. Um serviço de nuvem só pode ser um membro de uma rede virtual. Se você não especificar essa configuração, o serviço de nuvem não será implantado em uma rede virtual. O nome do site de rede virtual é definido por uma cadeia de caracteres para o atributo `name`.|
| InstanceAddress | Opcional. Especifica a associação de uma função a uma sub-rede ou a um conjunto de sub-redes na rede virtual. Ao associar um nome de função a um endereço de instância, você pode especificar as sub-redes às quais você deseja que essa função seja associada. O `InstanceAddress` contém um elemento sub-redes. O nome da função associada à sub-rede ou sub-redes é definido por uma cadeia de caracteres para o atributo `roleName`.|
| Subrede | Opcional. Especifica a sub-rede que corresponde ao nome da sub-rede no arquivo de configuração de rede. O nome da sub-rede é definido por uma cadeia de caracteres para o atributo `name`.|
| Reservado | Opcional. Especifica o endereço IP reservado que deve ser associado à implantação. Você deve usar criar IP Reservado endereço para criar o endereço IP reservado. Cada implantação em um serviço de nuvem pode ser associada a um endereço IP reservado. O nome do endereço IP reservado é definido por uma cadeia de caracteres para o atributo `name`.|

## <a name="see-also"></a>Veja também
[Esquema de configuração do serviço de nuvem (clássico)](schema-cscfg-file.md)
