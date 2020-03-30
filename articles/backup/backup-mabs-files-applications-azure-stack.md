---
title: Back up ficheiros em VMs De Stack Azure
description: Utilize o Backup Azure para fazer backup e recuperar ficheiros e aplicações do Azure Stack para o ambiente do Seu Stack Azure.
ms.topic: conceptual
ms.date: 06/05/2018
ms.openlocfilehash: 2bcdf7f720708db1487d7d5cdaee41dc93c05728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172324"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Back up ficheiros e aplicações em Azure Stack

Pode utilizar o Azure Backup para proteger (ou fazer backup) ficheiros e aplicações no Azure Stack. Para fazer backup de ficheiros e aplicações, instale o Microsoft Azure Backup Server como uma máquina virtual em funcionamento no Azure Stack. Pode proteger os ficheiros em qualquer servidor do Azure Stack na mesma rede virtual. Depois de ter instalado o Azure Backup Server, adicione discos Azure para aumentar o armazenamento local disponível para dados de backup de curto prazo. O Azure Backup Server utiliza o armazenamento Azure para retenção a longo prazo.

> [!NOTE]
> Embora o Servidor de Backup Azure e o System Center Data Protection Manager (DPM) sejam semelhantes, o DPM não é suportado para utilização com o Azure Stack.
>

Este artigo não cobre a instalação do Servidor de Backup Azure no ambiente Azure Stack. Para instalar o Servidor de Backup Azure no Azure Stack, consulte o artigo, [instalando o Servidor](backup-mabs-install-azure-stack.md)de Backup Azure .

## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Back up Ficheiros e Pastas em VMs de Stack Azure para Azure

Para configurar o Servidor de Backup Azure para proteger ficheiros em máquinas virtuais Do Empilh Apiná-lo, abra a consola do Servidor de Backup Azure. Utilizará a consola para configurar grupos de proteção e proteger os dados das suas máquinas virtuais.

1. Na consola Do Servidor de Backup Azure, clique em **Proteção** e na barra de ferramentas, clique em **Novo** para abrir o assistente do **Create New Protection Group.**

   ![Configure a proteção na consola do Servidor de Backup Azure](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Pode levar alguns segundos para o assistente abrir. Assim que o assistente abrir, clique em **Next** para avançar para o ecrã **Select Protection Group Type.**

   ![Novo assistente de grupo de proteção abre](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. No ecrã **Select Protection Group Type,** escolha **servidores** e clique em **Seguinte**.

    ![Novo assistente de grupo de proteção abre](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    O ecrã **Select Group Members** abre.

    ![Novo assistente de grupo de proteção abre](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. No ecrã Select Group **+** **Members,** clique para expandir a lista de subitems. Para todos os itens que pretende proteger, selecione a caixa de verificação. Uma vez selecionados todos os itens, clique em **Next**.

    ![Novo assistente de grupo de proteção abre](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    A Microsoft recomenda a colocação de todos os dados que partilhem uma política de proteção num único grupo de proteção. Para obter informações completas sobre o planeamento e implantação de grupos de proteção, consulte o artigo DPM do Centro de Sistema, [Deploy Protection Groups](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801).

4. No ecrã **Select Data Protection Method,** digite um nome para o grupo de proteção. Selecione a caixa de verificação para **que eu pretenda proteção de curto prazo usando:** e **eu quero proteção on-line**. Clique em **Seguinte**.

    ![Novo assistente de grupo de proteção abre](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Para selecionar **que quero proteção on-line,** primeiro deve selecionar **que quero proteção de curto prazo usando:** Disco. O Azure Backup Server não protege a fita, pelo que o disco é a única opção para proteção a curto prazo.

5. No ecrã **'Especificar Objetivos de Curto Prazo',** escolha quanto tempo para reter os pontos de recuperação guardados no disco e quando guardar backups incrementais. Clique em **Seguinte**.

    > [!IMPORTANT]
    > Não **deverá** reter dados de recuperação operacional (backup) em discos ligados ao Servidor de Backup Azure durante mais de cinco dias.
    >

    ![Novo assistente de grupo de proteção abre](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png)

    Em vez de selecionar um intervalo para backups incrementais, para executar uma cópia de segurança expressa antes de cada ponto de recuperação programado, clique um pouco antes de um ponto de **recuperação**. Se estiver a proteger as cargas de trabalho da aplicação, o Azure Backup Server cria pontos de recuperação de acordo com o calendário de frequências de sincronização (desde que a aplicação suporte backups incrementais). Se a aplicação não suportar cópias de segurança incrementais, o Azure Backup Server executa uma cópia de segurança completa expressa.

    Para **os pontos**de recuperação do Ficheiro, especifique quando criar pontos de recuperação. Clique em **Modificar** para definir os horários e dias da semana quando os pontos de recuperação forem criados.

6. No ecrã de atribuição de **discos de revisão,** reveja o espaço de disco de armazenamento atribuído ao grupo de proteção.

    **O tamanho total** de dados é o tamanho dos dados que pretende fazer backup e o **espaço do Disco a fornecer** no Servidor de Backup Azure é o espaço recomendado para o grupo de proteção. O Azure Backup Server escolhe o volume de backup ideal, com base nas definições. No entanto, pode editar as opções de cópia de segurança de volume em Detalhes de atribuição do disco. Para cargas de trabalho, selecione o armazenamento preferido no menu de lista pendente. As edições alteram os valores para Armazenamento Total e Armazenamento Gratuito no painel Armazenamento em Disco Disponível. Espaço subprovisionado é a quantidade de armazenamento O Servidor de Backup Azure sugere que adicione ao volume, para continuar com as cópias de segurança sem problemas no futuro.

7. Em Escolha o método de **criação**de réplicas, selecione como pretende lidar com a replicação inicial de dados completos. Se decidir replicar-se sobre a rede, o Azure recomenda que escolha um tempo fora do pico. Para grandes quantidades de dados ou menos do que as condições ideais de rede, considere replicar os dados utilizando meios amovíveis.

8. Em **Selecionar opções de verificação de consistência**, selecione como pretende automatizar as verificações de consistência. Permitir que os controlos de consistência sejam executados apenas quando a replicação de dados se tornar inconsistente, ou de acordo com um calendário. Se não quiser configurar a verificação automática de consistência, faça uma verificação manual a qualquer momento:
    * Na área de **Proteção** da consola Do Servidor de Backup Azure, clique no grupo de proteção e selecione **'Verificação de consistência de desempenho**.

9. Se optar por fazer o back-up do Azure, na página de dados de **proteção online Especte,** certifique-se de que as cargas de trabalho que pretende fazer de reserva para o Azure são selecionadas.

10. Em **especificar o calendário de cópias de segurança on-line,** especifique quando devem ocorrer cópias de segurança incrementais para o Azure.

    Pode agendar as cópias de segurança para serem executadas todos os dias/semanas/meses/anos, bem como a data e hora de execução. As cópias de segurança podem ocorrer, no máximo, duas vezes por dia. Cada vez que um trabalho de backup é executado, um ponto de recuperação de dados é criado em Azure a partir da cópia dos dados de backup armazenados no disco Do Servidor de Backup Azure.

11. Em especificar a política de **retenção on-line,** especifique como os pontos de recuperação criados a partir das cópias de segurança diárias/semanais/mensais/anuais são mantidos no Azure.

12. Em **Choose replicação on-line,** especifique como ocorre a replicação completa inicial dos dados.

13. Em **Resumo,** reveja as suas definições. Quando clica em **Criar Grupo,** a replicação inicial de dados ocorre. Quando a replicação de dados termina, na página **'Estado',** o estado do grupo de proteção mostra como **OK**. O trabalho inicial de backup ocorre de acordo com as definições do grupo de proteção.

## <a name="recover-file-data"></a>Recuperar dados de ficheiros

Utilize a consola Do Servidor de Backup Azure para recuperar dados da sua máquina virtual.

1. Na consola Azure Backup Server, na barra de navegação, clique em **Recovery** e procure os dados que pretende recuperar. No painel de resultados, selecione os dados.

2. No calendário da secção de pontos de recuperação, as datas em negrito indicam que os pontos de recuperação estão disponíveis. Selecione a data para recuperar.

3. No painel de **itens recuperável,** selecione o item que pretende recuperar.

4. No painel **Ações**, clique em **Recuperar** para abrir o Assistente de Recuperação.

5. Pode recuperar dados da seguinte forma:

    * **Recuperar para a localização original** - Se o computador cliente estiver ligado através da VPN, esta opção não funciona. Em vez disso, utilize uma localização alternativa e, em seguida, copie dados desse local.
    * **Recuperar para um local alternativo**

6. Especifique as opções de recuperação:

    * Para **o comportamento de recuperação**da versão existente, selecione Criar **cópia,** **Saltar**ou **Sobreescrever**. A sobreescrita só está disponível quando se recupera para a localização original.
    * Para **restaurar a segurança,** escolha **as definições de aplicar o computador de destino** ou aplique as **definições de segurança da versão do ponto de recuperação**.
    * Para a aceleração da largura de banda da **rede,** clique em **Modificar** para ativar o estrangulamento da largura de banda da rede.
    * **Notificação** Clique **Enviar um e-mail quando a recuperação estiver concluída**e especificar os destinatários que receberão a notificação. Separe os endereços de correio eletrónico por vírgulas.
    * Depois de fazer as seleções, clique **em Next**

7. Reveja as definições de recuperação e clique em **Recuperar**.

    >[!Note]
    >Enquanto o trabalho de recuperação está em andamento, todos os trabalhos de sincronização para os itens de recuperação selecionados são cancelados.

Se estiver a utilizar o Armazenamento de Backup Moderno (MBS), a recuperação do utilizador final do File Server (EUR) não é suportada. O File Server EUR tem uma dependência do Serviço de Cópia selada de Volume Sombra (VSS), que o Armazenamento de Cópia de Cópia de Cópia moderna não utiliza. Se o EUR estiver ativado, utilize as seguintes medidas para recuperar dados:

1. Navegue para os ficheiros protegidos e clique no nome do ficheiro e selecione **Propriedades**.

2. No menu **Propriedades,** clique em **Versões Anteriores** e escolha a versão que pretende recuperar.

## <a name="view-azure-backup-server-with-a-vault"></a>Ver servidor de backup Azure com um cofre

Para ver as entidades do Servidor de Backup Azure no portal Azure, pode seguir os seguintes passos:

1. Cofre dos Serviços de Recuperação Aberto.
2. Clique em Infraestrutura de Backup.
3. Ver Servidores de Gestão de Backup.

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre a utilização do Servidor de Backup Azure para proteger outras cargas de trabalho, consulte um dos seguintes artigos:

* [Back up SharePoint farm](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
* [Criar uma cópia de segurança do SQL Server](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
