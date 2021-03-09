---
title: Execute ações usando o backup center
description: Este artigo explica como realizar ações usando o backup center
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 8c21475e5a52cdce7e38bbeb9d00df3c3ac3a752
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506495"
---
# <a name="perform-actions-using-backup-center"></a>Execute ações usando o backup center

O backup center permite-lhe executar ações relacionadas com a chave a partir de uma interface central sem precisar navegar para um cofre individual. Algumas ações que pode realizar a partir do backup center são:

* Configure a cópia de segurança para as suas fontes de dados
* Restaurar uma instância de backup
* Criar um novo cofre
* Criar uma nova política de backup
* Desencadeie uma cópia de segurança a pedido para uma instância de backup
* Pare o backup para uma instância de backup

## <a name="supported-scenarios"></a>Cenários suportados

* O backup center é atualmente suportado para backup Azure VM, SQL em backup Azure VM, SAP HANA em backup VM Azure, backup de Ficheiros Azure e Base de Dados Azure para backup do Servidor PostgreSQL.
* Consulte a matriz de [suporte](backup-center-support-matrix.md) para obter uma lista detalhada de cenários suportados e não apoiados.

## <a name="configure-backup"></a>Configurar a cópia de segurança

Se estiver a apoiar os Azure VMs, SQL em VMs Azure, SAP HANA em VMs Azure ou Ficheiros Azure, deverá utilizar um cofre dos Serviços de Recuperação. Se estiver a fazer backup de Bases de Dados Azure para o Servidor PostgreSQL, deverá utilizar um cofre de reserva. 

Dependendo do tipo de fonte de dados que pretende fazer, siga as instruções apropriadas, conforme descrito abaixo.

### <a name="configure-backup-to-a-recovery-services-vault"></a>Configure o backup para um cofre dos Serviços de Recuperação

1. Navegue para o centro de backup e selecione **+ Backup** na parte superior do **separador Vista Geral.**

    ![Visão geral do Backup Center](./media/backup-center-actions/backup-center-overview-configure-backup.png)

2. Selecione o tipo de fonte de dados que deseja fazer.

    ![Selecione a fonte de dados para configurar a cópia de segurança VM](./media/backup-center-actions/backup-select-datasource-vm.png)

3. Escolha um cofre dos Serviços de Recuperação e selecione **Proceder**. Isto leva-o à experiência de configuração de backup que é idêntica à que é alcançável a partir de um cofre dos Serviços de Recuperação. [Saiba mais sobre como configurar o backup para máquinas virtuais Azure com um cofre dos Serviços de Recuperação.](tutorial-backup-vm-at-scale.md)

### <a name="configure-backup-to-a-backup-vault"></a>Configure o backup para um cofre de reserva

1. Navegue para o centro de backup e selecione **+ Backup** na parte superior do **separador Vista Geral.**
2. Selecione o tipo de fonte de dados que pretende fazer (Azure Database para o servidor PostgreSQL neste caso).

    ![Selecione a fonte de dados para configurar a Base de Dados de Azure para a cópia de segurança do Servidor PostgreSQL](./media/backup-center-actions/backup-select-datasource-type-postgresql.png)

3. **Selecione Proceder**. Isto leva-o à experiência de configuração de backup que é idêntica à que é alcançável a partir de um cofre de reserva. [Saiba mais sobre como configurar a cópia de segurança para a Base de Dados Azure para o Servidor PostgreSQL com um cofre de reserva](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases).

## <a name="restore-a-backup-instance"></a>Restaurar uma instância de backup

Dependendo do tipo de fonte de dados que pretende restaurar, siga as instruções apropriadas, conforme descrito abaixo.

### <a name="if-youre-restoring-from-a-recovery-services-vault"></a>Se você está restaurando de um cofre dos Serviços de Recuperação

1. Navegue no centro de backup e **selecione Restaurar** na parte superior do **separador Vista Geral.**

    ![Visão geral do Backup Center para restaurar o VM](./media/backup-center-actions/backup-center-overview-restore.png)

2. Selecione o tipo de fonte de dados que deseja restaurar.

    ![Selecione fonte de dados para restauro de VM](./media/backup-center-actions/restore-select-datasource-vm.png)

3. Escolha uma instância de backup e **selecione Proceder**. Isto leva-o à experiência de definições de restauro que é idêntica à que é alcançável a partir de um cofre dos Serviços de Recuperação. [Saiba mais sobre como restaurar uma Máquina Virtual Azure com um cofre dos Serviços de Recuperação.](backup-azure-arm-restore-vms.md#before-you-start)

### <a name="if-youre-restoring-from-a-backup-vault"></a>Se estás a restaurar de um cofre de reserva

1. Navegue no centro de backup e **selecione Restaurar** na parte superior do **separador Vista Geral.**
2. Selecione o tipo de fonte de dados que deseja restaurar (Azure Database for PostgreSQL Server neste caso).

    ![Selecione a fonte de dados para a base de dados Azure para restaurar o servidor pós-greSQL](./media/backup-center-actions/restore-select-datasource-postgresql.png)

3. Escolha uma instância de backup e **selecione Proceder**. Isto leva-o à experiência de definições de restauro que é idêntica à que é alcançável a partir de um cofre dos Serviços de Recuperação. [Saiba mais sobre como restaurar uma Base de Dados Azure para o Servidor PostgreSQL com um cofre de reserva](backup-azure-database-postgresql.md#restore).

## <a name="create-a-new-vault"></a>Criar um novo cofre

Pode criar um novo cofre navegando para o centro de backup e selecionando **+ Cofre** no topo do **separador Vista Geral.**

![Criar cofre](./media/backup-center-actions/backup-center-create-vault.png)

* [Saiba mais sobre a criação de um cofre de serviços de recuperação](backup-create-rs-vault.md)
* [Saiba mais sobre a criação de um cofre de reserva](backup-vault-overview.md)

## <a name="create-a-new-backup-policy"></a>Criar uma nova política de backup

Dependendo do tipo de fonte de dados que pretende fazer, siga as instruções apropriadas descritas abaixo.

### <a name="if-youre-backing-up-to-a-recovery-services-vault"></a>Se está a voltar para um cofre dos Serviços de Recuperação

1. Navegue para o centro de backup e selecione **+ Política** no topo do **separador Vista Geral.**

    ![Visão geral do backup center para a política de backup](./media/backup-center-actions/backup-center-overview-policy.png)

2. Selecione o tipo de fonte de dados que deseja fazer.

    ![Selecione a fonte de dados para a política de backup VM](./media/backup-center-actions/policy-select-datasource-vm.png)

3. Escolha um cofre de serviços de recuperação e **selecione Proceder**. Isto leva-o à experiência de criação de políticas idêntica à que é acessível a partir de um cofre dos Serviços de Recuperação. [Saiba mais sobre como criar uma nova política de backup para a Azure Virtual Machine com um cofre de serviços de recuperação.](backup-azure-arm-vms-prepare.md#create-a-custom-policy)

### <a name="if-youre-backing-up-to-a-backup-vault"></a>Se estás a subir para um cofre de reserva

1. Navegue para o centro de backup e selecione **+ Política** no topo do **separador Vista Geral.**
2. Selecione o tipo de fonte de dados que pretende fazer (Base de Dados Azure para o Servidor PostgreSQL neste caso).

    ![Selecione a fonte de dados para a política para a base de dados Azure para cópia de segurança do servidor PostgreSQL](./media/backup-center-actions/policy-select-datasource-postgresql.png)

3. **Selecione Proceder**. Isto leva-o à experiência de criação de políticas que é idêntica à que é acessível a partir de um cofre de reserva. [Saiba mais sobre como criar uma nova política de backup com um cofre de reserva.](backup-azure-database-postgresql.md#create-backup-policy)

## <a name="execute-an-on-demand-backup-for-a-backup-instance"></a>Execute uma cópia de segurança a pedido para uma instância de backup

O backup center permite-lhe procurar casos de backup em toda a sua propriedade de reserva e executar operações de backup a pedido.

Para ativar uma cópia de segurança a pedido, navegue para o backup center e selecione o item do menu **De Backup Instances.** A seleção desta coisa permite-lhe visualizar detalhes de todas as instâncias de backup a que tem acesso. Pode pesquisar a instância de backup que deseja fazer. Clicar com o direito num item na grelha abre uma lista de ações disponíveis. Selecione a opção **Backup Now** para executar uma cópia de segurança a pedido.

![Backup a pedido](./media/backup-center-actions/backup-center-on-demand-backup.png)

[Saiba mais sobre a realização de backups a pedido para Máquinas Virtuais Azure.](backup-azure-manage-vms.md#run-an-on-demand-backup)

[Saiba mais sobre a realização de backups a pedido para Azure Database para PostgreSQL Server](backup-azure-database-postgresql.md#on-demand-backup).

## <a name="stop-backup-for-a-backup-instance"></a>Pare o backup para uma instância de backup

Há cenários em que se pode querer parar o backup para uma instância de backup, como quando o recurso subjacente a ser apoiado já não existe.

Para ativar uma cópia de segurança a pedido, navegue para o backup center e selecione o item do menu **De Backup Instances.** Selecione isto permite-lhe visualizar detalhes de todas as instâncias de backup a que tem acesso. Pode pesquisar a instância de backup que deseja fazer. Clicar com o direito num item na grelha abre uma lista de ações disponíveis. Selecione a opção **Stop Backup** para parar a cópia de segurança para a instância de backup.

![Parar proteção](./media/backup-center-actions/backup-center-stop-protection.png)

[Saiba mais sobre a paragem de backup para Azure Virtual Machines](backup-azure-manage-vms.md#stop-protecting-a-vm).

[Saiba mais sobre parar a cópia de segurança para o Azure Database para o Servidor PostgreSQL](backup-azure-database-postgresql.md#stop-protection)

## <a name="next-steps"></a>Passos seguintes

* [Monitorizar e operar backups](backup-center-monitor-operate.md)
* [Governe a sua propriedade de reserva](backup-center-govern-environment.md)
* [Obtenha informações sobre as suas cópias de segurança](backup-center-obtain-insights.md)
