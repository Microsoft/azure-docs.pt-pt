---
title: Esquema de NetworkConfiguration dos serviços Cloud do Azure | Documentos da Microsoft
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: c1b94a9e-46e8-4a18-ac99-343c94b1d4bd
caps.latest.revision: 28
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: fb833904502c0c42b46201fd46a368de0376277c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130273"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Esquema de NetworkConfiguration de configuração dos serviços Cloud do Azure

O `NetworkConfiguration` elemento do ficheiro de configuração do serviço Especifica os valores de rede Virtual e o DNS. Estas definições são opcionais para serviços em nuvem.

Pode utilizar o seguinte recurso para obter mais informações sobre redes virtuais e os esquemas associados:

- [Esquema de configuração do cloud Service (clássico)](schema-cscfg-file.md)
- [Esquema de definição do cloud Service (clássico)](schema-csdef-file.md)
- [Criar uma rede Virtual (clássico)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>Elemento NetworkConfiguration
A exemplo a seguir mostra o `NetworkConfiguration` elemento e os respetivos elementos subordinados.

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
    <VirtualNetworkSite name="<site-name>"/>
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

A tabela seguinte descreve os elementos filho do `NetworkConfiguration` elemento.

| Elemento       | Descrição |
| ------------- | ----------- |
| AccessControl | Opcional. Especifica as regras para acesso a pontos finais num serviço cloud. O nome do controlo de acesso é definido por uma cadeia de caracteres para `name` atributo. O `AccessControl` elemento contém um ou mais `Rule` elementos. Mais do que um `AccessControl` podem ser definidos.|
| Regra | Opcional. Especifica a ação que deve ser levada para uma sub-rede especificada intervalo de endereços IP. A ordem da regra é definida por um valor de cadeia de caracteres para o `order` atributo. Quanto menor a regra número maior de prioridade. Por exemplo, regras podem ser especificadas com números de ordem de 100, 200 e 300. A regra com o número de ordem de 100 tem precedência sobre a regra que tem uma ordem de 200.<br /><br /> A ação para a regra é definida por uma cadeia de caracteres para o `action` atributo. Os valores possíveis são:<br /><br /> -   `permit` – Especifica que apenas os pacotes do intervalo de sub-rede especificada consegue comunicar com o ponto final.<br />-   `deny` – Especifica que o acesso é negado para os pontos finais no intervalo de sub-rede especificada.<br /><br /> O sub-rede intervalo de endereços IP que é afetados pela regra é definidos por uma cadeia de caracteres para o `remoteSubnet` atributo. A descrição para a regra é definida por uma cadeia de caracteres para o `description` atributo.|
| EndpointAcl | Opcional. Especifica a atribuição de regras de controlo de acesso para um ponto de extremidade. O nome da função que contém o ponto final é definido por uma cadeia de caracteres para o `role` atributo. O nome do ponto de extremidade é definido por uma cadeia de caracteres para o `endpoint` atributo. O nome do conjunto de `AccessControl` regras que devem ser aplicadas para o ponto final são definidas numa cadeia de caracteres para o `accessControl` atributo. Mais do que um `EndpointAcl` elementos podem ser definidos.|
| DnsServer | Opcional. Especifica as definições para um servidor DNS. Pode especificar definições para servidores DNS sem uma rede Virtual. O nome do servidor DNS é definido por uma cadeia de caracteres para o `name` atributo. O endereço IP do servidor DNS é definido por uma cadeia de caracteres para o `IPAddress` atributo. O endereço IP tem de ser um endereço IPv4 válido.|
| VirtualNetworkSite | Opcional. Especifica o nome do site de rede Virtual em que pretende implementar o seu serviço cloud. Esta definição não cria um Site de rede Virtual. Ele faz referência a um site que tenha sido anteriormente definido no ficheiro de rede da sua rede Virtual. Um serviço em nuvem só pode ser um membro de uma rede Virtual. Se não especificar esta definição, o serviço em nuvem não será implementado numa rede Virtual. O nome do site de rede Virtual é definido por uma cadeia de caracteres para o `name` atributo.|
| InstanceAddress | Opcional. Especifica a associação de uma função a uma sub-rede ou um conjunto de sub-redes na rede Virtual. Quando associa um nome de função para um endereço de instância, pode especificar as sub-redes para o qual pretende que esta função para ser associado. O `InstanceAddress` contém um elemento de sub-redes. O nome da função que estão associado com a sub-rede ou sub-redes é definido por uma cadeia de caracteres para o `roleName` atributo.|
| Subrede | Opcional. Especifica a sub-rede que corresponde ao nome de sub-rede no ficheiro de configuração de rede. O nome da sub-rede é definido por uma cadeia de caracteres para o `name` atributo.|
| ReservedIP | Opcional. Especifica o endereço IP reservado que deve ser associado com a implementação. Criar endereço IP reservado tem de utilizar para criar o endereço IP reservado. Cada implementação num serviço cloud pode ser associada um endereço IP reservado. O nome do endereço IP reservado é definido por uma cadeia de caracteres para o `name` atributo.|

## <a name="see-also"></a>Consultar Também
[Esquema de configuração do cloud Service (clássico)](schema-cscfg-file.md)