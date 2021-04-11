---
title: Segmentos de rede VMware HCX
description: São quatro redes necessárias para o VMware HCX.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: f2f5597471355bf4d74be7fbe370e6fa58770bf1
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251435"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Existem diferentes formas de configurar segmentos de rede VMware HCX no local. O seguinte descreve uma configuração simples que suporta um caso piloto ou pequeno de utilização de produção.  Ao projetar para centenas ou milhares de cargas de trabalho, esta configuração pode ter de ser alterada, dependendo das necessidades da migração.  

Na preparação para a implantação do VMware HCX para apoiar o caso piloto ou de utilização de pequena produção, identifique o seguinte:

- **Rede de gestão:** Ao implementar vMware HCX no local, terá de identificar uma rede de gestão para vMware HCX.  Tipicamente, é a mesma rede de gestão usada pelo seu cluster VMware no local.  No mínimo, identifique **dois** IPs neste segmento de rede para VMware HCX. Pode precisar de números maiores, dependendo da escala da sua implantação para além do piloto ou da pequena caixa de utilização.

  > [!NOTE]
  > Preparando-se para grandes ambientes, em vez de utilizar a rede de gestão utilizada para o cluster VMware no local, crie uma nova rede /26 e apresente essa rede como um grupo portuário para o seu cluster VMware no local.  Em seguida, pode criar até 10 malhas de serviço e 60 extensores de rede (-1 por malha de serviço). Pode esticar **oito** redes por extensão de rede utilizando nuvens privadas Azure VMware Solution.
  >

- **Rede de uplink:** Ao implementar vMware HCX no local, terá de identificar uma rede Uplink para VMware HCX. Utilize a mesma rede que utilizará para a rede Gestão. 

- **vMotion rede:** Ao implementar vMware HCX no local, terá de identificar uma rede vMotion para VMware HCX.  Tipicamente, é a mesma rede usada para vMotion pelo seu cluster VMware no local.  No mínimo, identifique **dois** IPs neste segmento de rede para VMware HCX. Pode precisar de números maiores, dependendo da escala da sua implantação para além do piloto ou da pequena caixa de utilização.

   A rede vMotion deve ser exposta num interruptor virtual distribuído ou vSwitch0. Se não for, modifique o ambiente para acomodar.

   > [!NOTE]
   > Muitos ambientes VMware utilizam segmentos de rede não encaminhados para vMotion, o que não levanta problemas.
  
- **Rede de replicação:** Ao implementar vMware HCX no local, terá de definir uma rede de replicação. Utilize a mesma rede que está a utilizar para as suas redes De Gestão e Uplink.  Se os anfitriões do cluster no local utilizarem uma rede VMkernel de replicação dedicada, reserve **dois** endereços IP neste segmento de rede e utilize a rede VMkernel de Replicação para a rede de replicação.
