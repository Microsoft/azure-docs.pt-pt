---
title: De volta a uma fazenda SharePoint para Azure com MABS
description: Utilize o Servidor de Backup Azure para fazer backup e restaurar os seus dados do SharePoint. Este artigo fornece as informações para configurar a sua quinta SharePoint para que os dados desejados possam ser armazenados em Azure. Pode restaurar os dados do SharePoint protegidos a partir do disco ou do Azure.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 441a896f2faa67a1380007ebb9474d7c311a4842
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78673127"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-mabs"></a>De volta a uma fazenda SharePoint para Azure com MABS

Você faz backup de uma fazenda SharePoint para o Microsoft Azure usando o Microsoft Azure Backup Server (MABS) da mesma forma que você faz backup de outras fontes de dados. O Azure Backup oferece flexibilidade na programação de backup para criar pontos de backup diários, semanais, mensais ou anuais e oferece opções de política de retenção para vários pontos de backup. Também fornece a capacidade de armazenar cópias de discos locais para objetivos de tempo de recuperação rápida (RTO) e de armazenar cópias para o Azure para retenção económica e a longo prazo.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>O SharePoint suportaversões suportadas e cenários de proteção conexos

O Backup Azure para DPM suporta os seguintes cenários:

| Carga de trabalho | Versão | Implementação do SharePoint | Proteção e recuperação |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3.0 |SharePoint implantado como servidor físico ou máquina virtual Hyper-V/VMware <br> -------------- <br> SQL AlwaysOn | Proteja as opções de recuperação da Fazenda SharePoint: Fazenda de recuperação, base de dados e ficheiro ou item de lista a partir de pontos de recuperação do disco.  Recuperação de quinta e base de dados dos pontos de recuperação do Azure. |

## <a name="before-you-start"></a>Antes de começar

Há algumas coisas que precisa confirmar antes de voltar a uma fazenda SharePoint para Azure.

### <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, certifique-se de que [instalou e preparou o Servidor](backup-azure-microsoft-azure-backup.md) de Backup Azure para proteger as cargas de trabalho.

### <a name="protection-agent"></a>Agente de proteção

O agente de backup Azure deve ser instalado no servidor que está a executar o SharePoint, os servidores que estão a executar o SQL Server e todos os outros servidores que fazem parte da quinta do SharePoint. Para obter mais informações sobre como configurar o agente de proteção, consulte o Agente de Proteção de [Configuração](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019).  A única exceção é que você instala o agente apenas num único servidor frontal web (WFE). O Azure Backup Server precisa do agente num servidor WFE apenas para servir como ponto de entrada para proteção.

### <a name="sharepoint-farm"></a>Farm do SharePoint

Por cada 10 milhões de itens na exploração, deve haver pelo menos 2 GB de espaço no volume onde está localizada a pasta MABS. Este espaço é necessário para a geração do catálogo. Para que o MABS recupere itens específicos (coleções de sites, sites, listas, bibliotecas de documentos, pastas, documentos individuais e itens de lista), a geração de catálogos cria uma lista dos URLs que estão contidos em cada base de dados de conteúdos. Pode visualizar a lista de URLs no painel de itens recuperável na área de tarefa de **recuperação** da Consola de Administrador MABS.

### <a name="sql-server"></a>SQL Server

O Azure Backup Server funciona como uma conta LocalSystem. Para fazer o back up das bases de dados do SQL Server, o MABS precisa de privilégios de sysadmin nessa conta para o servidor que está a executar o Servidor SQL. Desloque o SISTEMA NT AUTHORITY\SYSTEM para *sinsadmina* no servidor que está a executar o SQL Server antes de o fazer.

Se a exploração do SharePoint tiver bases de dados do SQL Server que estejam configuradas com pseudónimos do SQL Server, instale os componentes do cliente do Servidor SQL no servidor Web frontal que o MABS irá proteger.

### <a name="sharepoint-server"></a>SharePoint Server

Embora o desempenho dependa de muitos fatores, como o tamanho da fazenda SharePoint, como orientação geral um MABS pode proteger uma fazenda sharePoint de 25 TB.

### <a name="whats-not-supported"></a>O que não é suportado

* O MABS que protege uma quinta do SharePoint não protege índices de pesquisa ou bases de dados de serviços de aplicação. Terá de configurar separadamente a proteção destas bases de dados.
* O MABS não fornece cópias de segurança das bases de dados do SharePoint SQL Server que estão alojadas em ações do servidor de ficheiros scale-out (SOFS).

## <a name="configure-sharepoint-protection"></a>Configure a proteção SharePoint

Antes de poder utilizar o MABS para proteger o SharePoint, tem de configurar o serviço SharePoint VSS Writer (serviço WSS Writer) utilizando **o ConfigureSharePoint.exe**.

Pode encontrar **ConfigureSharePoint.exe** na pasta [MABS Installation Path]\bin no servidor web frontal. Esta ferramenta fornece ao agente de proteção as credenciais para a exploração SharePoint. Executa-o num único servidor WFE. Se tiver vários servidores WFE, selecione apenas um quando configurar um grupo de proteção.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Para configurar o serviço SharePoint VSS Writer

1. No servidor WFE, a uma solicitação de comando, vá para [localização de instalação MABS]\bin\
2. Introduza configuração -EnableSharePointProtection.
3. Introduza as credenciais de administrador do farm. Esta conta deve ser um membro do grupo de Administrador local no servidor WFE. Se o administrador do farm não for um administrador local, conceda as seguintes permissões no servidor WFE:
   * Conceda o WSS_Admin_WPG controlo total do grupo à pasta DPM (%Program Files%\Microsoft Azure Backup\DPM).
   * Conceda o WSS_Admin_WPG grupo ler o acesso à chave de registo dPM (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Terá de reexecutar configureSharePoint.exe sempre que houver uma alteração nas credenciais de administrador da fazenda SharePoint.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Back up a SharePoint farm usando MABS

Depois de configurar o MABS e a quinta SharePoint, como explicado anteriormente, o SharePoint pode ser protegido por MABS.

### <a name="to-protect-a-sharepoint-farm"></a>Para proteger uma quinta do SharePoint

1. A partir do separador **Proteção** da Consola de Administrador MABS, clique em **New**.
    ![Novo Separador de Proteção](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Na página Do Tipo do Grupo de **Proteção Selecione** do assistente **do Grupo de Proteção Nova,** selecione **Servidores**e, em seguida, clique em **Seguinte**.

    ![Selecione tipo de Grupo de Proteção](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. No ecrã **Select Group Members,** selecione a caixa de verificação do servidor SharePoint que pretende proteger e clique **em Next**.

    ![Selecione membros do grupo](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Com o agente de proteção instalado, pode ver o servidor no assistente. O MABS também mostra a sua estrutura. Uma vez que executou o ConfigureSharePoint.exe, o MABS comunica com o serviço SharePoint VSS Writer e as respetivas bases de dados do SQL Server e reconhece a estrutura agrícola sharePoint, as bases de dados de conteúdo associados e quaisquer itens correspondentes.
   >
   >
4. Na página **Select Data Protection Method,** introduza o nome do Grupo de **Proteção**e selecione os seus métodos de *proteção preferidos*. Clique em **Seguinte**.

    ![Selecionar método de proteção de dados](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > O método de proteção do disco ajuda a cumprir os objetivos de curto prazo de recuperação.
   >
   >
5. Na página **'Especificar Objetivos de Curto Prazo',** selecione a sua gama de **Retenção** preferida e identifique-se quando pretende que ocorram backups.

    ![Especificar objetivos de curto prazo](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Como a recuperação é mais frequentemente necessária para dados com menos de cinco dias, selecionamos uma gama de retenção de cinco dias em disco e garantimos que a cópia de segurança acontece durante o horário de não produção, por exemplo.
   >
   >
6. Reveja o espaço de disco de armazenamento atribuído ao grupo de proteção e clique em **seguida.**
7. Para cada grupo de proteção, a MABS aloca espaço em disco para armazenar e gerir réplicas. Neste ponto, o MABS deve criar uma cópia dos dados selecionados. Selecione como e quando quiser a réplica criada e, em seguida, clique em **Next**.

    ![Escolha método de criação de réplicas](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Para garantir que o tráfego da rede não seja efetuado, selecione um tempo fora do horário de produção.
   >
   >
8. O MABS garante a integridade dos dados através da realização de controlos de consistência na réplica. Há duas opções disponíveis. Pode definir um horário para executar verificações de consistência, ou DPM pode executar verificações de consistência automaticamente na réplica sempre que se tornar inconsistente. Selecione a sua opção preferida e, em seguida, clique em **Seguinte**.

    ![Verificação de consistência](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Na página Dedados de **Proteção Online Especificar,** selecione a quinta SharePoint que pretende proteger e, em seguida, clique em **Next**.

    ![Proteção de Ponto de Partilha DPM1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Na página **'Agenda', selecione** o seu horário preferido e clique em **Seguinte**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > O MABS fornece um máximo de duas cópias de segurança diárias ao Azure a partir do último ponto de backup do disco disponível. O Azure Backup também pode controlar a quantidade de largura de banda WAN que pode ser usada para backups em horas de pico e off-peak utilizando o estrangulamento da [rede Azure Backup](backup-windows-with-mars-agent.md#enable-network-throttling).
    >
    >
11. Dependendo da programação de backup que selecionou, na página **'Especificar Política de Retenção Online',** selecione a política de retenção para pontos de backup diários, semanais, mensais e anuais.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > O MABS usa um esquema de retenção entre o avô e o pai e o filho, no qual uma política de retenção diferente pode ser escolhida para diferentes pontos de apoio.
    >
    >
12. Semelhante ao disco, uma réplica inicial do ponto de referência precisa de ser criada em Azure. Selecione a sua opção preferida para criar uma cópia de cópia de cópia inicial para O Azure e, em seguida, clique **em Next**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Reveja as definições selecionadas na página **Resumo** e, em seguida, clique em **Criar Grupo**. Verá uma mensagem de sucesso após a criação do grupo de proteção.

    ![Resumo](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Restaurar um item SharePoint a partir do disco utilizando MABS

No exemplo seguinte, o *item Recovery SharePoint* foi acidentalmente eliminado e precisa de ser recuperado.
![Proteção do Ponto de Partilha MABS4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Abra a **consola de administrador dPM**. Todas as explorações sharePoint protegidas por DPM são mostradas no separador **Proteção.**

    ![Proteção do Ponto de Partilha MABS3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Para começar a recuperar o artigo, selecione o separador **Recovery.**

    ![Proteção do Ponto de Partilha MABS5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Pode pesquisar sharePoint para *recuperar o item sharePoint* utilizando uma pesquisa baseada em wildcard dentro de um intervalo de ponto de recuperação.

    ![Proteção do Ponto de Partilha MABS6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Selecione o ponto de recuperação apropriado dos resultados da pesquisa, clique no item para a direita e, em seguida, **selecione Recuperar**.
5. Também pode navegar por vários pontos de recuperação e selecionar uma base de dados ou item para recuperar. Selecione data > tempo de **recuperação**e, em seguida, selecione a base de dados correta **> fazenda SharePoint > ponto**de recuperação > ponto de recuperação .

    ![Proteção do Ponto de Partilha MABS7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Clique no item para a direita e, em seguida, selecione **Recuperar** para abrir o **Assistente de Recuperação**. Clique em **Seguinte**.

    ![Seleção de Recuperação de Avaliação](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Selecione o tipo de recuperação que pretende realizar e, em seguida, clique em **Seguinte**.

    ![Tipo de recuperação](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > A seleção de **Recuperar para original** no exemplo recupera o item para o site original do SharePoint.
   >
   >
8. Selecione o Processo de **Recuperação** que pretende utilizar.

   * Selecione **Recuperar sem utilizar uma quinta de recuperação** se a exploração SharePoint não tiver mudado e for o mesmo que o ponto de recuperação que está a ser restaurado.
   * Selecione Recuperar utilizando uma quinta de **recuperação** se a exploração SharePoint tiver mudado desde que o ponto de recuperação foi criado.

     ![Processo de Recuperação](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Forneça uma localização de instância de sql server de encenação para recuperar temporariamente a base de dados e fornecer uma partilha de ficheiros de encenação no MABS e no servidor que está a executar o SharePoint para recuperar o item.

    ![Localização de encenação1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    O MABS anexa a base de dados de conteúdo que está a hospedar o item SharePoint à instância temporária do Servidor SQL. A partir da base de dados de conteúdos, recupera o item e coloca-o na localização do ficheiro de encenação no MABS. O item recuperado que está no local de preparação precisa agora de ser exportado para o local de paragem na fazenda SharePoint.

    ![Local de encenação2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. **Selecione Especificar opções**de recuperação e aplicar as definições de segurança na exploração do SharePoint ou aplicar as definições de segurança do ponto de recuperação. Clique em **Seguinte**.

    ![Opções de Recuperação](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Pode optar por acelerar o uso da largura de banda da rede. Isto minimiza o impacto no servidor de produção durante o horário de produção.
    >
    >
11. Reveja as informações sumárias e, em seguida, clique em **Recuperar** para iniciar a recuperação do ficheiro.

    ![Resumo da recuperação](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Selecione agora o separador **monitorizador** na Consola de **Administrador MABS** para ver o **estado** da recuperação.

    ![Estado de Recuperação](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > O ficheiro está agora restaurado. Pode atualizar o site sharePoint para verificar o ficheiro restaurado.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Restaurar uma base de dados SharePoint a partir de Azure usando DPM

1. Para recuperar uma base de dados de conteúdo sharePoint, navegue por vários pontos de recuperação (como mostrado anteriormente), e selecione o ponto de recuperação que pretende restaurar.

    ![Proteção do Ponto de Partilha MABS8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Clique duas vezes no ponto de recuperação do SharePoint para mostrar as informações de catálogo do SharePoint disponíveis.

   > [!NOTE]
   > Uma vez que a exploração SharePoint está protegida para retenção a longo prazo em Azure, nenhuma informação de catálogo (metadados) está disponível no MABS. Como resultado, sempre que uma base de dados de conteúdo sharePoint ponto-a-tempo precisa de ser recuperada, precisa de catalogar novamente a quinta SharePoint.
   >
   >
3. Clique em **Re-catalogar**.

    ![Proteção do Ponto de Partilha MABS10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    A janela de estado do **Cloud Recatalog** abre-se.

    ![Proteção do Ponto de Partilha MABS11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Após a catalogação estar terminada, o estado muda para *O Sucesso*. Clique em **Fechar**.

    ![Proteção do Ponto de Partilha MABS12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Clique no objeto SharePoint mostrado no separador **De recuperação** MABS para obter a estrutura da base de dados de conteúdos. Clique no item para a direita e, em seguida, clique em **Recuperar**.

    ![Proteção do Ponto de Partilha MABS13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Neste ponto, siga os passos de recuperação no início deste artigo para recuperar uma base de dados de conteúdo sharePoint a partir do disco.

## <a name="next-steps"></a>Passos seguintes

Consulte o artigo do [servidor Back up Exchange.](backup-azure-exchange-mabs.md)
Consulte o artigo [do SQL Server](backup-azure-sql-mabs.md) de back up.
