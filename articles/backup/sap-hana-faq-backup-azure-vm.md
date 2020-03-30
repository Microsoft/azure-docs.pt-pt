---
title: FAQ - Fazer a cópia de segurança de bases de dados SAP HANA nas VMs do Azure
description: Neste artigo, descubra respostas a perguntas comuns sobre o backup das bases de dados SAP HANA utilizando o serviço de backup Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a46c4d6cccc00452a56567880400ef5779e6aed4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155397"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Perguntas frequentes – Back up SAP HANA bases de dados em VMs Azure

Este artigo responde a perguntas comuns sobre o backup das bases de dados SAP HANA utilizando o serviço de backup Azure.

## <a name="backup"></a>Cópia de segurança

### <a name="how-many-full-backups-are-supported-per-day"></a>Quantos reforços são suportados por dia?

Apoiamos apenas um reforço completo por dia. Não pode ter reforço diferencial e cópia de segurança completa desencadeada no mesmo dia.

### <a name="do-successful-backup-jobs-create-alerts"></a>As tarefas de cópia de segurança bem-sucedida criam alertas?

Não. Trabalhos de apoio bem sucedidos não geram alertas. Os alertas são enviados apenas para trabalhos de reserva que falham. O comportamento detalhado dos alertas do portal está documentado [aqui.](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) No entanto, se estiver interessado em ter alertas mesmo para empregos bem sucedidos, pode utilizar o [Azure Monitor.](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Posso ver trabalhos de reserva programados no menu Backup Jobs?

O menu Backup Job só mostrará trabalhos de apoio ad-hoc. Para trabalhos regulares, utilize [o Monitor Azure.](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)

### <a name="are-future-databases-automatically-added-for-backup"></a>As bases de dados futuras são adicionadas automaticamente para cópia de segurança?

Não, isto não é apoiado atualmente.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Se eu apagar uma base de dados de um caso, o que acontecerá com as cópias de segurança?

Se uma base de dados for retirada de uma instância SAP HANA, as cópias de dados ainda são tentadas. Isto implica que a base de dados eliminada começa a aparecer como pouco saudável em itens de **backup** e ainda está protegida.
A forma correta de parar de proteger esta base de dados é executar stop **backup com eliminar dados** nesta base de dados.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Se eu mudar o nome da base de dados depois de ter sido protegida, qual será o comportamento?

Uma base de dados renomeada é tratada como uma nova base de dados. Por conseguinte, o serviço tratará esta situação como se a base de dados não tivesse sido encontrada e com falhas nas cópias de segurança. A base de dados renomeada aparecerá como uma nova base de dados e deve ser configurada para proteção.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Quais são os pré-requisitos para apoiar as bases de dados da SAP HANA num VM Azure?

Consulte os [pré-requisitos](tutorial-backup-sap-hana-db.md#prerequisites) e [o que o script pré-registo faz.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Que permissões devem ser definidas para que o Azure possa fazer o backup das bases de dados da SAP HANA?

A execução do script de pré-registo define as permissões necessárias para permitir que o Azure reserve as bases de dados SAP HANA. Pode encontrar mais o que o script pré-inscrição faz [aqui.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>Os backups funcionarão depois de migrarem o SAP HANA de 1,0 para 2.0?

Consulte [esta secção](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) do guia de resolução de problemas.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>Pode o Azure HANA Backup ser configurado contra um IP virtual (equilibrador de carga) e não uma máquina virtual?

Atualmente não temos a capacidade de configurar a solução apenas contra um IP virtual. Precisamos de uma máquina virtual para executar a solução.

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>Tenho uma replicação do sistema SAP HANA (HSR), como devo configurar cópias de segurança para esta configuração?

Os nós primários e secundários do HSR serão tratados como dois VMs individuais e não relacionados. Você precisa configurar backup no nó principal e quando o fail-over acontece, você precisa configurar backup no nó secundário (que agora se torna o nó principal). Não existe uma "falha" automática de cópia seleções para o outro nó.

## <a name="restore"></a>Restauro

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Por que não posso ver o sistema HANA a que quero que a minha base de dados seja restaurada?

Verifique se todos os pré-requisitos para o restauro para o alvo SAP HANA instância são cumpridos. Para mais informações, consulte [Pré-requisitos - Restaurar as bases de dados SAP HANA em Azure VM](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Porque é que o restauro do OVERwrite DB está a falhar na minha base de dados?

Certifique-se de que a opção **de sobreposição** da força é selecionada durante a restauração.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Porque é que vejo que os "sistemas de origem e alvo para restauro são incompatíveis"?

Consulte a Nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148) para ver que tipos de restauro são suportados atualmente.

## <a name="next-steps"></a>Passos seguintes

Saiba como [fazer o back up sap HANA bases de dados](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) em execução em VMs Azure.
