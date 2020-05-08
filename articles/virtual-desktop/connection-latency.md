---
title: Latência de ligação ao utilizador do Windows Virtual Desktop - Azure
description: Latência de ligação para utilizadores de Ambiente de Trabalho Virtual windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8a60779fb045aa612a6ba0988c4635752f973f60
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607406"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Determine a latência da ligação do utilizador no Windows Virtual Desktop

O Windows Virtual Desktop está globalmente disponível. Os administradores podem criar máquinas virtuais (VMs) em qualquer região azure que queiram. A latência de ligação variará consoante a localização dos utilizadores e das máquinas virtuais. Os serviços de Desktop Virtual windows serão continuamente lançados para novas geografias para melhorar a latência. 
 
A ferramenta Estimativa de Experiência de Ambiente de [Trabalho Virtual do Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/) pode ajudá-lo a determinar a melhor localização para otimizar a latência dos seus VMs. Recomendamos que utilize a ferramenta de dois em dois a três meses para se certificar de que a localização ideal não mudou à medida que o Windows Virtual Desktop avança para novas áreas. 

## <a name="azure-traffic-manager"></a>Traffic Manager do Azure

O Windows Virtual Desktop utiliza o Gestor de Tráfego Azure, que verifica a localização do servidor DNS do utilizador para encontrar a instância de serviço de ambiente de trabalho virtual do Windows mais próxima. Recomendamos que os administradores revejam a localização do servidor DNS do utilizador antes de escolher a localização para os VMs.

## <a name="next-steps"></a>Passos seguintes

- Para verificar a melhor localização para obter uma latência ideal, consulte a ferramenta Estimativa de Experiência de Ambiente de [Trabalho Virtual do Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Para planos de preços, consulte os preços do [Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Para começar com a implementação do Windows Virtual Desktop, confira [o nosso tutorial.](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)