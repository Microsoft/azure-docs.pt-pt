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
ms.openlocfilehash: 8af5cd4f961a2138f09bb3e56fccf7aa8f726dd9
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653616"
---
# <a name="choose-a-tool-for-moving-azure-resources"></a>Escolha uma ferramenta para mover recursos Azure

Pode mover recursos dentro de Azure da seguinte forma:

- **Mover recursos através de regiões**: Mover recursos de dentro do centro de Recursos Mover, ou dentro de um grupo de recursos. 
- **Mover recursos através de grupos de recursos/subscrições**: Mover-se de dentro de um grupo de recursos. 
- **Mover recursos entre nuvens de Azure**: Use o serviço de recuperação do local de Azure para mover recursos entre nuvens públicas e governamentais.
- **Mover recursos entre zonas de disponibilidade na mesma região**: Utilize o serviço de Recuperação do Sítio Azure para mover recursos entre zonas de disponibilidade na mesma região do Azure.


## <a name="compare-move-tools"></a>Compare ferramentas de movimento

**Ferramenta** | **Quando utilizar**
--- | --- 
**Mover-se dentro do grupo de recursos** | Mover recursos para um grupo de recursos/subscrição diferente, ou em todas as regiões.<br/><br/> Se você se deslocar através de regiões, no grupo de recursos você seleciona os recursos que você quer mover, e em seguida, você se muda para o centro de Recursos Mover, para verificar dependências e mover os recursos para a região alvo.
**Mover-se do centro de mudanças de recursos** | Mover recursos através das regiões. <br/><br/> Você pode mover-se para uma região alvo, ou para uma zona de disponibilidade específica, ou disponibilidade definida, dentro da região alvo.
**Mover VMs com recuperação do local** | Use para mover VMs Azure entre o governo e nuvens privadas.<br/><br/> Utilize se quiser mover VMs entre zonas de disponibilidade na mesma região.

## <a name="next-steps"></a>Próximos passos

[Saiba mais](about-move-process.md) sobre os componentes do Resource Mover e o processo de mudança.
