---
title: Azure Cloud Services Def. NetworkTrafficRules Schema Microsoft Docs
description: Conheça as Regras de Tráfego de Rede, que limita as funções que podem aceder aos pontos finais internos de uma função. Combina com papéis num ficheiro de definição de serviço.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 351b369f-365e-46c1-82ce-03fc3655cc88
caps.latest.revision: 17
author: tgore03
ms.author: tagore
ms.openlocfilehash: e53c10395ec3168e656633cc43fb2d01902209fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79534733"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Definição de serviços azure cloud NetworkTrafficRules Schema
O `NetworkTrafficRules` nó é um elemento opcional no ficheiro de definição de serviço que especifica como as funções comunicam entre si. Limita as funções que podem aceder aos pontos finais internos do papel específico. O `NetworkTrafficRules` não é um elemento autónomo; é combinado com duas ou mais funções num ficheiro de definição de serviço.

A extensão predefinida para o ficheiro de definição de serviço é .csdef.

> [!NOTE]
>  O `NetworkTrafficRules` nó só está disponível utilizando a versão 1.3 do Azure SDK.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Esquema de definição de serviço básico para as regras de tráfego da rede
O formato básico de um ficheiro de definição de serviço que contém definições de tráfego de rede é o seguinte.

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementos Schema
O `NetworkTrafficRules` nó do ficheiro de definição de serviço inclui estes elementos, descritos em detalhe em secções posteriores neste tópico:

[Elemento regras de tráfego de rede](#NetworkTrafficRules)

[Elemento ApenasAllowTrafficto](#OnlyAllowTrafficTo)

[Elemento de destinos](#Destinations)

[Elemento roleendpoint](#RoleEndpoint)

Permitir o elemento de tráfego

[Elemento De Origem](#WhenSource)

[Elemento de Fromrole](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a>Elemento regras de tráfego de rede
O `NetworkTrafficRules` elemento especifica quais as funções com que ponto final se refere a outro papel. Um serviço pode `NetworkTrafficRules` conter uma definição.

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a>Elemento ApenasAllowTrafficto
O `OnlyAllowTrafficTo` elemento descreve uma coleção de pontos finais de destino e as funções que podem comunicar com eles. Pode especificar `OnlyAllowTrafficTo` vários nódosos.

##  <a name="destinations-element"></a><a name="Destinations"></a>Elemento de destinos
O `Destinations` elemento descreve uma coleção de RoleEndpoints do que pode ser comunicado.

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a>Elemento roleendpoint
O `RoleEndpoint` elemento descreve um ponto final sobre um papel para permitir comunicações com. Pode especificar `RoleEndpoint` vários elementos se houver mais de um ponto final sobre o papel.

| Atributo      | Tipo     | Descrição |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Necessário. O nome do ponto final para permitir o tráfego.|
| `roleName`     | `string` | Necessário. O nome do papel web para permitir a comunicação.|

## <a name="allowalltraffic-element"></a>Permitir o elemento de tráfego
O `AllowAllTraffic` elemento é uma regra que permite que todas `Destinations` as funções se comuniquem com os pontos finais definidos no nó.

##  <a name="whensource-element"></a><a name="WhenSource"></a>Elemento De Origem
O `WhenSource` elemento descreve uma coleção de papéis do `Destinations` que pode comunicar com os pontos finais definidos no nó.

| Atributo | Tipo     | Descrição |
| --------- | -------- | ----------- |
| `matches` | `string` | Necessário. Especifica a regra a aplicar ao permitir comunicações. O único valor válido `AnyRule`é atualmente.|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a>Elemento de Fromrole
O `FromRole` elemento especifica as funções que podem comunicar `Destinations` com os pontos finais definidos no nó. Pode especificar `FromRole` vários elementos se houver mais do que uma função que pode comunicar com os pontos finais.

| Atributo  | Tipo     | Descrição |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Necessário. O nome do papel a partir do qual permitir a comunicação.|

## <a name="see-also"></a>Veja também
[Serviço de Nuvem (clássico) Definição Schema](schema-csdef-file.md)




