---
title: Perguntas mais frequentes sobre o backup de bancos de dados do SQL Server em VMs do Azure com o Azure Backup
description: Encontre respostas a perguntas comuns sobre o backup de bancos de dados do SQL Server em VMs do Azure com o Azure Backup.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: sogup
ms.openlocfilehash: 3b7649a029c6c44cd8a25ea553ff2091f816dd3c
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214831"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>FAQ sobre bases de dados do SQL Server em execução numa cópia de segurança de VM do Azure

Este artigo responde a perguntas comuns sobre cópias de bases de dados do SQL Server funcionando em máquinas virtuais do Azure (VMs) e que utilizam o [Azure Backup](backup-overview.md) serviço.

> [!NOTE]
> Esta funcionalidade está atualmente em pré-visualização pública.

## <a name="can-i-throttle-the-backup-speed"></a>Pode limitar a velocidade de cópia de segurança?

Sim. Pode limitar a taxa em que a política de cópia de segurança é executado para minimizar o impacto numa instância do SQL Server. Para alterar a definição:
1. Na instância do SQL Server, na *C:\Program c:\programas\azure carga de trabalho Backup\bin* pasta, criar o *ExtensionSettingsOverrides.json* ficheiro.
2. Na *ExtensionSettingsOverrides.json* de ficheiros, alterar a **DefaultBackupTasksThreshold** na definição de um valor inferior (por exemplo, 5). <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Guardar as alterações e feche o ficheiro.
4. Na instância do SQL Server, abra **Gerenciador de tarefas**. Reinicie o **AzureWLBackupCoordinatorSvc** serviço.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Pode executar um backup completo de uma réplica secundária?
Não. Esta funcionalidade não é suportada.

## <a name="do-successful-backup-jobs-create-alerts"></a>Tarefas de cópia de segurança bem-sucedida criar alertas?

Não. Tarefas de cópia de segurança com êxito não geram alertas. Os alertas são enviados apenas para tarefas de cópia de segurança que não obedeçam.

## <a name="can-i-see-scheduled-backup-jobs-in-the-jobs-menu"></a>Pode ver tarefas de cópia de segurança agendadas no menu tarefas?

Não. O **tarefas de cópia de segurança** menu mostra detalhes da tarefa a pedido, mas não agendadas tarefas de cópia de segurança. Os alertas de tarefa falhada, se as tarefas de cópia de segurança agendadas falharem, encontrará detalhes. Para monitorizar todas as tarefas de cópia de segurança agendadas e não programadas, utilize [SQL Server Management Studio](manage-monitor-sql-database-backup.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Bases de dados futuros são adicionados automaticamente para cópia de segurança?

Não. Quando configurar a proteção para uma instância do SQL Server, todas as bases de dados são adicionados ao selecionar a opção de nível de servidor. Depois de configurar a proteção, tem de adicionar manualmente novas bases de dados para protegê-los. Novas bases de dados não estão automaticamente protegidas.

##  <a name="how-do-i-restart-protection-after-i-change-recovery-type"></a>Como reiniciar proteção depois de alterar o tipo de recuperação?

Acione uma cópia de segurança completa. Backups de log começarem conforme esperado.

## <a name="can-i-protect-availability-groups-on-premises"></a>Pode proteger disponibilidade grupos no local?

Não. O Azure Backup protege bases de dados do SQL Server em execução no Azure. Se um grupo de disponibilidade (AG) é distribuído por entre as máquinas do Azure e no local, o AG pode ser protegido apenas se a réplica primária está em execução no Azure. Além disso, o Azure Backup protege apenas os nós que são executados na mesma região do Azure que o Cofre dos serviços de recuperação.

## <a name="can-i-protect-availability-groups-across-regions"></a>Pode proteger grupos de disponibilidade em regiões?

O Cofre dos serviços de recuperação de cópia de segurança do Azure pode detetar e proteger todos os nós que estão na mesma região que o cofre. Se o seu grupo de disponibilidade Always On do SQL Server se estende por várias regiões do Azure, configure a cópia de segurança da região que tem o nó principal. Cópia de segurança do Azure pode detetar e proteger todas as bases de dados no grupo de disponibilidade, de acordo com sua preferência de cópia de segurança. Quando a sua preferência de cópia de segurança não for cumprida, cópias de segurança falham e receber o alerta de falha.

## <a name="can-i-exclude-databases-with-autoprotection-enabled"></a>Pode excluir bases de dados com autoprotection ativada?

Não. Autoprotection [aplica-se a toda a instância](backup-azure-sql-database.md#enable-auto-protection). Não é possível utilizar autoprotection para proteger as bases de dados numa instância de forma seletiva.

## <a name="can-i-have-different-policies-in-an-autoprotected-instance"></a>Pode ter políticas diferentes numa instância de autoprotected?

Se a sua instância já inclui algumas bases de dados protegidos, irá continuar a proteger sob seus respectivas diretivas, mesmo após [ativar o autoprotection](backup-azure-sql-database.md#enable-auto-protection). No entanto, todas as bases de dados não protegidos e adicionar posteriormente os bancos de dados terão apenas uma única política. Defina esta política sob **configurar a cópia de segurança** depois de selecionar as bases de dados. Na verdade, ao contrário de outras bases de dados protegidos, é possível até mesmo alterar a política para uma base de dados que está numa instância de autoprotected.
A única forma de alterar a política da base de dados é desativar temporariamente autoprotection para a instância. Em seguida, reativar o autoprotection para a instância.

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-will-backups-stop"></a>Se eu eliminar uma base de dados de uma instância de autoprotected, irão parar de cópias de segurança?

Não. Se uma base de dados é removida de uma instância de autoprotected, as cópias de segurança da base de dados ainda são tentadas. Isso implica que a base de dados eliminada começa a aparecer como mau estado de funcionamento sob **itens de cópia de segurança** e ainda está protegido.

A única forma de parar de proteger esta base de dados é temporariamente [desativar autoprotection](backup-azure-sql-database.md#enable-auto-protection) na instância. Em seguida, em **itens de cópia de segurança** para a base de dados, selecione **parar cópia de segurança**. Em seguida, reativar o autoprotection para esta instância.

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Por que motivo não vejo uma base de dados foi adicionado para uma instância de autoprotected?

A base de dados que [adicionar a uma instância de autoprotected](backup-azure-sql-database.md#enable-auto-protection) não pode aparecer imediatamente em itens protegidos. Isto acontece porque a deteção, normalmente, é executada a cada 8 horas. No entanto, pode detetar e proteger imediatamente novas bases de dados, se executar manualmente uma deteção, selecionando **bds recuperar**, conforme mostrado na imagem seguinte.

  ![Detetar manualmente uma base de dados adicionado recentemente](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Passos Seguintes

Saiba como [cópia de segurança de uma base de dados do SQL Server](backup-azure-sql-database.md) que está em execução numa VM do Azure.
