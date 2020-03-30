---
title: Visão geral dos anfitriões dedicados do Azure para máquinas virtuais
description: Saiba mais sobre como os Anfitriões Dedicados azure podem ser usados para a implementação de máquinas virtuais.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 39d1c0ddb4961800e889346ec110ca629ae73546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251553"
---
# <a name="azure-dedicated-hosts"></a>Anfitriões Dedicados azure

O Azure Dedicated Host é um serviço que fornece servidores físicos - capazes de acolher uma ou mais máquinas virtuais - dedicados a uma subscrição azure. Os anfitriões dedicados são os mesmos servidores físicos utilizados nos nossos centros de dados, fornecidos como recurso. Você pode fornecer anfitriões dedicados dentro de uma região, zona de disponibilidade e domínio de falha. Em seguida, pode colocar VMs diretamente nos seus anfitriões, em qualquer configuração que melhor atenda às suas necessidades.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Passos seguintes

- Pode implantar um hospedeiro dedicado utilizando o [Azure PowerShell,](dedicated-hosts-powershell.md)o [portal](dedicated-hosts-portal.md)e o [Azure CLI](../linux/dedicated-hosts-cli.md).

- Há um modelo de amostra, encontrado [aqui,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)que usa tanto zonas como domínios de falha para a máxima resiliência numa região.

- Você também pode economizar em custos com uma [instância reservada de anfitriões dedicados Azure.](../prepay-dedicated-hosts-reserved-instances.md)
