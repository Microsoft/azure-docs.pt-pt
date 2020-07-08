---
title: Visão geral dos anfitriões dedicados Azure para máquinas virtuais
description: Saiba mais sobre como os Anfitriões Dedicados Azure podem ser usados para implementar máquinas virtuais.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: c19b3410e106aaf5fed53aba45d06eac6dd9d601
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78970733"
---
# <a name="azure-dedicated-hosts"></a>Anfitriões Dedicados Azure

O Azure Dedicated Host é um serviço que fornece servidores físicos - capazes de hospedar uma ou mais máquinas virtuais - dedicados a uma subscrição do Azure. Os anfitriões dedicados são os mesmos servidores físicos utilizados nos nossos centros de dados, fornecidos como recurso. Você pode providenciar anfitriões dedicados dentro de uma região, zona de disponibilidade e domínio de falhas. Em seguida, pode colocar VMs diretamente nos seus anfitriões a provisionados, em qualquer configuração que satisfaça melhor as suas necessidades.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Próximos passos

- Pode implantar um anfitrião dedicado utilizando o [Azure CLI,](dedicated-hosts-cli.md) [portal](dedicated-hosts-portal.md)e [PowerShell](../windows/dedicated-hosts-powershell.md).

- Para mais informações, consulte a visão geral dos [anfitriões dedicados.](dedicated-hosts.md)

- Existe o modelo de amostra, encontrado [aqui,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)que usa ambas as zonas e domínios de avaria para máxima resiliência numa região.

- Também pode economizar em custos com uma [Instância Reservada de Anfitriões Dedicados Azure.](../prepay-dedicated-hosts-reserved-instances.md)
