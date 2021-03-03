---
title: Políticas virtuais wan point-to-site IPsec
titleSuffix: Azure Virtual WAN
description: Saiba mais sobre as políticas de conectividade IPsec do Azure Virtual WAN Point-to-site.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/24/2021
ms.author: wellee
ms.openlocfilehash: d64748bf7719af52b76c5e4141bfbbabe80bbae2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746988"
---
# <a name="point-to-site-ipsec-policies"></a>Políticas de IPsec ponto a local

Este artigo mostra as combinações de políticas IPsec suportadas para a conectividade VPN ponto-a-local em Azure Virtual WAN.

## <a name="default-ipsec-policies"></a>Políticas IPsec predefinidos

A tabela seguinte mostra os parâmetros IPsec padrão para ligações VPN ponto-a-local. Estes parâmetros são automaticamente escolhidos pela porta de entrada VPN ponto-a-local virtual quando um perfil ponto-a-local é criado.

| Definição | Parâmetros |
|--- |--- |
| Encriptação IKE fase 1 | AES256 |
| Integridade do IKE de fase 1 |  SHA256 |
| Grupo DH | DHGroup24 |
| Encriptação IPsec fase 2 | GCMAES256|
| Integridade do IPsec da fase 2 | GCMAES25 |
| Grupo PFS |PFS24|

## <a name="custom-ipsec-policies"></a>Políticas personalizadas do IPsec

Ao trabalhar com políticas IPsec personalizadas, tenha em mente os seguintes requisitos:

* **IKE** - Para a fase 1 IKE, pode selecionar qualquer parâmetro da Encriptação IKE, além de qualquer parâmetro da Integridade do IKE, além de qualquer parâmetro do Grupo DH.
* **IPsec** - Para a fase 2 IPsec, pode selecionar qualquer parâmetro da Encriptação IPsec, além de qualquer parâmetro da Integridade IPsec, mais PFS. Se algum dos parâmetros para encriptação IPsec ou Integridade IPsec for GCM, então tanto a Encriptação e Integridade IPsec devem usar o mesmo algoritmo. Por exemplo, se o GCMAES128 for escolhido para encriptação IPsec, o GCMAES128 também deve ser escolhido para integridade IPsec.  

A tabela a seguir mostra os parâmetros IPsec disponíveis para ligações VPN ponto-a-local.

| Definição | Parâmetros |
|--- |--- |
| Encriptação IKE fase 1 | AES128, AES256, GCMAES128, GCMAES256 |
| Integridade do IKE de fase 1 |  SHA256, SHA384 |
| Grupo DH | DHGroup14, DHGroup24, ECP256, ECP384 |
| Encriptação IPsec fase 2 | GCMAES128, GCMAES256, SHA256|
| Integridade do IPsec da fase 2 | GCMAES128, GCMAES256 |
| Grupo PFS |PFS14, PFS24, ECP256, ECP384|

## <a name="next-steps"></a>Passos seguintes

Ver [Como criar uma ligação ponto-a-local](virtual-wan-point-to-site-portal.md)

Para obter mais informações sobre o VIRTUAL WAN, consulte o [VIRTUAL WAN FAQ](virtual-wan-faq.md).
