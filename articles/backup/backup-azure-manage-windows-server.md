---
title: Gerenciar servidores e cofres dos serviços de recuperação do Azure
description: Gerencie trabalhos e alertas em um cofre dos serviços de recuperação do Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: dacurwin
ms.openlocfilehash: 7e7312f942103125217c1f61ae8fe8007a49529b
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954768"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Monitorizar e gerir cofres dos Serviços de Recuperação

Este artigo explica como usar o painel **visão geral** do cofre dos serviços de recuperação para monitorar e gerenciar seus cofres dos serviços de recuperação. Quando você abre um cofre dos serviços de recuperação na lista, o painel de **visão geral** do cofre selecionado é aberto. O painel fornece vários detalhes sobre o cofre. Há *blocos* que mostram: o status de alertas críticos e de aviso, trabalhos de backup em andamento e com falha e a quantidade de armazenamento com redundância local (LRS) e grs (armazenamento com redundância geográfica) usados. Se você fizer backup de VMs do Azure no cofre, o bloco de [ **status de pré-verificação de backup** exibirá os itens críticos ou de aviso](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/). A imagem a seguir é o painel de **visão geral** do **contoso-Vault**. O bloco **itens de backup** mostra que há nove itens registrados no cofre.

![painel do cofre dos serviços de recuperação](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

Os pré-requisitos para este artigo são: uma assinatura do Azure, um cofre dos serviços de recuperação e que há pelo menos um item de backup configurado para o cofre.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>Abrir um cofre dos serviços de recuperação

Para monitorar alertas ou exibir dados de gerenciamento sobre um cofre dos serviços de recuperação, abra o cofre.

1. Entre no [portal do Azure](https://portal.azure.com/) usando sua assinatura do Azure.

2. No portal, clique em **todos os serviços**.

   ![Abra a lista de cofres dos serviços de recuperação etapa 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. Na caixa de diálogo **todos os serviços** , digite **serviços de recuperação**. À medida que começa a escrever, a lista filtra com base na sua entrada. Quando a opção **cofres dos serviços de recuperação** for exibida, clique nela para abrir a lista de cofres dos serviços de recuperação em sua assinatura.

    ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Na lista de cofres, clique em um cofre para abrir seu painel de **visão geral** .

    ![painel do cofre dos serviços de recuperação](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    O painel Visão geral usa blocos para fornecer alertas e dados de trabalho de backup.

## <a name="monitor-backup-jobs-and-alerts"></a>Monitorar trabalhos e alertas de backup

O painel **visão geral** do cofre dos serviços de recuperação fornece blocos para informações de monitoramento e uso. Os blocos na seção monitoramento exibem alertas críticos e de aviso e, em andamento e trabalhos com falha. Clique em um alerta ou trabalho específico para abrir o menu alertas de backup ou trabalhos de backup, filtrados para esse trabalho ou alerta.

![Tarefas do painel de backup](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

A seção monitoramento mostra os resultados de **alertas de backup** predefinidos e consultas de **trabalhos de backup** . Os blocos de monitoramento fornecem informações atualizadas sobre:

* Alertas críticos e de aviso para trabalhos de backup (nas últimas 24 horas)
* Status de pré-verificação para VMs do Azure – para obter informações completas sobre o status de pré-verificação, consulte o [blog de backup em status de pré-verificação de backup](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/).
* Os trabalhos de backup em andamento e trabalhos que falharam (nas últimas 24 horas).

Os blocos de uso fornecem:

* O número de itens de backup configurados para o cofre.
* O armazenamento do Azure (separado por LRS e GRS) consumido pelo cofre.

Clique nos blocos (exceto armazenamento de backup) para abrir o menu associado. Na imagem acima, o bloco alertas de backup mostra três alertas críticos. Clicar na linha alertas críticos no bloco alertas de backup abre os alertas de backup filtrados para alertas críticos.

![Menu alertas de backup filtrados para alertas críticos](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

O menu alertas de backup, na imagem acima, é filtrado por: O status é ativo, a severidade é crítica e a hora é as 24 horas anteriores.

## <a name="manage-backup-alerts"></a>Gerenciar alertas de backup

Para acessar o menu alertas de backup, no menu cofre de serviços de recuperação, clique em **alertas de backup**.

![Alertas de backup](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

O relatório alertas de backup lista os alertas para o cofre.

![Alertas de backup](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Alertas

A lista alertas de backup exibe as informações selecionadas para os alertas filtrados. No menu alertas de backup, você pode filtrar alertas críticos ou de aviso.

| Nível de alerta | Eventos que geram alertas |
| ----------- | ----------- |
| Crítico | Você receberá alertas críticos quando: Os trabalhos de backup falham, os trabalhos de recuperação falham e quando você interrompe a proteção em um servidor, mas mantém os dados.|
| Aviso | Você recebe alertas de aviso quando: Trabalhos de backup concluídos com avisos, por exemplo, quando menos de 100 arquivos não são submetidos a backup devido a problemas de corrupção ou quando mais de 1 milhão arquivos são submetidos a backup com êxito). |
| Informativo | no momento, nenhum alerta informativo está em uso. |

### <a name="viewing-alert-details"></a>Exibindo detalhes do alerta

O relatório de alertas de backup acompanha oito detalhes sobre cada alerta. Use o botão **escolher colunas** para editar os detalhes no relatório.

![Alertas de backup](./media/backup-azure-manage-windows-server/backup-alerts.png)

Por padrão, todos os detalhes, exceto o **último tempo de ocorrência**, aparecem no relatório.

* Alerta
* Item de backup
* Servidor protegido
* Severity
* Duration
* Hora de criação
* Estado
* Hora da última ocorrência

### <a name="change-the-details-in-alerts-report"></a>Alterar os detalhes no relatório de alertas

1. Para alterar as informações do relatório, no menu **alertas de backup** , clique em **escolher colunas**.

   ![Alertas de backup](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   O menu **escolher colunas** é aberto.

2. No menu **escolher colunas** , escolha os detalhes que você deseja exibir no relatório.

    ![Menu escolher colunas](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. Clique em **concluído** para salvar as alterações e fechar o menu escolher colunas.

   Se você fizer alterações, mas não quiser manter as alterações, clique em **Redefinir** para retornar a selecionada para a última configuração salva.

### <a name="change-the-filter-in-alerts-report"></a>Alterar o filtro no relatório de alertas

Use o menu **Filtrar** para alterar a gravidade, o status, a hora de início e a hora de término dos alertas.

> [!NOTE]
> Editar o filtro de alertas de backup não altera os alertas críticos ou de aviso no painel Visão geral do cofre.
>  

1. Para alterar o filtro de alertas de backup, no menu alertas de backup, clique em **Filtrar**.

   ![Escolher menu de filtro](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   O menu Filtrar é exibido.

   ![Escolher menu de filtro](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Edite a gravidade, o status, a hora de início ou a hora de término e clique em **concluído** para salvar as alterações.

## <a name="configuring-notifications-for-alerts"></a>Configurando notificações para alertas

Configure notificações para gerar emails quando um alerta de aviso ou crítico ocorrer. Você pode enviar alertas por email a cada hora ou quando ocorrer um alerta específico.

   ![Filtrar alertas](./media/backup-azure-manage-windows-server/configure-notification.png)

Por padrão, as notificações poremail estão ativadas. Clique em **desativado** para interromper as notificações por email.

No controle **notificar** , escolha **por alerta** se não desejar agrupar ou não tiver muitos itens que possam gerar alertas. Cada alerta resulta em uma notificação (a configuração padrão) e um email de resolução é enviado imediatamente.

Se você selecionar **Resumo por hora**, um email será enviado aos destinatários explicando os alertas não resolvidos gerados na última hora. Um email de resolução é enviado ao final da hora.

Escolha a severidade do alerta (crítico ou aviso) usada para gerar email. No momento, não há alertas de informações.

## <a name="manage-backup-items"></a>Gerenciar itens de backup

Um cofre dos serviços de recuperação mantém muitos tipos de dados de backup. [Saiba mais](backup-overview.md#what-can-i-back-up) sobre o que você pode fazer backup. Para gerenciar os vários servidores, computadores, bancos de dados e cargas de trabalho, clique no bloco **itens de backup** para exibir o conteúdo do cofre.

![Bloco de itens de backup](./media/backup-azure-manage-windows-server/backup-items.png)

A lista de itens de backup, organizados por tipo de gerenciamento de backup, é aberta.

![lista de itens de backup](./media/backup-azure-manage-windows-server/list-backup-items.png)

Para explorar um tipo específico de instância protegida, clique no item na coluna tipo de gerenciamento de backup. Por exemplo, na imagem acima, há duas máquinas virtuais do Azure protegidas neste cofre. Clicar em **máquina virtual do Azure**abre a lista de máquinas virtuais protegidas neste cofre.

![lista de tipos de backup](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

A lista de máquinas virtuais tem dados úteis: o grupo de recursos associado, [pré-verificação de backup](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/)anterior, status do último backup e a data do ponto de restauração mais recente. As reticências, na última coluna, abrem o menu para disparar tarefas comuns. Os dados úteis fornecidos em colunas são diferentes para cada tipo de backup.

![lista de tipos de backup](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Gerir Tarefas de cópia de segurança

O bloco **trabalhos de backup** no painel do cofre mostra o número de trabalhos que estão em andamento ou que falharam nas últimas 24 horas. O bloco fornece uma visão do menu de trabalhos de backup.

![Fazer backup de itens de configurações](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

Para ver detalhes adicionais sobre os trabalhos, clique **em em andamento** ou **falha** ao abrir o menu trabalhos de backup filtrado para esse estado.

### <a name="backup-jobs-menu"></a>Menu de trabalhos de backup

O menu **trabalhos de backup** exibe informações sobre o tipo de item, a operação, o status, a hora de início e a duração.  

Para abrir o menu trabalhos de backup, no menu principal do cofre, clique em **trabalhos de backup**.

![Fazer backup de itens de configurações](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

A lista de trabalhos de backup é aberta.

![Fazer backup de itens de configurações](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

O menu trabalhos de backup mostra o status de todas as operações, em todos os tipos de backup, nas últimas 24 horas. Use **Filtrar** para alterar os filtros. Os filtros são explicados nas seções a seguir.

Para alterar os filtros:

1. No menu trabalhos de backup do cofre, clique em **Filtrar**.

   ![Fazer backup de itens de configurações](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    O menu Filtrar é aberto.

   ![Fazer backup de itens de configurações](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Escolha as configurações de filtro e clique em **concluído**. A lista filtrada é atualizada com base nas novas configurações.

#### <a name="item-type"></a>Tipo de item

O tipo de item é o tipo de gerenciamento de backup da instância protegida. Há quatro tipos; consulte a lista a seguir. Você pode exibir todos os tipos de item ou um tipo de item. Não é possível selecionar dois ou três tipos de item. Os tipos de item disponíveis são:

* Todos os tipos de item
* Máquina virtual do Azure
* Ficheiros e pastas
* Storage do Azure
* Carga de trabalho do Azure

#### <a name="operation"></a>Operação

Você pode exibir uma operação ou todas as operações. Você não pode selecionar duas ou três operações. As operações disponíveis são:

* Todas as Operações
* Registo
* Configurar a cópia de segurança
* Criar cópia de segurança
* Restaurar
* Desabilitar backup
* Eliminar dados de cópia de segurança

#### <a name="status"></a>Estado

Você pode exibir todo o status ou um. Não é possível selecionar dois ou três status. Os status disponíveis são:

* Todos os status
* Concluído
* Em curso
* Com Falhas
* Cancelado
* Concluído com avisos

#### <a name="start-time"></a>Hora de início

O dia e a hora em que a consulta começa. O padrão é um período de 24 horas.

#### <a name="end-time"></a>Hora de fim

O dia e a hora em que a consulta termina.

### <a name="export-jobs"></a>Exportar tarefas

Use **trabalhos de exportação** para criar uma planilha que contém todas as informações do menu de trabalhos. A planilha tem uma planilha que contém um resumo de todos os trabalhos e planilhas individuais para cada trabalho.

Para exportar as informações de trabalhos para uma planilha, clique em **Exportar trabalhos**. O serviço cria uma planilha usando o nome do cofre e a data, mas você pode alterar o nome.

## <a name="monitor-backup-usage"></a>Monitorar o uso de backup

O bloco armazenamento de backup no painel mostra o armazenamento consumido no Azure. O uso de armazenamento é fornecido para:

* Uso do armazenamento LRS de nuvem associado ao cofre
* Uso do armazenamento GRS de nuvem associado ao cofre


## <a name="troubleshooting-monitoring-issues"></a>Solucionando problemas de monitoramento

**Problema:** Trabalhos e/ou alertas do agente de backup do Azure não aparecem no Portal.

**Etapas de solução de problemas:** O processo, ```OBRecoveryServicesManagementAgent```, envia os dados de alerta e de trabalho para o serviço de backup do Azure. Ocasionalmente, esse processo pode ficar preso ou desligado.

1. Para verificar se o processo não está em execução, abra o Gerenciador ```OBRecoveryServicesManagementAgent``` de **tarefas**e verifique se está em execução.

2. Se o processo não estiver em execução, abra o **painel de controle**e procure a lista de serviços. Inicie ou reinicie **serviços de recuperação do Microsoft Azure agente de gerenciamento**.

    Para obter mais informações, procure os logs em:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*`Por exemplo:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Passos Seguintes
* [Restaurar o Windows Server ou o Windows Client do Azure](backup-azure-restore-windows-server.md)
* Para saber mais sobre o backup do Azure, consulte [visão geral do backup do Azure](backup-introduction-to-azure-backup.md)
* Visite o [Fórum do backup do Azure](https://go.microsoft.com/fwlink/p/?LinkId=290933)
