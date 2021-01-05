---
title: Versões de software VMware
description: Versões de software VMware suportadas para Azure VMware Solution.
ms.topic: include
ms.date: 12/29/2020
ms.openlocfilehash: c6ba2904bab6c6f44001cafed1bd4cbdeb786373
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825095"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


As versões de software atuais do software VMware utilizado nos clusters de nuvem privada Azure VMware Solution são:

| Software              |    Versão   |
| :---                  |     :---:    |
| VCSA / vSphere / ESXi |    6.7 U3    | 
| ESXi                  |    6.7 U3    | 
| vSAN                  |    6.7 U3    |
| NSX-T                 |      2.5     |


>[!NOTE]
>NSX-T é a única versão suportada do NSX.

Para qualquer novo cluster numa nuvem privada, a versão de software corresponde ao que está atualmente em execução. Para qualquer nova nuvem privada numa subscrição, a versão mais recente da stack de software é instalada. Para obter mais informações, consulte os requisitos da [versão do software VMware](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

As atualizações do pacote de software de nuvem privada mantêm o software dentro de uma versão da versão mais recente do pacote de software lançado a partir de VMware. As versões privadas de software em nuvem podem diferir das versões mais recentes dos componentes de software individuais (ESXi, NSX-T, vCenter, vSAN). Pode encontrar as políticas e processos gerais de upgrade para o software da plataforma Azure VMware Solution descrito em [atualizações e atualizações de nuvem privada .](../concepts-upgrades.md)

