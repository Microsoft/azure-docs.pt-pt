---
title: Matriz de compatibilidade do sistema operativo para SAP HANA (Grandes Instâncias); Microsoft Docs
description: A matriz de compatibilidade representa a compatibilidade de diferentes versões do Sistema Operativo com diferentes tipos de hardware (Grandes Instâncias)
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/03/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa19433ef6446932da3509694ccccd08538b964f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675642"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>Sistemas operativos compatíveis para grandes instâncias HANA

## <a name="hana-large-instance-type-i"></a>HANA Grande Instância Tipo I     
  | Sistema Operativo | Disponibilidade        | SKU                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | Não oferecido mais | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP3      | Disponível           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | Disponível           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  
### <a name="persistent-memory-skus"></a>SKUs de memória persistente
  | Sistema Operativo | Disponibilidade | SKU                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | Disponível    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>HANA Grande Instância Tipo II     
  |  Sistema Operativo       | Disponibilidade        | SKU                                                              |
  |-------------------------|---------------------|-------------------------------------------------------------------|
  | SLES 12 SP2             | Não oferecido mais | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP3             | Disponível           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  
## <a name="related-documents"></a>Documentos Relacionados

- Para saber mais sobre [SKUs disponíveis](hana-available-skus.md)
- Saber sobre [a modernização do Sistema Operativo](os-upgrade-hana-large-instance.md)
  

  
  
