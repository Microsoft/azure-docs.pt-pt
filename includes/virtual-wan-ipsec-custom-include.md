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
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168416"
---
Ao trabalhar com políticas IPsec personalizadas, tenha em mente os seguintes requisitos:

* **Ike** -para Ike, você pode selecionar qualquer parâmetro da criptografia IKE, além de qualquer parâmetro da integridade Ike, além de qualquer parâmetro do grupo DH.
* **IPSec** -para IPsec, você pode selecionar qualquer parâmetro da criptografia IPSec, além de qualquer parâmetro da integridade do IPSec, além do PFS. Se qualquer um dos parâmetros para criptografia IPsec ou integridade IPsec for GCM, os parâmetros para ambas as configurações deverão ser GCM.

>[!NOTE]
> Com as políticas IPsec personalizadas, não há nenhum conceito de respondente e iniciador (ao contrário das políticas IPsec padrão). Ambos os lados (local e gateway de VPN do Azure) usarão as mesmas configurações para a fase 1 de IKE e a fase 2 da IKE. Há suporte para os protocolos IKEv1 e IKEv2. Não há suporte apenas para o Azure como respondente.
>

**Configurações e parâmetros disponíveis**

| Definição | Parâmetros |
|--- |--- |
| Criptografia IKE | AES256, AES192, AES128 |
| Integridade do IKE | SHA384, SHA256, SHA1 |
| Grupo DH | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2 |
| Encriptação do IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128 |
| Integridade do IPsec | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1 |
| Grupo PFS | PFS24, ECP384, ECP256, PFS2048, PFS2 |
