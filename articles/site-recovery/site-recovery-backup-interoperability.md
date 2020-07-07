---
title: Suporte para utilização da recuperação do site Azure com backup Azure
description: Fornece uma visão geral de como a Recuperação do Site Azure e a Cópia de Segurança Azure podem ser usadas em conjunto.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: d8c896bc655339fbd7d51f738fb629450e8c12ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "72376223"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Suporte para utilização da Recuperação do Site com Backup Azure

Este artigo resume o suporte à utilização do [serviço de Recuperação](site-recovery-overview.md) do Local juntamente com o [serviço de backup Azure](https://docs.microsoft.com/azure/backup/backup-overview).

**Ação** | **Suporte do Site Recovery** | **Detalhes**
--- | --- | ---
**Implementar serviços em conjunto** | Suportado | Os serviços são interoperáveis e podem ser configurados em conjunto.
**Backup/restauro de ficheiros** | Suportado | Quando a cópia de segurança e a replicação são ativadas para um VM e as cópias de segurança são tomadas, não há problema em restaurar ficheiros nos VMs do lado da fonte, ou grupo de VMs. A replicação continua como de costume, sem alterações na saúde da replicação.
**Restauro do disco** | Sem suporte atual | Se restaurar um disco de arranque, tem de desativar e voltar a ativar a replicação para o VM.
**Restauro da VM** | Sem suporte atual | Se restaurar um VM ou um grupo de VMs, tem de desativar e reativar a replicação para o VM.  


