---
title: Restaurar as bases de dados SAP HANA em VMs Azure
description: Neste artigo, descubra como restaurar as bases de dados SAP HANA que estão a funcionar nas Máquinas Virtuais Azure. Também pode utilizar o Cross Region Restore para restaurar as suas bases de dados para uma região secundária.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: c502b7741acd343baefe5e2bf8b95cfc02e46688
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021678"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Restaurar as bases de dados SAP HANA em VMs Azure

Este artigo descreve como restaurar as bases de dados SAP HANA em execução numa Máquina Virtual Azure (VM), que o serviço de Backup Azure tem apoiado até um cofre dos Serviços de Recuperação. As restaurações podem ser usadas para criar cópias dos dados para cenários de dev/teste ou para regressar a um estado anterior.

Para obter mais informações sobre como fazer o backs de dados DA SAP HANA, consulte [as bases de dados SAP HANA em VMs Azure](./backup-azure-sap-hana-database.md).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Restaurar a um ponto no tempo ou a um ponto de recuperação

O Azure Backup pode restaurar as bases de dados SAP HANA que estão a funcionar em VMs Azure da seguinte forma:

* Restaurar para uma data ou hora específica (para a segunda) utilizando cópias de segurança de registo. O Azure Backup determina automaticamente as cópias de segurança completas e diferenciais adequadas e a cadeia de backups de registos que são necessárias para restaurar com base no tempo selecionado.

* Restaurar para uma cópia de segurança completa ou diferencial específica para restaurar um ponto de recuperação específico.

## <a name="prerequisites"></a>Pré-requisitos

Antes de restaurar uma base de dados, note o seguinte:

* Só pode restaurar a base de dados para uma hana que esteja na mesma região.

* A instância do alvo deve ser registada com o mesmo cofre que a fonte.

* O Azure Backup não consegue identificar duas instâncias diferentes da SAP HANA no mesmo VM. Então, restaurar dados de um caso para outro no mesmo VM não é possível.

* Para garantir que a instância SAP HANA alvo está pronta para ser restaurada, verifique o seu **estado de prontidão de backup:**

  1. Abra o cofre no qual está registado o caso SAP HANA alvo.

  1. No painel de abóbada, em **"Começar",** escolha **Backup**.

      ![Backup no painel de abóbada](media/sap-hana-db-restore/getting-started-backup.png)

  1. Em **Backup**, em O que pretende fazer **backup?** Escolha **SAP HANA em Azure VM**.

      ![Escolha SAP HANA em Azure VM](media/sap-hana-db-restore/sap-hana-backup.png)

  1. Em **Discover DBs em VMs,** selecione **Ver detalhes**.

      ![Ver detalhes](media/sap-hana-db-restore/view-details.png)

  1. Reveja a **prontidão** de backup do VM alvo.

      ![Servidores protegidos](media/sap-hana-db-restore/protected-servers.png)

* Para saber mais sobre os tipos de restauro que o SAP HANA suporta, consulte a Nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Restaurar uma base de dados

Para restaurar, precisa das seguintes permissões:

* **Permissões do operador de reserva** no cofre onde estás a fazer a restauração.
* **Acesso ao** VM de origem que está apoiado.
* **Acesso do contribuinte (escrever)** ao VM-alvo:
  * Se está a restaurar o mesmo VM, esta é a fonte VM.
  * Se está a restaurar para um local alternativo, este é o novo alvo VM.

1. Abra o cofre no qual está registada a base de dados SAP HANA a restaurar

1. No painel de abóbada, em **Itens Protegidos,** escolha **Itens de Reserva**

    ![Artigos de reserva](media/sap-hana-db-restore/backup-items.png)

1. Em **Itens de Backup**, sob o Tipo de **Gestão de Backup** Selecione SAP **HANA em Azure VM**

    ![Tipo de gestão de backup](media/sap-hana-db-restore/backup-management-type.png)

1. Selecione a base de dados a ser restaurada

    ![Base de dados para restaurar](media/sap-hana-db-restore/database-to-restore.png)

1. Reveja o menu da base de dados. Fornece informações sobre a cópia de segurança da base de dados, incluindo:

    * Os pontos de restauro mais antigos e mais recentes

    * O estado de backup de registo das últimas 24 e 72 horas para a base de dados

    ![Menu de base de dados](media/sap-hana-db-restore/database-menu.png)

1. Selecione **Restaurar DB**

1. Na **Configuração de Restauro,** especifique onde (ou como) restaurar os dados:

    * **Localização Alternativa**: Restaurar a base de dados para uma localização alternativa e manter a base de dados de origem original.

    * **Overwrite DB**: Restaurar os dados na mesma instância SAP HANA que a fonte original. Esta opção substitui a base de dados original.

      ![Restaurar a configuração](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Restaurar para localização alternativa

1. No menu **'Configuração',** em **Onde Restaurar,** selecione **Localização Alternativa**.

    ![Restaurar para localização alternativa](media/sap-hana-db-restore/restore-alternate-location.png)

1. Selecione o nome de anfitrião SAP HANA e o nome da instância a que pretende restaurar a base de dados.
1. Verifique se a instância SAP HANA do alvo está pronta para ser restaurada, garantindo a sua **prontidão de backup.** Consulte a [secção pré-requisitos](#prerequisites) para mais detalhes.
1. Na caixa **de nome DB restaurado,** insira o nome da base de dados-alvo.

    > [!NOTE]
    > As restaurações do Único Contentor de Bases de Dados (SDC) devem seguir estas [verificações](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore).

1. Se aplicável, selecione **Overwrite se o DB com o mesmo nome já existir em instância HANA selecionada**.
1. Selecione **OK**.

    ![Configuração de restauro - tela final](media/sap-hana-db-restore/restore-configuration-last.png)

1. No **ponto de restauro Select**, selecione **Logs (Ponto no Tempo)** para restaurar um ponto específico no [tempo](#restore-to-a-specific-point-in-time). Ou selecione **Full & Differential** [para restaurar um ponto de recuperação específico](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Restaurar e substituir

1. No menu **'Configuração',** em **"Onde Restaurar",** selecione **Overwrite DB**  >  **OK**.

    ![Substituir Base de Dados](media/sap-hana-db-restore/overwrite-db.png)

1. No **ponto de restauro Select**, selecione **Logs (Ponto no Tempo)** para restaurar um ponto específico no [tempo](#restore-to-a-specific-point-in-time). Ou selecione **Full & Differential** [para restaurar um ponto de recuperação específico](#restore-to-a-specific-recovery-point).

### <a name="restore-as-files"></a>Restaurar como ficheiros

Para restaurar os dados de cópia de segurança como ficheiros em vez de uma base de dados, escolha **Restaurar como Ficheiros**. Uma vez que os ficheiros são despejados para um caminho especificado, pode levar estes ficheiros a qualquer máquina SAP HANA onde pretenda restaurá-los como base de dados. Como pode mover estes ficheiros para qualquer máquina, pode agora restaurar os dados através de subscrições e regiões.

1. No menu **'Configuração',** em **'Onde e como Restaurar',** selecione **Restaurar como ficheiros**.
1. Selecione o nome **do servidor host** / HANA ao qual pretende restaurar os ficheiros de cópia de segurança.
1. Na **trajetória destino do servidor,** introduza o caminho da pasta no servidor selecionado no passo 2. Este é o local onde o serviço irá despejar todos os ficheiros de cópia de segurança necessários.

    Os ficheiros que são despejados são:

    * Ficheiros de backup de bases de dados
    * Arquivos de catálogo
    * Ficheiros de metadados JSON (para cada ficheiro de backup envolvido)

    Normalmente, um caminho de partilha de rede, ou caminho de uma partilha de ficheiros Azure montado quando especificado como o caminho de destino, permite um acesso mais fácil a estes ficheiros por outras máquinas na mesma rede ou com a mesma partilha de ficheiros Azure montada neles.

    >[!NOTE]
    >Para restaurar os ficheiros de backup da base de dados numa partilha de ficheiros Azure montada no VM registado no alvo, certifique-se de que a conta raiz leu/escreveu permissões na partilha de ficheiros Azure.

    ![Escolha o caminho do destino](media/sap-hana-db-restore/restore-as-files.png)

1. Selecione o **Ponto de Restauro** correspondente ao qual todos os ficheiros e pastas de cópia de segurança serão restaurados.

    ![Selecione ponto de restauro](media/sap-hana-db-restore/select-restore-point.png)

1. Todos os ficheiros de backup associados ao ponto de restauro selecionado são despejados no caminho de destino.
1. Com base no tipo de ponto de restauração escolhido **(Ponto a tempo** ou **Diferencial de & Completo),** verá uma ou mais pastas criadas no caminho do destino. Uma das pastas `Data_<date and time of restore>` nomeadas contém as cópias de segurança completas e diferenciais, e a outra pasta nomeada `Log` contém as cópias de segurança de registo.
1. Mova estes ficheiros restaurados para o servidor SAP HANA, onde pretende restaurá-los como base de dados.
1. Em seguida, siga estes passos:
    1. Desaguise permissões na pasta/diretório onde os ficheiros de cópia de segurança são armazenados utilizando o seguinte comando:

        ```bash
        chown -R <SID>adm:sapsys <directory>
        ```

    1. Executar o próximo conjunto de comandos como `<SID>adm`

        ```bash
        su - <sid>adm
        ```

    1. Gere o ficheiro do catálogo para ser restaurado. Extrair o **BackupId** do ficheiro de metadados JSON para a cópia de segurança completa, que será usada mais tarde na operação de restauro. Certifique-se de que as cópias de segurança completas e de registo estão em diferentes pastas e elimina os ficheiros de catálogo e metadados JSON nestas pastas.

        ```bash
        hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
        ```

        No comando acima:

        * `<DataFileDir>` - a pasta que contém as cópias de segurança completas
        * `<LogFilesDir>` - a pasta que contém as cópias de segurança de registo
        * `<PathToPlaceCatalogFile>` - a pasta onde o ficheiro de catálogo gerado deve ser colocado

    1. Restaurar usando o arquivo de catálogo recém-gerado através do HANA Studio ou executar a consulta de restauro HDBSQL com este catálogo recentemente gerado. As consultas HDBSQL estão listadas abaixo:

    * Para restaurar a um ponto no tempo:

        Se estiver a criar uma nova base de dados restaurada, gere o comando HDBSQL para criar uma nova base de dados `<DatabaseName>` e, em seguida, pare a base de dados para restaurar. No entanto, se estiver apenas a restaurar uma base de dados existente, execute o comando HDBSQL para parar a base de dados.

        Em seguida, executar o seguinte comando para restaurar a base de dados:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` - Nome da nova base de dados ou base de dados existente que pretende restaurar
        * `<Timestamp>` - Prazo exato do ponto no tempo restaurado
        * `<DatabaseName@HostName>` - Nome da base de dados cuja cópia de segurança é utilizada para restauro e o nome do servidor **host** /SAP HANA no qual esta base de dados reside. A `USING SOURCE <DatabaseName@HostName>` opção especifica que a cópia de segurança de dados (utilizada para restauro) é de uma base de dados com um SID ou nome diferente do alvo da máquina SAP HANA. Portanto, não precisa de ser especificado para restauros feitos no mesmo servidor HANA de onde a cópia de segurança é tomada.
        * `<PathToGeneratedCatalogInStep3>` - Caminho para o arquivo de catálogo gerado no **passo C**
        * `<DataFileDir>` - a pasta que contém as cópias de segurança completas
        * `<LogFilesDir>` - a pasta que contém as cópias de segurança de registo
        * `<BackupIdFromJsonFile>` - o **BackupId** extraído no **passo C**

    * Para restaurar a uma cópia de segurança completa ou diferencial:

        Se estiver a criar uma nova base de dados restaurada, gere o comando HDBSQL para criar uma nova base de dados `<DatabaseName>` e, em seguida, pare a base de dados para restaurar. No entanto, se estiver apenas a restaurar uma base de dados existente, execute o comando HDBSQL para parar a base de dados:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` - o nome da nova base de dados ou da base de dados existente que pretende restaurar
        * `<Timestamp>` - a data exata da restauração do ponto no tempo
        * `<DatabaseName@HostName>` - o nome da base de dados cuja cópia de segurança é utilizada para restauro e o nome do servidor **host** /SAP HANA no qual esta base de dados reside. A `USING SOURCE <DatabaseName@HostName>`  opção especifica que a cópia de segurança de dados (utilizada para restauro) é de uma base de dados com um SID ou nome diferente do alvo da máquina SAP HANA. Portanto, não precisa de ser especificado para restauros feitos no mesmo servidor HANA de onde a cópia de segurança é tomada.
        * `<PathToGeneratedCatalogInStep3>` - o caminho para o arquivo de catálogo gerado no **passo C**
        * `<DataFileDir>` - a pasta que contém as cópias de segurança completas
        * `<LogFilesDir>` - a pasta que contém as cópias de segurança de registo
        * `<BackupIdFromJsonFile>` - o **BackupId** extraído no **passo C**

### <a name="restore-to-a-specific-point-in-time"></a>Restaurar a um ponto específico no tempo

Se selecionou **Os Registos (Ponto no Tempo)** como tipo de restauro, faça o seguinte:

1. Selecione um ponto de recuperação a partir do gráfico de registo e selecione **OK** para escolher o ponto de restauro.

    ![Ponto de restauro](media/sap-hana-db-restore/restore-point.png)

1. No menu **Restaurar,** **selecione Restaurar** para iniciar a função de restauro.

    ![Selecione restaurar](media/sap-hana-db-restore/restore-restore.png)

1. Acompanhe o progresso do restauro na área de **Notificações** ou rastreie-o selecionando **empregos de Restauro** no menu da base de dados.

    ![Restaurar desencadeado com sucesso](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Restaurar a um ponto de recuperação específico

Se selecionou **o Diferencial Full &** como tipo de restauro, faça o seguinte:

1. Selecione um ponto de recuperação da lista e selecione **OK** para escolher o ponto de restauro.

    ![Restaurar o ponto de recuperação específico](media/sap-hana-db-restore/specific-recovery-point.png)

1. No menu **Restaurar,** **selecione Restaurar** para iniciar a função de restauro.

    ![Começar a restaurar o trabalho](media/sap-hana-db-restore/restore-specific.png)

1. Acompanhe o progresso do restauro na área de **Notificações** ou rastreie-o selecionando **empregos de Restauro** no menu da base de dados.

    ![Restaurar o progresso](media/sap-hana-db-restore/restore-progress.png)

    > [!NOTE]
    > Em Multiple Database Container (MDC) restaura-se após o DB do sistema ser restaurado para uma instância-alvo, é necessário voltar a executar o script de pré-registo. Só então o inquilino subsequente DB restaura será bem sucedido. Para saber mais, consulte a [Resolução de Problemas – Restauro do MDC](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="cross-region-restore"></a>Restauro da Região Transversal

Como uma das opções de restauro, o Cross Region Restore (CRR) permite-lhe restaurar as bases de dados SAP HANA hospedadas em VMs Azure numa região secundária, que é uma região emparelhada Azure.

Para embarcar na funcionalidade durante a pré-visualização, leia a [secção Antes de Começar](./backup-create-rs-vault.md#set-cross-region-restore).

Para ver se a CRR está ativada, siga as instruções no [Configure Cross Region Restore](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Ver itens de backup na região secundária

Se o CRR estiver ativado, pode ver os itens de reserva na região secundária.

1. A partir do portal, aceda a itens de backup de **cofres dos Serviços de Recuperação.**  >  
1. Selecione **Região Secundária** para ver os itens na região secundária.

>[!NOTE]
>Apenas os tipos de gestão de backup que suportam a função CRR serão apresentados na lista. Atualmente, apenas é permitido o apoio à reposição de dados da região secundária para uma região secundária.

![Artigos de reserva na região secundária](./media/sap-hana-db-restore/backup-items-secondary-region.png)

![Bases de dados na região secundária](./media/sap-hana-db-restore/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>Restauro na região secundária

A região secundária restaurará a experiência do utilizador será semelhante à região primária restaurar a experiência do utilizador. Ao configurar detalhes no painel de configuração restaurar para configurar a sua restauração, será solicitado que forneça apenas parâmetros de região secundária.

![Onde e como restaurar](./media/sap-hana-db-restore/restore-secondary-region.png)

>[!NOTE]
>A rede virtual na região secundária precisa de ser atribuída de forma única, e não pode ser usada para quaisquer outros VMs nesse grupo de recursos.

![Trigger restaurar na notificação de progresso](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>
>* Após o restauro ser desencadeado e na fase de transferência de dados, o trabalho de restauro não pode ser cancelado.
>* Os papéis do Azure necessários para restaurar na região secundária são os mesmos que na região primária.

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitorização da região secundária restabelece postos de trabalho

1. A partir do portal, vá para o **cofre dos Serviços de Recuperação**  >  **trabalhos de backup**
1. Selecione **Região Secundária** para ver os itens na região secundária.

    ![Trabalhos de reserva filtrados](./media/sap-hana-db-restore/backup-jobs-secondary-region.png)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como](sap-hana-db-manage.md) gerir as bases de dados SAP HANA com recurso a Backup Azure
