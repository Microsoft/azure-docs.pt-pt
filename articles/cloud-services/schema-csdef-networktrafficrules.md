---
title: Azure Cloud Services Def. NetworkTrafficRules Schema Microsoft Docs
description: Saiba mais sobre o NetworkTrafficRules, que limita as funções que podem aceder aos pontos finais internos de um papel. Combina com papéis num ficheiro de definição de serviço.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "79534733"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Azure Cloud Services Definition NetworkTrafficRules Schema
O `NetworkTrafficRules` nó é um elemento opcional no ficheiro de definição de serviço que especifica como as funções comunicam entre si. Limita quais as funções que podem aceder aos pontos finais internos do papel específico. O `NetworkTrafficRules` não é um elemento autónomo; é combinado com duas ou mais funções num ficheiro de definição de serviço.

A extensão por defeito para o ficheiro de definição de serviço é .csdef.

> [!NOTE]
>  O `NetworkTrafficRules` nó só está disponível utilizando a versão Azure SDK 1.3 ou superior.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Esquema de definição de serviço básico para as regras de tráfego de rede
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

## <a name="schema-elements"></a>Elementos De Esquema
O `NetworkTrafficRules` nó do ficheiro de definição de serviço inclui estes elementos, descritos em detalhe nas secções subsequentes neste tópico:

[Elemento NetworkTrafficRules](#NetworkTrafficRules)

[Elemento OnlyAllowTrafficTo](#OnlyAllowTrafficTo)

[Elemento destinos](#Destinations)

[Elemento RoleEndpoint](#RoleEndpoint)

Permitir Elemento AllTraffic

[Quando Elemento DeOrigem](#WhenSource)

[Elemento FromRole](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a> Elemento NetworkTrafficRules
O `NetworkTrafficRules` elemento especifica quais as funções que podem comunicar com qual ponto final noutra função. Um serviço pode conter uma `NetworkTrafficRules` definição.

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a> Elemento OnlyAllowTrafficTo
O `OnlyAllowTrafficTo` elemento descreve uma coleção de pontos finais de destino e as funções que podem comunicar com eles. Pode especificar vários `OnlyAllowTrafficTo` nós.

##  <a name="destinations-element"></a><a name="Destinations"></a> Elemento destinos
O `Destinations` elemento descreve uma coleção de RoleEndpoints com o que pode ser comunicado.

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a> Elemento RoleEndpoint
O `RoleEndpoint` elemento descreve um ponto final sobre um papel para permitir comunicações com. Pode especificar `RoleEndpoint` vários elementos se houver mais de um ponto final na função.

| Atributo      | Tipo     | Descrição |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Obrigatório. O nome do ponto final para permitir o tráfego.|
| `roleName`     | `string` | Obrigatório. O nome da função web para permitir a comunicação.|

## <a name="allowalltraffic-element"></a>Permitir Elemento AllTraffic
O `AllowAllTraffic` elemento é uma regra que permite que todas as funções se comuniquem com os pontos finais definidos no `Destinations` nó.

##  <a name="whensource-element"></a><a name="WhenSource"></a> Quando Elemento DeOrigem
O `WhenSource` elemento descreve uma coleção de papéis que pode comunicar com os pontos finais definidos no `Destinations` nó.

| Atributo | Tipo     | Descrição |
| --------- | -------- | ----------- |
| `matches` | `string` | Obrigatório. Especifica a regra a aplicar ao permitir comunicações. O único valor válido é `AnyRule` atualmente.|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a> Elemento FromRole
O `FromRole` elemento especifica as funções que podem comunicar com os pontos finais definidos no `Destinations` nó. Pode especificar `FromRole` vários elementos se houver mais de uma função que possa comunicar com os pontos finais.

| Atributo  | Tipo     | Descrição |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Obrigatório. O nome para o papel a partir do qual permitir a comunicação.|

## <a name="see-also"></a>Consulte também
[Esquema de Definição de Cloud Service (clássico)](schema-csdef-file.md)




