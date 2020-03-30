---
title: Back up Exchange server com o Servidor de Backup Azure
description: Saiba como fazer backup de um servidor de troca para o Backup Azure usando o Servidor de Backup Azure
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 9e623b1bdce93c340ccd0e61f9f5145e7154beff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295849"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Faça backup de um servidor de troca para Azure com o Servidor de Backup Azure

Este artigo descreve como configurar o Microsoft Azure Backup Server (MABS) para fazer o backup de um servidor da Microsoft Exchange para o Azure.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, certifique-se de que o Servidor de Backup Azure está [instalado e preparado](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>Agente de proteção MABS

Para instalar o agente de proteção MABS no servidor Exchange, siga estes passos:

1. Certifique-se de que as firewalls estão corretamente configuradas. Consulte as exceções à [firewall configurar para o agente](https://docs.microsoft.com/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019).
2. Instale o agente no servidor Exchange clicando em **Agentes de Gestão > > Instalar** na Consola de Administrador MABS. Consulte [a instalação do agente de proteção MABS](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) para obter passos detalhados.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Criar um grupo de proteção para o servidor De troca

1. Na consola de administrador mABS, clique em **Proteção**, e clique em **Nova** na fita da ferramenta para abrir o assistente do **Grupo de Proteção Nova.**
2. No ecrã **de boas-vindas** do assistente, clique em **Next**.
3. No ecrã do tipo de grupo de **proteção Select,** selecione **Servidores** e clique em **Next**.
4. Selecione a base de dados do servidor De troca que pretende proteger e clique **em Next**.

   > [!NOTE]
   > Se estiver a proteger a Exchange 2013, verifique os [pré-requisitos da Bolsa de 2013.](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/dn751029(v=sc.12))
   >
   >

    No exemplo seguinte, a base de dados Exchange 2010 é selecionada.

    ![Selecione membros do grupo](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Selecione o método de proteção de dados.

    Nomeie o grupo de proteção e, em seguida, selecione ambas as seguintes opções:

   * Quero proteção a curto prazo usando o Disk.
   * Quero proteção online.
6. Clique em **Seguinte**.
7. Selecione o **Eseutil executar para verificar a** opção de integridade de dados se pretender verificar a integridade das bases de dados do Exchange Server.

    Depois de selecionar esta opção, a verificação da consistência da cópia de segurança será executada no MABS para evitar o tráfego de I/S gerado através do comando **eseutil** no servidor Detroca.

   > [!NOTE]
   > Para utilizar esta opção, tem de copiar os ficheiros Ese.dll e Eseutil.exe para o c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin diretório no servidor MAB. Caso contrário, é desencadeado o seguinte erro:  
   > ![erro eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Clique em **Seguinte**.
9. Selecione a base de dados para **Copy Backup**, e, em seguida, clique **em Seguinte**.

   > [!NOTE]
   > Se não selecionar "Cópia completa" para pelo menos uma cópia da DAG de uma base de dados, os registos não serão truncados.
   >
   >
10. Configure os objetivos para **backup de curto prazo**e, em seguida, clique em **Next**.
11. Reveja o espaço disponível do disco e, em seguida, clique em **Next**.
12. Selecione o tempo em que o Servidor MAB irá criar a replicação inicial e, em seguida, clique **em Seguinte**.
13. Selecione as opções de verificação de consistência e, em seguida, clique em **Seguinte**.
14. Escolha a base de dados que pretende fazer de volta ao Azure e, em seguida, clique em **Next**. Por exemplo:

    ![Especificar dados de proteção on-line](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Defina o horário para **Backup Azure**e, em seguida, clique **em Next**. Por exemplo:

    ![Especificar o calendário de backup online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Nota Os pontos de recuperação online baseiam-se em pontos de recuperação completos expressos. Por isso, deve agendar o ponto de recuperação on-line após o tempo especificado para o ponto de recuperação total expresso.
    >
    >
16. Configure a política de retenção para **Backup Azure,** e, em seguida, clique **em Seguinte**.
17. Escolha uma opção de replicação on-line e clique em **Next**.

    Se tiver uma grande base de dados, pode demorar muito tempo para que o backup inicial seja criado sobre a rede. Para evitar este problema, pode criar um backup offline.  

    ![Especificar a política de retenção online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Confirme as definições e, em seguida, clique em **Criar Grupo**.
19. Clique em **Fechar**.

## <a name="recover-the-exchange-database"></a>Recuperar a base de dados exchange

1. Para recuperar uma base de dados de Troca, clique em **Recovery** na Consola de Administrador MABS.
2. Localize a base de dados exchange que pretende recuperar.
3. Selecione um ponto de recuperação on-line da lista de abandono do tempo de *recuperação.*
4. Clique em **Recuperar** para iniciar o **Assistente de Recuperação**.

Para pontos de recuperação on-line, existem cinco tipos de recuperação:

* **Recuperar para a localização original do Exchange Server:** Os dados serão recuperados para o servidor de Intercâmbio original.
* **Recupere para outra base de dados num Servidor de Câmbio:** Os dados serão recuperados para outra base de dados noutro servidor de Intercâmbio.
* **Recuperar para uma base de dados de recuperação:** Os dados serão recuperados numa Base de Dados de Recuperação de Intercâmbio (RDB).
* **Copiar para uma pasta de rede:** Os dados serão recuperados numa pasta de rede.
* **Cópia para fita:** Se tiver uma biblioteca de fita ou uma unidade de fita autónoma anexada e configurada no MABS, o ponto de recuperação será copiado para uma fita gratuita.

    ![Escolha a replicação online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Passos seguintes

* [Azure Backup FAQ](backup-azure-backup-faq.md)
