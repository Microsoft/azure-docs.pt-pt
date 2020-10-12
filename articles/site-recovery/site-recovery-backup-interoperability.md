---
title: Suporte para utilização da recuperação do site Azure com backup Azure
description: Fornece uma visão geral de como a Recuperação do Site Azure e a Cópia de Segurança Azure podem ser usadas em conjunto.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: c334eee34eb878135d3d81ab15d03618c6604846
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86135186"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Suporte para utilização da Recuperação do Site com Backup Azure

Este artigo resume o suporte à utilização do [serviço de Recuperação](site-recovery-overview.md) do Local juntamente com o [serviço de backup Azure](../backup/backup-overview.md).

**Ação** | **Suporte do Site Recovery** | **Detalhes**
--- | --- | ---
**Implementar serviços em conjunto** | Suportado | Os serviços são interoperáveis e podem ser configurados em conjunto.
**Backup/restauro de ficheiros** | Suportado | Quando a cópia de segurança e a replicação são ativadas para um VM e as cópias de segurança são tomadas, não há problema em restaurar ficheiros nos VMs do lado da fonte, ou grupo de VMs. A replicação continua como de costume, sem alterações na saúde da replicação.
**Restauro do disco** | Sem suporte atual | Se restaurar um disco de arranque, tem de desativar e voltar a ativar a replicação para o VM.
**Restauro da VM** | Sem suporte atual | Se restaurar um VM ou um grupo de VMs, tem de desativar e reativar a replicação para o VM.  


