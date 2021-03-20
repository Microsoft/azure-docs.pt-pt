---
title: Fazer o back up em VMs Azure Stack
description: Utilize o Azure Backup para fazer backup e recuperar ficheiros e aplicações do Azure Stack para o ambiente Azure Stack.
ms.topic: conceptual
ms.date: 06/05/2018
ms.openlocfilehash: cae95c10c510969cc0553a54a506789d6be427d7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89180988"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Fazer o back up ficheiros e aplicações no Azure Stack

Pode utilizar o Azure Backup para proteger (ou fazer backup) ficheiros e aplicações no Azure Stack. Para fazer backup de ficheiros e aplicações, instale o Microsoft Azure Backup Server como uma máquina virtual em funcionamento no Azure Stack. Pode proteger os ficheiros de qualquer servidor Azure Stack na mesma rede virtual. Depois de instalar o Azure Backup Server, adicione discos Azure para aumentar o armazenamento local disponível para dados de backup de curto prazo. O Azure Backup Server utiliza o armazenamento Azure para retenção a longo prazo.

> [!NOTE]
> Embora o Azure Backup Server e o System Center Data Protection Manager (DPM) sejam semelhantes, o DPM não é suportado para uso com Azure Stack.
>

Este artigo não cobre a instalação do Azure Backup Server no ambiente Azure Stack. Para instalar o Servidor de Backup Azure no Azure Stack, consulte o artigo, instalando o [Servidor de Backup do Azure](backup-mabs-install-azure-stack.md).

## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Fazer o back up ficheiros e pastas em VMs Azure Stack para Azure

Para configurar o Servidor de Backup Azure para proteger ficheiros em máquinas virtuais Azure Stack, abra a consola Azure Backup Server. Utilizará a consola para configurar grupos de proteção e proteger os dados das suas máquinas virtuais.

1. Na consola Azure Backup Server, selecione **Proteção** e na barra de ferramentas, selecione **Novo** para abrir o assistente **do Grupo criar novas proteções.**

   ![Proteção de configuração na consola Azure Backup Server](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Pode levar alguns segundos para o assistente abrir. Assim que o assistente abrir, selecione **Seguinte** para avançar para o ecrã **Select Protection Group Type.**

   ![Novo assistente do grupo de proteção abre](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. No ecrã **Select Protection Group Type,** escolha **Servidores** e selecione **Seguinte**.

    ![Selecione tipo de grupo de proteção](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    O ecrã **select group members** abre.

    ![Selecione membros do grupo](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. No ecrã **Select Group Members,** selecione **+** para expandir a lista de subitems. Para todos os itens que pretende proteger, selecione a caixa de verificação. Uma vez selecionados todos os itens, selecione **Next**.

    ![Selecione cada item que pretende proteger](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    A Microsoft recomenda colocar todos os dados que partilhem uma política de proteção num só grupo de proteção. Para obter informações completas sobre o planeamento e implantação de grupos de proteção, consulte o artigo DPM do Centro de Sistema, [Grupos de Proteção de Implantação](/system-center/dpm/create-dpm-protection-groups).

4. No ecrã **Select Data Protection Method,** escreva um nome para o grupo de proteção. Selecione a caixa de verificação para **que eu queira proteção a curto prazo usando:** e **eu quero proteção on-line**. Selecione **Seguinte**.

    ![Selecione método de proteção de dados](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Para selecionar **quero proteção on-line,** primeiro deve selecionar **Que quero proteção a curto prazo utilizando:** Disco. O Azure Backup Server não protege a fita, por isso o disco é a única opção para proteção a curto prazo.

5. No ecrã **'Objetivos Short-Term' especifique,** escolha quanto tempo para reter os pontos de recuperação guardados no disco e quando guardar cópias de segurança incrementais. Selecione **Seguinte**.

    > [!IMPORTANT]
    > **Não** deve reter dados de recuperação operacional (backup) em discos ligados ao Servidor de Backup Azure durante mais de cinco dias.
    >

    ![Especificar Short-Term Objetivos](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png)

    Em vez de selecionar um intervalo para cópias de segurança incrementais, para executar uma cópia de segurança completa expressa pouco antes de cada ponto de recuperação programado, selecione **Just before a recovery point**. Se estiver a proteger as cargas de trabalho da aplicação, o Azure Backup Server cria pontos de recuperação de acordo com o calendário de frequência de sincronização (desde que a aplicação suporte cópias de segurança incrementais). Se a aplicação não suportar cópias de segurança incrementais, o Azure Backup Server executa uma cópia de segurança expressa completa.

    Para **pontos de recuperação de ficheiros,** especifique quando criar pontos de recuperação. Selecione **Modificar** para definir os horários e dias da semana quando os pontos de recuperação forem criados.

6. No ecrã de atribuição do **disco Review,** reveja o espaço do disco de armazenamento atribuído ao grupo de proteção.

    **O tamanho total** dos dados é o tamanho dos dados que pretende fazer e o **espaço do disco a ser a provisionado** no Azure Backup Server é o espaço recomendado para o grupo de proteção. O Azure Backup Server escolhe o volume de backup ideal, com base nas definições. No entanto, pode editar as opções de cópia de segurança de volume em Detalhes de atribuição do disco. Para cargas de trabalho, selecione o armazenamento preferido no menu de lista pendente. As edições alteram os valores para Armazenamento Total e Armazenamento Gratuito no painel Armazenamento em Disco Disponível. Espaço subprovisionado é a quantidade de armazenamento que o Azure Backup Server sugere que adicione ao volume, para continuar com cópias de segurança suavemente no futuro.

7. No **método de criação de réplicas,** selecione como pretende lidar com a replicação completa inicial dos dados. Se decidir replicar-se sobre a rede, o Azure recomenda que escolha um tempo fora do pico. Para grandes quantidades de dados ou menos do que as condições ideais de rede, considere replicar os dados utilizando suportes amovíveis.

8. Em **Selecionar opções de verificação de consistência**, selecione como pretende automatizar as verificações de consistência. Permitir que os controlos de consistência só serão executados quando a replicação de dados se tornar inconsistente, ou de acordo com um calendário. Se não quiser configurar a verificação automática de consistência, faça uma verificação manual a qualquer momento:
    * Na área de **Proteção** da consola Azure Backup Server, clique com o botão direito no grupo de proteção e selecione **'Executar verificação de consistência'**.

9. Se optar por voltar ao Azure, na página **de dados de proteção online Specify** certifique-se de que as cargas de trabalho que pretende fazer até ao Azure estão selecionadas.

10. No **programa de backup on-line ,** especifique quando devem ocorrer cópias de segurança incrementais para a Azure.

    Pode agendar as cópias de segurança para serem executadas todos os dias/semanas/meses/anos, bem como a data e hora de execução. As cópias de segurança podem ocorrer, no máximo, duas vezes por dia. Cada vez que um trabalho de backup é executado, um ponto de recuperação de dados é criado em Azure a partir da cópia dos dados de backup armazenados no disco Azure Backup Server.

11. Na **política de retenção on-line,** especifique como os pontos de recuperação criados a partir das cópias de segurança diárias/semanais/mensais são mantidos em Azure.

12. In **Escolha a replicação on-line**, especifique como ocorre a replicação completa inicial dos dados.

13. Em **Resumo,** reveja as suas definições. Quando seleciona **Criar Grupo,** ocorre a replicação inicial dos dados. Quando a replicação de dados termina, na página **'Estado',** o estado do grupo de proteção mostra-se **OK**. O trabalho inicial de backup ocorre de acordo com as definições do grupo de proteção.

## <a name="recover-file-data"></a>Recuperar dados de ficheiros

Utilize a consola Azure Backup Server para recuperar dados para a sua máquina virtual.

1. Na consola Azure Backup Server, na barra de navegação, selecione **Recovery** e navegue pelos dados que pretende recuperar. No painel de resultados, selecione os dados.

2. No calendário na secção pontos de recuperação, as datas em negrito indicam que os pontos de recuperação estão disponíveis. Selecione a data para recuperar.

3. No painel **de artigos recuperável,** selecione o item que pretende recuperar.

4. No painel **de Ações,** selecione **Recuperar** para abrir o Assistente de Recuperação.

5. Pode recuperar dados da seguinte forma:

    * **Recuperar para a localização original** - Se o computador cliente estiver ligado através da VPN, esta opção não funciona. Em vez disso, use uma localização alternativa e, em seguida, copie dados desse local.
    * **Recuperar para um local alternativo**

6. Especificar as opções de recuperação:

    * Para **o comportamento de recuperação da versão existente**, selecione Criar **cópia,** **saltar** ou **substituir**. O overwrite só está disponível quando se recupera para o local original.
    * Para **restaurar a segurança,** escolha **Aplicar as definições do computador de destino** ou aplicar as **definições de segurança da versão ponto de recuperação**.
    * Para **o estrangulamento da largura de banda da rede,** selecione **Modificar** para permitir o estrangulamento da largura de banda da rede.
    * **Notificação** Selecione **Enviar um e-mail quando a recuperação estiver concluída** e especificar os destinatários que receberão a notificação. Separe os endereços de correio eletrónico por vírgulas.
    * Depois de fazer as seleções, selecione **Next**

7. Reveja as definições de recuperação e selecione **Recuperar**.

    >[!Note]
    >Enquanto o trabalho de recuperação está em curso, todos os trabalhos de sincronização para os itens de recuperação selecionados são cancelados.

Se estiver a utilizar o Modern Backup Storage (MBS), a recuperação do utilizador final do Servidor de Ficheiros (EUR) não é suportada. O File Server EUR tem uma dependência do Serviço de Cópia Sombra de Volume (VSS), que o Armazenamento de Backup Moderno não utiliza. Se o EUR estiver ativado, utilize as seguintes medidas para recuperar dados:

1. Navegue para os ficheiros protegidos e clique com o nome do ficheiro e selecione **Propriedades**.

2. No menu **Propriedades,** selecione **Versões Anteriores** e escolha a versão que pretende recuperar.

## <a name="view-azure-backup-server-with-a-vault"></a>Ver Servidor de Backup Azure com um cofre

Para ver as entidades do Servidor backup Azure no portal Azure, pode seguir os seguintes passos:

1. Cofre dos Serviços de Recuperação Abertos.
2. Selecione infraestrutura de backup.
3. Ver Servidores de Gestão de Cópias de Segurança.

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre a utilização do Azure Backup Server para proteger outras cargas de trabalho, consulte um dos seguintes artigos:

* [Back up SharePoint farm](./backup-mabs-sharepoint-azure-stack.md)
* [Criar uma cópia de segurança do SQL Server](./backup-mabs-sql-azure-stack.md)
