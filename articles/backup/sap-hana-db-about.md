---
title: Sobre a cópia de segurança da base de dados SAP HANA em VMs Azure
description: Neste artigo, saiba apoiar as bases de dados SAP HANA que estão a funcionar em máquinas virtuais Azure.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 52c235c95cea73a0c51c62fcb55f7f711d2eff21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79476462"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Sobre a cópia de segurança da base de dados SAP HANA em VMs Azure

As bases de dados SAP HANA são cargas de trabalho críticas da missão que requerem um objetivo de ponto de recuperação baixo (RPO) e um objetivo de tempo de recuperação rápida (RTO). Agora pode fazer backup nas bases de [dados SAP HANA em execução em VMs Azure](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) utilizando [backup azure](https://docs.microsoft.com/azure/backup/backup-overview).

O Azure Backup é [certificado](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) pela SAP, para fornecer suporte de backup nativo, alavancando as API nativas da SAP HANA. Esta oferta da Azure Backup alinha-se com o mantra da Azure Backup de backups **de infraestruturas zero,** eliminando a necessidade de implantar e gerir a infraestrutura de backup. Agora pode agora fazer backup e restaurar as bases de dados SAP HANA em execução em VMs Azure[(VMs](../virtual-machines/m-series.md) série M também suportados agora!) e alavancar as capacidades de gestão da empresa que o Azure Backup fornece.

## <a name="added-value"></a>Valor acrescentado

A utilização de Backup Azure para fazer backup e restaurar as bases de dados SAP HANA, dá as seguintes vantagens:

* **Objetivo do Ponto de Recuperação de 15 minutos (RPO)**: A recuperação de dados críticos de até 15 minutos é agora possível.
* **Um clique, restaurações ponto-a-tempo:** Restaurar os dados de produção para servidores HANA alternativos é facilitado. A correnção de backups e catálogos para executar restauros é tudo gerido pela Azure nos bastidores.
* **Retenção a longo prazo**: Para rigorosas necessidades de conformidade e auditoria. Mantenha os seus backups durante anos, com base na duração da retenção, para além do qual os pontos de recuperação serão podados automaticamente pela capacidade de gestão do ciclo de vida incorporado.
* **Gestão de Backup do Azure**: Utilize as capacidades de gestão e monitorização da Azure Backup para uma melhor experiência de gestão. O Azure CLI também é apoiado.

Para ver os cenários de backup e restauro que apoiamos hoje, consulte a matriz de suporte ao [cenário SAP HANA](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support).

## <a name="backup-architecture"></a>Arquitetura da cópia de segurança

![Diagrama de arquitetura de backup](./media/sap-hana-db-about/backup-architecture.png)

* O processo de backup começa pela criação de um cofre de serviços de [recuperação](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault) em Azure. Este cofre será usado para armazenar as cópias de segurança e pontos de recuperação criados ao longo do tempo.
* O servidor SAP HANA do Azure vM está registado no cofre, e as bases de dados a serem apoiadas são [descobertas](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases). Para permitir que o serviço De backup Azure descubra bases de dados, um [script de pré-registo](https://aka.ms/scriptforpermsonhana) deve ser executado no servidor HANA como um utilizador raiz.
* Este script cria o utilizador **DO IDWLBACKUPHANAUSER** E uma chave correspondente com o mesmo nome na **hdbuserstore**. Consulte o [what the pre-registration script does](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) section to understand more about what the script does.
* O Serviço de Backup Azure instala agora o **Plugin de Backup Azure para HANA** no servidor SAP HANA registado.
* O utilizador **AZUREWLBACKUPHANAUSER** DB criado pelo script de pré-registo é utilizado pelo **Plugin de Backup Azure para HANA** para realizar todas as operações de backup e restauro. Se tentar configurar a cópia de segurança para DBs SAP HANA sem executar este script, poderá receber o seguinte erro: **UserErrorHanaScriptNotRun**.
* Para [configurar](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup) a cópia de segurança nas bases de dados descobertas, escolha a política de backup necessária e ative cópias de segurança.

* Uma vez configurada a cópia de segurança, o serviço de backup Azure configura os seguintes parâmetros Backint ao nível da BASE DE DADOS no servidor SAP HANA protegido:
  * [catalog_backup_using_backint:verdade]
  * [enable_accumulated_catalog_backup:falso]
  * [parallel_data_backup_backint_channels:1]
  * [log_backup_timeout_s:900)]
  * [backint_response_timeout:7200]

>[!NOTE]
>Certifique-se de que estes parâmetros *não* estão presentes ao nível do HOST. Os parâmetros de nível de hospedeiro sobrepor-se-ão a estes parâmetros e podem causar comportamentos inesperados.
>

* O **Plugin de Backup Azure para HANA** mantém todos os horários de backup e detalhes da política. Aciona as cópias de segurança programadas e comunica com o motor de **reserva HANA** através das APIs backint.
* O **motor de reserva HANA** devolve um fluxo Backint com os dados a serem apoiados.
* Todos os backups programados e backups a pedido (desencadeados a partir do portal Azure) que são completos ou diferenciais são iniciados pelo **Plugin de Backup Azure para HANA**. No entanto, as cópias de segurança de log são geridas e acionadas pelo próprio Motor de **Backup HANA.**
* O Azure Backup para o SAP HANA, sendo uma solução certificada backInt, não depende dos tipos de discos ou VM subjacentes. A cópia de segurança é executada por fluxos gerados pela HANA.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Utilização de backup Azure VM com backup Azure SAP HANA

Além de utilizar a cópia de segurança SAP HANA em Azure que fornece cópia de segurança e recuperação de nível de base de dados, pode utilizar a solução de backup Azure VM para fazer backup dos discos DE e não-base de dados.

A solução de [backup Azure SAP HANA certificada pela Backint](#backup-architecture) pode ser utilizada para backup e recuperação de bases de dados.

A cópia de [segurança Azure VM](backup-azure-vms-introduction.md) pode ser utilizada para fazer backup do SISTEMA e de outros discos não base de dados. A cópia de segurança VM é tomada uma vez por dia e faz cópiade de segurança todos os discos (exceto **write accelerator (WA)** e **UltraDisks).** Uma vez que a base de dados está a ser apoiada utilizando a solução de backup Azure SAP HANA, pode utilizar uma cópia de segurança consistente com ficheiros apenas dos discos DE e não-base de dados utilizando a capacidade de exclusão do disco, que está atualmente em pré-visualização.

>[!NOTE]
> A utilização de scripts pré-post com a cópia de segurança Azure VM permitirá cópias de segurança consistentes com aplicações dos volumes de dados da base de dados. No entanto, se a área de registo residir nos discos WA, tirar uma foto destes discos pode não garantir a consistência da área de registo. Hana tem uma maneira explícita de gerar backups de registo por esta razão exata. Ative o mesmo no seu SAP HANA e pode ser apoiado usando cópia de segurança Azure SAP HANA.

Para restaurar um VM em execução SAP HANA, siga estes passos:

* [Restaure um novo VM do Azure VM](backup-azure-arm-restore-vms.md) a partir do último ponto de recuperação. Ou criar um novo VM vazio e fixar os discos a partir do último ponto de recuperação.
* Como os discos wa não são apoiados, não são restaurados. Crie discos WA vazios e área de registo.
* Depois de todas as outras configurações (como IP, nome do sistema, e assim por diante) estarem definidas, o VM está definido para receber dados DB da cópia de segurança do Azure.
* Agora restaure o DB no VM do [backup Azure SAP HANA DB](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) para o desejado ponto-a-tempo.

## <a name="next-steps"></a>Passos seguintes

* Saiba como restaurar uma base de [dados SAP HANA em execução num VM Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Saiba como gerir as bases de [dados SAP HANA que são apoiadas usando o Azure Backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
