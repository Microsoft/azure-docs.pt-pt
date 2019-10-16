---
title: Suporte para o uso de Azure Site Recovery com o backup do Azure
description: Fornece uma visão geral de como o Azure Site Recovery e o backup do Azure podem ser usados juntos.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: d8c896bc655339fbd7d51f738fb629450e8c12ae
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376223"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Suporte para o uso de Site Recovery com o backup do Azure

Este artigo resume o suporte para usar o [serviço de site Recovery](site-recovery-overview.md) junto com o [serviço de backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview).

**Ação** | **Suporte a Site Recovery** | **Detalhes**
--- | --- | ---
**Implantar serviços juntos** | Suportadas | Os serviços são interoperáveis e podem ser configurados juntos.
**Backup/restauração de arquivo** | Suportadas | Quando o backup e a replicação estão habilitados para uma VM e os backups são feitos, não há nenhum problema na restauração de arquivos nas VMs do lado da origem ou no grupo de VMs. A replicação continua normalmente sem alteração na integridade da replicação.
**Restauração de disco** | Não há suporte atual | Se você restaurar um disco de backup, você precisará desabilitar e reabilitar novamente a replicação para a VM.
**Restauro da VM** | Não há suporte atual | Se você restaurar uma VM ou um grupo de VMs, será necessário desabilitar e reabilitar a replicação para a VM.  


