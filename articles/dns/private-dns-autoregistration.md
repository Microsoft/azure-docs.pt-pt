---
title: O que é recurso de auto-registo das zonas privadas Azure DNS
description: Visão geral da funcionalidade de auto-registo das zonas privadas do Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9da94f80f9a9c1b3cba7b8e3ac4fef7e717918c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88042756"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Qual é a característica de auto-registo das zonas privadas Azure DNS

A função de registo de zonas privadas Azure DNS retira a dor da gestão de registos DNS para máquinas virtuais implantadas numa rede virtual. Quando [liga uma rede virtual](./private-dns-virtual-network-links.md) a uma zona privada de DNS e permite o registo automático de todas as máquinas virtuais, os registos DNS para as máquinas virtuais implantadas na rede virtual são automaticamente criados na zona privada do DNS. Além de registar registos de look (registos A), os registos de procura inversa (registos PTR) também são automaticamente criados para as máquinas virtuais.
Se adicionar mais máquinas virtuais à rede virtual, os registos DNS destas máquinas virtuais também são automaticamente criados na zona privada de DNS ligada.

Quando elimina uma máquina virtual, os registos DNS para a máquina virtual são automaticamente eliminados da zona privada de DNS.

Pode ativar o registo automático selecionando a opção "Ativar o registo automático" enquanto cria uma ligação de rede virtual.

![Ativar o registo automático](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Restrições

* O autoregistration funciona apenas para máquinas virtuais. Para todos os outros recursos, como os equilibradores de carga internos, etc., pode criar registos DNS manualmente na zona privada de DNS ligada à rede virtual.
* Os registos DNS são criados automaticamente apenas para a máquina virtual primária NIC . Se as suas máquinas virtuais tiverem mais do que um NIC, pode criar manualmente os registos DNS para outras interfaces de rede.
* Os registos DNS são criados automaticamente apenas se a máquina virtual primária NIC estiver a utilizar o DHCP. Se os endereços IP estáticos estiverem configurados (por exemplo, para utilizar [vários endereços IP em Azure),](https://docs.microsoft.com/azure/virtual-network/virtual-network-multiple-ip-addresses-portal#os-config)a auto-registo não cria registos para essa máquina virtual.
* O registo automático do IPv6 (registos AAAA) não é suportado.

## <a name="next-steps"></a>Passos seguintes

* Saiba como criar uma zona privada em Azure DNS utilizando [a Azure PowerShell](./private-dns-getstarted-powershell.md) ou [Azure CLI](./private-dns-getstarted-cli.md).

* Leia sobre [alguns cenários comuns de zona privada](./private-dns-scenarios.md) que podem ser realizados com zonas privadas em Azure DNS.

* Para perguntas e respostas comuns sobre zonas privadas em Azure DNS, incluindo comportamentos específicos que pode esperar para determinados tipos de operações, consulte [o DNS PRIVADO FAQ](./dns-faq-private.md).
