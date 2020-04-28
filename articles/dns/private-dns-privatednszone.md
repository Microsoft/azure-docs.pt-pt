---
title: O que é uma zona privada Azure DNS
description: Visão geral de uma zona privada de DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: a951bc07c4a8ed42b1c116332d13674656bbaafd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75646800"
---
# <a name="what-is-a-private-azure-dns-zone"></a>O que é uma zona privada de DNS Azure

O Azure Private DNS fornece um serviço DNS fiável e seguro para gerir e resolver nomes de domínio numa rede virtual sem a necessidade de adicionar uma solução DNS personalizada. Ao utilizar zonas privadas de DNS, pode utilizar os seus próprios nomes de domínio personalizados em vez dos nomes fornecidos pelo Azure hoje em dia. 

Os registos contidos numa zona privada de DNS não são resolúveis da Internet. A resolução do DNS contra uma zona privada de DNS funciona apenas a partir de redes virtuais que estão ligadas a ela.

Pode ligar uma zona privada de DNS a uma ou mais redes virtuais através da criação de [ligações de rede virtuais.](./private-dns-virtual-network-links.md)
Também pode ativar a função [de registo automático](./private-dns-autoregistration.md) para gerir automaticamente o ciclo de vida dos registos DNS para as máquinas virtuais implantadas numa rede virtual.

## <a name="limits"></a>Limites

Para entender quantas zonas privadas de DNS pode criar numa subscrição e quantos conjuntos de discos são suportados numa zona privada de DNS ver [limites de DNS Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="restrictions"></a>Restrições

* As zonas privadas de DNS com rótulo único não são suportadas. A sua zona privada de DNS deve ter duas ou mais etiquetas. Por exemplo, contoso.com tem duas etiquetas separadas por um ponto. Uma zona privada de DNS pode ter um máximo de 34 etiquetas.
* Não se pode criar delegações de zona (registos de NS) numa zona privada de DNS. Se pretender utilizar um domínio infantil, pode criar diretamente o domínio como uma zona Privada de DNS e ligá-lo à rede virtual sem criar uma delegação de servidor de nomes a partir da zona-mãe.

## <a name="next-steps"></a>Passos seguintes

* Saiba como criar uma zona privada em Azure DNS utilizando [o Azure PowerShell](./private-dns-getstarted-powershell.md) ou [o Azure CLI](./private-dns-getstarted-cli.md).

* Leia sobre [alguns cenários de zona privada](./private-dns-scenarios.md) comum que podem ser realizados com zonas privadas em DNS Azure.

* Para perguntas e respostas comuns sobre zonas privadas em DNS Azure, incluindo comportamentos específicos que pode esperar para determinados tipos de operações, consulte o [DNS faQ privado](./dns-faq-private.md).
