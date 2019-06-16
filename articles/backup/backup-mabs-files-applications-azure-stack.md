---
title: Fazer uma cópia de segurança de ficheiros no Azure Stack das VMs
description: Utilize o Azure Backup para fazer cópias de segurança e recuperar ficheiros do Azure Stack e aplicativos para o seu ambiente do Azure Stack.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/5/2018
ms.author: adigan
ms.openlocfilehash: 67d79f2aa41bab8a14d693098538d22ffeb05a4e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60848761"
---
# <a name="back-up-files-on-azure-stack"></a>Fazer uma cópia de segurança de ficheiros no Azure Stack
Pode utilizar o Azure Backup para proteger (ou criar cópias de segurança) ficheiros e aplicações no Azure Stack. Para fazer uma cópia de segurança de ficheiros e aplicações, instale o Microsoft Azure Backup Server como uma máquina virtual em execução no Azure Stack. Pode proteger os ficheiros em qualquer servidor do Azure Stack na mesma rede virtual. Assim que tiver instalado o servidor de cópia de segurança do Azure, adicione discos do Azure para aumentar o armazenamento local disponível para os dados de cópia de segurança de curta duração. O servidor de cópia de segurança do Azure utiliza o armazenamento do Azure para retenção a longo prazo.

> [!NOTE]
> Embora o Azure Backup Server e System Center Data Protection Manager (DPM) são semelhantes, o DPM não é suportado para utilização com o Azure Stack.
>

Este artigo não abrange a instalar o Azure Backup Server no ambiente do Azure Stack. Para instalar o Azure Backup Server no Azure Stack, veja o artigo [instalar o Azure Backup Server](backup-mabs-install-azure-stack.md).


## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Fazer cópias de segurança de ficheiros e pastas em VMs do Azure Stack para o Azure

Para configurar o servidor de cópia de segurança do Azure para proteger ficheiros em máquinas de virtuais do Azure Stack, abra a consola do servidor de cópia de segurança do Azure. Vai utilizar a consola para configurar grupos de proteção e para proteger os dados nas suas máquinas virtuais.

1. Na consola do servidor de cópia de segurança do Azure, clique em **proteção** e na barra de ferramentas, clique em **New** para abrir o **criar novo grupo de proteção** assistente.

   ![Configurar a proteção na consola do servidor de cópia de segurança do Azure](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Pode demorar alguns segundos para que o assistente abrir. Depois de abrir o assistente, clique em **próxima** para avançar para o **selecionar tipo de grupo de proteção** ecrã.

   ![Abre o Assistente de novo grupo de proteção](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Sobre o **selecionar tipo de grupo de proteção** ecrã, escolha **servidores** e clique em **seguinte**.

    ![Abre o Assistente de novo grupo de proteção](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    O **selecionar membros do grupo** é aberto o ecrã. 

    ![Abre o Assistente de novo grupo de proteção](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. Na **selecionar membros do grupo** ecrã, clique em **+** para expandir a lista de subitems. Para todos os itens que pretende proteger, selecione a caixa de verificação. Depois de todos os itens foram selecionados, clique em **seguinte**.

    ![Abre o Assistente de novo grupo de proteção](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    A Microsoft recomenda a colocar todos os dados que irão partilhar uma política de proteção, no grupo de proteção de um. Para obter informações completas sobre como planear e implementar grupos de proteção, consulte o artigo da System Center DPM [implementar grupos de proteção](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801).

4. Na **selecionar método de proteção de dados** ecrã, escreva um nome para o grupo de proteção. Selecione a caixa de verificação **pretendo proteção curto prazo utilizando:** e **pretendo proteção online**. Clique em **Seguinte**.

    ![Abre o Assistente de novo grupo de proteção](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Para selecionar **pretendo proteção online**, tem de selecionar primeiro **pretendo proteção curto prazo utilizando:** Disco. O Azure Backup Server não protege em banda, para que o disco é a única opção para proteção de curta duração.

5. Na **especificar objetivos a curto prazo** ecrã, selecione o período de tempo reter os pontos de recuperação salvos em disco e quando guardar cópias de segurança incrementais. Clique em **Seguinte**.

    > [!IMPORTANT]
    > Deve **não** manter os dados de recuperação operacional (cópia de segurança) em discos ligado ao servidor de cópia de segurança do Azure por mais de cinco dias.
    >

    ![Abre o Assistente de novo grupo de proteção](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png) 

    Em vez de selecionar um intervalo para cópias de segurança incrementais, para executar um express agendadas de cópia de segurança completa apenas antes de cada ponto de recuperação, clique em **apenas antes de um ponto de recuperação**. Se estiver a proteger cargas de trabalho de aplicação, o Azure Backup Server cria pontos de recuperação pela agenda da frequência de sincronização (desde que a aplicação suportar cópias de segurança incrementais). Se a aplicação não suporta cópias de segurança incrementais, o servidor de cópia de segurança do Azure é executado um express cópia de segurança completa.

    Para **pontos de recuperação de ficheiros**, especifique quando deve criar pontos de recuperação. Clique em **modificar** para definir as horas e dias da semana quando são criados pontos de recuperação.

6. Na **rever atribuição do disco** ecrã, reveja o espaço de disco de agrupamento de armazenamento atribuído para o grupo de proteção.

    **Tamanho de dados total** é o tamanho dos dados que pretende criar cópias de segurança e **espaço no disco a ser aprovisionado** no servidor de cópia de segurança do Azure é o espaço recomendado para o grupo de proteção. Servidor de cópia de segurança do Azure escolhe o volume de cópia de segurança ideal, com base nas definições. No entanto, pode editar as opções de cópia de segurança de volume nos detalhes de alocação do disco. Para cargas de trabalho, selecione o armazenamento preferido no menu pendente. As edições alteram os valores para o armazenamento Total e livre de armazenamento no painel de armazenamento em disco disponível. O espaço com aprovisionamento a menos é a quantidade de armazenamento que do Azure Backup Server sugere que adicione ao volume, para continuar com cópias de segurança sem problemas no futuro.

7. Na **escolher método de criação de réplica**, selecione como pretende processar a replicação de dados completa inicial. Se optar por replicar na rede, o Azure recomenda que escolher uma hora de ponta. Para grandes quantidades de dados ou condições de rede não ideais, considere replicar os dados usando mídia removível.

8. Na **escolher opções de verificação de consistência**, selecione como pretende automatizar as verificações de consistência. Ative a verificação de consistência ser executado apenas quando a replicação de dados se tornar inconsistente ou segundo uma agenda. Se não quiser configurar a verificação de consistência automática, execute uma verificação manual em qualquer altura por:
    * Na **proteção** área da consola do servidor de cópia de segurança do Azure, com o botão direito do grupo de proteção e selecione **efetuar verificação de consistência**.

9. Se optar por criar cópias de segurança para o Azure, no **especificar dados da proteção online** página Certifique-se de que as cargas de trabalho que pretende criar cópias de segurança para o Azure estão selecionadas.

10. Na **especifique a agenda de cópia de segurança online**, especifique quando as cópias de segurança incrementais para o Azure devem ocorrer. 

    Pode agendar cópias de segurança para executar cada dia/semana/mês/ano e a data e hora em que deve executar. As cópias de segurança podem ocorrer até duas vezes por dia. Sempre que for executada uma tarefa de cópia de segurança, um ponto de recuperação de dados é criado no Azure partir da cópia dos dados de cópia de segurança armazenados no disco do servidor de cópia de segurança do Azure.

11. Na **especificar a política de retenção online**, especificar como os pontos de recuperação criados a partir de cópias de segurança diárias/semanais/mensais/anuais são retidos no Azure.

12. Na **escolher replicação online**, especifique como ocorre a replicação de dados completa inicial. 

13. No **resumo**, reveja as definições. Quando clica em **criar grupo**, ocorre a replicação de dados inicial. Quando a replicação de dados for concluída, no **Status** página, o estado do grupo de proteção é apresentado como **OK**. A tarefa de cópia de segurança inicial é está em conformidade com a proteção de definições do grupo.

## <a name="recover-file-data"></a>Recuperar dados de ficheiro

Utilize a consola do Azure Backup Server para recuperar dados à sua máquina virtual.

1. Na consola do servidor de cópia de segurança do Azure, na barra de navegação, clique em **recuperação** e navegue para os dados que pretende recuperar. No painel de resultados, selecione os dados.

2. No calendário na secção de pontos de recuperação, as datas em negrito indicam pontos de recuperação estão disponíveis. Selecione a data para recuperar.

3. Na **item recuperável** painel, selecione o item que pretende recuperar.

4. Na **ações** painel, clique em **recuperar** para abrir o Assistente de recuperação.

5. Pode recuperar os dados da seguinte forma:

    * **Recuperar para a localização original** -se o computador cliente estiver ligado através de VPN, esta opção não funcionar. Em vez disso, utilize uma localização alternativa e, em seguida, copiar dados a partir dessa localização.
    * **Recuperar para uma localização alternativa**

6. Especifique as opções de recuperação:

    * Para **comportamento de recuperação da versão existente**, selecione **criar cópia**, **ignorar**, ou **substituir**. Substituir só está disponível quando recuperar para a localização original.
    * Para **restaurar segurança**, escolha **aplicar as definições do computador de destino** ou **aplicar as definições de segurança da versão do ponto de recuperação de**.
    * Para **limitação da utilização de largura de banda de rede**, clique em **modificar** para ativar a limitação da utilização de largura de banda de rede.
    * **Notificação** clique em **enviar um e-mail ao concluir a recuperação**, e especifique os destinatários que receberão a notificação. Separe os endereços de e-mail por vírgulas.
    * Depois de fazer as seleções, clique em **seguinte**

7. Reveja as definições de recuperação e clique em **recuperar**. 

    > [!Note] 
    > Enquanto a tarefa de recuperação está em curso, serão canceladas todas as tarefas de sincronização para os itens de recuperação selecionado.
    >

Se estiver a utilizar o armazenamento de cópia de segurança Moderno (MBS), a recuperação de utilizador final (EUR) do servidor de ficheiros não é suportada. EUR do servidor de ficheiros tem uma dependência no Volume de cópia sombra Service (VSS), que não utiliza o armazenamento de cópia de segurança Moderno. Se estiver ativada EUR, utilize os seguintes passos para recuperar dados:

1. Navegue para os ficheiros protegidos e o nome de ficheiro com o botão direito e selecione **propriedades**.

2. Sobre o **propriedades** menu, clique em **versões anteriores** e escolha a versão que pretende recuperar.

## <a name="view-azure-backup-server-with-a-vault"></a>Ver servidor de cópia de segurança do Azure com um cofre
Para ver as entidades de servidor de cópia de segurança do Azure no Portal do Azure, pode seguir os passos seguintes:
1. Abra o Cofre dos serviços de recuperação.
2. Clique em infraestrutura de cópia de segurança.
3. Servidores de gestão de cópia de segurança de modo de exibição.

## <a name="see-also"></a>Consulte também
Para obter informações sobre como utilizar o Azure Backup Server para proteger outras cargas de trabalho, consulte um dos seguintes artigos:
- [Fazer cópias de segurança do farm do SharePoint](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Criar cópias de segurança do SQL server](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
