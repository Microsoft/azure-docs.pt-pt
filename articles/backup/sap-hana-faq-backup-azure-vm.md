---
title: Perguntas frequentes-fazer backup de bancos de dados SAP HANA em VMs do Azure
description: Neste artigo, descubra respostas para perguntas comuns sobre como fazer backup de bancos de dados SAP HANA usando o serviço de backup do Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: e5684024668103ccbe13be4af3d7a9336651df77
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287843"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Perguntas frequentes – fazer backup de bancos de dados SAP HANA em VMs do Azure

Este artigo responde a perguntas comuns sobre como fazer backup de bancos de dados do SAP HANA usando o serviço de backup do Azure.

## <a name="backup"></a>Cópia de segurança

### <a name="how-many-full-backups-are-supported-per-day"></a>Quantos backups completos têm suporte por dia?

Damos suporte a apenas um backup completo por dia. Não é possível ter backup diferencial e backup completo disparados no mesmo dia.

### <a name="do-successful-backup-jobs-create-alerts"></a>Os trabalhos de backup bem-sucedidos criam alertas?

Não. Os trabalhos de backup bem-sucedidos não geram alertas. Os alertas são enviados somente para trabalhos de backup que falham. O comportamento detalhado para alertas do portal está documentado [aqui](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). No entanto, se você estiver interessado em ter alertas mesmo para trabalhos bem-sucedidos, poderá usar [Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Posso ver os trabalhos de backup agendados no menu trabalhos de backup?

O menu trabalho de backup mostrará apenas os trabalhos de backup ad hoc. Para trabalhos agendados, use [Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>Os bancos de dados futuros são adicionados automaticamente para backup?

Não, não há suporte para isso no momento.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Se eu excluir um banco de dados de uma instância, o que acontecerá com os backups?

Se um banco de dados for descartado de uma instância de SAP HANA, os backups de banco de dados ainda serão tentados. Isso significa que o banco de dados excluído começa a aparecer como não íntegro em **itens de backup** e ainda está protegido.
A maneira correta de interromper a proteção desse banco de dados é executar o **backup de parar com excluir os dados** neste banco de dado.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Se eu alterar o nome do banco de dados após ele ter sido protegido, qual será o comportamento?

Um banco de dados renomeado é tratado como um novo banco de dados. Portanto, o serviço tratará essa situação como se o banco de dados não fosse encontrado e com a falha dos backups. O banco de dados renomeado será exibido como um novo banco de dados e deverá ser configurado para proteção.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Quais são os pré-requisitos para fazer backup de bancos de dados SAP HANA em uma VM do Azure?

Consulte as seções [pré-requisitos](tutorial-backup-sap-hana-db.md#prerequisites) e [Configurando permissões](tutorial-backup-sap-hana-db.md#setting-up-permissions) .

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Quais permissões devem ser definidas para que o Azure possa fazer backup de bancos de dados SAP HANA?

A execução do script de pré-registro define as permissões necessárias para permitir que o Azure faça backup de bancos de dados SAP HANA. Você pode encontrar mais informações sobre as permissões [aqui](tutorial-backup-sap-hana-db.md#setting-up-permissions).

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>Os backups funcionarão após a migração de SAP HANA de 1,0 para 2,0?

Consulte [esta seção](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) do guia de solução de problemas.

## <a name="restore"></a>Restauro

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Por que não consigo ver o sistema HANA no qual desejo que meu banco de dados seja restaurado?

Verifique se todos os pré-requisitos da restauração para o destino SAP HANA instância foram atendidos. Para obter mais informações, consulte [pré-requisitos-restaurar SAP Hana bancos de dados na VM do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Por que a restauração do BD de substituição falha para meu banco de dados?

Verifique se a opção **forçar substituição** está selecionada durante a restauração.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Por que vejo o erro "sistemas de origem e de destino para restauração são incompatíveis"?

Consulte a SAP HANA nota [1642148](https://launchpad.support.sap.com/#/notes/1642148) para ver quais tipos de restauração têm suporte no momento.

## <a name="next-steps"></a>Passos seguintes

Saiba como [fazer backup de bancos de dados SAP Hana](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) em execução em VMs do Azure.
