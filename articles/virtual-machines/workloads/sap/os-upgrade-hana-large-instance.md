---
title: Atualização do sistema operacional para o SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Executar atualização do sistema operacional para SAP HANA no Azure (instâncias grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6341f58791c2fad71a65650e32cff02fb52d78c0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098686"
---
# <a name="operating-system-upgrade"></a>Atualização do sistema operacional
Este documento descreve os detalhes sobre atualizações do sistema operacional nas instâncias grandes do HANA.

>[!NOTE]
>A atualização do sistema operacional é responsabilidade dos clientes, o suporte às operações da Microsoft pode orientá-lo nas principais áreas a serem observadas durante a atualização. Você deve consultar o fornecedor do sistema operacional também antes de planejar uma atualização.

No momento, do provisionamento da unidade HLI, a equipe de operações da Microsoft instala o sistema operacional. Ao longo do tempo, é necessário manter o sistema operacional (exemplo: Aplicação de patch, ajuste, atualização, etc.) na unidade HLI.

Antes de fazer alterações importantes no sistema operacional (por exemplo, atualizar o SP1 para o SP2), você precisa entrar em contato com a equipe de operações da Microsoft abrindo um tíquete de suporte para consultar.

Incluir no seu tíquete:

* Sua ID de assinatura do HLI.
* O nome do servidor.
* O nível de patch que você está planejando aplicar.
* A data em que você está planejando essa alteração. 

Recomendamos que você abra esse tíquete pelo menos uma semana antes da data de atualização desejável devido à existência de uma equipe de operações verificando se uma atualização de firmware será necessária na folha do servidor.


Para obter a matriz de suporte das diferentes versões de SAP HANA com as diferentes versões do Linux, consulte [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Problemas conhecidos

A seguir estão alguns problemas conhecidos comuns durante a atualização:
- Na SKU da classe do tipo SKU II, o Software Foundation do software (SFS) é removido após a atualização do sistema operacional. Você precisa reinstalar o SFS compatível após a atualização do sistema operacional.
- Drivers de placa Ethernet (ENIC e FNIC) revertidos para a versão mais antiga. Você precisa reinstalar a versão compatível dos drivers após a atualização.

## <a name="next-steps"></a>Passos seguintes
- Consulte [backup e restauração](hana-overview-high-availability-disaster-recovery.md) para a classe SKU do tipo de backup do so.
- Consulte [backup do sistema operacional para SKUs do tipo II de carimbos de revisão 3](os-backup-type-ii-skus.md) para a classe SKU do tipo II.
