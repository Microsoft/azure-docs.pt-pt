---
title: Segmentos de rede VMware HCX
description: São quatro redes necessárias para o VMware HCX.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 1f0fcc1ddeeb26702a297035e6ae2a2f73dd71d6
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462343"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

São necessárias quatro redes para o VMware HCX:

- **Rede de gestão:** Tipicamente, é a mesma rede de gestão usada pelo cluster Azure VMware Solution. No mínimo, identifique **dois** IPs neste segmento de rede para VMware HCX. Pode precisar de números maiores, dependendo da sua implantação.

   > [!NOTE]
   > O método recomendado é criar uma rede /26 porque pode utilizar até 10 malhas de serviço e 60 extensores de rede (-1 por malha de serviço). Pode esticar **oito** redes por extensão de rede utilizando nuvens privadas Azure VMware Solution.
   >
   
- **vMotion rede:** Tipicamente, é a mesma rede usada para vMotion no cluster Azure VMware Solution.  No mínimo, identifique **dois** IPs neste segmento de rede para VMware HCX. Pode precisar de números maiores, dependendo da sua implantação.  

   A rede vMotion deve ser exposta num interruptor virtual distribuído ou vSwitch0. Se não for, modifique o ambiente.

   > [!NOTE]
   > Esta rede pode ser privada (não encaminhada).

- **Rede de uplink:** Pretende criar uma nova rede para vMware HCX Uplink e alargá-la ao seu cluster vSphere através de um grupo portuário. No mínimo, identifique **dois** IPs neste segmento de rede para VMware HCX. Pode precisar de números maiores, dependendo da sua implantação.  

   > [!NOTE]
   > O método recomendado é criar uma rede /26 porque pode utilizar até 10 malhas de serviço e 60 extensores de rede (-1 por malha de serviço). Pode esticar **oito** redes por extensão de rede utilizando nuvens privadas Azure VMware Solution.
   >
   
- **Rede de replicação:** Isto é opcional. Pretende criar uma nova rede para a replicação VMware HCX e estender essa rede ao seu cluster vSphere através de um grupo portuário. No mínimo, identifique **dois** IPs neste segmento de rede para VMware HCX. Pode precisar de números maiores, dependendo da sua implantação.

   > [!NOTE]
   > Esta configuração só é possível quando os anfitriões do cluster no local usam uma rede VMkernel de replicação dedicada.  Se o seu cluster no local não tiver uma rede VMkernel de replicação dedicada definida, não há necessidade de criar esta rede.
