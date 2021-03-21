---
title: Observação de internet - PERGUNTAS Frequentes
titleSuffix: Azure
description: Observação de internet - PERGUNTAS Frequentes
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75775501"
---
# <a name="internet-peering---faqs"></a>Observação de internet - PERGUNTAS Frequentes

Pode rever as informações abaixo para questões gerais.

**Qual é a diferença entre o serviço de observação da Internet e o Serviço de Observação de Pares?**

O Peering Service é um serviço que pretende fornecer conectividade IP pública de nível empresarial à Microsoft para os seus clientes empresariais. A Enterprise Grade Internet inclui conectividade através de ISPs que têm alta conectividade de produção para a Microsoft e redundância para uma conectividade HA. Além disso, o tráfego de utilizadores é otimizado para a latência para o Microsoft Edge mais próximo. O Serviço de Observação baseia-se na conectividade de espreitar com a transportadora parceira. A conectividade de espreitar com o parceiro deve ser o olhar direto em oposição ao persto de Exchange. O espreitamento direto deve ter redundância local e geo-redundância.

**O que é o legado espreitando?**

A ligação de perscrute configurada utilizando o Azure PowerShell é gerida como um recurso Azure. As ligações de persiagens configuradas no passado são armazenadas no nosso sistema como um esprevamento legado que pode optar por converter para gerir como um recurso Azure.

**Quando New-AzPeeringDirectConnectionObject é chamado, quais os endereços IP que endereços IP são dados aos dispositivos Microsoft e Peer?**

Ao ligar para New-AzPeeringDirectConnectionObject cmdlet, é introduzido um endereço /31 (a.b.c.d/31) ou um endereço /30 (a.b.c.d/30). O primeiro endereço IP (a.b.c.d+0) é dado ao dispositivo peer e o segundo endereço IP (a.b.c.d+1) é dado ao dispositivo Microsoft.

**O que é MaxPrefixesAdvertisedIPv4 e MaxPrefixesDvertisedIPv6 em New-AzPeeringDirectConnectionObject cmdlet?**

Os parâmetros MaxPrefixesAdvertisedIPv4 e MaxPrefixesAdvertisedIPv6 representam o número máximo de prefixos IPv4 e IPv6 que um peer quer que a Microsoft aceite. Estes parâmetros podem ser modificados a qualquer momento.