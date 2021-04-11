---
title: Latência de ligação ao utilizador virtual do Windows Desktop - Azure
description: Latência de ligação para utilizadores de desktop virtual do Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 2fa03fa948c7768f5e74cc68b528e847c5351273
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447865"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Determinar a latência da ligação do utilizador no Windows Virtual Desktop

O Windows Virtual Desktop está disponível globalmente. Os administradores podem criar máquinas virtuais (VMs) em qualquer região do Azure que queiram. A latência da ligação variará consoante a localização dos utilizadores e das máquinas virtuais. Os serviços de Desktop Virtual do Windows serão continuamente lançados para novas geografias para melhorar a latência.

A [ferramenta de estimativa de experiência virtual do Windows desktop](https://azure.microsoft.com/services/virtual-desktop/assessment/) pode ajudá-lo a determinar a melhor localização para otimizar a latência dos seus VMs. Recomendamos que utilize a ferramenta de dois em dois a três meses para se certificar de que a localização ideal não foi alterada à medida que o Windows Virtual Desktop se estende para novas áreas.

## <a name="interpreting-results-from-the-windows-virtual-desktop-experience-estimator-tool"></a>Interpretação dos resultados da ferramenta de estimativa de experiência de ambiente de trabalho virtual do Windows

No Windows Virtual Desktop, a latência até 150 ms não deve ter impacto na experiência do utilizador que não envolva renderização ou vídeo. As latências entre 150 e 200 ms devem ser boas para o processamento de texto. A latência acima de 200 ms pode ter impacto na experiência do utilizador. 

Além disso, a ligação virtual do Windows Desktop depende da ligação à Internet da máquina a que o utilizador está a utilizar o serviço. Os utilizadores podem perder a ligação ou experimentar o atraso de entrada numa das seguintes situações:

 - O utilizador não tem uma ligação à Internet local estável e a latência é superior a 200 ms.
 - A rede está saturada ou limitada à taxa.

Recomendamos que escolha locais de VMs que estejam o mais próximo possível dos seus utilizadores. Por exemplo, se o utilizador estiver localizado na Índia, mas o VM estiver nos Estados Unidos, haverá latência que afetará a experiência geral do utilizador. 

## <a name="azure-front-door"></a>Azure Front Door

O Windows Virtual Desktop utiliza [a Porta Frontal Azure](https://azure.microsoft.com/services/frontdoor/) para redirecionar a ligação do utilizador para o gateway virtual do Windows mais próximo com base no endereço IP de origem. O Windows Virtual Desktop utilizará sempre o gateway virtual do Windows que o cliente escolhe.

## <a name="next-steps"></a>Passos seguintes

- Para verificar a melhor localização para obter uma latência ótima, consulte a [ferramenta de estimativa de experiência virtual do Windows Desktop](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Para planos de preços, consulte [os preços do Windows Virtual Desktop.](https://azure.microsoft.com/pricing/details/virtual-desktop/)
- Para começar com a sua implementação virtual do Windows Desktop, confira o [nosso tutorial](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
