---
title: Utilizar o servidor de cópia de segurança do Azure para fazer backup de um farm do SharePoint para o Azure
description: Utilize o servidor de cópia de segurança do Azure para criar cópias de segurança e restaurar os dados do SharePoint. Este artigo fornece as informações para configurar o seu farm do SharePoint para que os dados desejados podem ser armazenados no Azure. Pode restaurar dados de SharePoint protegidos do disco ou do Azure.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: kasinh
ms.openlocfilehash: 7fa68e11ccac69db9335e589f5048264df9d0a47
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58074986"
---
# <a name="back-up-a-sharepoint-farm-to-azure"></a>Fazer cópia de segurança de um farm do SharePoint para o Azure
Criar cópias de segurança um farm do SharePoint para o Microsoft Azure com o servidor de cópia de segurança do Azure (MABS) da Microsoft no quase da mesma forma que efetue cópias de segurança de outras origens de dados. O Azure Backup fornece flexibilidade na programação de cópia de segurança para criar diariamente, pontos de cópia de segurança semana, mensal ou anual e dá-lhe opções de política de retenção para vários pontos de cópia de segurança. Ele também fornece a capacidade para armazenar cópias de disco local para rápida objetivos de tempo de recuperação (RTO) e para armazenar cópias para o Azure para retenção económica de longo prazo.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Versões suportadas do SharePoint e o relacionadas com cenários de proteção
Cópia de segurança do Azure para o DPM suporta os seguintes cenários:

| Carga de trabalho | Versão | Implantação do SharePoint | Proteção e recuperação |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3.0 |SharePoint implementado como um servidor físico ou máquina virtual de Hyper-V/VMware <br> -------------- <br> SQL AlwaysOn | Proteger as opções de recuperação do Farm do SharePoint: Farm de recuperação, a base de dados e o ficheiro ou item de lista de pontos de recuperação do disco.  Recuperação de farm e base de dados de pontos de recuperação do Azure. |

## <a name="before-you-start"></a>Antes de começar
Existem algumas coisas que precisa para confirmar antes de cópia de segurança um farm do SharePoint para o Azure.

### <a name="prerequisites"></a>Pré-requisitos
Antes de continuar, certifique-se de que tenha [instalado e preparar o servidor de cópia de segurança do Azure](backup-azure-microsoft-azure-backup.md) para proteger cargas de trabalho.

### <a name="protection-agent"></a>Agente de proteção
O agente de cópia de segurança do Azure tem de estar instalado no servidor que está a executar o SharePoint, os servidores que estejam a executar o SQL Server e todos os outros servidores que fazem parte do farm do SharePoint. Para obter mais informações sobre como configurar o agente de proteção, consulte [configuração do agente de proteção](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  A única exceção é que instale o agente apenas no servidor web único front-end (WFE). O Azure Backup Server precisa do agente num servidor WFE, apenas para servir de ponto de entrada para proteção.

### <a name="sharepoint-farm"></a>Farm do SharePoint
Para cada 10 milhões de itens no farm, tem de existir pelo menos 2 GB de espaço no volume onde está localizada na pasta do MABS. Este espaço é necessário para a geração do catálogo. Para o MABS recuperar itens específicos (conjuntos de sites, sites, listas, bibliotecas de documentos, pastas, documentos individuais e itens de lista), a geração do catálogo cria uma lista dos URLs que estão contidos dentro de cada banco de dados de conteúdo. Pode ver a lista de URLs no painel item recuperável na **recuperação** área da consola do administrador do MABS de tarefas.

### <a name="sql-server"></a>SQL Server
O servidor de cópia de segurança do Azure é executado como uma conta LocalSystem. Para fazer backup de bancos de dados SQL, MABS precisa de privilégios sysadmin nessa conta para o servidor que está a executar o SQL Server. Defina NT AUTHORITY\SYSTEM como *sysadmin* no servidor que está a executar o SQL Server antes de fazer um backup.

Se o farm do SharePoint tem bases de dados do SQL Server que estão configuradas com aliases do SQL Server, instale os componentes de cliente do SQL Server no servidor Web front-end que irá proteger o MABS.

### <a name="sharepoint-server"></a>SharePoint Server
Embora o desempenho depende de vários fatores, como o tamanho do farm do SharePoint, um MABS pode proteger um farm do SharePoint 25 TB como orientação geral.

### <a name="whats-not-supported"></a>O que não é suportado
* MABS que protege um farm do SharePoint não protege os índices de pesquisa ou bases de dados de serviço de aplicações. Terá de configurar a proteção destas bases de dados separadamente.
* MABS não fornece cópia de segurança de bases de dados do SQL Server do SharePoint que também se encontram em partilhas de servidor (SOFS) de ficheiros de escalamento horizontal.

## <a name="configure-sharepoint-protection"></a>Configurar a proteção do SharePoint
Antes de poder utilizar o MABS para proteger o SharePoint, tem de configurar o serviço de escritor de VSS do SharePoint (serviço de escritor WSS), utilizando **ConfigureSharePoint.exe**.

Pode encontrar **ConfigureSharePoint.exe** na pasta \bin [caminho de instalação de MABS] no servidor web front-end. Esta ferramenta disponibiliza o agente de proteção com as credenciais do farm do SharePoint. Executá-lo num único servidor WFE. Se tiver múltiplos servidores WFE, selecione apenas um quando configurar um grupo de proteção.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Para configurar o serviço de escritor VSS do SharePoint
1. No servidor WFE, numa linha de comando, aceda a \bin\. [localização da instalação de MABS]
2. Enter ConfigureSharePoint -EnableSharePointProtection.
3. Introduza as credenciais de administrador do farm. Esta conta deve ser um membro do grupo de administrador local no servidor WFE. Se o administrador do farm não for local administrador conceda as seguintes permissões no servidor WFE:
   * Conceda o controle total do grupo WSS_Admin_WPG da pasta (% Program Files%\Microsoft Azure Backup\DPM).
   * Conceda o acesso de leitura de grupo WSS_Admin_WPG para a chave de registo do DPM (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Terá de voltar a executar ConfigureSharePoint.exe sempre que houver uma alteração nas credenciais de administrador do farm do SharePoint.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Fazer cópias de segurança de um farm do SharePoint com o MABS
Depois de ter configurado o MABS e o farm do SharePoint como explicado anteriormente, o SharePoint pode ser protegido pelo MABS.

### <a name="to-protect-a-sharepoint-farm"></a>Para proteger um farm do SharePoint
1. Partir do **proteção** separador da consola do administrador do MABS, clique em **New**.
    ![Novo separador de proteção](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Sobre o **selecionar tipo de grupo de proteção** página do **criar novo grupo de proteção** assistente, selecione **servidores**e, em seguida, clique em **seguinte**.

    ![Tipo de selecionar o grupo de proteção](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Sobre o **selecionar membros do grupo** ecrã, selecione a caixa de verificação para o servidor do SharePoint que pretende proteger e clique em **próxima**.

    ![Selecionar Membros do grupo](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Com o agente de proteção instalado, pode ver o servidor no assistente. MABS também mostra sua estrutura. Uma vez que executou ConfigureSharePoint.exe, MABS se comunica com o serviço de escritor VSS do SharePoint e respetivas bases de dados do SQL Server correspondentes e reconhece a estrutura de farm do SharePoint, as bases de dados de conteúdo associados e quaisquer itens correspondentes.
   >
   >
4. Na **selecionar método de proteção de dados** página, introduza o nome da **grupo de proteção**e selecione sua preferencial *métodos de proteção*. Clique em **Seguinte**.

    ![Selecionar método de proteção de dados](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > O método de proteção de disco ajuda a cumprir os objetivos de tempo de recuperação de curtos.
   >
   >
5. Na **especificar objetivos a curto prazo** , selecione seu preferencial **período de retenção** e identificar quando pretender que as cópias de segurança ocorrer.

    ![Especificar objetivos de curto prazo](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Como recuperação com mais frequência é necessária para os dados que é inferior a cinco dias de antiguidade, podemos selecionar um período de retenção de cinco dias no disco e certificar-se de que a cópia de segurança ocorre durante o horário de não produção, para este exemplo.
   >
   >
6. Examine o agrupamento de armazenamento atribuído para o grupo de proteção de espaço em disco e, em seguida, clique **seguinte**.
7. Para cada grupo de proteção, o MABS aloca espaço em disco para armazenar e gerir réplicas. Neste momento, MABS tem de criar uma cópia dos dados selecionados. Selecione como e quando pretende a réplica criada e, em seguida, clique em **seguinte**.

    ![Escolher método de criação de réplica](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Para certificar-se de que o tráfego de rede não seja afetado, selecione uma hora fora do horário de produção.
   >
   >
8. MABS garante a integridade dos dados ao realizar verificações de consistência na réplica. Existem duas opções disponíveis. Pode definir uma agenda a executar verificações de consistência ou DPM pode executar verificações de consistência automaticamente na réplica sempre que ele se torna inconsistente. Selecione a sua opção preferida e, em seguida, clique em **seguinte**.

    ![Verificação de consistência](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Sobre o **especificar dados da proteção Online** , selecione o farm do SharePoint que pretende proteger e, em seguida, clique em **próxima**.

    ![O DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Sobre o **Especificar agenda de cópia de segurança Online** página, selecione o horário da sua preferência e, em seguida, clique em **próxima**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > MABS fornece um máximo de duas cópias de segurança diárias para o Azure desde o, em seguida, disponível mais recente cópia de segurança ponto de disco. O Azure Backup também pode controlar a quantidade de largura de banda WAN, que pode ser utilizada para cópias de segurança em horas de pico e das horas de pico utilizando [otimização de rede de cópia de segurança do Azure](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. Consoante a agenda de cópia de segurança que selecionou, diante da **especificar política de retenção Online** , selecione a política de retenção para os pontos de cópia de segurança diárias, semanais, mensais e anuais.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > MABS utiliza um esquema de retenção de avô-pai-filho em que pode ser escolhida uma política de retenção diferentes para diferentes pontos de cópia de segurança.
    >
    >
12. Semelhante ao disco, uma réplica de ponto de referência inicial tem de ser criada no Azure. Selecione a sua opção preferida para criar uma cópia de segurança inicial para o Azure e, em seguida, clique em **seguinte**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Reveja as definições selecionadas no **resumo** página e, em seguida, clique em **criar grupo**. Verá uma mensagem de êxito depois de ter sido criado o grupo de proteção.

    ![Resumo](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Restaurar um item do SharePoint a partir de disco com o MABS
No exemplo a seguir, o *item do SharePoint recuperar* foi eliminado acidentalmente e precisam de ser recuperados.
![MABS SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Abra o **consola de administrador do DPM**. Todos os farms do SharePoint que estão protegidos pelo DPM são mostrados na **proteção** separador.

    ![MABS SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Para começar a recuperar o item, selecione o **recuperação** separador.

    ![MABS SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Pode pesquisar o SharePoint para *item do SharePoint recuperar* utilizando uma pesquisa com base no caráter universal dentro de uma recuperação do ponto de intervalo.

    ![MABS SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Selecione o ponto de recuperação apropriadas nos resultados da pesquisa, clique com o botão direito do item e, em seguida, selecione **recuperar**.
5. Também pode procurar por meio de vários pontos de recuperação e selecione uma base de dados ou o item a recuperar. Selecione **data > tempo de recuperação**e, em seguida, selecione o correto **base de dados > farm do SharePoint > ponto de recuperação > Item**.

    ![MABS SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Com o botão direito do item e, em seguida, selecione **recuperar** para abrir o **Assistente de recuperação**. Clique em **Seguinte**.

    ![Rever seleção de recuperação](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Selecione o tipo de recuperação que pretende executar e, em seguida, clique em **seguinte**.

    ![Tipo de recuperação](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > A seleção de **recuperar original** no exemplo recupera o item para o site do SharePoint original.
   >
   >
8. Selecione o **processo de recuperação** que pretende utilizar.

   * Selecione **recuperar sem utilizar um farm de recuperação** se o farm do SharePoint não foi alterado e é o mesmo que o ponto de recuperação que está a ser restaurado.
   * Selecione **recuperar através de um farm de recuperação** se o farm do SharePoint foi alterada desde que o ponto de recuperação foi criado.

     ![Processo de recuperação](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Forneça uma localização de instância do SQL Server transição temporariamente a recuperar a base de dados e fornecer uma partilha de ficheiros de teste no MABS e no servidor que está a executar o SharePoint para recuperar o item.

    ![Location1 de teste](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS anexa a base de dados de conteúdo que está a alojar o item do SharePoint para a instância do SQL Server temporário. A partir da base de dados de conteúdo, ele recupera o item e coloca-o na localização do ficheiro de preparação em MABS. O item recuperado, que agora está na localização de preparação tem de ser exportado para a localização de transição no farm do SharePoint.

    ![Location2 de teste](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Selecione **especificar as opções de recuperação**e aplicar definições de segurança para o farm do SharePoint ou aplicar as definições de segurança dos pontos de recuperação. Clique em **Seguinte**.

    ![Opções de recuperação](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Pode optar por limitar a utilização de largura de banda de rede. Isso minimiza o impacto para o servidor de produção durante o horário de produção.
    >
    >
11. Reveja as informações de resumo e, em seguida, clique em **recuperar** para iniciar a recuperação do ficheiro.

    ![Resumida de recuperação](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Agora, selecione o **monitorização** separador a **consola do administrador do MABS** para ver o **estado** da recuperação.

    ![Estado de recuperação](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > O ficheiro agora é restaurado. Pode atualizar o site do SharePoint para verificar o arquivo restaurado.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Restaurar uma base de dados do SharePoint a partir do Azure por meio do DPM
1. Para recuperar um banco de dados do SharePoint, navegar em vários pontos de recuperação (como mostrado anteriormente) e selecione o ponto de recuperação que pretende restaurar.

    ![MABS SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Clique duas vezes o ponto de recuperação do SharePoint para mostrar as informações de catálogo disponíveis do SharePoint.

   > [!NOTE]
   > Dado que o farm do SharePoint está protegido para retenção a longo prazo no Azure, não existem informações de catálogo (metadados) estão disponíveis em MABS. Como resultado, sempre que um banco de dados ponto anterior no tempo do conteúdo do SharePoint precisa de ser recuperados, terá do farm do SharePoint do catálogo novamente.
   >
   >
3. Clique em **recatalogação**.

    ![MABS SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    O **Cloud Recatalogar** é aberta a janela de estado.

    ![MABS SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Depois de catalogação estiver concluída, o estado muda para *êxito*. Clique em **Fechar**.

    ![MABS SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Clique no objeto do SharePoint mostrado o MABS **recuperação** separador para obter a estrutura de base de dados de conteúdo. O item com o botão direito e, em seguida, clique em **recuperar**.

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Neste momento, siga os passos de recuperação anteriormente no artigo para recuperar um banco de dados do SharePoint a partir do disco.

## <a name="faqs"></a>FAQs
P: Posso recuperar um item do SharePoint para a localização original se o SharePoint estiver configurado com o SQL AlwaysOn (com a proteção no disco)?<br>
R: Sim, o item pode ser recuperado para o site do SharePoint original.

P: Posso recuperar uma base de dados do SharePoint para a localização original se o SharePoint estiver configurado com o AlwaysOn de SQL?<br>
R: Como os bancos de dados do SharePoint são configurados no SQL AlwaysOn, não é possível modificar a menos que o grupo de disponibilidade é removido. Como resultado, o MABS não é possível restaurar uma base de dados para a localização original. Pode recuperar uma base de dados do SQL Server para outra instância do SQL Server.

## <a name="next-steps"></a>Próximos Passos

Consulte a [cópia de segurança do Exchange server](backup-azure-exchange-mabs.md) artigo.
Consulte a [cópia de segurança do SQL Server](backup-azure-sql-mabs.md) artigo.
