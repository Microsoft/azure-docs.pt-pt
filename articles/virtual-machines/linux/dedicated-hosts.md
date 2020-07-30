---
title: Visão geral dos anfitriões dedicados Azure para máquinas virtuais
description: Saiba mais sobre como os Anfitriões Dedicados Azure podem ser usados para implementar máquinas virtuais.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 07/28/2020
ms.author: cynthn
ms.openlocfilehash: 97e65528a7f049c213ec077c83da1b7672469d90
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388174"
---
# <a name="azure-dedicated-hosts-for-virtual-machines"></a>Azure Anfitriões Dedicados para máquinas virtuais

O Azure Dedicated Host é um serviço que fornece servidores físicos - capazes de hospedar uma ou mais máquinas virtuais - dedicados a uma subscrição do Azure. Os anfitriões dedicados são os mesmos servidores físicos utilizados nos nossos centros de dados, fornecidos como recurso. Você pode providenciar anfitriões dedicados dentro de uma região, zona de disponibilidade e domínio de falhas. Em seguida, pode colocar VMs diretamente nos seus anfitriões a provisionados, em qualquer configuração que satisfaça melhor as suas necessidades.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Próximos passos

- Pode implantar um anfitrião dedicado utilizando o [Azure CLI,](dedicated-hosts-cli.md) [portal](dedicated-hosts-portal.md)e [PowerShell](../windows/dedicated-hosts-powershell.md).

- Para mais informações, consulte a visão geral dos [anfitriões dedicados.](dedicated-hosts.md)

- Existe o modelo de amostra, encontrado [aqui,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)que usa ambas as zonas e domínios de avaria para máxima resiliência numa região.

- Também pode economizar em custos com uma [Instância Reservada de Anfitriões Dedicados Azure.](../prepay-dedicated-hosts-reserved-instances.md)
