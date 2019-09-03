---
title: Suporte para o uso de Azure Site Recovery com o backup do Azure
description: Fornece uma visão geral de como o Azure Site Recovery e o backup do Azure podem ser usados juntos.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: 2078aa33ca735b91b5923d94028a286427f107a6
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146862"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Suporte para o uso de Site Recovery com o backup do Azure

Este artigo resume o suporte para usar o [serviço de site Recovery](site-recovery-overview.md) junto com o [serviço de backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview).

**ação** | **Suporte a Site Recovery** | **Detalhes**
--- | --- | ---
**Implantar serviços juntos** | Suportadas | Os serviços são interoperáveis e podem ser configurados juntos.
**Backup/restauração de arquivo** | Suportadas | Quando o backup e a replicação estão habilitados para uma VM e os backups são feitos, não há nenhum problema na restauração de arquivos nas VMs do lado da origem ou no grupo de VMs. A replicação continua normalmente sem alteração na integridade da replicação.
**Backup/restauração de disco** | Não há suporte atual | Se você restaurar um disco de backup, será necessário desabilitar e reabilitar novamente a replicação para a VM.
**Backup/restauração da VM** | Não há suporte atual | Se você fizer backup ou restaurar uma VM ou um grupo de VMs, será necessário desabilitar e reabilitar a replicação para a VM.  


