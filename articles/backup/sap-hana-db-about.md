---
title: Sobre a base de dados SAP HANA em VMs Azure
description: Neste artigo, saiba como fazer backup das bases de dados SAP HANA que estão a funcionar em máquinas virtuais Azure.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: efb9c3f786e429df404e261f053a9c9a9b032e11
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296459"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Sobre a base de dados SAP HANA em VMs Azure

As bases de dados SAP HANA são cargas de trabalho críticas de missão que requerem um objetivo de ponto de recuperação baixo (RPO) e um objetivo de tempo de recuperação rápida (RTO). Agora pode [fazer backup das bases de dados SAP HANA em execução em VMs Azure](./tutorial-backup-sap-hana-db.md) usando [Azure Backup](./backup-overview.md).

A Azure Backup é [o Backint certificado](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) pela SAP, para fornecer suporte de reserva nativo, aproveitando as APIs nativas da SAP HANA. Esta oferta da Azure Backup alinha-se com o mantra da Azure Backup de backups **de infraestruturas zero,** eliminando a necessidade de implantar e gerir a infraestrutura de backup. Agora pode fazer backup e restaurar as bases de dados SAP HANA em execução em VMs Azure[(VMs série M](../virtual-machines/m-series.md) também suportados agora!) e aproveitar as capacidades de gestão da empresa que o Azure Backup fornece.

## <a name="added-value"></a>Valor acrescentado

A utilização do Azure Backup para fazer backup e restaurar as bases de dados SAP HANA, dá as seguintes vantagens:

* **Objetivo do ponto de recuperação de 15 minutos (RPO)**: A recuperação de dados críticos até 15 minutos é agora possível.
* **Um clique, ponto no tempo restaura**: A restauração dos dados de produção para servidores HANA alternativos é facilitada. Acorrentação de backups e catálogos para executar restauros é tudo gerido pela Azure nos bastidores.
* **Retenção a longo prazo**: Para rigorosa conformidade e necessidades de auditoria. Mantenha as suas cópias de segurança durante anos, com base na duração da retenção, para além da qual os pontos de recuperação serão automaticamente podados pela capacidade de gestão do ciclo de vida incorporado.
* **Gestão de Backup da Azure**: Use as capacidades de gestão e monitorização da Azure Backup para uma melhor experiência de gestão. O Azure CLI também é apoiado.

Para ver os cenários de backup e restauro que apoiamos hoje, consulte a matriz de suporte do [cenário SAP HANA.](./sap-hana-backup-support-matrix.md#scenario-support)

## <a name="backup-architecture"></a>Arquitetura da cópia de segurança

![Diagrama de arquitetura de reserva](./media/sap-hana-db-about/backup-architecture.png)

* O processo de backup começa por [criar um cofre dos Serviços de Recuperação](./tutorial-backup-sap-hana-db.md#create-a-recovery-services-vault) em Azure. Este cofre será usado para armazenar os backups e os pontos de recuperação criados ao longo do tempo.
* O servidor Azure VM que executa o servidor SAP HANA está registado no cofre e as bases de dados a serem apoiadas são [descobertas](./tutorial-backup-sap-hana-db.md#discover-the-databases). Para permitir que o serviço de Backup Azure descubra bases de dados, um [script de pré-registo](https://aka.ms/scriptforpermsonhana) deve ser executado no servidor HANA como um utilizador de raiz.
* Este script cria o utilizador **DB AZUREWLBACKUPHANAUSER** e uma chave correspondente com o mesmo nome na **hdbuserstore**. Consulte a secção  [O que o script de pré-registo faz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) para saber mais sobre o que o script faz.
* O Azure Backup Service instala agora o **Plugin Azure Backup para HANA** no servidor SAP HANA registado.
* O utilizador **AZUREWLUPHANAUSER** DB criado pelo script de pré-registo é utilizado pelo **Plugin de Backup Azure para a HANA** realizar todas as operações de backup e restauro. Se tentar configurar a cópia de segurança para DBs SAP HANA sem executar este script, poderá receber o seguinte erro: **UserErrorHanaScriptNotRun**.
* Para [configurar a cópia de segurança](./tutorial-backup-sap-hana-db.md#configure-backup) nas bases de dados descobertas, escolha a política de backup necessária e permita cópias de segurança.

* Uma vez configurada a cópia de segurança, o serviço de backup Azure configura os seguintes parâmetros backint no nível BASE de base no servidor SAP HANA protegido:
  * [catalog_backup_using_backint:verdade]
  * [enable_accumulated_catalog_backup:falso]
  * [parallel_data_backup_backint_channels:1]
  * [log_backup_timeout_s:900)]
  * [backint_response_timeout:7200]

>[!NOTE]
>Certifique-se de que estes parâmetros *não* estão presentes ao nível do HOSPEDEIRO. Os parâmetros de nível de anfitrião irão sobrepor-se a estes parâmetros e podem causar comportamentos inesperados.
>

* O **Azure Backup Plugin para HANA** mantém todos os horários de backup e detalhes da política. Ativa as cópias de segurança programadas e comunica com o **motor de backup HANA** através das APIs de backint.
* O **motor de reserva HANA** devolve um fluxo backint com os dados a serem apoiados.
* Todos os backups programados e backups a pedido (desencadeados pelo portal Azure) que são completos ou diferenciais são iniciados pelo **Plugin de Backup Azure para HANA**. No entanto, as cópias de segurança de registo são geridas e acionadas pelo próprio **Motor de Backup HANA.**
* A azure Backup para SAP HANA, sendo uma solução certificada backint, não depende de tipos de disco ou VM subjacentes. A cópia de segurança é executada por fluxos gerados pela HANA.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Usando backup Azure VM com backup Azure SAP HANA

Além de utilizar a cópia de segurança SAP HANA em Azure que fornece cópia de segurança e recuperação de nível de base de dados, pode utilizar a solução de backup Azure VM para fazer backup dos discos de segurança e não-base de dados.

A [solução de backup Azure SAP HANA certificada backint](#backup-architecture) pode ser usada para cópia de segurança e recuperação de bases de dados.

[A cópia de segurança Azure VM](backup-azure-vms-introduction.md) pode ser usada para fazer backup do SISTEMA e de outros discos não-base de dados. A cópia de segurança VM é tomada uma vez por dia e faz backup de todos os discos (exceto **discos OS do Write Accelerator (WA)** e discos **ultra).** Uma vez que a base de dados está a ser apoiada utilizando a solução de backup Azure SAP HANA, pode obter uma cópia de segurança consistente apenas com o sistema operativo e discos não-base utilizando a cópia de segurança seletiva do disco e restaurar a funcionalidade [VMs Azure.](selective-disk-backup-restore.md)

Para restaurar um VM em execução SAP HANA, siga estes passos:

* [Restaurar um novo VM da Azure VM backup](backup-azure-arm-restore-vms.md) a partir do último ponto de recuperação. Ou criar um novo VM vazio e anexar os discos do último ponto de recuperação.
* Se os discos WA forem excluídos, não são restaurados. Neste caso, crie discos WA vazios e área de registo.
* Depois de todas as outras configurações (como IP, nome do sistema, e assim por diante) estão definidas, o VM está definido para receber dados DB da Azure Backup.
* Agora, restaure o DB no VM a partir da cópia de [segurança Azure SAP HANA DB](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) para o ponto de tempo desejado.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [restaurar uma base de dados SAP HANA em execução num Azure VM](./sap-hana-db-restore.md)
* Saiba como [gerir as bases de dados SAP HANA que são apoiadas através do Azure Backup](./sap-hana-db-manage.md)
