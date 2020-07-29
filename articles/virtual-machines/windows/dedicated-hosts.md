---
title: Visão geral dos anfitriões dedicados Azure para máquinas virtuais
description: Saiba mais sobre como os Anfitriões Dedicados Azure podem ser usados para implementar máquinas virtuais.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 327eebb4ea1804fc19c8f1b82a57dd0a9a994803
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284579"
---
# <a name="azure-dedicated-hosts"></a>Anfitriões Dedicados Azure

O Azure Dedicated Host é um serviço que fornece servidores físicos - capazes de hospedar uma ou mais máquinas virtuais - dedicados a uma subscrição do Azure. Os anfitriões dedicados são os mesmos servidores físicos utilizados nos nossos centros de dados, fornecidos como recurso. Você pode providenciar anfitriões dedicados dentro de uma região, zona de disponibilidade e domínio de falhas. Em seguida, pode colocar VMs diretamente nos seus anfitriões a provisionados, em qualquer configuração que satisfaça melhor as suas necessidades.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Passos seguintes

- Pode implementar um anfitrião dedicado utilizando [a Azure PowerShell,](dedicated-hosts-powershell.md)o [portal](dedicated-hosts-portal.md)e [o Azure CLI](../linux/dedicated-hosts-cli.md).

- Existe o modelo de amostra, encontrado [aqui,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)que usa ambas as zonas e domínios de avaria para máxima resiliência numa região.

- Também pode economizar em custos com uma [Instância Reservada de Anfitriões Dedicados Azure.](../prepay-dedicated-hosts-reserved-instances.md)
