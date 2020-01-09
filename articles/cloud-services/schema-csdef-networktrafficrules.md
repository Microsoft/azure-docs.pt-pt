---
title: Esquema de Def. NetworkTrafficRules do Azure Cloud Services | Microsoft Docs
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
ms.openlocfilehash: e6d156810b9fdee69ddac122eec06db7267ddf36
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449046"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Esquema NetworkTrafficRules de definição de serviços de nuvem do Azure
O nó `NetworkTrafficRules` é um elemento opcional no arquivo de definição de serviço que especifica como as funções se comunicam entre si. Ele limita quais funções podem acessar os pontos de extremidade internos da função específica. O `NetworkTrafficRules` não é um elemento autônomo; Ele é combinado com duas ou mais funções em um arquivo de definição de serviço.

A extensão padrão para o arquivo de definição de serviço é. csdef.

> [!NOTE]
>  O nó `NetworkTrafficRules` só está disponível usando o SDK do Azure versão 1,3 ou superior.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Esquema de definição de serviço básico para as regras de tráfego de rede
O formato básico de um arquivo de definição de serviço que contém definições de tráfego de rede é o seguinte.

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
O `NetworkTrafficRules` nó do arquivo de definição de serviço inclui esses elementos, descritos em detalhes nas seções subsequentes neste tópico:

[Elemento NetworkTrafficRules](#NetworkTrafficRules)

[Elemento OnlyAllowTrafficTo](#OnlyAllowTrafficTo)

[Elemento destinos](#Destinations)

[Elemento RoleEndpoint](#RoleEndpoint)

Elemento AllowAllTraffic

[Elemento whenname](#WhenSource)

[Elemento FromRole](#FromRole)

##  <a name="NetworkTrafficRules"></a>Elemento NetworkTrafficRules
O elemento `NetworkTrafficRules` especifica quais funções podem se comunicar com qual ponto de extremidade em outra função. Um serviço pode conter uma definição de `NetworkTrafficRules`.

##  <a name="OnlyAllowTrafficTo"></a>Elemento OnlyAllowTrafficTo
O elemento `OnlyAllowTrafficTo` descreve uma coleção de pontos de extremidade de destino e as funções que podem se comunicar com eles. Você pode especificar vários nós de `OnlyAllowTrafficTo`.

##  <a name="Destinations"></a>Elemento destinos
O elemento `Destinations` descreve uma coleção de RoleEndpoints que pode ser comunicada com.

##  <a name="RoleEndpoint"></a>Elemento RoleEndpoint
O elemento `RoleEndpoint` descreve um ponto de extremidade em uma função para permitir a comunicação com o. Você pode especificar vários elementos `RoleEndpoint` se houver mais de um ponto de extremidade na função.

| Atributo      | Tipo     | Descrição |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Necessário. O nome do ponto de extremidade para o qual permitir o tráfego.|
| `roleName`     | `string` | Necessário. O nome da função Web à qual permitir a comunicação.|

## <a name="allowalltraffic-element"></a>Elemento AllowAllTraffic
O elemento `AllowAllTraffic` é uma regra que permite que todas as funções se comuniquem com os pontos de extremidade definidos no nó `Destinations`.

##  <a name="WhenSource"></a>Elemento whenname
O elemento `WhenSource` descreve uma coleção de funções do que pode se comunicar com os pontos de extremidade definidos no nó `Destinations`.

| Atributo | Tipo     | Descrição |
| --------- | -------- | ----------- |
| `matches` | `string` | Necessário. Especifica a regra a ser aplicada ao permitir comunicações. O único valor válido é atualmente `AnyRule`.|
  
##  <a name="FromRole"></a>Elemento FromRole
O elemento `FromRole` especifica as funções que podem se comunicar com os pontos de extremidade definidos no nó `Destinations`. Você pode especificar vários elementos `FromRole` se houver mais de uma função que possa se comunicar com os pontos de extremidade.

| Atributo  | Tipo     | Descrição |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Necessário. O nome da função da qual permitir a comunicação.|

## <a name="see-also"></a>Veja também
[Esquema de definição do serviço de nuvem (clássico)](schema-csdef-file.md)




