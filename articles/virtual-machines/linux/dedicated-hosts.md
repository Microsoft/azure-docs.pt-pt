---
title: Visão geral dos anfitriões dedicados Azure para máquinas virtuais
description: Saiba mais sobre como os Anfitriões Dedicados Azure podem ser usados para implementar máquinas virtuais.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 7e19f74c40bbeb83c9230e620c4488778823ef99
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86510808"
---
# <a name="azure-dedicated-hosts-for-virtual-machines"></a>Azure Anfitriões Dedicados para máquinas virtuais

O Azure Dedicated Host é um serviço que fornece servidores físicos - capazes de hospedar uma ou mais máquinas virtuais - dedicados a uma subscrição do Azure. Os anfitriões dedicados são os mesmos servidores físicos utilizados nos nossos centros de dados, fornecidos como recurso. Você pode providenciar anfitriões dedicados dentro de uma região, zona de disponibilidade e domínio de falhas. Em seguida, pode colocar VMs diretamente nos seus anfitriões a provisionados, em qualquer configuração que satisfaça melhor as suas necessidades.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Próximos passos

- Pode implantar um anfitrião dedicado utilizando o [Azure CLI,](dedicated-hosts-cli.md) [portal](dedicated-hosts-portal.md)e [PowerShell](../windows/dedicated-hosts-powershell.md).

- Para mais informações, consulte a visão geral dos [anfitriões dedicados.](dedicated-hosts.md)

- Existe o modelo de amostra, encontrado [aqui,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)que usa ambas as zonas e domínios de avaria para máxima resiliência numa região.

- Também pode economizar em custos com uma [Instância Reservada de Anfitriões Dedicados Azure.](../prepay-dedicated-hosts-reserved-instances.md)
