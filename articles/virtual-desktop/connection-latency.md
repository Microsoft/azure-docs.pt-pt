---
title: Latência de ligação ao utilizador virtual do Windows Desktop - Azure
description: Latência de ligação para utilizadores de desktop virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8a60779fb045aa612a6ba0988c4635752f973f60
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82607406"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Determinar a latência da ligação do utilizador no Windows Virtual Desktop

O Windows Virtual Desktop está disponível globalmente. Os administradores podem criar máquinas virtuais (VMs) em qualquer região do Azure que queiram. A latência da ligação variará consoante a localização dos utilizadores e das máquinas virtuais. Os serviços de Desktop Virtual do Windows serão continuamente lançados para novas geografias para melhorar a latência. 
 
A [ferramenta de estimativa de experiência virtual do Windows desktop](https://azure.microsoft.com/services/virtual-desktop/assessment/) pode ajudá-lo a determinar a melhor localização para otimizar a latência dos seus VMs. Recomendamos que utilize a ferramenta de dois em dois a três meses para se certificar de que a localização ideal não foi alterada à medida que o Windows Virtual Desktop se estende para novas áreas. 

## <a name="azure-traffic-manager"></a>Traffic Manager do Azure

O Windows Virtual Desktop utiliza o Gestor de Tráfego Azure, que verifica a localização do servidor DNS do utilizador para encontrar a instância de serviço virtual do Windows virtual mais próxima. Recomendamos que os administradores revejam a localização do servidor DNS do utilizador antes de escolherem a localização para os VMs.

## <a name="next-steps"></a>Passos seguintes

- Para verificar a melhor localização para obter uma latência ótima, consulte a [ferramenta de estimativa de experiência virtual do Windows Desktop](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Para planos de preços, consulte [os preços do Windows Virtual Desktop.](https://azure.microsoft.com/pricing/details/virtual-desktop/)
- Para começar com a sua implementação virtual do Windows Desktop, confira o [nosso tutorial](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).