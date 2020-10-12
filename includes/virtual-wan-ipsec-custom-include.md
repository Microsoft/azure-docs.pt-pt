---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e2a950037aed2a8ded4d4e55920721285cbfc05c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82204436"
---
Ao trabalhar com políticas IPsec personalizadas, tenha em mente os seguintes requisitos:

* **IKE** - Para o IKE, pode selecionar qualquer parâmetro da Encriptação IKE, além de qualquer parâmetro da Integridade do IKE, além de qualquer parâmetro do Grupo DH.
* **IPsec** - Para o IPsec, pode selecionar qualquer parâmetro da Encriptação IPsec, além de qualquer parâmetro da IPsec Integrity, mais PFS. Se algum dos parâmetros para encriptação IPsec ou Integridade IPsec for GCM, então os parâmetros para ambas as definições devem ser GCM.

>[!NOTE]
> Com as políticas IPsec personalizadas, não existe nenhum conceito de resposta e iniciador (ao contrário das políticas padrão do IPsec). Ambos os lados (no local e gateway Azure VPN) utilizarão as mesmas definições para a Fase 1 e IKE fase 2 do IKE. Os protocolos IKEv1 e IKEv2 são suportados. Não há apoio para o Azure como um único socorrista.
>

**Definições e parâmetros disponíveis**

| Definição | Parâmetros |
|--- |--- |
| Encriptação IKE | GCMAES256, GCMAES128, AES256, AES128 |
| Integridade do IKE | SHA384, SHA256 |
| Grupo DH | ECP384, ECP256, DHGroup24, DHGroup14 |
| Encriptação do IPsec | GCMAES256, GCMAES128, AES256, AES128, NENHUM |
| Integridade do IPsec | GCMAES256, GCMAES128, SHA256 |
| Grupo PFS | ECP384, ECP256, PFS24, PFS14, Nenhum |
