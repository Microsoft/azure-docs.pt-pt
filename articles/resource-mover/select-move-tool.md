---
title: Escolha uma ferramenta para mover recursos Azure através das regiões
description: Rever opções e ferramentas para mover recursos do Azure através das regiões
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 1b233028b52175842c73660ff116ac592d8296e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90603361"
---
# <a name="choose-a-tool-for-moving-azure-resources"></a>Escolha uma ferramenta para mover recursos Azure

Pode mover recursos dentro de Azure da seguinte forma:

- **Mover recursos através de regiões**: Mover recursos de dentro do centro de Recursos Mover, ou dentro de um grupo de recursos. 
- **Mover recursos através de grupos de recursos/subscrições**: Mover-se de dentro de um grupo de recursos. 
- **Mover recursos entre nuvens de Azure**: Use o serviço de recuperação do local de Azure para mover recursos entre nuvens públicas e governamentais.
- **Mover recursos entre zonas de disponibilidade na mesma região**: Utilize o serviço de Recuperação do Sítio Azure para mover recursos entre zonas de disponibilidade na mesma região do Azure.


## <a name="compare-move-tools"></a>Compare ferramentas de movimento

**Ferramenta** | **Quando utilizar** | **Saiba mais**
--- | --- | ---
**Mover-se dentro do grupo de recursos** | Mover recursos para um grupo de recursos/subscrição diferente, ou em todas as regiões.<br/><br/> Se você se deslocar através de regiões, no grupo de recursos você seleciona os recursos que você quer mover, e em seguida, você se muda para o centro de Recursos Mover, para verificar dependências e mover os recursos para a região alvo. | [Mover recursos para outro grupo de recursos/subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md).<br/><br/> [Mover recursos para outra região de um grupo de recursos.](move-region-within-resource-group.md)
**Mover-se do centro de mudanças de recursos** | Mover recursos através das regiões. <br/><br/> Você pode mover-se para uma região alvo, ou para uma zona de disponibilidade específica, ou disponibilidade definida, dentro da região alvo. | [Mover recursos através das regiões no centro de Resource Mover.]()
**Mover VMs com recuperação do local** | Use para mover VMs Azure entre o governo e as nuvens públicas.<br/><br/> Utilize se quiser mover VMs entre zonas de disponibilidade na mesma região. |[Mover recursos entre o governo/nuvens públicas,](../site-recovery/region-move-cross-geos.md) [mover recursos para zonas de disponibilidade na mesma região.](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](about-move-process.md) sobre os componentes do Resource Mover e o processo de mudança.
