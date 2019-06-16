---
title: Gerir servidores e cofres dos serviços de recuperação do Azure
description: Gerir tarefas e alertas num cofre dos serviços de recuperação do Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/21/2018
ms.author: raynew
ms.openlocfilehash: f7d97866bb3ae895f83fff248ceec2e83dd60bf9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66688908"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Monitorizar e gerir cofres dos Serviços de Recuperação

Este artigo explica como utilizar o Cofre dos serviços de recuperação **descrição geral** dashboard para monitorizar e gerir os cofres de serviços de recuperação. Quando abre um cofre dos serviços de recuperação na lista, o **descrição geral** dashboard do cofre selecionado, é aberto. O dashboard fornece vários detalhes sobre o cofre. Existem *mosaicos* que mostram: o estado de críticas e alertas de aviso, tarefas de cópia de segurança em curso e com falha e a quantidade de armazenamento localmente redundante (LRS) e o armazenamento georredundante (GRS) utilizado. Se efetuar cópias de segurança de VMs do Azure para o cofre, o [ **estado de pré-verificação de cópia de segurança** mosaico apresenta todos os itens críticos ou de aviso](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/). A imagem seguinte é o **descrição geral** dashboard para **Contoso-cofre**. O **itens de cópia de segurança** mosaico mostra há nove itens registados no cofre.

![dashboard do Cofre de serviços de recuperação](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

Os pré-requisitos para este artigo são: pelo menos um item de cópia de segurança configurado para o Cofre de é de uma subscrição do Azure e que existe um cofre dos serviços de recuperação.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>Abra um cofre dos serviços de recuperação

Para monitorizar alertas, ou ver os dados de gestão sobre um cofre dos serviços de recuperação, abra o cofre.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com/) com a sua subscrição do Azure.

2. No portal, clique em **todos os serviços**.

   ![Abra a lista de cofres de serviços de recuperação passo 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. Na **todos os serviços** caixa de diálogo, escreva **dos serviços de recuperação**. À medida que começa a escrever, a lista filtra com base na sua entrada. Quando o **cofres dos serviços de recuperação** opção é apresentada, clique nele para abrir a lista de cofres dos serviços de recuperação na sua subscrição.

    ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Na lista de cofres, clique num cofre para abrir o respetivo **descrição geral** dashboard.

    ![dashboard do Cofre de serviços de recuperação](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    O dashboard de descrição geral utiliza mosaicos para fornecer alertas e dados da tarefa de cópia de segurança.

## <a name="monitor-backup-jobs-and-alerts"></a>Monitorizar tarefas de cópia de segurança e alertas

O Cofre dos serviços de recuperação **descrição geral** dashboard fornece mosaicos para informações de monitorização e a utilização. Os mosaicos na exibição de secção monitorização crítico e alertas de aviso e, em curso e tarefas com falhas. Clique numa tarefa para abrir o menu de alertas de cópia de segurança ou tarefas de cópia de segurança, filtrado para essa tarefa ou um alerta ou alerta específico.

![Tarefas do dashboard de cópia de segurança](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

A secção de monitorização mostra os resultados de predefinidos **alertas de cópia de segurança** e **as tarefas de cópia de segurança** consultas. Os mosaicos de monitorização fornecem informações atualizadas sobre:

* Alertas de críticas e de aviso para tarefas de cópia de segurança (nas últimas 24 horas)
* Estado de pré-verificação para VMs do Azure - para obter informações completas sobre o estado de pré-verificação, consulte a [blogue de cópia de segurança no estado de pré-verificação de cópia de segurança](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/).
* As tarefas de cópia de segurança em curso e tarefas que falharam (nas últimas 24 horas).

Fornecem os mosaicos de utilização:

* O número de itens de cópia de segurança configurados para o cofre.
* O armazenamento do Azure (separado por LRS e GRS) consumido por cofre.

Clique em mosaicos (exceto o armazenamento de cópia de segurança) para abrir o menu associado. Na imagem acima, o mosaico alertas de cópia de segurança mostra três alertas críticos. Clicar na linha de alertas críticos no mosaico alertas de cópia de segurança, abre-se os alertas de cópia de segurança filtrada para os alertas críticos.

![Menu de alertas de cópia de segurança filtrada para os alertas críticos](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

O menu de alertas de cópia de segurança, na imagem acima, é filtrado por: Estado está ativa, é de gravidade crítica e tempo é de 24 horas anteriores.

## <a name="manage-backup-alerts"></a>Gerir alertas de cópia de segurança

Para aceder ao menu de alertas de cópia de segurança, no menu do cofre dos serviços de recuperação, clique em **alertas de cópia de segurança**.

![Alertas de cópia de segurança](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

O relatório de alertas de cópia de segurança apresenta os alertas para o cofre.

![Alertas de cópia de segurança](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Alertas

A lista de alertas de cópia de segurança apresenta as informações selecionadas para os alertas filtrados. No menu alertas de cópia de segurança, pode filtrar alertas críticas ou de aviso.

| Nível de alerta | Eventos que geram alertas |
| ----------- | ----------- |
| Crítico | Receber críticas de alertas quando: As tarefas de cópia de segurança falhar, as tarefas de recuperação a falharem, e quando parar a proteção num servidor, mas manter os dados.|
| Aviso | Recebe o aviso de alertas quando: Tarefas de cópia de segurança concluída com avisos, por exemplo quando menos de 100 ficheiros não são uma cópia de segurança devido a problemas de Corrupção, ou quando maior que 1 000 000 ficheiros são guardados com êxito). |
| Informativo | Atualmente, não existem alertas informativos estão em utilização. |

### <a name="viewing-alert-details"></a>Visualizar detalhes do alerta

O relatório de alertas de cópia de segurança controla os oito detalhes sobre cada alerta. Utilize o **escolher colunas** botão para editar os detalhes no relatório.

![Alertas de cópia de segurança](./media/backup-azure-manage-windows-server/backup-alerts.png)

Por predefinição, todos os detalhes, exceto **hora da última ocorrência**, são apresentados no relatório.

* Alerta
* Item de cópia de segurança
* Servidor protegido
* Gravidade
* Duração
* Hora de criação
* Estado
* Hora da última ocorrência

### <a name="change-the-details-in-alerts-report"></a>Alterar os detalhes no relatório de alertas

1. Para alterar as informações de relatório, o **alertas de cópia de segurança** menu, clique em **escolher colunas**.

   ![Alertas de cópia de segurança](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   O **escolher colunas** é aberto o menu.

2. Na **escolher colunas** menu, escolha os detalhes que pretende apresentar no relatório.

    ![Escolha o menu de colunas](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. Clique em **feito** para guardar as alterações e fechar o menu de colunas de escolha.

   Se efetuar alterações, mas não quer manter as alterações, clique em **repor** retornar o selecionado para a última Guardar configuração.

### <a name="change-the-filter-in-alerts-report"></a>Alterar o filtro no relatório de alertas

Utilize o **filtro** menu para alterar a gravidade, estado, a hora de início e a hora de fim para os alertas.

> [!NOTE]
> Editar os alertas de cópia de segurança do filtro não altera os alertas críticas ou de aviso do dashboard de descrição geral do cofre.
>  

1. Para alterar o filtro de alertas de cópia de segurança, no menu alertas de cópia de segurança, clique em **filtro**.

   ![Escolha o menu de filtro](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   É apresentado o menu de filtro.

   ![Escolha o menu de filtro](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Editar a gravidade, estado, a hora de início ou a hora de fim e clique em **feito** para guardar as alterações.

## <a name="configuring-notifications-for-alerts"></a>Configurar notificações de alertas

Configure notificações para gerar mensagens de correio eletrónico quando ocorrer um alerta de aviso ou crítico. Pode enviar alertas por e-mail a cada hora, ou quando ocorre um alerta específico.

   ![Filtrar alertas](./media/backup-azure-manage-windows-server/configure-notification.png)

Por predefinição, as notificações de E-Mail estão **no**. Clique em **desativar** para parar as notificações por e-mail.

Na **notificar** controlar, escolha **por alerta** se não quiser agrupamento ou não tem muitos itens que podem gerar alertas. Cada alerta resulta numa notificação (a predefinição) e é enviado um e-mail de resolução imediatamente.

Se selecionou **resumo de hora a hora**, é enviado um e-mail aos destinatários que explica os alertas não resolvidos gerados na última hora. É enviada uma mensagem de e-mail de resolução no fim da hora.

Escolha a gravidade do alerta (crítico ou de aviso) utilizada para gerar o e-mail. Atualmente, existem não existem alertas de informações.

## <a name="manage-backup-items"></a>Gerir itens de cópia de segurança

Um cofre dos serviços de recuperação contém muitos tipos de dados de cópia de segurança. [Saiba mais](backup-overview.md#what-can-i-back-up) sobre o que pode criar cópias de segurança. Para gerir vários servidores, computadores, bases de dados e cargas de trabalho, clique a **itens de cópia de segurança** mosaico para ver os conteúdos do cofre.

![Mosaico de itens de cópia de segurança](./media/backup-azure-manage-windows-server/backup-items.png)

A lista de itens de cópia de segurança, organizados por tipo de gestão de cópia de segurança, é aberto.

![lista de itens de cópia de segurança](./media/backup-azure-manage-windows-server/list-backup-items.png)

Para explorar um tipo específico de instância protegida, clique no item na coluna tipo de gestão de cópia de segurança. Por exemplo, na imagem acima, existem duas máquinas virtuais do Azure protegidas neste cofre. Clicar **Máquina Virtual do Azure**, abre-se a lista de máquinas virtuais protegidas neste cofre.

![lista de tipo de cópia de segurança](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

A lista de máquinas virtuais tem dados úteis: o grupo de recursos associados, anterior [pré-verificação de cópia de segurança](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/), estado da última cópia de segurança e a data do ponto de restauro mais recente. O botão de reticências na última coluna, abre o menu para acionar tarefas comuns. Os dados úteis fornecidos nas colunas, é diferente para cada tipo de cópia de segurança.

![lista de tipo de cópia de segurança](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Gerir Tarefas de cópia de segurança

O **tarefas de cópia de segurança** mosaico no dashboard do cofre mostra o número de tarefas que estão em progresso ou com falhas nas últimas 24 horas. O mosaico fornece uma visão rápida no menu tarefas de cópia de segurança.

![Itens de cópia de segurança das definições](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

Para ver detalhes adicionais sobre as tarefas, clique em **em curso** ou **falha** para abrir o menu de tarefas de cópia de segurança filtrado para esse Estado.

### <a name="backup-jobs-menu"></a>Menu de tarefas de cópia de segurança

O **tarefas de cópia de segurança** menu apresenta informações sobre o Item tipo, operação, estado, hora de início e a duração.  

Para abrir o menu de tarefas de cópia de segurança, no menu principal do cofre, clique em **tarefas de cópia de segurança**.

![Itens de cópia de segurança das definições](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

É aberta a lista de tarefas de cópia de segurança.

![Itens de cópia de segurança das definições](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

O menu de tarefas de cópia de segurança mostra o estado para todas as operações, em todos os tipos de cópia de segurança, nas últimas 24 horas. Uso **filtro** para alterar os filtros. Os filtros são explicados nas seções a seguir.

Para alterar os filtros:

1. No menu de tarefas de cópia de segurança do cofre, clique em **filtro**.

   ![Itens de cópia de segurança das definições](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    É aberto o menu de filtro.

   ![Itens de cópia de segurança das definições](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Escolha as definições de filtro e clique em **feito**. As atualizações de lista filtrada com base nas definições de novo.

#### <a name="item-type"></a>Tipo de item

O tipo de Item é o tipo de gestão de cópia de segurança da instância protegida. Há quatro tipos; ver a lista a seguir. Pode ver todos os tipos de item ou tipo de um item. Não é possível selecionar dois ou três tipos de item. Os tipos de Item disponíveis são:

* Todos os tipos de item
* Máquina virtual do Azure
* Ficheiros e pastas
* Storage do Azure
* Carga de trabalho do Azure

#### <a name="operation"></a>Operação

Pode ver uma operação ou todas as operações. Não é possível selecionar dois ou três operações. As operações disponíveis são:

* Todas as Operações
* Registar
* Configurar a cópia de segurança
* Cópia de segurança
* Restauro
* Desativar cópia de segurança
* Eliminar dados de cópia de segurança

#### <a name="status"></a>Estado

Pode ver todos os Estados ou um. Não é possível selecionar dois ou três Estados. Os Estados disponíveis são:

* Todos os Estados
* Concluído
* Em curso
* Com Falhas
* Cancelado
* Foi concluída com avisos

#### <a name="start-time"></a>Hora de início

O dia e hora em que a consulta começa. A predefinição é um período de 24 horas.

#### <a name="end-time"></a>Hora de fim

O dia e hora quando termina a consulta.

### <a name="export-jobs"></a>Exportar tarefas

Uso **exportar tarefas** para criar uma folha de cálculo que contém todas as informações de menu de tarefas. A folha de cálculo tem uma folha de cálculo que contém um resumo de todas as tarefas e folhas individuais para cada tarefa.

Para exportar as informações de tarefas para uma folha de cálculo, clique em **exportar tarefas**. O serviço cria uma folha de cálculo com o nome do cofre e data, mas pode alterar o nome.

## <a name="monitor-backup-usage"></a>Monitorizar a utilização de cópia de segurança

O mosaico de armazenamento de cópia de segurança no dashboard mostra o armazenamento consumido no Azure. Utilização do armazenamento é fornecida para:

* Utilização do armazenamento na cloud LRS associada ao Cofre
* Utilização do armazenamento na cloud GRS associada ao Cofre


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Quanto tempo demora para o estado da tarefa de agente de cópia de segurança do Azure refletir no portal?
O portal do Azure pode demorar até 15 minutos para refletir o estado da tarefa de agente de cópia de segurança do Azure.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Quando uma tarefa de cópia de segurança falhar, o tempo que demora para emitir um alerta?
Um alerta é gerado dentro de 20 minutos da falha de cópia de segurança do Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Existe um caso onde não ser enviado um e-mail se estiverem configuradas notificações?
Sim. Nas seguintes situações, não são enviadas notificações.

* Se as notificações estão configuradas por hora e um alerta for emitido e resolvido dentro da hora
* Quando uma tarefa é cancelada
* Se uma segunda tarefa de cópia de segurança falhar porque a tarefa de cópia de segurança original está em curso

## <a name="troubleshooting-monitoring-issues"></a>Resolução de problemas de monitorização

**Problema:** Tarefas de e/ou alertas a partir do agente de cópia de segurança do Azure não aparecem no portal.

**Passos de resolução de problemas:** O processo, ```OBRecoveryServicesManagementAgent```, envia os dados de tarefa e alerta para o serviço de cópia de segurança do Azure. Ocasionalmente, este processo pode ficar bloqueado ou encerramento.

1. Para verificar o processo não está em execução, abra **Gerenciador de tarefas**e verificar ```OBRecoveryServicesManagementAgent``` está em execução.

2. Se o processo não está em execução, abra **painel de controlo**e procure na lista de serviços. Iniciar ou reiniciar **agente de gestão de serviços de recuperação do Microsoft Azure**.

    Para obter mais informações, procure os registos em:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` Por exemplo:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Passos Seguintes
* [Restaurar o Windows Server ou cliente Windows do Azure](backup-azure-restore-windows-server.md)
* Para saber mais sobre o Azure Backup, consulte [descrição geral de cópia de segurança do Azure](backup-introduction-to-azure-backup.md)
* Visite o [fórum de cópia de segurança do Azure](https://go.microsoft.com/fwlink/p/?LinkId=290933)
