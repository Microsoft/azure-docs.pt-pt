---
title: Interoperabilidade de cópia de segurança do Azure Site Recovery - | Documentos da Microsoft
description: Fornece uma descrição geral de como Azure Site Recovery e o Azure Backup podem ser utilizado em conjunto.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/26/2019
ms.author: sideeksh
ms.openlocfilehash: 6658ab8c967c70ac1deaeba3d1dfeac602515591
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731877"
---
# <a name="about-site-recovery-and-backup-interoperability"></a>Sobre o Site Recovery e interoperabilidade de cópia de segurança

Este artigo fornece orientações para a recuperação de desastre a cópia de segurança de VM de IaaS do Azure e a VM do Azure a utilizar com êxito.

## <a name="azure-backup"></a>Azure Backup

O Azure Backup ajuda a proteger os dados para servidores no local, máquinas virtuais, cargas de trabalho virtualizadas, servidores do SQL Server, servidores do SharePoint e muito mais. O Azure Site Recovery orquestra e gere a recuperação após desastre para VMs do Azure, VMs no local e servidores físicos.

## <a name="azure-site-recovery"></a>Azure Site Recovery

É possível configurar a cópia de segurança do Azure e Azure Site Recovery numa VM ou um grupo de VMs. Ambos os produtos são interoperáveis. Seguem-se alguns cenários em que a interoperabilidade entre a cópia de segurança e recuperação de sites do Azure torna-se importantes:

### <a name="file-backuprestore"></a>Cópia de segurança/restauro de ficheiros

Se a cópia de segurança e de replicação estão ativadas e é feita uma cópia de segurança, não existe nenhum problema com o restauro de qualquer ficheiro (s) na VM de origem lado ou do grupo de VMs. Replicação continuará como de costume, sem qualquer alteração no estado de funcionamento de replicação.

### <a name="disk-backuprestore"></a>Cópia de segurança/restauro de disco

Se restaurar o disco a partir da cópia de segurança, em seguida, a proteção da máquina virtual tem de ser ativada novamente.

### <a name="vm-backuprestore"></a>Cópia de segurança/restauro de VMS

Não é suportada a cópia de segurança e restauro de uma VM ou um grupo de VMs. Para fazê-lo funcionar, proteção tem de ser ativada novamente.

**Cenário** | **Suportado pelo Azure Site Recovery?** | **Solução alternativa, se aplicável**  
--- | --- | ---
Cópia de segurança do ficheiro/pasta | Sim | Não Aplicável
Cópia de segurança do disco | Atualmente, não é | Desativar e ativar a proteção
Cópia de segurança VM | Não | Desativar e ativar a proteção
