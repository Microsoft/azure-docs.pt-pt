---
title: Visão geral dos hosts dedicados do Azure para máquinas virtuais
description: Saiba mais sobre como os hosts dedicados do Azure podem ser usados para implantar máquinas virtuais.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/25/2019
ms.author: cynthn
ms.openlocfilehash: cfcc8aeb3d1cc1f199b19abc16cc54db416a307e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033385"
---
# <a name="preview-azure-dedicated-hosts"></a>Versão prévia: hosts dedicados do Azure

O host dedicado do Azure é um serviço que fornece servidores físicos capazes de hospedar uma ou mais máquinas virtuais-dedicadas a uma assinatura do Azure. Os hosts dedicados são os mesmos servidores físicos usados em nossos data centers, fornecidos como um recurso. Você pode provisionar hosts dedicados em uma região, zona de disponibilidade e domínio de falha. Em seguida, você pode posicionar VMs diretamente em seus hosts provisionados, em qualquer configuração que melhor atenda às suas necessidades.

[!INCLUDE [virtual-machines-common-dedicated-hosts-preview](../../../includes/virtual-machines-common-dedicated-hosts-preview.md)]

[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Passos seguintes

- Você pode implantar um host dedicado usando [Azure PowerShell](dedicated-hosts-powershell.md), o [portal](dedicated-hosts-portal.md)e o [CLI do Azure](../linux/dedicated-hosts-cli.md).

- Há um modelo de exemplo, encontrado [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), que usa zonas e domínios de falha para obter máxima resiliência em uma região.
