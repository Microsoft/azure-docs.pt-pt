---
title: Versões de software VMware
description: Versões de software VMware suportadas para Azure VMware Solution.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 9a1781d163e67c2f9b6a4f756a616e59a969696f
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462524"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


As versões de software VMware utilizadas em novas implementações de aglomerados de nuvens privadas Azure VMware Solution são:

| Software              |    Versão   |
| :---                  |     :---:    |
| VCSA / vSphere / ESXi |    6.7 U3    | 
| ESXi                  |    6.7 U3    | 
| vSAN                  |    6.7 U3    |
| NSX-T <br />**NOTA:** NSX-T é a única versão suportada do NSX.               |      2.5     |


Novos clusters adicionados a uma nuvem privada existente, a versão de software atualmente em execução é aplicada. Para obter mais informações, consulte os requisitos da [versão do software VMware](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

As atualizações do pacote de software de nuvem privada mantêm o software dentro de uma versão da versão mais recente do pacote de software lançado a partir de VMware. As versões privadas de software em nuvem podem diferir das versões mais recentes dos componentes de software individuais (ESXi, NSX-T, vCenter, vSAN). Pode encontrar as políticas e processos gerais de upgrade para o software da plataforma Azure VMware Solution descrito em [atualizações e atualizações de nuvem privada .](../concepts-upgrades.md)

