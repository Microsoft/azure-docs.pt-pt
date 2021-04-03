---
title: O que é uma zona privada Azure DNS
description: Visão geral de uma zona privada de DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9eaa320e79f1d595303c6d9fe1399df12cb6c52b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954414"
---
# <a name="what-is-a-private-azure-dns-zone"></a>O que é uma zona privada de DNS Azure

O Azure Private DNS fornece um serviço DNS fiável e seguro para gerir e resolver nomes de domínio numa rede virtual sem a necessidade de adicionar uma solução de DNS personalizada. Ao utilizar zonas de DNS privadas, pode usar os seus próprios nomes de domínio personalizados em vez dos nomes fornecidos pelo Azure disponíveis hoje. 

Os registos contidos numa zona privada de DNS não são resolúveis a partir da Internet. A resolução do DNS contra uma zona privada de DNS funciona apenas a partir de redes virtuais que estão ligadas à sua.

Pode ligar uma zona de DNS privada a uma ou mais redes virtuais criando [links de rede virtuais.](./private-dns-virtual-network-links.md)
Também é possível ativar a funcionalidade [de registo automático](./private-dns-autoregistration.md) para gerir automaticamente o ciclo de vida dos registos DNS para as máquinas virtuais implantadas numa rede virtual.

## <a name="limits"></a>Limites

Para entender quantas zonas privadas de DNS pode criar numa subscrição e quantos conjuntos de discos são suportados numa zona privada de DNS ver [limites de DNS do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)

## <a name="restrictions"></a>Restrições

* As zonas de DNS privadas com rótulo único não são suportadas. A sua zona privada de DNS deve ter duas ou mais etiquetas. Por exemplo, contoso.com tem duas etiquetas separadas por um ponto. Uma zona privada de DNS pode ter um máximo de 34 rótulos.
* Não é possível criar delegações de zona (registos NS) numa zona privada de DNS. Se pretender utilizar um domínio infantil, pode criar diretamente o domínio como zona privada de DNS e ligá-lo à rede virtual sem configurar uma delegação de nomes da zona-mãe.

## <a name="next-steps"></a>Passos seguintes

* Saiba como criar uma zona privada em Azure DNS utilizando [a Azure PowerShell](./private-dns-getstarted-powershell.md) ou [Azure CLI](./private-dns-getstarted-cli.md).

* Leia sobre [alguns cenários comuns de zona privada](./private-dns-scenarios.md) que podem ser realizados com zonas privadas em Azure DNS.

* Para perguntas e respostas comuns sobre zonas privadas em Azure DNS, incluindo comportamentos específicos que pode esperar para determinados tipos de operações, consulte [o DNS PRIVADO FAQ](./dns-faq-private.md).