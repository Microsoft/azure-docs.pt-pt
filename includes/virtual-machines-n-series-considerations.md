---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 1de7221f100077e07a2211bdb94e0198b35cb77c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776322"
---
## <a name="deployment-considerations"></a>Considerações sobre implementação

* Para uma disponibilidade de VMs de série N, consulte [produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

* VMs de série N só podem ser implementadas no modelo de implementação do Resource Manager.

* VMs de série N são diferentes no tipo de armazenamento do Azure, eles oferecem suporte para os respetivos discos. NC e NV VMs só suportam discos VM que são apoiados pelo armazenamento de disco Standard (HDD). NCv2, NCv3, ND, NDv2 e NVv2 VMs só suportam discos VM que são apoiados pelo armazenamento de disco Premium (SSD).

* Se pretender implementar mais do que algumas VMs de série N, considere uma subscrição pay as you go ou outras opções de compra. Se estiver a utilizar uma [conta gratuita do Azure](https://azure.microsoft.com/free/), pode utilizar apenas um número limitado de núcleos de computação do Azure.

* Poderá ter de aumentar a quota de núcleos (por região) na sua subscrição do Azure e aumentar a quota separada para NC, NCv2, NCv3, ND, NDv2, NV ou NVv2 núcleos. Para pedir um aumento de quota [abra um pedido de suporte do cliente online](../articles/azure-supportability/how-to-create-azure-support-request.md) sem encargos. Limites predefinidos podem variar consoante a categoria de subscrição.




