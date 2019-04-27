---
title: Def de serviços Cloud do Azure NetworkTrafficRules Schema | Microsoft Docs
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
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 8925943b0a5d151d55adedcfe3f01b5a14c63c1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613893"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Esquema de NetworkTrafficRules de definição dos serviços Cloud do Azure
O `NetworkTrafficRules` nó é um elemento opcional no ficheiro de definição do serviço, que especifica a forma como o funções comunicam entre si. Limita quais funções podem aceder os pontos finais internos da função específica. O `NetworkTrafficRules` não é um elemento de autônomo; ele é combinado com duas ou mais funções num arquivo de definição de serviço.

A extensão de padrão para o ficheiro de definição de serviço é. csdef.

> [!NOTE]
>  O `NetworkTrafficRules` nó só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Esquema de definição de serviço básico para as regras de tráfego de rede
Segue-se o formato básico de um ficheiro de definição de serviço que contém definições de tráfego de rede.

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

## <a name="schema-elements"></a>Elementos de esquema
O `NetworkTrafficRules` nó do arquivo de definição de serviço inclui esses elementos, descritos em detalhe nas secções seguintes deste tópico:

[Elemento de NetworkTrafficRules](#NetworkTrafficRules)

[Elemento de OnlyAllowTrafficTo](#OnlyAllowTrafficTo)

[Elemento de destinos](#Destinations)

[Elemento de RoleEndpoint](#RoleEndpoint)

Elemento de AllowAllTraffic

[Elemento de WhenSource](#WhenSource)

[Elemento de FromRole](#FromRole)

##  <a name="NetworkTrafficRules"></a> Elemento de NetworkTrafficRules
O `NetworkTrafficRules` elemento Especifica quais funções podem comunicar com o ponto final em outra função. Um serviço pode conter um `NetworkTrafficRules` definição.

##  <a name="OnlyAllowTrafficTo"></a> Elemento de OnlyAllowTrafficTo
O `OnlyAllowTrafficTo` elemento descreve uma coleção de pontos de extremidade de destino e as funções que podem comunicar com os mesmos. Pode especificar várias `OnlyAllowTrafficTo` nós.

##  <a name="Destinations"></a> Elemento de destinos
O `Destinations` elemento descreve uma coleção de RoleEndpoints que pode ser comunicada.

##  <a name="RoleEndpoint"></a> Elemento de RoleEndpoint
O `RoleEndpoint` elemento descreve um ponto final numa função para permitirem comunicações com. Pode especificar várias `RoleEndpoint` elementos se existirem mais de um ponto final da função.

| Atributo      | Type     | Descrição |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Necessário. O nome do ponto de extremidade para permitir que o tráfego.|
| `roleName`     | `string` | Necessário. O nome da função da web para permitir a comunicação para.|

## <a name="allowalltraffic-element"></a>Elemento de AllowAllTraffic
O `AllowAllTraffic` elemento é uma regra que permite que todas as funções comunicar com os pontos finais definidos no `Destinations` nó.

##  <a name="WhenSource"></a> Elemento de WhenSource
O `WhenSource` elemento descreve uma coleção de funções que pode comunicar com os pontos finais definidos no `Destinations` nó.

| Atributo | Type     | Descrição |
| --------- | -------- | ----------- |
| `matches` | `string` | Necessário. Especifica a regra se aplique ao permitir que comunicações. O único valor válido é atualmente `AnyRule`.|
  
##  <a name="FromRole"></a> Elemento de FromRole
O `FromRole` elemento Especifica as funções que podem se comunicar com os pontos finais definidos no `Destinations` nó. Pode especificar várias `FromRole` elementos se existirem mais de uma função que possa comunicar com os pontos de extremidade.

| Atributo  | Type     | Descrição |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Necessário. O nome para a função a partir do qual pode permitir a comunicação.|

## <a name="see-also"></a>Consultar Também
[Esquema de definição do cloud Service (clássico)](schema-csdef-file.md)
