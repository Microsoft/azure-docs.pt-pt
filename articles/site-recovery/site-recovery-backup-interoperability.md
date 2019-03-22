---
title: Suporte para utilizar o Azure Site Recovery com o Azure Backup | Documentos da Microsoft
description: Fornece uma descrição geral de como Azure Site Recovery e o Azure Backup podem ser utilizado em conjunto.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: e902f70225ec0eb0caa98f7e19a16c87220cb6f9
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312891"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Suporte para utilizar o Site Recovery com o Azure Backup

Este artigo resume o suporte para utilizar o [serviço Site Recovery](site-recovery-overview.md) em conjunto com o [serviço de cópia de segurança do Azure](https://docs.microsoft.com/azure/backup/backup-overview).

**Ação** | **Suporte de recuperação de site** | **Detalhes**
--- | --- | ---
**Implementar serviços em conjunto** | Suportadas | Os serviços são interoperáveis e podem ser configurados em conjunto.
**Cópia de segurança/restauro de ficheiros** | Suportadas | Quando a cópia de segurança e de replicação estão ativadas para uma VM e backups são feitos, não existe nenhum problema no restauro de ficheiros no lado da origem VMs ou o grupo de VMs. Replicação como de costume continua sem qualquer alteração no estado de funcionamento de replicação.
**Cópia de segurança/restauro de disco** | Não há suporte atual | Se restaurar um disco de cópia de segurança, terá de desativar e reativar a replicação novamente para a VM.
**Cópia de segurança/restauro de VMS** | Não há suporte atual | Se criar cópias de segurança ou restaurar uma VM ou um grupo de VMs, terá de desativar e reativar a replicação para a VM.  


