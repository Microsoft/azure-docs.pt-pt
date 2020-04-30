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
ms.openlocfilehash: e2a950037aed2a8ded4d4e55920721285cbfc05c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204436"
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
| Encriptação IKE | GCMAES256, GCMAES128, AES256, AES128 |
| Integridade ike | SHA384, SHA256 |
| Grupo DH | ECP384, ECP256, DHGroup24, DHGroup14 |
| Encriptação do IPsec | GCMAES256, GCMAES128, AES256, AES128, Nenhum |
| Integridade do IPsec | GCMAES256, GCMAES128, SHA256 |
| Grupo PFS | ECP384, ECP256, PFS24, PFS14, nenhum |
