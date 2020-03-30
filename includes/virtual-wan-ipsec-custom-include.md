---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f7167cbb26e69941cade01ab8c0b8d9dc633f0d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72168416"
---
Ao trabalhar com políticas personalizadas de IPsec, tenha em mente os seguintes requisitos:

* **IKE** - Para IKE, pode selecionar qualquer parâmetro da Encriptação IKE, além de qualquer parâmetro da IKE Integrity, além de qualquer parâmetro do Grupo DH.
* **IPsec** - Para iPsec, pode selecionar qualquer parâmetro da IPsec Encryption, além de qualquer parâmetro da Integridade IPsec, mais PFS. Se algum dos parâmetros para encriptação IPsec ou Integridade IPsec for GCM, então os parâmetros para ambas as definições devem ser GCM.

>[!NOTE]
> Com as políticas personalizadas do IPsec, não existe nenhum conceito de resposta e iniciador (ao contrário das políticas de IPsec padrão). Ambos os lados (no local e o gateway Azure VPN) utilizarão as mesmas definições para as fases 1 e IKE ike. Os protocolos IKEv1 e IKEv2 são apoiados. Não há apoio para o Azure apenas como resposta.
>

**Definições e parâmetros disponíveis**

| Definição | Parâmetros |
|--- |--- |
| Encriptação IKE | AES256, AES192, AES128 |
| Integridade ike | SHA384, SHA256, SHA1 |
| Grupo DH | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2 |
| Encriptação do IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128 |
| Integridade do IPsec | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1 |
| Grupo PFS | PFS24, ECP384, ECP256, PFS2048, PFS2 |
