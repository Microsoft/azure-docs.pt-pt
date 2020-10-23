---
title: Segmentos de rede VMware HCX
description: São quatro redes necessárias para o VMware HCX.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 8137b4383d2a243d53db317db6f5a78b3bc68e67
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173627"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

São necessárias quatro redes para o VMware HCX:

- **Rede de gestão:** Tipicamente, é a mesma rede de gestão usada no cluster vSphere. No mínimo, identifique dois IPs neste segmento de rede para VMware HCX. (Pode precisar de números maiores, dependendo da sua implantação.)

- **vMotion rede:** Tipicamente, é a mesma rede usada para vMotion no cluster vSphere.  No mínimo, identifique dois IPs neste segmento de rede para VMware HCX. (Pode precisar de números maiores, dependendo da sua implantação.)  

   A rede vMotion deve ser exposta num interruptor virtual distribuído ou vSwitch0. Se não for, modifique o ambiente.

   > [!NOTE]
   > Se esta rede não for encaminhada (privada), tudo bem.

- **Rede de uplink:** Pretende criar uma nova rede para vMware HCX Uplink e alargá-la ao seu cluster vSphere através de um grupo portuário. No mínimo, identifique dois IPs neste segmento de rede para VMware HCX. (Pode precisar de números maiores, dependendo da sua implantação.)  

   > [!NOTE]
   > O método recomendado é criar uma rede /29, mas qualquer tamanho de rede serve.

- **Rede de replicação:** Pretende criar uma nova rede para a replicação VMware HCX e estender essa rede ao seu cluster vSphere através de um grupo portuário. No mínimo, identifique dois IPs neste segmento de rede para VMware HCX. (Pode precisar de números maiores, dependendo da sua implantação.)

   > [!NOTE]
   > O método recomendado é criar uma rede /29, mas qualquer tamanho de rede serve.