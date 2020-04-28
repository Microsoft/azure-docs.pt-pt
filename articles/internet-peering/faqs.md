---
title: Internet peering - FAQs
titleSuffix: Azure
description: Internet peering - FAQs
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75775501"
---
# <a name="internet-peering---faqs"></a>Internet peering - FAQs

Pode rever as informações abaixo para questões gerais.

**Qual é a diferença entre o serviço de observação de Internet e o Serviço de Peering?**

O Peering Service é um serviço que pretende fornecer conectividade IP pública de nível empresarial à Microsoft para os seus clientes empresariais. A Internet de nível empresarial inclui conectividade através de ISPs que têm alta conectividade de entrada para a Microsoft e redundância para uma conectividade HA. Além disso, o tráfego de utilizadores está otimizado para latência ao Microsoft Edge mais próximo. O Serviço de Peering baseia-se na conectividade de pares com o parceiro. A conectividade de pares com o parceiro deve ser o peering direto em oposição ao peering exchange. O peering direto deve ter redundância local e geo-redundância.

**O que é o legado a espreitar?**

A ligação de observação criada utilizando o Azure PowerShell é gerida como um recurso Azure. As ligações de observação criadas no passado são armazenadas no nosso sistema como um olhar legado que você pode optar por converter para gerir como um recurso Azure.

**Quando o New-AzPeeringDirectConnectionObject é chamado, que endereços IP são dados aos dispositivos Microsoft e Peer?**

Ao ligar para o New-AzPeeringDirectConnectionObject cmdlet, é introduzido um endereço /31 (a.b.c.d/31) ou um endereço /30 (a.b.c.c.d/30). O primeiro endereço IP (a.b.c.d+0) é dado ao dispositivo de Peer e o segundo endereço IP (a.b.c.d+1) é dado ao dispositivo da Microsoft.

**O que é MaxPrefixesAdvertisedIPv4 e MaxPrefixesAdvertisedIPv6 em New-AzPeeringDirectConnectionObject cmdlet?**

MaxPrefixesAdvertisedIPv4 e MaxPrefixesOsparâmetrosPublicamenteIPv6 representam o número máximo de prefixos IPv4 e IPv6 que um Par quer que a Microsoft aceite. Estes parâmetros podem ser modificados a qualquer momento.