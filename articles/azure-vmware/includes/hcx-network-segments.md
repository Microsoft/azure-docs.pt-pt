---
title: Segmentos de rede VMware HCX
description: São quatro redes necessárias para o VMware HCX.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: e9b37c125db82a95c137ede8d642888fba8b6c80
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622358"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Existem diferentes formas de configurar segmentos de rede VMware HCX no local. O seguinte descreve uma configuração simples que suporta um caso piloto ou pequeno de utilização de produção.  Ao projetar para centenas ou milhares de cargas de trabalho, esta configuração pode ter de ser alterada, dependendo das necessidades da migração.  

Na preparação para a implantação do VMware HCX para apoiar o caso piloto ou de utilização de pequena produção, identifique o seguinte:

- **Rede de gestão:** Ao implementar vMware HCX no local, terá de definir uma rede de gestão.  Tipicamente, é a mesma rede de gestão usada pelo seu cluster VMware no local.  No mínimo, identifique **dois** IPs neste segmento de rede para VMware HCX. Pode precisar de números maiores, dependendo da escala da sua implantação para além do piloto ou da pequena caixa de utilização.

> [!NOTE]
   > O método recomendado é criar uma rede /26 porque pode utilizar até 10 malhas de serviço e 60 extensores de rede (-1 por malha de serviço). Pode esticar **oito** redes por extensão de rede utilizando nuvens privadas Azure VMware Solution.
   >
   
- **vMotion rede:** Ao implementar vMware HCX no local, terá de definir uma rede vMotion.  Tipicamente, é a mesma rede usada para vMotion pelo seu cluster VMware no local.  No mínimo, identifique **dois** IPs neste segmento de rede para VMware HCX. Pode precisar de números maiores, dependendo da escala da sua implantação para além do piloto ou da pequena caixa de utilização.

   A rede vMotion deve ser exposta num interruptor virtual distribuído ou vSwitch0. Se não for, modifique o ambiente para acomodar.

   > [!NOTE]
   > Muitos ambientes VMware utilizam segmentos de rede não encaminhados para vMotion, o que não levanta problemas.

- **Rede de uplink:** Ao implementar vMware HCX no local, terá de definir uma rede Uplink. Utilize a rede de gestão definida como a sua rede de uplink.
   
- **Rede de replicação:** Ao implementar vMware HCX no local, terá de definir uma rede de replicação. Utilize a rede de gestão definida como a sua rede de replicação.  Se os anfitriões do cluster no local utilizarem uma rede VMkernel de replicação dedicada, reserve **dois** endereços IP neste segmento de rede e utilize a rede VMkernel de Replicação para a rede de replicação.
