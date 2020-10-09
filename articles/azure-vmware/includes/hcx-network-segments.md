---
title: Segmentos de rede VMware HCX
description: São quatro redes necessárias para o VMware HCX.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: f01aba13d688dfed573acfc8c4344511fbfd9fa8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91583400"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Existem quatro redes necessárias para vMware HCX:

- **Rede de Gestão:** Tipicamente, é a mesma rede de gestão usada no cluster vSphere.  No mínimo, identifique dois IPs neste segmento de rede para VMware HCX (podem ser necessários números maiores dependendo da sua implementação).

- **vMotion Network:** Tipicamente, é a mesma rede usada para vMotion no cluster vSphere.  No mínimo, identifique dois IPs neste segmento de rede para VMware HCX (podem ser necessários números maiores dependendo da sua implementação).  

   A rede vMotion deve ser exposta num interruptor virtual distribuído ou vSwitch0. Se não for, modifique o ambiente.

   > [!NOTE]
   > Se esta rede não for encaminhada (privada), tudo bem.

- **Rede uplink:** Pretende criar uma nova rede para vMware HCX Uplink e alargá-la ao seu cluster vSphere através de um grupo portuário.  No mínimo, identifique dois IPs neste segmento de rede para VMware HCX (podem ser necessários números maiores dependendo da sua implementação).  

   > [!NOTE]
   > O método recomendado é criar uma rede /29, mas qualquer tamanho de rede serve.

- **Rede de replicação:** Pretende criar uma nova rede para a replicação VMware HCX e estender essa rede ao seu cluster vSphere através de um grupo portuário.  No mínimo, identifique dois IPs neste segmento de rede para VMware HCX (podem ser necessários números maiores dependendo da sua implementação).

   > [!NOTE]
   > O método recomendado é criar uma rede /29, mas qualquer tamanho de rede serve.