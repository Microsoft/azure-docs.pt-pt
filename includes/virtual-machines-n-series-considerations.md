---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 14f5998ee1c562b649257f7dce9ffc2f52a66226
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174985"
---
## <a name="deployment-considerations"></a>Considerações sobre implementação

* Para obter a disponibilidade das VMs da série N, consulte [produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

* As VMs da série N só podem ser implantadas no modelo de implantação do Gerenciador de recursos.

* As VMs da série N diferem no tipo de armazenamento do Azure com suporte para seus discos. As VMs NC e NV só dão suporte a discos de VM que são apoiados por Armazenamento em Disco padrão (HDD). As VMs NCv2, NCv3, ND, NDv2 e NVv2 só dão suporte a discos de VM apoiados por SSD (Armazenamento em Disco Premium).

* Se você quiser implantar mais de algumas VMs da série N, considere uma assinatura paga conforme o uso ou outras opções de compra. Se estiver a utilizar uma [conta gratuita do Azure](https://azure.microsoft.com/free/), pode utilizar apenas um número limitado de núcleos de computação do Azure.

* Talvez seja necessário aumentar a cota de núcleos (por região) em sua assinatura do Azure e aumentar a cota separada para os núcleos NC, NCv2, NCv3, ND, NDv2, NV ou NVv2. Para solicitar um aumento de cota, [abra uma solicitação](../articles/azure-supportability/how-to-create-azure-support-request.md) de atendimento ao cliente online sem encargos. Os limites padrão podem variar dependendo da sua categoria de assinatura.




