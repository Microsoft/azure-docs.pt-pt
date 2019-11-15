---
title: Fazer backup de arquivos em VMs Azure Stack-backup do Azure
description: Use o backup do Azure para fazer backup e recuperar Azure Stack arquivos e aplicativos para seu ambiente de Azure Stack.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/05/2018
ms.author: dacurwin
ms.openlocfilehash: 70da426f1dff843f1e2515052431f54f8ea83780
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091570"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Fazer cópias de segurança de ficheiros e aplicações no Azure Stack

Você pode usar o backup do Azure para proteger (ou fazer backup) arquivos e aplicativos em Azure Stack. Para fazer backup de arquivos e aplicativos, instale o Backup do Microsoft Azure Server como uma máquina virtual em execução no Azure Stack. Você pode proteger os arquivos em qualquer Azure Stack servidor na mesma rede virtual. Depois de instalar o Servidor de Backup do Azure, adicione discos do Azure para aumentar o armazenamento local disponível para dados de backup de curto prazo. Servidor de Backup do Azure usa o armazenamento do Azure para retenção de longo prazo.

> [!NOTE]
> Embora Servidor de Backup do Azure e o System Center Data Protection Manager (DPM) sejam semelhantes, o DPM não tem suporte para uso com o Azure Stack.
>

Este artigo não aborda a instalação de Servidor de Backup do Azure no ambiente de Azure Stack. Para instalar o Servidor de Backup do Azure no Azure Stack, consulte o artigo [instalando servidor de backup do Azure](backup-mabs-install-azure-stack.md).

## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Fazer backup de arquivos e pastas em Azure Stack VMs para o Azure

Para configurar Servidor de Backup do Azure proteger arquivos em máquinas virtuais Azure Stack, abra o console do Servidor de Backup do Azure. Você usará o console do para configurar grupos de proteção e proteger os dados em suas máquinas virtuais.

1. No console do Servidor de Backup do Azure, clique em **proteção** e, na barra de ferramentas, clique em **novo** para abrir o assistente para **criar novo grupo de proteção** .

   ![Configurar a proteção no console Servidor de Backup do Azure](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Pode levar alguns segundos para que o assistente seja aberto. Depois que o assistente for aberto, clique em **Avançar** para ir para a tela **Selecionar tipo de grupo de proteção** .

   ![O assistente de novo grupo de proteção é aberto](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Na tela **Selecionar tipo de grupo de proteção** , escolha **servidores** e clique em **Avançar**.

    ![O assistente de novo grupo de proteção é aberto](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    A tela **selecionar membros do grupo** é aberta.

    ![O assistente de novo grupo de proteção é aberto](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. Na tela **selecionar membros do grupo** , clique em **+** para expandir a lista de subitens. Para todos os itens que você deseja proteger, marque a caixa de seleção. Depois que todos os itens tiverem sido selecionados, clique em **Avançar**.

    ![O assistente de novo grupo de proteção é aberto](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    A Microsoft recomenda colocar todos os dados que compartilharão uma política de proteção em um grupo de proteção. Para obter informações completas sobre o planejamento e a implantação de grupos de proteção, consulte o artigo System Center DPM, [implantar grupos de proteção](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801).

4. Na tela **selecionar método de proteção de dados** , digite um nome para o grupo de proteção. Marque a caixa de seleção para **que eu queira proteção de curto prazo usando:** e **quero proteção online**. Clique em **Seguinte**.

    ![O assistente de novo grupo de proteção é aberto](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Para selecionar **desejo proteção online**, primeiro você deve selecionar **desejo proteção de curto prazo usando:** disco. O Servidor de Backup do Azure não se protege à fita, portanto o disco é a única opção para proteção de curto prazo.

5. Na tela **especificar objetivos de curto prazo** , escolha por quanto tempo manter os pontos de recuperação salvos em disco e quando salvar backups incrementais. Clique em **Seguinte**.

    > [!IMPORTANT]
    > Você **não** deve manter os dados de recuperação operacional (backup) em discos anexados servidor de backup do Azure por mais de cinco dias.
    >

    ![O assistente de novo grupo de proteção é aberto](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png)

    Em vez de selecionar um intervalo para backups incrementais, executar um backup completo expresso antes de cada ponto de recuperação agendado, clique **pouco antes de um ponto de recuperação**. Se você estiver protegendo cargas de trabalho de aplicativo, o Servidor de Backup do Azure criará pontos de recuperação por agendamento de frequência de sincronização (desde que o aplicativo dê suporte a backups incrementais). Se o aplicativo não oferecer suporte a backups incrementais, Servidor de Backup do Azure executará um backup completo expresso.

    Para **pontos de recuperação de arquivo**, especifique quando criar pontos de recuperação. Clique em **Modificar** para definir as horas e os dias da semana em que os pontos de recuperação são criados.

6. Na tela **rever alocação do disco** , examine o espaço em disco do pool de armazenamento alocado para o grupo de proteção.

    **Tamanho total dos dados** é o tamanho dos dados que você deseja fazer backup e o **espaço em disco a ser provisionado** em servidor de backup do Azure é o espaço recomendado para o grupo de proteção. Servidor de Backup do Azure escolhe o volume de backup ideal, com base nas configurações. No entanto, você pode editar as opções de volume de backup nos detalhes de alocação de disco. Para as cargas de trabalho, selecione o armazenamento preferencial no menu suspenso. Suas edições alteram os valores para armazenamento total e armazenamento livre no painel de Armazenamento em Disco disponível. O espaço subprovisionado é a quantidade de Servidor de Backup do Azure de armazenamento que sugere que você adicione ao volume para continuar com os backups sem problemas no futuro.

7. Em **escolher método de criação de réplica**, selecione como você deseja manipular a replicação de dados completa inicial. Se você decidir replicar pela rede, o Azure recomenda que você escolha um horário de pico. Para grandes quantidades de dados ou condições de rede abaixo do ideal, considere a possibilidade de replicar os dados usando mídia removível.

8. Em **escolher opções de verificação de consistência**, selecione como você deseja automatizar as verificações de consistência. Habilitar verificações de consistência para execução somente quando a replicação de dados se tornar inconsistente ou de acordo com uma agenda. Se você não quiser configurar a verificação de consistência automática, execute uma verificação manual a qualquer momento:
    * Na área **proteção** do console do servidor de backup do Azure, clique com o botão direito do mouse no grupo de proteção e selecione **executar verificação de consistência**.

9. Se você optar por fazer backup no Azure, na página **especificar dados de proteção online** , verifique se as cargas de trabalho que você deseja fazer backup no Azure estão selecionadas.

10. Em **especificar agendamento de backup online**, especifique quando os backups incrementais no Azure devem ocorrer.

    Você pode agendar backups para serem executados todos os dias/semanas/mês/ano e a data/hora em que eles devem ser executados. Os backups podem ocorrer até duas vezes por dia. Cada vez que um trabalho de backup é executado, um ponto de recuperação de dados é criado no Azure por meio da cópia dos dados de backup armazenados no disco Servidor de Backup do Azure.

11. Em **especificar política de retenção online**, especifique como os pontos de recuperação criados a partir dos backups diários/semanais/mensais/anuais são mantidos no Azure.

12. Em **escolher replicação online**, especifique como ocorre a replicação completa inicial dos dados.

13. Em **Resumo**, examine suas configurações. Quando você clica em **Criar grupo**, ocorre a replicação de dados inicial. Quando a replicação de dados for concluída, na página **status** , o status do grupo de proteção aparecerá como **OK**. O trabalho de backup inicial ocorre em linha com as configurações do grupo de proteção.

## <a name="recover-file-data"></a>Recuperar dados de arquivo

Use Servidor de Backup do Azure console para recuperar dados para sua máquina virtual.

1. No console do Servidor de Backup do Azure, na barra de navegação, clique em **recuperação** e procure os dados que você deseja recuperar. No painel de resultados, selecione os dados.

2. No calendário da seção pontos de recuperação, as datas em negrito indicam que os pontos de recuperação estão disponíveis. Selecione a data a ser recuperada.

3. No painel **Item recuperável** , selecione o item que você deseja recuperar.

4. No painel **ações** , clique em **recuperar** para abrir o assistente de recuperação.

5. Você pode recuperar dados da seguinte maneira:

    * **Recuperar no local original** – se o computador cliente estiver conectado pela VPN, essa opção não funcionará. Em vez disso, use um local alternativo e copie os dados desse local.
    * **Recuperar em um local alternativo**

6. Especifique as opções de recuperação:

    * Para **comportamento de recuperação de versão existente**, selecione **criar cópia**, **ignorar**ou **substituir**. A substituição está disponível somente ao recuperar para o local original.
    * Para **segurança de restauração**, escolha **aplicar configurações do computador de destino** ou **aplicar as configurações de segurança da versão do ponto de recuperação**.
    * Para **limitação do uso de largura de banda**, clique em **Modificar** para habilitar a limitação do uso de largura de banda.
    * **Notificação** do Clique em **enviar um email quando a recuperação for concluída**e especifique os destinatários que receberão a notificação. Separe os endereços de email por vírgulas.
    * Depois de fazer as seleções, clique em **Avançar**

7. Examine as configurações de recuperação e clique em **recuperar**.

    >[!Note]
    >Enquanto o trabalho de recuperação está em andamento, todos os trabalhos de sincronização para os itens de recuperação selecionados são cancelados.

Se você estiver usando o MBS (Armazenamento de Backup Moderno), não há suporte para a recuperação do usuário final do servidor de arquivos (EUR). O servidor de arquivos EUR tem uma dependência em Serviço de Cópias de Sombra de Volume (VSS), que Armazenamento de Backup Moderno não usa. Se o EUR estiver habilitado, use as seguintes etapas para recuperar dados:

1. Navegue até os arquivos protegidos e clique com o botão direito do mouse no nome do arquivo e selecione **Propriedades**.

2. No menu **Propriedades** , clique em **versões anteriores** e escolha a versão que você deseja recuperar.

## <a name="view-azure-backup-server-with-a-vault"></a>Exibir Servidor de Backup do Azure com um cofre

Para exibir Servidor de Backup do Azure entidades no portal do Azure, você pode seguir as seguintes etapas:

1. Abra o cofre dos serviços de recuperação.
2. Clique em infraestrutura de backup.
3. Exibir servidores de gerenciamento de backup.

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como usar Servidor de Backup do Azure para proteger outras cargas de trabalho, consulte um dos seguintes artigos:

* [Fazer backup do farm do SharePoint](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
* [Fazer backup do SQL Server](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
