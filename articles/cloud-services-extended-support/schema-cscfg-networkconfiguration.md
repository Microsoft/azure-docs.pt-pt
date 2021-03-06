---
title: Azure Cloud Services (suporte alargado) RedeConfiguration Schema | Microsoft Docs
description: Informações relacionadas com o esquema de configuração da rede para serviços em nuvem (suporte alargado)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ed2d48288bf97fe3ebaa1e8ffc1336d8a82d940e
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719030"
---
# <a name="azure-cloud-services-extended-support-config-networkconfiguration-schema"></a>Azure Cloud Services (suporte alargado) configurar esquema de configuração da rede

O `NetworkConfiguration` elemento do ficheiro de configuração de serviço especifica os valores de Rede Virtual e DNS. Estas definições são opcionais para serviços cloud.

Pode utilizar o seguinte recurso para saber mais sobre redes virtuais e os esquemas associados:

- [Sistema de configuração do Serviço de Nuvem (suporte alargado) .](schema-cscfg-file.md)
- [Sistema de Definição do Serviço de Nuvem (suporte alargado) Definição](schema-csdef-file.md).
- [Criar uma Rede Virtual.](../virtual-network/manage-virtual-network.md)

## <a name="networkconfiguration-element"></a>Elemento deconfiguação de rede
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

| Elemento       | Descrição |
| ------------- | ----------- |
| AcessoControl | Opcional. Especifica as regras de acesso aos pontos finais num Serviço cloud. O nome do controlo de acesso é definido por uma cadeia para `name` atributo. O `AccessControl` elemento contém um ou mais `Rule` elementos. Mais de um `AccessControl` elemento pode ser definido.|
| Regra | Opcional. Especifica a ação que deve ser tomada para uma gama de sub-redes especificada de endereços IP. A ordem da regra é definida por um valor de cadeia para o `order` atributo. Quanto menor for o número de regra, maior é a prioridade. Por exemplo, as regras poderiam ser especificadas com números de encomendas de 100, 200 e 300. A regra com o número de encomenda de 100 tem precedência sobre a regra que tem uma ordem de 200.<br /><br /> A ação para a regra é definida por uma cadeia para o `action` atributo. Os valores possíveis são:<br /><br /> -   `permit` – Especifica que apenas os pacotes da gama de sub-redes especificada podem comunicar com o ponto final.<br />-   `deny` – Especifica que o acesso é negado aos pontos finais na gama de sub-redes especificada.<br /><br /> A gama de endereços IP que são afetados pela regra é definida por um fio para o `remoteSubnet` atributo. A descrição da regra é definida por uma cadeia para o `description` atributo.|
| EndpointAcl | Opcional. Especifica a atribuição das regras de controlo de acesso a um ponto final. O nome do papel que contém o ponto final é definido por uma cadeia para o `role` atributo. O nome do ponto final é definido por uma cadeia para o `endpoint` atributo. O nome do conjunto de `AccessControl` regras que deve ser aplicado ao ponto final é definido numa cadeia para o `accessControl` atributo. Mais de um `EndpointAcl` elemento pode ser definido.|
| DnsServer | Opcional. Especifica as definições para um servidor DNS. Pode especificar as definições para servidores DNS sem rede virtual. O nome do servidor DNS é definido por uma cadeia para o `name` atributo. O endereço IP do servidor DNS é definido por uma cadeia para o `IPAddress` atributo. O endereço IP deve ser um endereço IPv4 válido.|
| VirtualNetworkSite | Opcional. Especifica o nome do site da Rede Virtual no qual pretende implementar o seu Serviço cloud. Esta definição não cria um Site de Rede Virtual. Refere-se a um site previamente definido no ficheiro de rede para a sua Rede Virtual. Um Serviço cloud só pode ser membro de uma Rede Virtual. Se não especificar esta definição, o Serviço cloud não será implantado numa Rede Virtual. O nome do site da Rede Virtual é definido por uma cadeia para o `name` atributo.|
| CasoAddress | Opcional. Especifica a associação de uma função a uma sub-rede ou conjunto de sub-redes na Rede Virtual. Quando associar um nome de função a um endereço de exemplo, pode especificar as sub-redes às quais deseja que esta função seja associada. Contém `InstanceAddress` um elemento sub-redes. O nome do papel que está associado à sub-rede ou sub-redes é definido por uma cadeia para o `roleName` atributo.|
| Sub-rede | Opcional. Especifica a sub-rede que corresponde ao nome da sub-rede no ficheiro de configuração da rede. O nome da sub-rede é definido por uma cadeia para o `name` atributo.|
| Reservado | Opcional. Especifica o endereço IP reservado que deve ser associado à implantação. O método de atribuição de um IP reservado deve ser especificado como `Static` para implantações de modelos e de cascas de poder. Cada implantação num Serviço cloud pode ser associada a apenas um endereço IP reservado. O nome do endereço IP reservado é definido por um string para o `name` atributo.|

## <a name="see-also"></a>Ver também
[Sistema de configuração do Serviço de Nuvem (suporte alargado) .](schema-cscfg-file.md)
