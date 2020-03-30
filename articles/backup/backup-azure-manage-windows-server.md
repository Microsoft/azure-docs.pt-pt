---
title: Gerir cofres e servidores dos Serviços de Recuperação Azure
description: Neste artigo, aprenda a usar o painel de visão geral do cofre dos Serviços de Recuperação para monitorizar e gerir os seus cofres de Serviços de Recuperação.
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 1a4d23c157700f42422cfe7ca8fa1c49e2cf128a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131979"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Monitorizar e gerir cofres dos Serviços de Recuperação

Este artigo explica como usar o painel de visão **geral** do cofre dos Serviços de Recuperação para monitorizar e gerir os seus cofres de Serviços de Recuperação. Quando abrir um cofre dos Serviços de Recuperação da lista, abre o painel de visão **geral** para o cofre selecionado. O painel fornece vários detalhes sobre o cofre. Há *azulejos* que mostram: o estado dos alertas críticos e de alerta, os postos de trabalho em curso e os trabalhos de backup falhados, bem como a quantidade de armazenamento redundante localmente (LRS) e armazenamento georedundant (GRS) utilizados. Se apoiar os VMs Azure no cofre, o azulejo [ **de pré-verificação** de backup apresenta quaisquer itens críticos ou de aviso](https://docs.microsoft.com/azure/backup/backup-azure-manage-windows-server#backup-pre-check-status). A imagem seguinte é o painel de **visão geral** para **Contoso-cofre**. Os itens de **backup** mostram que há nove itens registados no cofre.

![painel de dados de serviços de recuperação](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

Os pré-requisitos para este artigo são: uma subscrição Azure, um cofre de Serviços de Recuperação, e que há pelo menos um item de reserva configurado para o cofre.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="open-a-recovery-services-vault"></a>Abra um cofre de Serviços de Recuperação

Para monitorizar alertas, ou ver dados de gestão sobre um cofre dos Serviços de Recuperação, abra o cofre.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com a sua subscrição do Azure.

2. No portal, clique em **Todos os serviços.**

   ![Lista aberta de cofres dos Serviços de Recuperação passo 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. Na caixa de diálogo **de todos os serviços,** digite Serviços de **Recuperação.** À medida que começa a escrever, a lista filtra com base na sua entrada. Quando a opção de cofres dos Serviços de **Recuperação** aparecer, clique nela para abrir a lista de cofres dos Serviços de Recuperação na sua subscrição.

    ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. A partir da lista de cofres, clique num cofre para abrir o seu painel de **visão geral.**

    ![painel de dados de serviços de recuperação](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    O painel de visão geral utiliza azulejos para fornecer alertas e dados de trabalho de backup.

## <a name="monitor-backup-jobs-and-alerts"></a>Monitorizar trabalhos de backup e alertas

O painel de **visão geral** do cofre dos Serviços de Recuperação fornece azulejos para informações de monitorização e utilização. Os azulejos da secção de Monitorização exibem alertas críticos e de alerta e em curso e trabalhos falhados. Clique num alerta ou trabalho específico para abrir o menu Backup Alerts ou Backup Jobs, filtrado para esse trabalho ou alerta.

![Tarefas de painel de backup](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

A secção de Monitorização mostra os resultados de **alertas** de backup pré-definidos e consultas **de backup jobs.** As telhas de monitorização fornecem informações atualizadas sobre:

* Alertas críticos e de alerta para trabalhos de backup (nas últimas 24 horas)
* Verifique o estado dos VMs Azure. Para obter informações completas sobre o estado de pré-verificação, consulte o [Estado de Pré-verificação de Backup](#backup-pre-check-status).
* Os postos de trabalho em curso e os postos de trabalho que falharam (nas últimas 24 horas).

Os azulejos de utilização fornecem:

* O número de itens de reserva configurados para o cofre.
* O armazenamento Azure (separado por LRS e GRS) consumido pelo cofre.

Clique nos azulejos (exceto no Armazenamento de Cópia de Segurança) para abrir o menu associado. Na imagem acima, o azulejo Backup Alerts mostra três alertas críticos. Clicando na linha de alertas Críticos no azulejo 'Backup Alerts', abre os Alertas de Backup filtrados para alertas Críticos.

![Menu de alertas de backup filtrado para alertas críticos](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

O menu Backup Alerts, na imagem acima, é filtrado por: O estado é Ativo, a Gravidade é Crítica, e o tempo é das 24 horas anteriores.

### <a name="backup-pre-check-status"></a>Estado de pré-verificação de cópia de segurança

As pré-verificações de backup verifiquem a configuração dos seus VMs para obter problemas que possam afetar negativamente as cópias de segurança. Eles agregam esta informação para que você possa vê-la diretamente a partir do painel de dados do Cofre de Serviços de Recuperação e fornecer recomendações para medidas corretivas para garantir backups consistentes com ficheiros ou aplicações consistentes com sucesso. Não necessitam de infraestruturas e não têm custos adicionais.  

Os pré-controlos de backup funcionam como parte das operações de backup programadas para os seus VMs Azure. Concluem com um dos seguintes Estados:

* **Aprovado**: Este estado indica que a configuração do seu VM deve levar a cópias de segurança bem sucedidas e não é necessário tomar medidas corretivas.
* **Aviso**: Este estado indica um ou mais problemas na configuração do VM que *podem* levar a falhas de backup. Fornece medidas *recomendadas* para garantir cópias de segurança bem sucedidas. Por exemplo, não ter o mais recente Agente VM instalado pode fazer com que as cópias de segurança falhem intermitentemente. Esta situação proporcionaria um estado de alerta.
* **Crítico**: Este estado indica um ou mais problemas críticos na configuração do VM que *levarão* a falhas de backup e fornece as medidas *necessárias* para garantir cópias de segurança bem sucedidas. Por exemplo, um problema de rede causado por uma atualização às regras nsg de um VM, fará com que as cópias de segurança falhem, uma vez que impede que o VM se comunique com o serviço de backup Azure. Esta situação proporcionaria um estado crítico.

Siga os passos abaixo para começar a resolver quaisquer problemas reportados pela Backup Pre-Checks para backups VM no seu Cofre de Serviços de Recuperação.

* Selecione o estado de **pré-verificação de backup (VMs Azure)** no painel de instrumentos do Cofre de Serviços de Recuperação.
* Selecione qualquer VM com um estado de pré-verificação de backup de **críticoou** **aviso**. Esta ação abrirá o painel de **detalhes vm.**
* Selecione a notificação do painel na parte superior do painel para revelar a descrição do problema de configuração e os passos corretivos.

## <a name="manage-backup-alerts"></a>Gerir alertas de backup

Para aceder ao menu Backup Alerts, no menu do cofre dos Serviços de Recuperação, clique em **Alertas de Backup**.

![Alertas de cópias de segurança](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

O relatório dos Alertas de Apoio lista os alertas para o cofre.

![Alertas de cópias de segurança](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Alertas

A lista de Alertas de Backup apresenta as informações selecionadas para os alertas filtrados. No menu Backup Alerts, pode filtrar para alertas críticos ou de aviso.

| Nível de Alerta | Eventos que geram alertas |
| ----------- | ----------- |
| Crítica | Recebe alertas críticos quando: Os trabalhos de backup falham, os trabalhos de recuperação falham, e quando se para a proteção num servidor, mas retém os dados.|
| Aviso | Recebe alertas quando: Trabalhos de backup completos com avisos, por exemplo, quando menos de 100 ficheiros não são apoiados devido a problemas de corrupção, ou quando mais de 1.000.000 ficheiros são apoiados com sucesso). |
| Informativo | atualmente, não existem alertas informativos. |

### <a name="viewing-alert-details"></a>Visualizar detalhes do alerta

O relatório de Alertas de Apoio rastreia oito detalhes sobre cada alerta. Utilize o botão Escolha de **colunas** para editar os detalhes do relatório.

![Alertas de cópias de segurança](./media/backup-azure-manage-windows-server/backup-alerts.png)

Por defeito, todos os detalhes, exceto o Tempo de **Última Ocorrência,** aparecem no relatório.

* Alerta
* Artigo de backup
* Servidor Protegido
* Gravidade
* Duração
* Tempo de Criação
* Estado
* Última ocorrência

### <a name="change-the-details-in-alerts-report"></a>Alterar os detalhes no relatório de alertas

1. Para alterar a informação do relatório, no menu **'Alertas de Cópia de Segurança',** clique em **Escolher colunas**.

   ![Alertas de cópias de segurança](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   O menu **de colunas Escolha** abre.

2. No menu **'Escolha' de colunas,** escolha os detalhes que pretende apresentar no relatório.

    ![Escolha menu de colunas](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. Clique em **Feito** para guardar as suas alterações e fechar o menu Escolha colunas.

   Se fizer alterações, mas não quiser manter as alterações, clique **em Reset** para devolver o selecionado à última configuração guardada.

### <a name="change-the-filter-in-alerts-report"></a>Alterar o filtro no relatório de alertas

Utilize o menu **Filter** para alterar a gravidade, o estado, o tempo de início e o tempo de fim para os alertas.

> [!NOTE]
> A edição do filtro 'Alertas de Reserva' não altera os alertas Críticos ou de Aviso no painel de visão geral do cofre.
>  

1. Para alterar o filtro 'Alertas de Cópia de Segurança', no menu 'Alertas de Cópia de Segurança', clique em **Filtro**.

   ![Escolha menu de filtro](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   O menu Filter aparece.

   ![Escolha menu de filtro](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Editar a Gravidade, Estado, Tempo de Início ou Fim e clicar **em Feito** para guardar as suas alterações.

## <a name="configuring-notifications-for-alerts"></a>Configurar notificações para alertas

Configure notificações para gerar e-mails quando ocorrer um alerta de aviso ou crítico. Pode enviar alertas de e-mail a cada hora, ou quando ocorrer um alerta específico.

   ![Alertas de filtro](./media/backup-azure-manage-windows-server/configure-notification.png)

Por defeito, as notificações por e-mail estão **on**. Clique **em Off** para parar as notificações de e-mail.

No controlo **notificar,** escolha **Per Alert** se não quiser agrupar-se ou não tiver muitos itens que possam gerar alertas. Cada alerta resulta numa notificação (a definição padrão) e um e-mail de resolução é enviado imediatamente.

Se selecionar **A Digestão Horária,** é enviado um e-mail aos destinatários explicando os alertas não resolvidos gerados na última hora. Um e-mail de resolução é enviado no final da hora.

Escolha a gravidade do alerta (Crítico ou Aviso) utilizada para gerar e-mail. Atualmente não existem alertas de informação.

## <a name="manage-backup-items"></a>Gerir itens de backup

Um cofre dos Serviços de Recuperação contém muitos tipos de dados de backup. [Saiba mais](backup-overview.md#what-can-i-back-up) sobre o que pode recuar. Para gerir os vários servidores, computadores, bases de dados e cargas de trabalho, clique no azulejo **'Backup Itens'** para ver o conteúdo do cofre.

![Backup itens azulejos](./media/backup-azure-manage-windows-server/backup-items.png)

Abre a lista de Itens de Backup, organizado saem pelo Backup Management Type.

![lista de itens de backup](./media/backup-azure-manage-windows-server/list-backup-items.png)

Para explorar um tipo específico de instância protegida, clique no item na coluna 'Backup Management Type'. Por exemplo, na imagem acima, existem duas máquinas virtuais Azure protegidas neste cofre. Clicando na **Máquina Virtual Azure,** abre a lista de máquinas virtuais protegidas neste cofre.

![lista do tipo de backup](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

A lista de máquinas virtuais tem dados úteis: o Grupo de Recursos Associados, [pré-verificação](https://docs.microsoft.com/azure/backup/backup-azure-manage-windows-server#backup-pre-check-status)prévia de backup, último estado de backup e data do ponto de restauro mais recente. A elipse, na última coluna, abre o menu para desencadear tarefas comuns. Os dados úteis fornecidos nas colunas são diferentes para cada tipo de cópia de segurança.

![lista do tipo de backup](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Gerir Tarefas de cópia de segurança

O azulejo **backup Jobs** no painel de instrumentos do cofre mostra o número de empregos que estão em progresso, ou falhado nas últimas 24 horas. O azulejo dá um vislumbre do menu Backup Jobs.

![Itens de back up a partir de configurações](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

Para ver detalhes adicionais sobre os trabalhos, clique **em In Progress** ou **Failed open** the Backup Jobs menu filtered for that state.

### <a name="backup-jobs-menu"></a>Menu de trabalhos de backup

O menu **Backup Jobs** apresenta informações sobre o tipo de item, Operação, Estado, Hora de Início e Duração.  

Para abrir o menu Backup Jobs, no menu principal do cofre, clique em **Backup Jobs**.

![Itens de back up a partir de configurações](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

A lista de trabalhos de apoio abre.

![Itens de back up a partir de configurações](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

O menu Backup Jobs mostra o estado de todas as operações, em todos os tipos de backup, nas últimas 24 horas. Utilize o **Filtro** para alterar os filtros. Os filtros são explicados nas seguintes secções.

Para alterar os filtros:

1. No menu backup jobs do cofre, clique em **Filtro**.

   ![Itens de back up a partir de configurações](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    O menu Filter abre.

   ![Itens de back up a partir de configurações](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Escolha as definições do filtro e clique **em 'Feito '.** A lista filtrada atualiza-se com base nas novas definições.

#### <a name="item-type"></a>Tipo de item

O tipo item é o tipo de gestão de backup da instância protegida. Existem quatro tipos; ver a seguinte lista. Pode ver todos os tipos de artigos, ou um tipo de item. Não é possível selecionar dois ou três tipos de itens. Os tipos de artigos disponíveis são:

* Todos os tipos de itens
* Máquina virtual do Azure
* Ficheiros e pastas
* Storage do Azure
* Carga de trabalho azul

#### <a name="operation"></a>Operação

Pode ver uma operação, ou todas as operações. Não se pode selecionar duas ou três operações. As Operações disponíveis são:

* Todas as Operações
* Registar
* Configurar a cópia de segurança
* Cópia de segurança
* Restauro
* Desativar a cópia de segurança
* Eliminar dados de cópia de segurança

#### <a name="status"></a>Estado

Pode ver Todo o Estado ou um. Não se pode selecionar dois ou três estatutos. Os estatutos disponíveis são:

* Todo o Estatuto
* Concluído
* Em curso
* Falhou
* Cancelado
* Concluído com avisos

#### <a name="start-time"></a>Hora de início

O dia e a hora que a consulta começa. O padrão é um período de 24 horas.

#### <a name="end-time"></a>Hora de fim

O dia e a hora em que a consulta termina.

### <a name="export-jobs"></a>Empregos de exportação

Utilize trabalhos de **exportação** para criar uma folha de cálculo contendo todas as informações sobre o menu Jobs. A folha de cálculo tem uma folha que contém um resumo de todos os trabalhos, e lençóis individuais para cada trabalho.

Para exportar a informação sobre o emprego para uma folha de cálculo, clique em **empregos de exportação.** O serviço cria uma folha de cálculo usando o nome do cofre e da data, mas pode alterar o nome.

## <a name="monitor-backup-usage"></a>Monitorizar a utilização de backup

O azulejo de armazenamento de reserva no painel de instrumentos mostra o armazenamento consumido em Azure. Está prevista a utilização do armazenamento:

* Uso de armazenamento Cloud LRS associado ao cofre
* Uso de armazenamento CLOUD GRS associado ao cofre

## <a name="troubleshooting-monitoring-issues"></a>Problemas de monitorização de resolução de problemas

**Emissão:** Empregos e/ou alertas do agente de backup Azure não aparecem no portal.

**Passos de resolução de problemas:** O processo, ```OBRecoveryServicesManagementAgent```envia o trabalho e os dados de alerta para o serviço de backup Azure. Ocasionalmente, este processo pode ficar preso ou encerrado.

1. Para verificar se o processo não está em ```OBRecoveryServicesManagementAgent``` execução, abra o Task **Manager**e verifique se está em execução.

2. Se o processo não estiver em execução, abra o Painel de **Controlo**e navegue na lista de serviços. Inicie ou reinicie o Agente de **Gestão de Serviços de Recuperação do Microsoft Azure**.

    Para mais informações, navegue nos registos em:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*`Por exemplo:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Passos seguintes

* [Restaurar o Windows Server ou o Windows Client do Azure](backup-azure-restore-windows-server.md)
* Para saber mais sobre o Backup Azure, consulte a visão geral do [Azure Backup](backup-introduction-to-azure-backup.md)
