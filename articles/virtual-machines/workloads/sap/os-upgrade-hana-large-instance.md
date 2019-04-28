---
title: Atualização do sistema operativo para o SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Efetuar a atualização do sistema operativo para o SAP HANA no Azure (instâncias grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7d451f3831309b4755170915b35a23da8910510
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935641"
---
# <a name="operating-system-upgrade"></a>Atualização do sistema operativo
Este documento descreve os detalhes sobre as atualizações de sistema operativo nas instâncias grandes do HANA.

>[!NOTE]
>A atualização de SO é da responsabilidade de clientes, o suporte de operações da Microsoft pode orientá-lo para as principais áreas a serem observadas durante a atualização. Deve procurar também o seu fornecedor de sistema operativo antes de planejar para uma atualização.

No momento da unidade HLI aprovisionamento, a equipe de operações do Microsoft instalar o sistema operativo. Ao longo do tempo, tem de manter o sistema operacional (exemplo: Aplicação de patches, otimização, atualizar etc.) na unidade HLI.

Principais alterações no sistema operativo (por exemplo, atualizar o SP1 ou SP2), tem de contactar a equipe do Microsoft Operations ao abrir um pedido de suporte consultar.

Inclua no seu pedido de suporte:

* O ID de subscrição de HLI.
* O nome do servidor.
* O nível de correção que pretende aplicar.
* A data em que estiver a planear esta alteração. 

Recomendamos que que abra este pedido de suporte, pelo menos, uma semana antes da data de atualização desejável devido a ter a equipe de operações a verificar se uma atualização de firmware será necessária no seu painel de servidor.


Para a matriz de suporte de diferentes versões do SAP HANA com diferentes versões do Linux, consulte [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Problemas conhecidos

Seguem-se alguns problemas conhecidos comuns durante a atualização:
- Na classe de tipo II do SKU SKU, o software de base de software (SFS) é removido depois de atualizar o sistema operacional. Tem de reinstalar o SFS compatíveis depois de atualizar o sistema operacional.
- Os controladores de smart de Ethernet (ENIC e FNIC) revertida para a versão mais antiga. Tem de reinstalar a versão compatível dos controladores após a atualização.

## <a name="next-steps"></a>Passos Seguintes
- Consultar [Backup e restauração](hana-overview-high-availability-disaster-recovery.md) de SO de classe de tipo I SKU de cópia de segurança.
- Consultar [cópia de segurança do SO para SKUs do tipo II](os-backup-type-ii-skus.md) para a classe de tipo II SKU.
