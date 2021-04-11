---
title: Versões de software VMware
description: Versões de software VMware suportadas para Azure VMware Solution.
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: a6441b55bbc6a8f694c50bbf022a6a2ae52d60bf
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097392"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


As versões de software VMware utilizadas em novas implementações de aglomerados de nuvens privadas Azure VMware Solution são:

| Software              |    Versão   |
| :---                  |     :---:    |
| VCSA / vSphere / ESXi |    6.7 U3l    | 
| ESXi                  |    6.7 U3l    | 
| vSAN                  |    6.7 U3l    |
| NSX-T <br />**NOTA:** NSX-T é a única versão suportada do NSX.               |      3.1.1     |


Novos clusters adicionados a uma nuvem privada existente, a versão de software atualmente em execução é aplicada. Para obter mais informações, consulte os requisitos da [versão do software VMware](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

As atualizações do pacote de software de nuvem privada mantêm o software dentro de uma versão da versão mais recente do pacote de software lançado a partir de VMware. As versões privadas de software em nuvem podem diferir das versões mais recentes dos componentes de software individuais (ESXi, NSX-T, vCenter, vSAN). Pode encontrar as políticas e processos gerais de upgrade para o software da plataforma Azure VMware Solution descrito em [atualizações e atualizações de nuvem privada .](../concepts-upgrades.md)

