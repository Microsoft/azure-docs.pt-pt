---
title: Perguntas mais frequentes sobre o backup de bancos de dados do SQL Server em VMs do Azure com o Azure Backup
description: Encontre respostas a perguntas comuns sobre o backup de bancos de dados do SQL Server em VMs do Azure com o Azure Backup.
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: sachdevaswati
ms.openlocfilehash: f9020cd6d35c59e9293e6e8a96b09319f9f8bb00
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225688"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>FAQ sobre bases de dados do SQL Server em execução numa cópia de segurança de VM do Azure

Este artigo responde a perguntas comuns sobre cópias de bases de dados do SQL Server funcionando em máquinas virtuais do Azure (VMs) e que utilizam o [Azure Backup](backup-overview.md) serviço.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>A solução Repita ou autorrecuperação as cópias de segurança?

Em algumas circunstâncias, o serviço de cópia de segurança do Azure aciona cópias de segurança para correções. Autorrecuperação pode acontecer para qualquer uma das seis condições mencionadas a seguir:

  - Se o registo ou cópia de segurança diferencial falhar devido a erro de validação de LSN, o próximo registo ou cópia de segurança diferencial em vez disso, é convertida numa cópia de segurança completa.
  - Se não ocorreu nenhuma cópia de segurança completa antes de um registo ou cópia de segurança diferencial, esse registo ou cópia de segurança diferencial em vez disso, é convertida para uma cópia de segurança completa.
  - Se ponto anterior no tempo do mais recente cópia de segurança completa com mais de 15 dias, o próximo registo ou cópia de segurança diferencial em vez disso, é convertida para uma cópia de segurança completa.
  - Todas as tarefas de cópia de segurança que ser canceladas devido a uma atualização da extensão voltar são acionadas após a conclusão da atualização e a extensão é iniciada.
  - Se optar por substituir a base de dados durante o restauro, a próxima cópia de segurança de registo/diferencial falha e um backup completo é acionado em vez disso.
  - Em casos em que um backup completo é necessário para repor as cadeias de registo devido a alteração no modelo de recuperação de base de dados, um completo é ativado automaticamente na agenda seguinte.

Auto-tratá-lo como um recurso está ativado para todos os utilizadores por padrão. No entanto no caso de optar por sair do mesmo, em seguida, execute o abaixo:

  * Na instância do SQL Server, na *C:\Program c:\programas\azure carga de trabalho Backup\bin* pasta, criar ou editar a **ExtensionSettingsOverrides.json** ficheiro.
  * Na **ExtensionSettingsOverrides.json**, defina *{"EnableAutoHealer": false}*.
  * Guardar as alterações e feche o ficheiro.
  * Na instância do SQL Server, abra **gerir tarefas** e, em seguida, reinicie o **AzureWLBackupCoordinatorSvc** serviço.  

## <a name="can-i-control-as-to-how-many-concurrent-backups-run-on-the-sql-server"></a>Pode controlar como o número de cópias de segurança simultâneas executam no SQL server?

Sim. Pode limitar a taxa em que a política de cópia de segurança é executado para minimizar o impacto numa instância do SQL Server. Para alterar a definição:
1. Na instância do SQL Server, na *C:\Program c:\programas\azure carga de trabalho Backup\bin* pasta, criar o *ExtensionSettingsOverrides.json* ficheiro.
2. Na *ExtensionSettingsOverrides.json* de ficheiros, alterar a **DefaultBackupTasksThreshold** na definição de um valor inferior (por exemplo, 5). <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Guardar as alterações e feche o ficheiro.
4. Na instância do SQL Server, abra **Gerenciador de tarefas**. Reinicie o **AzureWLBackupCoordinatorSvc** serviço.

> [!NOTE]
> A experiência do Usuário ainda pode ir em frente e agendar cópias de segurança tantos em qualquer momento, no entanto, será processado numa janela deslizante de, digamos, 5, de acordo com o exemplo acima.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Pode executar um backup completo de uma réplica secundária?
De acordo com limitações de SQL, pode executar a cópia de segurança cópia apenas completa na réplica secundária; No entanto, não é permitida a cópia de segurança completa.

## <a name="can-i-protect-availability-groups-on-premises"></a>Pode proteger disponibilidade grupos no local?
Não. O Azure Backup protege bases de dados do SQL Server em execução no Azure. Se um grupo de disponibilidade (AG) é distribuído por entre as máquinas do Azure e no local, o AG pode ser protegido apenas se a réplica primária está em execução no Azure. Além disso, o Azure Backup protege apenas os nós que são executados na mesma região do Azure que o Cofre dos serviços de recuperação.

## <a name="can-i-protect-availability-groups-across-regions"></a>Pode proteger grupos de disponibilidade em regiões?
O Cofre dos serviços de recuperação de cópia de segurança do Azure pode detetar e proteger todos os nós que estão na mesma região que o cofre. Se o seu grupo de disponibilidade Always On do SQL Server se estende por várias regiões do Azure, configure a cópia de segurança da região que tem o nó principal. Cópia de segurança do Azure pode detetar e proteger todas as bases de dados no grupo de disponibilidade, de acordo com sua preferência de cópia de segurança. Quando a sua preferência de cópia de segurança não for cumprida, cópias de segurança falham e receber o alerta de falha.

## <a name="do-successful-backup-jobs-create-alerts"></a>Tarefas de cópia de segurança bem-sucedida criar alertas?
Não. Tarefas de cópia de segurança com êxito não geram alertas. Os alertas são enviados apenas para tarefas de cópia de segurança que não obedeçam. Comportamento detalhado para alertas portais está documentado [aqui](backup-azure-monitoring-built-in-monitor.md). No entanto, caso esteja interessado tem alertas mesmo para tarefas concluídas com êxito, pode usar [monitorização com o Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Pode ver tarefas de cópia de segurança agendadas no menu tarefas de cópia de segurança?
O **tarefa de cópia de segurança** menu mostrará apenas ad-hoc tarefas de cópia de segurança. Para a tarefa agendada utilize [monitorização com o Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Bases de dados futuros são adicionados automaticamente para cópia de segurança?
Sim, pode obter esta capacidade com [proteção automática](backup-azure-sql-database.md#enable-auto-protection).  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Se eliminar uma base de dados de uma instância de autoprotected, o que acontecerá com as cópias de segurança?
Se uma base de dados é removida de uma instância de autoprotected, as cópias de segurança da base de dados ainda são tentadas. Isso implica que a base de dados eliminada começa a aparecer como mau estado de funcionamento sob **itens de cópia de segurança** e ainda está protegido.

A forma correta para parar de proteger esta base de dados é fazer **parar cópia de segurança** com **eliminar dados** nesta base de dados.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Se parar a operação de cópia de segurança de uma base de dados autoprotected qual será seu comportamento?
Se o fizer **parar cópia de segurança com reter dados**, não existem cópias de segurança futuras terá lugar e os pontos de recuperação existentes permanecerão intactos. A base de dados ainda será considerado como protegido e ser apresentado no **itens de cópia de segurança**.

Se o fizer **parar cópia de segurança com eliminação de dados**, não existem cópias de segurança futuras terá lugar e os pontos de recuperação existentes também serão eliminados. A base de dados será considerado não protegido e mostrado sob a instância na cópia de segurança de configurar. No entanto, ao contrário de outros protegido por cima bases de dados que podem ser selecionados manualmente ou que pode obter autoprotected, esta base de dados aparece a cinzento e não pode ser selecionada. A única forma de voltar a proteger esta base de dados é desativar a proteção automática na instância. Pode agora selecionar esta base de dados e configurar a proteção no mesmo ou volte a ativar a proteção automática na instância.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Alterar o nome da base de dados depois que ele tenha sido protegido, o qual será o comportamento?
Uma base de dados novamente com o nome é tratado como uma nova base de dados. Por conseguinte, o serviço irão tratar nesta situação, como se a base de dados não foram encontrados e com falha as cópias de segurança.

Pode selecionar a base de dados, que agora é mudado e configurar a proteção no mesmo. No caso da proteção automática está ativada na instância, a base de dados de nome mudado irá ser detetado e protegido automaticamente.

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Por que motivo não vejo uma base de dados foi adicionado para uma instância de autoprotected?
A base de dados que [adicionar a uma instância de autoprotected](backup-azure-sql-database.md#enable-auto-protection) não pode aparecer imediatamente em itens protegidos. Isto acontece porque a deteção, normalmente, é executada a cada 8 horas. No entanto, pode detetar e proteger imediatamente novas bases de dados, se executar manualmente uma deteção, selecionando **bds recuperar**, conforme mostrado na imagem seguinte.

  ![Detetar manualmente uma base de dados adicionado recentemente](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>Passos Seguintes

Saiba como [cópia de segurança de uma base de dados do SQL Server](backup-azure-sql-database.md) que está em execução numa VM do Azure.
