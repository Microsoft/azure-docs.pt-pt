---
title: Backup do servidor de troca com servidor de backup Azure
description: Saiba como fazer backup de um servidor de Troca para Azure Backup utilizando o Azure Backup Server
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 2e3e89d93ab35724a09138d48863badad6a41b71
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86514123"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Faça backup de um servidor de troca para Azure com O Azure Backup Server

Este artigo descreve como configurar o Microsoft Azure Backup Server (MABS) para fazer backup de um servidor da Microsoft Exchange para o Azure.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, certifique-se de que o Azure Backup Server está [instalado e preparado](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>Agente de proteção MABS

Para instalar o agente de proteção MABS no servidor Exchange, siga estes passos:

1. Certifique-se de que as firewalls estão corretamente configuradas. Consulte [as exceções de firewall configure para o agente](/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019).
2. Instale o agente no servidor Exchange clicando em **Gestão > agentes > instalar** na Consola de Administrador MABS. Consulte [a instalação do agente de proteção MABS](/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) para obter etapas detalhadas.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Criar um grupo de proteção para o servidor Exchange

1. Na consola de administrador MABS, clique em **Proteção**e, em seguida, clique em **Novo** na fita da ferramenta para abrir o assistente **do Grupo criar novas proteções.**
2. No ecrã **de boas-vindas** do assistente, clique em **Seguinte**.
3. No ecrã **do tipo de grupo de proteção Select,** selecione **Servidores** e clique em **Seguinte**.
4. Selecione a base de dados do servidor Exchange que pretende proteger e clique em **Seguinte**.

   > [!NOTE]
   > Se estiver a proteger o Exchange 2013, consulte os [pré-requisitos da Bolsa de 2013.](/system-center/dpm/back-up-exchange)
   >
   >

    No exemplo seguinte, é selecionada a base de dados Exchange 2010.

    ![Selecione membros do grupo](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Selecione o método de proteção de dados.

    Nomeie o grupo de proteção e, em seguida, selecione ambas as seguintes opções:

   * Quero proteção a curto prazo usando o Disco.
   * Quero proteção online.
6. Clique em **Seguinte**.
7. Selecione o **Run Eseutil para verificar a** opção de integridade dos dados se pretender verificar a integridade das bases de dados do Servidor de Câmbio.

    Depois de selecionar esta opção, a verificação de consistência de backup será executada no MABS para evitar o tráfego de E/S gerado pela execução do comando **eseutil** no servidor Exchange.

   > [!NOTE]
   > Para utilizar esta opção, tem de copiar os ficheiros Ese.dll e Eseutil.exe para o C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin directy no servidor MABS. Caso contrário, é desencadeado o seguinte erro:  
   > ![erro eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Clique em **Seguinte**.
9. Selecione a base de dados para **Cópia de Cópia e,** em seguida, clique em **Seguinte**.

   > [!NOTE]
   > Se não selecionar "Cópia de segurança completa" para pelo menos uma cópia da DAG de uma base de dados, os registos não serão truncados.
   >
   >
10. Configure os objetivos para **o backup de curto prazo**e, em seguida, clique em **Seguinte**.
11. Reveja o espaço disponível do disco e, em seguida, clique em **Seguinte**.
12. Selecione a hora em que o MABS Server criará a replicação inicial e, em seguida, clique em **Seguinte**.
13. Selecione as opções de verificação de consistência e, em seguida, clique em **Seguinte**.
14. Escolha a base de dados que pretende fazer até Azure e, em seguida, clique em **Seguinte**. Por exemplo:

    ![Especificar dados de proteção on-line](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Defina o calendário para **Azure Backup**e, em seguida, clique em **Seguinte**. Por exemplo:

    ![Especificar o horário de backup on-line](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Nota Os pontos de recuperação online baseiam-se em pontos de recuperação expressos. Por isso, deve agendar o ponto de recuperação on-line após o tempo especificado para o ponto de recuperação total expresso.
    >
    >
16. Configure a política de retenção para **Azure Backup**e, em seguida, clique em **Seguinte**.
17. Escolha uma opção de replicação on-line e clique em **Seguinte**.

    Se tiver uma grande base de dados, pode demorar muito tempo até que a cópia de segurança inicial seja criada por toda a rede. Para evitar este problema, pode criar uma cópia de segurança offline.  

    ![Especificar a política de retenção on-line](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Confirme as definições e, em seguida, clique em **Criar Grupo**.
19. Clique em **Close** (Fechar).

## <a name="recover-the-exchange-database"></a>Recuperar a base de dados de câmbio

1. Para recuperar uma base de dados de Câmbio, clique em **Recovery** na Consola de Administrador MABS.
2. Localize a base de dados Do Exchange que pretende recuperar.
3. Selecione um ponto de recuperação on-line da lista de recuos do tempo de *recuperação.*
4. Clique em **Recuperar** para iniciar o **Assistente de Recuperação**.

Para os pontos de recuperação online, existem cinco tipos de recuperação:

* **Recuperar para a localização original do Servidor de Câmbio:** Os dados serão recuperados para o servidor Exchange original.
* **Recupere para outra base de dados num Servidor de Intercâmbio:** Os dados serão recuperados para outra base de dados noutro servidor da Exchange.
* **Recuperar para uma Base de Dados de Recuperação:** Os dados serão recuperados para uma Base de Dados de Recuperação de Intercâmbio (RDB).
* **Copiar para uma pasta de rede:** Os dados serão recuperados para uma pasta de rede.
* **Copiar para fita:** Se tiver uma biblioteca de fitas ou uma fita adesiva autónoma anexada e configurada no MABS, o ponto de recuperação será copiado para uma fita grátis.

    ![Escolha a replicação online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Próximos passos

* [Azure Backup FAQ](backup-azure-backup-faq.md)
