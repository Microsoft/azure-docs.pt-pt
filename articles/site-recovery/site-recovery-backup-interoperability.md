---
title: Suporte para utilização de recuperação de site azure com backup Azure
description: Fornece uma visão geral de como a Recuperação do Site Azure e a Backup Azure podem ser usadas em conjunto.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: d8c896bc655339fbd7d51f738fb629450e8c12ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72376223"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Suporte para utilização de recuperação do site com backup Azure

Este artigo resume o suporte à [utilização](site-recovery-overview.md) do serviço de recuperação do site juntamente com o [serviço de backup Azure](https://docs.microsoft.com/azure/backup/backup-overview).

**Ação** | **Suporte do Site Recovery** | **Detalhes**
--- | --- | ---
**Implementar serviços em conjunto** | Suportado | Os serviços são interoperáveis e podem ser configurados em conjunto.
**Cópia de segurança/restauro de ficheiros** | Suportado | Quando a cópia de segurança e a replicação são ativadas para um VM e as cópias de segurança são tomadas, não há qualquer problema em restaurar ficheiros nos VMs do lado da fonte, ou grupo de VMs. A replicação continua como de costume, sem alterações na saúde da replicação.
**Restauro do disco** | Nenhum suporte atual | Se restaurar um disco apoiado, terá de desativar e reativar novamente a replicação para o VM.
**Restauro da VM** | Nenhum suporte atual | Se restaurar um VM ou um grupo de VMs, precisa de desativar e reativar a replicação para o VM.  


