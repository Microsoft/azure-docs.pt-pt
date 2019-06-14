---
title: Criar cópias de segurança um servidor Exchange para o Azure Backup no servidor de cópia de segurança do Azure
description: Saiba como criar cópias de um servidor Exchange para o Azure Backup com o Azure Backup Server
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 03/24/2017
ms.author: kasinh
ms.openlocfilehash: 40541596b4da9e0590d497785afd7d6d7f4cbcb4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60641509"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Criar cópias de segurança um servidor Exchange para o Azure com o Azure Backup Server
Este artigo descreve como configurar o Microsoft Azure Backup Server (MABS) para fazer backup de um servidor do Microsoft Exchange para o Azure.  

## <a name="prerequisites"></a>Pré-requisitos
Antes de continuar, certifique-se de que o servidor de cópia de segurança do Azure está [instalada e preparada](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>Agente de proteção do MABS
Para instalar o agente de proteção do MABS no Exchange server, siga estes passos:

1. Certifique-se de que as firewalls estão corretamente configuradas. Ver [configurar exceções de firewall para o agente](https://technet.microsoft.com/library/Hh758204.aspx).
2. Instalar o agente no servidor do Exchange clicando **gestão > agentes > instalar** na consola do administrador do MABS. Ver [instalar o agente de proteção do MABS](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) para obter passos detalhados.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Crie um grupo de proteção para o Exchange server
1. Na consola do administrador do MABS, clique em **proteção**e, em seguida, clique em **New** no Friso de ferramentas para abrir o **criar novo grupo de proteção** assistente.
2. Sobre o **bem-vindo** ecrã do assistente, clique em **próxima**.
3. Sobre o **selecione o tipo de grupo de proteção** ecrã, selecione **servidores** e clique em **seguinte**.
4. Selecione a base de dados de servidor Exchange que pretende proteger e clique em **seguinte**.

   > [!NOTE]
   > Se estiver a proteger o Exchange 2013, verifique os [pré-requisitos do Exchange 2013](https://technet.microsoft.com/library/dn751029.aspx).
   >
   >

    No exemplo seguinte, a base de dados do Exchange 2010 está selecionada.

    ![Selecionar Membros do grupo](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Selecione o método de proteção de dados.

    Nome do grupo de proteção e, em seguida, selecione ambas as opções seguintes:

   * Pretendo proteção de curta duração com disco.
   * Pretendo proteção online.
6. Clique em **Seguinte**.
7. Selecione o **executar o Eseutil para verificar a integridade dos dados** opção se pretender verificar a integridade das bases de dados do Exchange Server.

    Depois de selecionar esta opção, a verificação de consistência de cópia de segurança vai ser executada no MABS para evitar o tráfego de e/s que é gerado ao executar o **eseutil** comando no servidor do Exchange.

   > [!NOTE]
   > Para utilizar esta opção, tem de copiar os ficheiros ESE. dll e Eseutil.exe para o diretório C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin no servidor MAB. Caso contrário, é acionado o seguinte erro:  
   > ![Erro de Eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Clique em **Seguinte**.
9. Selecione a base de dados **cópia de segurança**e, em seguida, clique em **próxima**.

   > [!NOTE]
   > Se não selecionar "Cópia de segurança completa" para, pelo menos, uma cópia do DAG das bases de dados, os registos não serão truncados.
   >
   >
10. Configurar os objetivos de **backup de curto prazo**e, em seguida, clique em **próxima**.
11. Reveja o espaço em disco disponível e, em seguida, clique em **seguinte**.
12. Selecione a hora a que o servidor MAB criar a replicação inicial e, em seguida, clique em **seguinte**.
13. Selecione as opções de verificação de consistência e, em seguida, clique em **seguinte**.
14. Selecione a base de dados que pretende criar cópias de segurança para o Azure e, em seguida, clique em **seguinte**. Por exemplo:

    ![Especificar dados da proteção online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definir a agenda para **Azure Backup**e, em seguida, clique em **próxima**. Por exemplo:

    ![Especifique a agenda de cópia de segurança online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Tenha em atenção de pontos de recuperação Online baseiam-se no express total de pontos de recuperação. Por conseguinte, tem de agendar o ponto de recuperação online após a hora em que é especificada para a completa rápida do ponto de recuperação.
    >
    >
16. Configurar a política de retenção para **Azure Backup**e, em seguida, clique em **próxima**.
17. Escolha uma opção de replicação online e clique em **seguinte**.

    Se tiver uma base de dados grandes, pode demorar muito tempo para a cópia de segurança inicial a ser criado através da rede. Para evitar este problema, pode criar uma cópia de segurança offline.  

    ![Especificar Política de retenção online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Confirme as definições e, em seguida, clique em **criar grupo**.
19. Clique em **Fechar**.

## <a name="recover-the-exchange-database"></a>Recuperar a base de dados do Exchange
1. Para recuperar uma base de dados do Exchange, clique em **recuperação** na consola do administrador do MABS.
2. Localize a base de dados do Exchange que pretende recuperar.
3. Selecione um ponto de recuperação online a partir da *tempo de recuperação* na lista pendente.
4. Clique em **recuperar** para iniciar o **Assistente de recuperação**.

Para pontos de recuperação online, há cinco tipos de recuperação:

* **Recupere para a localização original do Exchange Server:** Os dados serão recuperados para o servidor original do Exchange.
* **Recupere para outra base de dados num Exchange Server:** Os dados serão recuperados para outra base de dados noutro servidor do Exchange.
* **Recupere para uma base de dados de recuperação:** Os dados serão recuperados para uma base de dados de recuperação do Exchange (RDB).
* **Copiar para uma pasta de rede:** Os dados serão recuperados para uma pasta de rede.
* **Copiar para banda:** Se tiver uma biblioteca de bandas ou uma unidade de banda autónomo ligado e configurado no MABS, o ponto de recuperação será copiado para uma banda livre.

    ![Escolher replicação online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Passos Seguintes
* [FAQ sobre cópia de segurança do Azure](backup-azure-backup-faq.md)
