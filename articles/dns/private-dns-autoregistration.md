---
title: O que é a funcionalidade de auto-registo das zonas privadas Azure DNS
description: Visão geral da funcionalidade de registo automático das zonas privadas Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9d1854b459e799d5cbb401de9ac717dd7d0fde1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71961212"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Qual é a funcionalidade de registo automático de zonas privadas Azure DNS

A funcionalidade de registo automóvel de zonas privadas Azure DNS retira a dor da gestão de registos dNS para máquinas virtuais implantadas numa rede virtual. Quando [liga uma rede virtual](./private-dns-virtual-network-links.md) a uma zona Privada de DNS e permite o registo automático de todas as máquinas virtuais, os registos DNS para as máquinas virtuais implantadas na rede virtual são automaticamente criados na zona privada de DNS. Além dos registos de look para a frente (Registos A), os registos de procura reversa (registos PTR) também são automaticamente criados para as máquinas virtuais.
Se adicionar mais máquinas virtuais à rede virtual, os registos DNS para estas máquinas virtuais também são automaticamente criados na zona privada dNS ligada.

Ao eliminar uma máquina virtual, os registos DNS da máquina virtual são automaticamente eliminados da zona Privada DNS.

Pode ativar o registo automático selecionando a opção "Ativar o registo automático" enquanto cria uma ligação de rede virtual.

![Ativar o registo automático](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Restrições

* O registo automático funciona apenas para máquinas virtuais. Para todos os outros recursos, como equilibradores de carga internos, etc., pode criar registos DNS manualmente na zona privada de DNS ligada à rede virtual.
* Os registos DNS são criados automaticamente apenas para a máquina virtual primária NIC . Se as suas máquinas virtuais tiverem mais de um NIC, pode criar manualmente os registos DNS para outras interfaces de rede.
* O registo automático do IPv6 (registos AAAA) não é suportado.

## <a name="next-steps"></a>Passos seguintes

* Saiba como criar uma zona privada em Azure DNS utilizando [o Azure PowerShell](./private-dns-getstarted-powershell.md) ou [o Azure CLI](./private-dns-getstarted-cli.md).

* Leia sobre [alguns cenários de zona privada](./private-dns-scenarios.md) comum que podem ser realizados com zonas privadas em DNS Azure.

* Para perguntas e respostas comuns sobre zonas privadas em DNS Azure, incluindo comportamentos específicos que pode esperar para determinados tipos de operações, consulte o [DNS faQ privado](./dns-faq-private.md).
