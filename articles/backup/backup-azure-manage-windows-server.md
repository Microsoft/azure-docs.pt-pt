---
title: Gerir cofres e servidores dos Serviços de Recuperação Azure
description: Neste artigo, aprenda a usar o painel de visão geral do cofre dos Serviços de Recuperação para monitorizar e gerir os cofres dos Serviços de Recuperação.
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 74351d781287d863db8be0fc7d20517e0479106c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89002135"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Monitorizar e gerir cofres dos Serviços de Recuperação

Este artigo explica como usar o painel **de visão geral** do cofre dos Serviços de Recuperação para monitorizar e gerir os cofres dos Serviços de Recuperação. Quando abre um cofre dos Serviços de Recuperação da lista, abre-se o painel **de controlo** geral do cofre selecionado. O painel fornece vários detalhes sobre o cofre. Há *azulejos* que mostram: o estado dos alertas críticos e de alerta, os trabalhos de backup em curso e os empregos de reserva falhados, e a quantidade de armazenamento localmente redundante (LRS) e armazenamento geodu redundante (GRS) utilizados. Se fizer cópias de segurança dos VMs Azure para o cofre, o azulejo [ **de pré-verificação de cópia** de segurança apresenta quaisquer itens críticos ou de aviso](#backup-pre-check-status). A imagem a seguir é o **painel de visão** geral para **o cima da câmara de Contoso.** O **azulejo de Itens de Reserva** mostra que há nove itens registados no cofre.

![Painel de abóbada de serviços de recuperação](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

Os pré-requisitos para este artigo são: uma assinatura Azure, um cofre dos Serviços de Recuperação, e que há pelo menos um item de reserva configurado para o cofre.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="open-a-recovery-services-vault"></a>Abra um cofre dos Serviços de Recuperação

Para monitorizar alertas, ou ver dados de gestão sobre um cofre dos Serviços de Recuperação, abra o cofre.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com a sua subscrição do Azure.

2. No portal, selecione **Todos os serviços**.

   ![Lista aberta de cofres dos Serviços de Recuperação passo 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. Na caixa de diálogo **de todos os serviços,** tipo **Serviços de Recuperação.** À medida que começa a escrever, a lista filtra com base na sua entrada. Quando aparecer a opção **de cofres dos Serviços de Recuperação,** selecione-a para abrir a lista de cofres dos Serviços de Recuperação na sua subscrição.

    ![Criar serviços de recuperação passo 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Da lista de cofres, selecione um cofre para abrir o painel **de visão** geral.

    ![Painel de abóbada de serviços de recuperação](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    O painel de visão geral utiliza azulejos para fornecer alertas e dados de trabalho de backup.

## <a name="monitor-backup-jobs-and-alerts"></a>Monitorize trabalhos e alertas de backup

O painel **overview** do cofre dos Serviços de Recuperação fornece azulejos para informações de monitorização e utilização. Os azulejos da secção de monitorização apresentam alertas críticos e de aviso, e em curso e trabalhos falhados. Selecione um alerta ou trabalho específico para abrir o menu Alertas de Reserva ou Backup Jobs, filtrado para esse trabalho ou alerta.

![Tarefas de painel de backup](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

A secção de Monitorização mostra os resultados de **alertas** de backup predefinidos e consultas **de trabalhos de backup.** Os azulejos de monitorização fornecem informações atualizadas sobre:

* Alertas críticos e de alerta para trabalhos de backup (nas últimas 24 horas)
* Pré-verificação do estado de VMs Azure. Para obter informações completas sobre o estado de verificação prévia, consulte [o estado de pré-verificação de cópias de segurança](#backup-pre-check-status).
* Os empregos de reserva em curso, e os empregos que falharam (nas últimas 24 horas).

Os azulejos de utilização fornecem:

* O número de itens de reserva configurados para o cofre.
* O armazenamento Azure (separado por LRS e GRS) consumido pelo cofre.

Selecione os azulejos (exceto o Backup Storage) para abrir o menu associado. Na imagem acima, o azulejo de alerta de cópia de segurança mostra três alertas críticos. Selecionando a linha de alertas Críticos no azulejo alertas de cópia de segurança, abre os Alertas de Cópia de Segurança filtrados para alertas críticos.

![Menu de alertas de backup filtrado para alertas críticos](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

O menu Alertas de Cópia de Segurança, na imagem acima, é filtrado por: Status is Ative, Severity is Critical, e o tempo é das 24 horas anteriores.

### <a name="backup-pre-check-status"></a>Estado de pré-verificação de backup

Pré-Verificação de cópias de segurança Verifique a configuração dos seus VMs para problemas que possam afetar negativamente as cópias de segurança. Agregam estas informações para que possa vê-la diretamente do painel de abóbada dos Serviços de Recuperação e fornecer recomendações para medidas corretivas para garantir cópias de segurança consistentes com sucesso em ficheiros ou de aplicações. Não necessitam de infraestruturas e não têm custos adicionais.  

Os Pré-Verificações de Backup funcionam como parte das operações de backup programadas para os seus VMs Azure. Concluem com um dos seguintes estados:

* **Aprovado**: Este estado indica que a configuração do seu VM deve levar a cópias de segurança bem sucedidas e não é necessário tomar nenhuma ação corretiva.
* **Aviso:** Este estado indica um ou mais problemas na configuração do VM que *podem* levar a falhas de backup. Fornece medidas *recomendadas* para garantir cópias de segurança bem sucedidas. Por exemplo, não ter o mais recente Agente VM instalado pode fazer com que as cópias de segurança falhem intermitentemente. Esta situação proporcionará um estado de alerta.
* **Crítico**: Este estado indica um ou mais problemas críticos na configuração do VM que *levarão* a falhas de backup e fornece as medidas *necessárias* para garantir cópias de segurança bem sucedidas. Por exemplo, um problema de rede causado por uma atualização das regras NSG de um VM, irá causar falhas nas cópias de segurança, uma vez que impede o VM de comunicar com o serviço de Backup Azure. Esta situação proporcionará um estado crítico.

Siga os passos abaixo para começar a resolver quaisquer problemas reportados por Backup Pré-Verificações de cópias de segurança VM no seu cofre de Serviços de Recuperação.

* Selecione o **azulejo pré-verificação de cópia de segurança (Azure VMs)** no painel de abóbada dos Serviços de Recuperação.
* Selecione qualquer VM com um pré-verificação de cópia de segurança de **um crítico** ou **aviso**. Esta ação abrirá o painel de detalhes do **VM.**
* Selecione a notificação do painel na parte superior do painel para revelar a descrição do problema de configuração e os passos de reparação.

## <a name="manage-backup-alerts"></a>Gerir alertas de backup

Para aceder ao menu Alertas de Cópia de Segurança, no menu do cofre dos Serviços de Recuperação, selecione **Alertas de Cópia de Segurança**.

![Alertas de cópias de segurança](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

O relatório de alertas de reserva lista os alertas para o cofre.

![Relatório de alertas de backup](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Alertas

A lista de Alertas de Cópia de Segurança apresenta as informações selecionadas para os alertas filtrados. No menu Alertas de Cópia de Segurança, pode filtrar para alertas críticos ou de aviso.

| Nível de Alerta | Eventos que geram alertas |
| ----------- | ----------- |
| Crítico | Recebe alertas críticos quando: Os trabalhos de backup falham, os trabalhos de recuperação falham e quando paras a proteção num servidor, mas retém os dados.|
| Aviso | Recebe alertas de aviso quando: Trabalhos de reserva completos com avisos. Por exemplo, quando menos de 100 ficheiros não são apoiados por problemas de corrupção, ou quando mais de 1.000.000 ficheiros são apoiados com sucesso. |
| Informativo | atualmente, não estão a ser utilizados alertas informativos. |

### <a name="viewing-alert-details"></a>Visualizar detalhes do alerta

O relatório backup alerts rastreia oito detalhes sobre cada alerta. Utilize o botão **Escolha colunas** para editar os detalhes no relatório.

![Alertas de backup escolhem botão de colunas](./media/backup-azure-manage-windows-server/backup-alerts.png)

Por padrão, todos os detalhes, exceto **a última hora de ocorrência,** aparecem no relatório.

* Alerta
* Item de Cópia de Segurança
* Servidor Protegido
* Gravidade
* Duração
* Tempo da Criação
* Estado
* Última hora da ocorrência

### <a name="change-the-details-in-alerts-report"></a>Alterar os detalhes no relatório de alertas

1. Para alterar as informações do relatório, no menu **Alertas de Cópia de Segurança,** **selecione Escolha colunas**.

   ![Selecione escolha colunas](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   O menu **de colunas Choose** abre.

2. No menu **Escolha colunas,** escolha os detalhes que pretende aparecer no relatório.

    ![Escolha o menu de colunas](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. Selecione **Feito** para guardar as suas alterações e fechar o menu Escolha colunas.

   Se escoar alterações, mas não quiser manter as alterações, selecione **Reset** para retornar o selecionado à última configuração guardada.

### <a name="change-the-filter-in-alerts-report"></a>Altere o filtro no relatório de alertas

Utilize o menu **Filtro** para alterar a Severidade, Estado, Hora de Início e Hora de Fim para os alertas.

> [!NOTE]
> A edição do filtro Alertas de Cópia de Segurança não altera os alertas de alerta crítico ou de aviso no painel de controlo do cofre.
>  

1. Para alterar o filtro Alertas de Cópia de Segurança, no menu Alertas de Cópia de Segurança, selecione **Filtro**.

   ![Escolha o menu do filtro](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   O menu Filtro aparece.

   ![Menu de alerta de filtro](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Edite a Severidade, Estado, Hora de Início ou Tempo de fim e selecione **Fazer** para guardar as suas alterações.

## <a name="configuring-notifications-for-alerts"></a>Configurar notificações para alertas

Configure notificações para gerar e-mails quando ocorre um alerta de aviso ou crítico. Pode enviar alertas de e-mail a cada hora, ou quando ocorre um alerta específico.

   ![Alertas de filtro](./media/backup-azure-manage-windows-server/configure-notification.png)

Por predefinição, as notificações por e-mail estão **on**. **Selecione Off** para parar as notificações de e-mail.

No controlo **'Notificar',** escolha **Per Alert** se não quiser agrupamento ou não tiver muitos itens que possam gerar alertas. Cada alerta resulta numa única notificação (a definição predefinição) e um e-mail de resolução é enviado imediatamente.

Se selecionar **Hourly Digest,** é enviado um e-mail aos destinatários explicando os alertas não resolvidos gerados na última hora. Um e-mail de resolução é enviado no final da hora.

Escolha a gravidade do alerta (Crítico ou Aviso) utilizada para gerar e-mail. Atualmente não existem alertas de informação.

## <a name="manage-backup-items"></a>Gerir itens de backup

Um cofre dos Serviços de Recuperação contém muitos tipos de dados de backup. [Saiba mais](backup-overview.md#what-can-i-back-up) sobre o que pode apoiar. Para gerir os vários servidores, computadores, bases de dados e cargas de trabalho, selecione o azulejo **de Itens de Cópia** de Segurança para visualizar o conteúdo do cofre.

![Azulejo de itens de reserva](./media/backup-azure-manage-windows-server/backup-items.png)

A lista de Itens de Backup, organizada pelo Backup Management Type, abre.

![Lista de itens de backup](./media/backup-azure-manage-windows-server/list-backup-items.png)

Para explorar um tipo específico de instância protegida, selecione o item na coluna Tipo de Gestão de Cópia de Segurança. Por exemplo, na imagem acima, existem duas máquinas virtuais Azure protegidas neste cofre. Selecionando **Azure Virtual Machine,** abre a lista de máquinas virtuais protegidas neste cofre.

![Lista de máquinas virtuais protegidas](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

A lista de máquinas virtuais tem dados úteis: o Grupo de Recursos Associado, [o Pré-Check de Cópia de Segurança](#backup-pre-check-status)anterior, o Último Estado de Backup e a data do mais recente Ponto de Restauro. A elipse, na última coluna, abre o menu para desencadear tarefas comuns. Os dados úteis fornecidos nas colunas são diferentes para cada tipo de backup.

![Menu de elipse aberta para tarefas comuns](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Gerir Tarefas de cópia de segurança

O **azulejo backup Jobs** no painel de abóbada mostra o número de empregos que estão em progresso, ou falhados nas últimas 24 horas. O azulejo proporciona um vislumbre do menu Backup Jobs.

![Azulejo de back jobs](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

Para ver detalhes adicionais sobre os trabalhos, selecione **In Progress** ou **Failed** para abrir o menu Backup Jobs filtrado para esse estado.

### <a name="backup-jobs-menu"></a>Menu de trabalhos de backup

O menu **Backup Jobs** apresenta informações sobre o tipo de item, operação, estado, hora de início e duração.  

Para abrir o menu Backup Jobs, no menu principal do cofre, selecione **Backup Jobs**.

![Selecione trabalhos de backup](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

A lista de trabalhos de reserva abre.

![Lista de trabalhos de backup](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

O menu Backup Jobs mostra o estado de todas as operações, em todos os tipos de backup, nas últimas 24 horas. Utilize **o Filtro** para alterar os filtros. Os filtros são explicados nas seguintes secções.

Para mudar os filtros:

1. No menu Backup Jobs do cofre, selecione **Filter**.

   ![Selecione filtro para trabalhos de backup](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    O menu Filtro abre.

   ![Menu de filtro abre para trabalhos de backup](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Escolha as definições do filtro e selecione **Feito**. A lista filtrada atualiza-se com base nas novas definições.

#### <a name="item-type"></a>Tipo de artigo

O tipo de item é o tipo de gestão de backup da instância protegida. Existem quatro tipos; ver a seguinte lista. Pode ver todos os tipos de artigos ou um tipo de artigo. Não é possível selecionar dois ou três tipos de artigos. Os tipos de item disponíveis são:

* Todos os tipos de artigos
* Máquina virtual do Azure
* Ficheiros e pastas
* Storage do Azure
* Carga de trabalho azul

#### <a name="operation"></a>Operação

Pode ver uma operação, ou todas as operações. Não pode selecionar duas ou três operações. As Operações disponíveis são:

* Todas as Operações
* Registar
* Configurar a cópia de segurança
* Cópia de segurança
* Restauro
* Desativar a cópia de segurança
* Eliminar dados de cópia de segurança

#### <a name="status"></a>Estado

Pode ver All Status ou one. Não pode selecionar dois ou três estatutos. Os estatutos disponíveis são:

* Todo o estado
* Concluído
* Em curso
* Com falhas
* Cancelado
* Concluído com avisos

#### <a name="start-time"></a>Hora de início

O dia e a hora que a consulta começa. O padrão é um período de 24 horas.

#### <a name="end-time"></a>Hora de fim

O dia e a hora em que a consulta termina.

### <a name="export-jobs"></a>Empregos de exportação

Utilize **trabalhos de exportação** para criar uma folha de cálculo contendo todas as informações do menu Jobs. A folha de cálculo tem uma folha que contém um resumo de todos os trabalhos, e folhas individuais para cada trabalho.

Para exportar a informação sobre o emprego para uma folha de cálculo, selecione **empregos de exportação.** O serviço cria uma folha de cálculo usando o nome do cofre e data, mas pode alterar o nome.

## <a name="monitor-backup-usage"></a>Monitorize o uso de backup

O azulejo de armazenamento de reserva no painel de instrumentos mostra o armazenamento consumido em Azure. A utilização do armazenamento é prevista para:

* Utilização de armazenamento de LRS em nuvem associada ao cofre
* Utilização de armazenamento cloud GRS associada ao cofre

## <a name="troubleshooting-monitoring-issues"></a>Problemas de monitorização da resolução de problemas

**Emissão:** Empregos e/ou alertas do agente Azure Backup não aparecem no portal.

**Etapas de resolução de problemas:** O processo, ```OBRecoveryServicesManagementAgent``` envia o trabalho e alerta os dados para o serviço de Backup Azure. Ocasionalmente, este processo pode ficar preso ou desligado.

1. Para verificar se o processo não está a decorrer, abra o **Gestor de Tarefas**e ```OBRecoveryServicesManagementAgent``` verifique se está a decorrer.

2. Se o processo não estiver em curso, abra o **Painel de Controlo**e consulte a lista de serviços. Iniciar ou reiniciar o **Agente de Gestão de Serviços de Recuperação do Microsoft Azure**.

    Para mais informações, consulte os registos em:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` Por exemplo:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Passos seguintes

* [Restaurar o Servidor do Windows ou cliente do Windows a partir do Azure](backup-azure-restore-windows-server.md)
* Para saber mais sobre o Azure Backup, consulte [a Visão Geral do Backup da Azure](./backup-overview.md)
