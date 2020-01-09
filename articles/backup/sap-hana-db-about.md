---
title: Sobre o backup de banco de dados SAP HANA em VMs do Azure
description: Neste artigo, saiba mais sobre como fazer backup de bancos de dados SAP HANA em execução em máquinas virtuais do Azure.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: aad9e7e89c54100f460a7f348702d0a59e88f519
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479465"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Sobre o backup de banco de dados SAP HANA em VMs do Azure

SAP HANA bancos de dados são cargas de trabalho de missão crítica que exigem um RPO (objetivo de ponto de recuperação) baixo e um RTO (objetivo de tempo de recuperação) rápido. Agora você pode [fazer backup de bancos de dados SAP Hana em execução em VMs do Azure](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) usando o [backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview).

O backup do Azure é [BACKINT certificado](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) pela SAP, para fornecer suporte de backup nativo aproveitando as APIs nativas do SAP Hana. Essa oferta do backup do Azure se alinha com os mantra de backups de **infraestrutura zero** do backup do Azure, eliminando a necessidade de implantar e gerenciar a infraestrutura de backup. Agora você pode fazer backup e restaurar diretamente SAP HANA bancos de dados em execução em VMs do Azure (as[VMs da série M](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#m-series) também têm suporte agora!) e aproveitar os recursos de gerenciamento corporativo que o backup do Azure fornece.

## <a name="added-value"></a>Valor adicionado

Usar o backup do Azure para fazer backup e restaurar SAP HANA bancos de dados, oferece as seguintes vantagens:

* **RPO (objetivo de ponto de recuperação) de 15 minutos**: a recuperação de dados críticos de até 15 minutos agora é possível.
* **Restaurações point-in-time com um único clique**: a restauração de dados de produção para servidores do Hana alternativos torna-se fácil. O encadeamento de backups e catálogos para realizar restaurações é todo gerenciado pelo Azure nos bastidores.
* **Retenção de longo prazo**: para necessidades rigorosas de conformidade e auditoria. Mantenha seus backups por anos, com base na duração da retenção, além da qual os pontos de recuperação serão removidos automaticamente pelo recurso interno de gerenciamento do ciclo de vida.
* **Gerenciamento de backup do Azure**: Use os recursos de monitoramento e gerenciamento do backup do Azure para melhorar a experiência de gerenciamento. Também há suporte para CLI do Azure.

Para exibir os cenários de backup e restauração aos quais damos suporte hoje, consulte a [matriz de suporte do cenário de SAP Hana](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support).

## <a name="backup-architecture"></a>Arquitetura da cópia de segurança

![Diagrama de arquitetura de backup](./media/sap-hana-db-about/backup-architecture.png)

* O processo de backup começa [criando um cofre dos serviços de recuperação](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault) no Azure. Este cofre será usado para armazenar os backups e pontos de recuperação criados ao longo do tempo.
* A VM do Azure que executa o SAP HANA Server é registrada no cofre, e os bancos de dados a serem submetidos a backup são [descobertos](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases). Para habilitar o serviço de backup do Azure para descobrir bancos de dados, um [script de auto-registro](https://aka.ms/scriptforpermsonhana) deve ser executado no servidor Hana como um usuário raiz.
* Esse script cria o usuário do **AZUREWLBACKUPHANAUSER** DB e uma chave correspondente com o mesmo nome em **hdbuserstore**. Consulte a [seção Configurando permissões](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#setting-up-permissions) para saber mais sobre o que o script faz.
* O serviço de backup do Azure agora instala o **plug-in do backup do Azure para Hana** no servidor de SAP Hana registrado.
* O usuário do **AZUREWLBACKUPHANAUSER** DB criado pelo script de auto-registro é usado pelo **plug-in do backup do Azure para Hana** para executar todas as operações de backup e restauração. Se você tentar configurar o backup para bancos de SAP HANAs sem executar esse script, você poderá receber o seguinte erro: **UserErrorHanaScriptNotRun**.
* Para [Configurar o backup](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup) nos bancos de dados descobertos, escolha a política de backup necessária e habilite os backups.

* Depois que o backup é configurado, o serviço de backup do Azure configura os seguintes parâmetros BACKINT no nível de banco de dados no servidor de SAP HANA protegido:
  * [catalog_backup_using_backint: true]
  * [enable_accumulated_catalog_backup: false]
  * [parallel_data_backup_backint_channels: 1]
  * [log_backup_timeout_s:900)]
  * [backint_response_timeout: 7200]

>[!NOTE]
>Verifique se esses parâmetros *não* estão presentes no nível do host. Os parâmetros de nível de host substituirão esses parâmetros e poderão causar um comportamento inesperado.
>

* O **plug-in do backup do Azure para Hana** mantém todos os agendamentos de backup e detalhes da política. Ele dispara os backups agendados e se comunica com o **mecanismo de backup do Hana** por meio das APIs do BACKINT.
* O **mecanismo de backup do Hana** retorna um fluxo de BACKINT com os dados a serem submetidos a backup.
* Todos os backups agendados e backups sob demanda (disparados a partir do portal do Azure) que são completos ou diferenciais são iniciados pelo **plug-in do backup do Azure para Hana**. No entanto, os backups de log são gerenciados e disparados pelo **mecanismo de backup do Hana** .

## <a name="next-steps"></a>Passos seguintes

* Saiba como [restaurar um banco de dados SAP Hana em execução em uma VM do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Saiba como [gerenciar SAP Hana bancos de dados cujo backup é feito usando o backup do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
