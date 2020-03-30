---
title: Restaurar as bases de dados SAP HANA em VMs Azure
description: Neste artigo, descubra como restaurar as bases de dados SAP HANA que estão em execução em Máquinas Virtuais Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287921"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Restaurar as bases de dados SAP HANA em VMs Azure

Este artigo descreve como restaurar as bases de dados SAP HANA que estão em execução na Máquina Virtual Azure (VM), que o serviço de backup Azure tem apoiado um cofre azure Backup Services. Os restauros podem ser utilizados para criar cópias dos dados para cenários de dev/teste ou para regressar a um estado anterior.

Para mais informações, sobre como fazer o back up sap HANA bases de dados, consulte as [bases de dados SAP HANA em VMs Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Restaurar a um ponto no tempo ou a um ponto de recuperação

A Azure Backup pode restaurar as bases de dados SAP HANA que estão em execução em VMs Azure da seguinte forma:

* Restaurar para uma data ou hora específica (para a segunda) utilizando cópias de segurança de registo. A Cópia de Segurança Azure determina automaticamente as cópias de segurança completas e diferenciais apropriadas e a cadeia de backups de registo que são necessárias para restaurar com base no tempo selecionado.

* Restaurar para uma cópia de segurança específica completa ou diferencial para restaurar um ponto de recuperação específico.

## <a name="prerequisites"></a>Pré-requisitos

Antes de restaurar uma base de dados, note o seguinte:

* Pode restaurar a base de dados apenas para uma instância SAP HANA que está na mesma região

* A instância-alvo deve ser registada com o mesmo cofre que a fonte

* O Azure Backup não consegue identificar duas instâncias diferentes do SAP HANA no mesmo VM. Portanto, restaurar dados de uma instância para outra no mesmo VM não é possível

* Para garantir que a instância sap HANA alvo está pronta para ser restaurada, verifique o seu estado de prontidão de **backup:**

  * Abra o cofre no qual o alvo SAP HANA instância está registrado

  * No painel do cofre, sob **o início,** escolha **Backup**

![Backup no painel do cofre](media/sap-hana-db-restore/getting-started-backup.png)

* Em **Backup**, sob o que **SAP HANA in Azure VM** quer **fazer backup?**

![Escolha SAP HANA em Azure VM](media/sap-hana-db-restore/sap-hana-backup.png)

* Em **Discover DBs em VMs** clique em **Ver detalhes**

![Ver detalhes](media/sap-hana-db-restore/view-details.png)

* Reveja a **prontidão** de backup do alvo VM

![Servidores protegidos](media/sap-hana-db-restore/protected-servers.png)

* Para saber mais sobre os tipos de restauro que o SAP HANA suporta, consulte a Nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Restaurar uma base de dados

* Abra o cofre no qual a base de dados SAP HANA a restaurar está registada

* No painel de instrumentos do cofre, sob **itens protegidos,** escolha **itens de backup**

![Itens de backup](media/sap-hana-db-restore/backup-items.png)

* Em itens de backup , em **'Backup Management Type** select **SAP HANA in Azure VM'** **Backup Items**

![Tipo de gestão de backup](media/sap-hana-db-restore/backup-management-type.png)

* Selecione a base de dados a restaurar

 ![Base de dados para restaurar](media/sap-hana-db-restore/database-to-restore.png)

* Reveja o menu da base de dados. Fornece informações sobre cópiade segurança da base de dados, incluindo:

  * Os pontos de restauro mais antigos e últimos

  * O estado de reserva de registo nas últimas 24 e 72 horas para a base de dados

![Menu de base de dados](media/sap-hana-db-restore/database-menu.png)

* Selecione **Restaurar DB**

* Sob configuração de **restauro,** especifique onde (ou como) restaurar os dados:

  * **Localização Alternativa**: Restaurar a base de dados num local alternativo e manter a base de dados original de origem.

  * **Repor DB:** Restaurar os dados na mesma instância SAP HANA que a fonte original. Esta opção substitui a base de dados original.

![Restaurar a configuração](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Restaurar para localização alternativa

* No menu **de configuração restaurar,** em **onde restaurar,** selecione **Localização Alternativa**.

![Restaurar para localização alternativa](media/sap-hana-db-restore/restore-alternate-location.png)

* Selecione o nome de anfitrião sAP HANA e o nome da instância para o qual pretende restaurar a base de dados.
* Verifique se o alvo SAP HANA está pronto para ser restaurado, garantindo a sua prontidão de **reserva.** Consulte a [secção de pré-requisitos](#prerequisites) para mais detalhes.
* Na caixa **de nomes DB restaurados,** introduza o nome da base de dados do alvo.

> [!NOTE]
> Os restauros do recipiente de base de dados único (SDC) devem seguir estes [controlos](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore).

* Se aplicável, **selecione Sobreescrever se o DB com o mesmo nome já existir na instância HANA selecionada**.
* Selecione **OK**.

![Restaurar a configuração - ecrã final](media/sap-hana-db-restore/restore-configuration-last.png)

* No **ponto de restauração Selecione** **Registos (Ponto no Tempo)** para [restaurar a um ponto específico no tempo](#restore-to-a-specific-point-in-time). Ou selecione **Full & Diferencial** para [restaurar um ponto de recuperação específico](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Restaurar e substituir

* No menu **de configuração restaurar,** em **onde restaurar,** selecione **Overwrite DB** > **OK**.

![Substituir Base de Dados](media/sap-hana-db-restore/overwrite-db.png)

* No **ponto de restauração Selecione** **Registos (Ponto no Tempo)** para [restaurar a um ponto específico no tempo](#restore-to-a-specific-point-in-time). Ou selecione **Full & Diferencial** para [restaurar um ponto de recuperação específico](#restore-to-a-specific-recovery-point).

### <a name="restore-to-a-specific-point-in-time"></a>Restaurar a um ponto específico no tempo

Se selecionou **os Registos (Ponto no Tempo)** como o tipo de restauro, faça o seguinte:

* Selecione um ponto de recuperação do gráfico de registo e selecione **OK** para escolher o ponto de restauro.

![Ponto de restauro](media/sap-hana-db-restore/restore-point.png)

* No menu **Restaurar,** selecione **Restaurar** para iniciar o trabalho de restauro.

![Selecione restaurar](media/sap-hana-db-restore/restore-restore.png)

* Acompanhe o progresso da restauração na área de **Notificações** ou rastreie-o selecionando **trabalhos de restauro** no menu de base de dados.

![Restaurar desencadeado com sucesso](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Restaurar para um ponto de recuperação específico

Se selecionou **Full & Diferencial** como o tipo de restauro, faça o seguinte:

* Selecione um ponto de recuperação da lista e selecione **OK** para escolher o ponto de restauro.

![Restaurar ponto de recuperação específico](media/sap-hana-db-restore/specific-recovery-point.png)

* No menu **Restaurar,** selecione **Restaurar** para iniciar o trabalho de restauro.

![Começar a restaurar o trabalho](media/sap-hana-db-restore/restore-specific.png)

* Acompanhe o progresso da restauração na área de **Notificações** ou rastreie-o selecionando **trabalhos de restauro** no menu de base de dados.

![Restaurar o progresso](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> Em Multiple Database Container (MDC) restaura após o sistema DB ser restaurado para uma instância-alvo, é preciso executar novamente o script de pré-registo. Só então o inquilino subsequente db restaurados será bem sucedido. Para saber mais, consulte a [Resolução de Problemas – MDC Restore](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="next-steps"></a>Passos seguintes

* [Saiba como](sap-hana-db-manage.md) gerir as bases de dados do SAP HANA apoiadas usando o Azure Backup
