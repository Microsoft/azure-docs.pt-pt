---
title: Fazer backup de um farm do SharePoint no Azure Stack
description: Use Servidor de Backup do Azure para fazer backup e restaurar os dados do SharePoint no Azure Stack. Este artigo fornece as informações para configurar seu farm do SharePoint para que os dados desejados possam ser armazenados no Azure. Você pode restaurar dados protegidos do SharePoint do disco ou do Azure.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: dacurwin
ms.openlocfilehash: c15f3f899b856cb715406bf0e2f95914cd58cca0
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091675"
---
# <a name="back-up-a-sharepoint-farm-on-azure-stack"></a>Fazer backup de um farm do SharePoint no Azure Stack

Você faz backup de um farm do SharePoint em Azure Stack para Microsoft Azure usando o servidor Backup do Microsoft Azure (MABS) da mesma maneira que faz o backup de outras fontes de dados. O backup do Azure fornece flexibilidade no agendamento de backup para criar pontos de backup diários, semanais, mensais ou anuais e fornece opções de política de retenção para vários pontos de backup. Ele também fornece a capacidade de armazenar cópias de disco locais para RTO (objetivos de tempo de recuperação) rápidos e armazenar cópias no Azure para uma retenção econômica e de longo prazo.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Versões com suporte do SharePoint e cenários de proteção relacionados

O backup do Azure para MABS dá suporte aos seguintes cenários:

| Carga de trabalho | Versão | Implantação do SharePoint | Proteção e recuperação |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010 |SharePoint implantado como uma máquina virtual Azure Stack <br> -------------- <br> AlwaysOn do SQL | Proteger opções de recuperação de farm do SharePoint: farm de recuperação, banco de dados e arquivo ou item de lista de pontos de recuperação de disco.  Recuperação de farm e banco de dados de pontos de recuperação do Azure. |

## <a name="before-you-start"></a>Antes de começar

Há algumas coisas que você precisa confirmar antes de fazer backup de um farm do SharePoint no Azure.

### <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, verifique se você [instalou e preparou o servidor de backup do Azure](backup-mabs-install-azure-stack.md) para proteger as cargas de trabalho.

### <a name="protection-agent"></a>Agente de proteção

O agente de backup do Azure deve ser instalado no servidor que está executando o SharePoint, os servidores que estão executando o SQL Server e todos os outros servidores que fazem parte do farm do SharePoint. Para obter mais informações sobre como configurar o agente de proteção, consulte [Configurar o agente de proteção](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  A única exceção é que você instala o agente somente em um único servidor de front-end da Web (WFE). Servidor de Backup do Azure precisa do agente em um servidor WFE apenas para servir como o ponto de entrada para proteção.

### <a name="sharepoint-farm"></a>Farm do SharePoint

Para cada 10 milhões itens no farm, deve haver pelo menos 2 GB de espaço no volume em que a pasta MABS está localizada. Esse espaço é necessário para a geração de catálogo. Para MABS recuperar itens específicos (conjuntos de sites, sites, listas, bibliotecas de documentos, pastas, documentos individuais e itens de lista), a geração de catálogo cria uma lista das URLs contidas em cada banco de dados de conteúdo. Você pode exibir a lista de URLs no painel Item recuperável na área de tarefa **recuperação** do mAbs console do administrador.

### <a name="sql-server"></a>SQL Server

Servidor de Backup do Azure é executado como uma conta LocalSystem. Para fazer backup de bancos de dados do SQL Server, o MABS precisa de privilégios sysadmin nessa conta para o servidor que está executando o SQL Server. Defina NT AUTHORITY\SYSTEM para *sysadmin* no servidor que está executando SQL Server antes de fazer o backup.

Se o farm do SharePoint tiver SQL Server bancos de dados configurados com aliases de SQL Server, instale os componentes de cliente do SQL Server no servidor Web front-end que o MABS protegerá.

### <a name="whats-not-supported"></a>O que não é suportado

* MABS que protege um farm do SharePoint não protege índices de pesquisa ou bancos de dados de serviço de aplicativo. Você precisará configurar a proteção desses bancos de dados separadamente.
* O MABS não fornece backup de bancos de dados do SharePoint SQL Server hospedados em compartilhamentos de SOFS (servidor de arquivos de escalabilidade horizontal).

## <a name="configure-sharepoint-protection"></a>Configurar a proteção do SharePoint

Antes de usar o MABS para proteger o SharePoint, você deve configurar o Serviço gravador VSS do SharePoint (serviço de gravador do WSS) usando o **ConfigureSharePoint. exe**.

Você pode encontrar **ConfigureSharePoint. exe** na pasta [caminho de instalação do mAbs] \bin no servidor Web front-end. Essa ferramenta fornece o agente de proteção com as credenciais para o farm do SharePoint. Você o executa em um único servidor WFE. Se você tiver vários servidores WFE, selecione apenas um ao configurar um grupo de proteção.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Para configurar o serviço de gravador VSS do SharePoint

1. No servidor WFE, em um prompt de comando, vá para [local de instalação do MABS] \bin\
2. Digite ConfigureSharePoint-EnableSharePointProtection.
3. Insira as credenciais de administrador do farm. Essa conta deve ser um membro do grupo de Administradores local no servidor WFE. Se o administrador do farm não for um administrador local, conceda as seguintes permissões no servidor WFE:
   * Conceda ao grupo de WSS_Admin_WPG controle total para a pasta do DPM (% Program Files%\Microsoft Azure Backup\DPM).
   * Conceda ao grupo de WSS_Admin_WPG acesso de leitura à chave do registro do DPM (HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Você precisará executar novamente o ConfigureSharePoint. exe sempre que houver uma alteração nas credenciais de administrador do farm do SharePoint.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Fazer backup de um farm do SharePoint usando o MABS

Depois de configurar o MABS e o farm do SharePoint, conforme explicado anteriormente, o SharePoint pode ser protegido pelo MABS.

### <a name="to-protect-a-sharepoint-farm"></a>Para proteger um farm do SharePoint

1. Na guia **proteção** do console do administrador mAbs, clique em **novo**.
    ![nova guia proteção](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Na página **Selecionar tipo de grupo de proteção** do assistente para **criar novo grupo de proteção** , selecione **servidores**e clique em **Avançar**.

    ![Selecionar tipo de grupo de proteção](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Na tela **selecionar membros do grupo** , marque a caixa de seleção do servidor do SharePoint que você deseja proteger e clique em **Avançar**.

    ![Selecionar membros do grupo](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Com o agente de proteção instalado, você pode ver o servidor no assistente. MABS também mostra sua estrutura. Como você executou o ConfigureSharePoint. exe, o MABS se comunica com o serviço do gravador VSS do SharePoint e seus bancos de dados de SQL Server correspondentes e reconhece a estrutura do farm do SharePoint, os bancos de dados de conteúdo associados e quaisquer itens correspondentes.
   >
   >
4. Na página **selecionar método de proteção de dados** , insira o nome do **grupo de proteção**e selecione seus métodos de *proteção*preferenciais. Clique em **Seguinte**.

    ![Selecionar método de proteção de dados](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > O método de proteção de disco ajuda a atender aos objetivos de tempo de recuperação curtos.
   >
   >
5. Na página **especificar objetivos de curto prazo** , selecione o período de **retenção**preferencial e identifique quando deseja que os backups ocorram.

    ![Especificar objetivos de curto prazo](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Como a recuperação é geralmente necessária para dados com menos de cinco dias, selecionamos um período de retenção de cinco dias no disco e garantimos que o backup ocorre durante horas de não produção, para este exemplo.
   >
   >
6. Examine o espaço em disco do pool de armazenamento alocado para o grupo de proteção e clique em **Avançar**.
7. Para cada grupo de proteção, o MABS aloca espaço em disco para armazenar e gerenciar réplicas. Neste ponto, o MABS deve criar uma cópia dos dados selecionados. Selecione como e quando deseja que a réplica seja criada e clique em **Avançar**.

    ![Escolher método de criação de réplica](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Para garantir que o tráfego de rede não seja afetado, selecione um horário fora do horário de produção.
   >
   >
8. O MABS garante a integridade dos dados executando verificações de consistência na réplica. Há duas opções disponíveis. Você pode definir uma agenda para executar verificações de consistência ou o DPM pode executar verificações de consistência automaticamente na réplica sempre que ela se tornar inconsistente. Selecione sua opção preferida e clique em **Avançar**.

    ![Verificação de consistência](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Na página **especificar dados de proteção online** , selecione o farm do SharePoint que você deseja proteger e clique em **Avançar**.

    ![Protection1 do SharePoint do DPM](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Na página **especificar agendamento de backup online** , selecione sua agenda preferida e clique em **Avançar**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > O MABS fornece um máximo de dois backups diários para o Azure do ponto de backup em disco mais recente disponível. O backup do Azure também pode controlar a quantidade de largura de banda de WAN que pode ser usada para backups em horários de pico e fora de pico usando a [limitação de rede de backup do Azure](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. Dependendo do agendamento de backup que você selecionou, na página **especificar política de retenção online** , selecione a política de retenção para pontos de backup diários, semanais, mensais e anuais.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > O MABS usa um esquema de retenção de avô-pai-filho no qual uma política de retenção diferente pode ser escolhida para pontos de backup diferentes.
    >
    >
12. Semelhante ao disco, uma réplica de ponto de referência inicial precisa ser criada no Azure. Selecione a opção preferida para criar uma cópia de backup inicial para o Azure e clique em **Avançar**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Examine as configurações selecionadas na página **Resumo** e clique em **Criar grupo**. Você verá uma mensagem de êxito após a criação do grupo de proteção.

    ![Resumo](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Restaurar um item do SharePoint do disco usando o MABS

No exemplo a seguir, a *recuperação do item do SharePoint* foi acidentalmente excluída e precisa ser recuperada.
![MABS SharePoint protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Abra o **console do administrador do DPM**. Todos os farms do SharePoint protegidos pelo DPM são mostrados na guia **proteção** .

    ![Protection3 SharePoint do MABS](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Para começar a recuperar o item, selecione a guia **recuperação** .

    ![Protection5 SharePoint do MABS](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Você pode pesquisar o SharePoint para *recuperar o item do SharePoint* usando uma pesquisa baseada em curinga dentro de um intervalo de pontos de recuperação.

    ![Protection6 SharePoint do MABS](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Selecione o ponto de recuperação apropriado nos resultados da pesquisa, clique com o botão direito do mouse no item e selecione **recuperar**.
5. Você também pode navegar por vários pontos de recuperação e selecionar um banco de dados ou item a ser recuperado. Selecione **data > hora de recuperação**e, em seguida, selecione o **banco de dados correto > farm do SharePoint > ponto de recuperação > item**.

    ![Protection7 SharePoint do MABS](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Clique com o botão direito do mouse no item e selecione **recuperar** para abrir o **Assistente de recuperação**. Clique em **Seguinte**.

    ![Examinar a seleção de recuperação](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Selecione o tipo de recuperação que você deseja executar e clique em **Avançar**.

    ![Tipo de recuperação](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > A seleção de **recuperar no original** no exemplo recupera o item para o site do SharePoint original.
   >
   >
8. Selecione o **processo de recuperação** que você deseja usar.

   * Selecione **recuperar sem usar um farm de recuperação** se o farm do SharePoint não tiver sido alterado e for o mesmo que o ponto de recuperação que está sendo restaurado.
   * Selecione **recuperar usando um farm de recuperação** se o farm do SharePoint tiver sido alterado desde que o ponto de recuperação foi criado.

     ![Processo de recuperação](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Forneça um local de instância de SQL Server de preparo para recuperar o banco de dados temporariamente e fornecer um compartilhamento de arquivos de preparo no MABS e o servidor que está executando o SharePoint para recuperar o item.

    ![Location1 de preparo](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS anexa o banco de dados de conteúdo que está hospedando o item do SharePoint à instância de SQL Server temporária. No banco de dados de conteúdo, ele recupera o item e o coloca no local do arquivo de preparo em MABS. O item recuperado que está no local de preparo agora precisa ser exportado para o local de preparo no farm do SharePoint.

    ![Location2 de preparo](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Selecione **especificar opções de recuperação**e aplicar configurações de segurança ao farm do SharePoint ou aplicar as configurações de segurança do ponto de recuperação. Clique em **Seguinte**.

    ![Opções de recuperação](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Você pode optar por limitar o uso de largura de banda da rede. Isso minimiza o impacto no servidor de produção durante o horário de produção.
    >
    >
11. Examine as informações de resumo e clique em **recuperar** para iniciar a recuperação do arquivo.

    ![Resumo da recuperação](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Agora, selecione a guia **monitoramento** no **console do administrador mAbs** para exibir o **status** da recuperação.

    ![Status da recuperação](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > O arquivo agora é restaurado. Você pode atualizar o site do SharePoint para verificar o arquivo restaurado.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Restaurar um banco de dados do SharePoint do Azure usando o DPM

1. Para recuperar um banco de dados de conteúdo do SharePoint, navegue por vários pontos de recuperação (conforme mostrado anteriormente) e selecione o ponto de recuperação que deseja restaurar.

    ![Protection8 SharePoint do MABS](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Clique duas vezes no ponto de recuperação do SharePoint para mostrar as informações de catálogo do SharePoint disponíveis.

   > [!NOTE]
   > Como o farm do SharePoint está protegido para retenção de longo prazo no Azure, nenhuma informação de catálogo (metadados) está disponível em MABS. Como resultado, sempre que um banco de dados de conteúdo do SharePoint de ponto no tempo precisar ser recuperado, você precisará catalogar o farm do SharePoint novamente.
   >
   >
3. Clique em **recatalogar**.

    ![Protection10 SharePoint do MABS](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    A janela status de **recatalogação de nuvem** é aberta.

    ![Protection11 SharePoint do MABS](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Depois que a catalogação for concluída, o status será alterado para *êxito*. Clique em **Fechar**.

    ![Protection12 SharePoint do MABS](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Clique no objeto do SharePoint mostrado na guia **recuperação** mAbs para obter a estrutura do banco de dados de conteúdo. Clique com o botão direito do mouse no item e clique em **recuperar**.

    ![Protection13 SharePoint do MABS](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Neste ponto, siga as etapas de recuperação anteriores neste artigo para recuperar um banco de dados de conteúdo do SharePoint do disco.

## <a name="faqs"></a>Perguntas mais frequentes

P: posso recuperar um item do SharePoint no local original se o SharePoint estiver configurado usando o SQL AlwaysOn (com proteção em disco)?<br>
R: Sim, o item pode ser recuperado para o site do SharePoint original.

P: posso recuperar um banco de dados do SharePoint no local original se o SharePoint estiver configurado usando o SQL AlwaysOn?<br>
R: como os bancos de dados do SharePoint são configurados no SQL AlwaysOn, eles não podem ser modificados, a menos que o grupo de disponibilidade seja removido. Como resultado, o MABS não pode restaurar um banco de dados para o local original. Você pode recuperar um banco de dados SQL Server para outra instância do SQL Server.

## <a name="next-steps"></a>Passos seguintes

Consulte o artigo [arquivos e aplicativos de backup](backup-mabs-files-applications-azure-stack.md) .
Consulte o artigo [SQL Server de backup no Azure Stack](backup-mabs-sql-azure-stack.md) .
