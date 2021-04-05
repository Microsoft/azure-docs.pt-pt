---
title: Azure Cloud Services (suporte alargado) Def. NetworkTrafficRules Schema | Microsoft Docs
description: Informação relacionada com as regras de tráfego de rede associadas aos Serviços cloud (suporte alargado)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 0064794701e87419da086c458673f7ccee4f37dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98744794"
---
# <a name="azure-cloud-services-extended-support-definition-networktrafficrules-schema"></a>Azure Cloud Services (suporte alargado) definição NetworkTrafficRules schema

O `NetworkTrafficRules` nó é um elemento opcional no ficheiro de definição de serviço que especifica como as funções comunicam entre si. Limita quais as funções que podem aceder aos pontos finais internos do papel específico. O `NetworkTrafficRules` não é um elemento autónomo; é combinado com duas ou mais funções num ficheiro de definição de serviço.

A extensão padrão para o ficheiro de definição de serviço é csdef.

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

## <a name="schema-elements"></a>Elementos de esquema
O `NetworkTrafficRules` nó do ficheiro de definição de serviço inclui estes elementos, descritos em detalhe nas secções subsequentes neste tópico:

[Elemento NetworkTrafficRules](#NetworkTrafficRules)

[Elemento OnlyAllowTrafficTo](#OnlyAllowTrafficTo)

[Elemento destinos](#Destinations)

[Elemento RoleEndpoint](#RoleEndpoint)

[Permitir Elemento AllTraffic](#AllowAllTraffic)

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

| Atributo      | Tipo     | Description |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Obrigatório. O nome do ponto final para permitir o tráfego.|
| `roleName`     | `string` | Obrigatório. O nome da função web para permitir a comunicação.|

## <a name="allowalltraffic-element"></a><a name="AllowAllTraffic"></a> Permitir elemento AllTraffic
O `AllowAllTraffic` elemento é uma regra que permite que todas as funções se comuniquem com os pontos finais definidos no `Destinations` nó.

##  <a name="whensource-element"></a><a name="WhenSource"></a> Elemento WhenSource
O `WhenSource` elemento descreve uma coleção de papéis que pode comunicar com os pontos finais definidos no `Destinations` nó.

| Atributo | Tipo     | Description |
| --------- | -------- | ----------- |
| `matches` | `string` | Obrigatório. Especifica a regra a aplicar ao permitir comunicações. O único valor válido é `AnyRule` atualmente.|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a> Elemento FromRole
O `FromRole` elemento especifica as funções que podem comunicar com os pontos finais definidos no `Destinations` nó. Pode especificar `FromRole` vários elementos se houver mais de uma função que possa comunicar com os pontos finais.

| Atributo  | Tipo     | Description |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Obrigatório. O nome para o papel a partir do qual permitir a comunicação.|

## <a name="see-also"></a>Ver também
[Sistema de Definição do Serviço de Nuvem (suporte alargado) Definição](schema-csdef-file.md).




