---
title: O que é o recurso Autoregistro de zonas privadas do DNS do Azure
description: Visão geral do recurso de Autoregistro de zonas privadas do DNS do Azure
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9d1854b459e799d5cbb401de9ac717dd7d0fde1d
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961212"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>O que é o recurso Autoregistro de zonas privadas do DNS do Azure

O recurso de registro automático de zonas privadas do DNS do Azure elimina o problema do gerenciamento de registros DNS para máquinas virtuais implantadas em uma rede virtual. Quando você [vincula uma rede virtual](./private-dns-virtual-network-links.md) a uma zona DNS privada e habilita o registro automático para todas as máquinas virtuais, os registros DNS para as máquinas virtuais implantadas na rede virtual são criados automaticamente na zona DNS privada. Além de encaminhar registros de aparência (registros A), os registros de pesquisa inversa (registros PTR) também são criados automaticamente para as máquinas virtuais.
Se você adicionar mais máquinas virtuais à rede virtual, os registros DNS para essas máquinas virtuais também serão criados automaticamente na zona DNS privada vinculada.

Quando você exclui uma máquina virtual, os registros DNS da máquina virtual são automaticamente excluídos da zona DNS privada.

Você pode habilitar o registro automático selecionando a opção "Habilitar registro auto" ao criar um link de rede virtual.

![Habilitar o registro automático](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Restrições

* O Autoregistro funciona apenas para máquinas virtuais. Para todos os outros recursos, como balanceadores de carga internos, etc., você pode criar registros DNS manualmente na zona DNS privada vinculada à rede virtual.
* Os registros DNS são criados automaticamente somente para a NIC de máquina virtual primária. Se suas máquinas virtuais tiverem mais de uma NIC, você poderá criar manualmente os registros DNS para outras interfaces de rede.
* Não há suporte para o registro em IPv6 (registros AAAA).

## <a name="next-steps"></a>Passos seguintes

* Saiba como criar uma zona privada no DNS do Azure usando [Azure PowerShell](./private-dns-getstarted-powershell.md) ou [CLI do Azure](./private-dns-getstarted-cli.md).

* Leia sobre alguns cenários comuns de [zona privada](./private-dns-scenarios.md) que podem ser percebidos com zonas privadas no DNS do Azure.

* Para perguntas e respostas comuns sobre zonas privadas no DNS do Azure, incluindo um comportamento específico que você pode esperar para determinados tipos de operações, consulte [DNS privado perguntas frequentes](./dns-faq-private.md).
