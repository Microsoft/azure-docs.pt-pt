---
title: Segmentos de rede VMware HCX
description: São quatro redes necessárias para o VMware HCX.
ms.topic: include
ms.date: 11/23/2020
ms.openlocfilehash: 48894c532c97b70cde1473fb8b81f406ded70343
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999638"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

São necessárias quatro redes para o VMware HCX:

- **Rede de gestão:** Tipicamente, é a mesma rede de gestão usada no cluster vSphere. No mínimo, identifique dois IPs neste segmento de rede para VMware HCX. Pode precisar de números maiores, dependendo da sua implantação.

   > [!NOTE]
   > O método que recomendamos é a criação de uma rede /26. Numa rede /26, pode utilizar até 10 malhas de serviço e 60 extensores de rede (-1 por malha de serviço). Pode esticar oito redes por extensão de rede utilizando nuvens privadas Azure VMware Solution.
   >
   
- **vMotion rede:** Tipicamente, é a mesma rede usada para vMotion no cluster vSphere.  No mínimo, identifique dois IPs neste segmento de rede para VMware HCX. Pode precisar de números maiores, dependendo da sua implantação.  

   A rede vMotion deve ser exposta num interruptor virtual distribuído ou vSwitch0. Se não for, modifique o ambiente.

   > [!NOTE]
   > Esta rede pode ser privada (não encaminhada).

- **Rede de uplink:** Pretende criar uma nova rede para vMware HCX Uplink e alargá-la ao seu cluster vSphere através de um grupo portuário. No mínimo, identifique dois IPs neste segmento de rede para VMware HCX. Pode precisar de números maiores, dependendo da sua implantação.  

   > [!NOTE]
   > O método que recomendamos é a criação de uma rede /26. Numa rede /26, pode utilizar até 10 malhas de serviço e 60 extensores de rede (-1 por malha de serviço). Pode esticar oito redes por extensão de rede utilizando nuvens privadas Azure VMware Solution.
   >
   
- **Rede de replicação:** Isto é opcional. Pretende criar uma nova rede para a replicação VMware HCX e estender essa rede ao seu cluster vSphere através de um grupo portuário. No mínimo, identifique dois IPs neste segmento de rede para VMware HCX. Pode precisar de números maiores, dependendo da sua implantação.

   > [!NOTE]
   > Esta configuração só é possível quando os anfitriões do cluster no local usam uma rede VMkernel de replicação dedicada.  Se o seu cluster no local não tiver uma rede VMkernel de replicação dedicada definida, não há necessidade de criar esta rede.
