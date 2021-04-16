---
title: Fazer o back up um servidor de troca via System Center DPM
description: Saiba como fazer backup de um servidor de Intercâmbio para Azure Backup usando System Center 2012 R2 DPM
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: a89c37b8447b318c44faf0d4e0b1921d305e7f59
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519396"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Fazer cópia de segurança de um servidor do Exchange para o Azure Backup com o System Center 2012 R2 DPM

Este artigo descreve como configurar um servidor Do System Center 2012 R2 Data Protection Manager (DPM) para fazer backup de um servidor da Microsoft Exchange para Azure Backup.  

## <a name="updates"></a>Atualizações

Para registar com sucesso o servidor DPM com Azure Backup, tem de instalar o rollup de atualização mais recente para o System Center 2012 R2 DPM e a versão mais recente do Agente de Backup Azure. Obtenha a mais recente atualização do Catálogo da [Microsoft.](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager)

> [!NOTE]
> Para os exemplos deste artigo, a versão 2.0.8719.0 do Agente de Backup Azure está instalada e o Update Rollup 6 está instalado no System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, certifique-se de que todos os [pré-requisitos](backup-azure-dpm-introduction.md#prerequisites-and-limitations) para a utilização do Microsoft Azure Backup para proteger as cargas de trabalho foram cumpridos. Estes pré-requisitos incluem o seguinte:

* Foi criado um cofre de reserva no local do Azure.
* As credenciais de agente e cofre foram transferidas para o servidor DPM.
* O agente está instalado no servidor DPM.
* As credenciais do cofre foram usadas para registar o servidor DPM.
* Se estiver a proteger o Exchange 2016, atualize para DPM 2012 R2 UR9 ou mais tarde.

## <a name="dpm-protection-agent"></a>Agente de proteção DPM

Para instalar o agente de proteção DPM no servidor Exchange, siga estes passos:

1. Certifique-se de que as firewalls estão corretamente configuradas. Consulte [as exceções de firewall configure para o agente](/system-center/dpm/configure-firewall-settings-for-dpm).
2. Instale o agente no servidor Exchange selecionando **Agentes de Gestão > > instalar** na Consola de Administrador DPM. Consulte [a instalação do agente de proteção DPM](/system-center/dpm/deploy-dpm-protection-agent) para obter etapas detalhadas.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Criar um grupo de proteção para o servidor Exchange

1. Na consola DPM Administrator, selecione **Protection** e, em seguida, selecione **Novo** na fita da ferramenta para abrir o assistente **criar novo grupo de proteção.**
2. No ecrã **de boas-vindas** do assistente, selecione **Seguinte**.
3. No ecrã **do tipo de grupo de proteção Select,** selecione **Servidores** e selecione **Seguinte**.
4. Selecione a base de dados do servidor Exchange que pretende proteger e selecione **Next**.

   > [!NOTE]
   > Se estiver a proteger a Exchange 2013, consulte os [pré-requisitos da Exchange 2013.](/system-center/dpm/back-up-exchange)
   >
   >

    No exemplo seguinte, é selecionada a base de dados Exchange 2010.

    ![Selecione membros do grupo](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Selecione o método de proteção de dados.

    Nomeie o grupo de proteção e, em seguida, selecione ambas as seguintes opções:

   * Quero proteção a curto prazo usando o Disco.
   * Quero proteção online.
6. Selecione **Seguinte**.
7. Selecione o **Run Eseutil para verificar a** opção de integridade dos dados se pretender verificar a integridade das bases de dados do Servidor de Câmbio.

    Depois de selecionar esta opção, a verificação da consistência da cópia de segurança será executada no servidor DPM para evitar o tráfego de E/S gerado pela execução do comando **eseutil** no servidor Exchange.

   > [!NOTE]
   > Para utilizar esta opção, tem de copiar os ficheiros Ese.dll e Eseutil.exe para o C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin no servidor DPM. Caso contrário, é desencadeado o seguinte erro:  
   > ![erro eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Selecione **Seguinte**.
9. Selecione a base de dados para **cópia de cópia** e, em seguida, selecione **Seguinte**.

   > [!NOTE]
   > Se não selecionar "Cópia de segurança completa" para pelo menos uma cópia da DAG de uma base de dados, os registos não serão truncados.
   >
   >
10. Configure os objetivos para **o backup de curto prazo** e, em seguida, selecione **Next**.
11. Reveja o espaço disponível do disco e, em seguida, selecione **Next**.
12. Selecione a hora em que o servidor DPM criará a replicação inicial e, em seguida, selecione **Seguinte**.
13. Selecione as opções de verificação de consistência e, em seguida, selecione **Seguinte**.
14. Escolha a base de dados que pretende fazer até Azure e, em seguida, selecione **Next**. Por exemplo:

    ![Especificar dados de proteção on-line](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Defina o horário para **Azure Backup** e, em seguida, selecione **Next**. Por exemplo:

    ![Especificar o horário de backup on-line](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Nota Os pontos de recuperação online baseiam-se em pontos de recuperação expressos. Por isso, deve agendar o ponto de recuperação on-line após o tempo especificado para o ponto de recuperação total expresso.
    >
    >
16. Configure a política de retenção para **Azure Backup** e, em seguida, selecione **Next**.
17. Escolha uma opção de replicação on-line e selecione **Next**.

    Se tiver uma grande base de dados, pode demorar muito tempo até que a cópia de segurança inicial seja criada por toda a rede. Para evitar este problema, pode criar uma cópia de segurança offline.  

    ![Especificar a política de retenção on-line](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Confirme as definições e, em seguida, **selecione Criar Grupo**.
19. Selecione **Fechar**.

## <a name="recover-the-exchange-database"></a>Recuperar a base de dados de câmbio

1. Para recuperar uma base de dados de Câmbio, selecione **Recovery** in the DPM Administrator Console.
2. Localize a base de dados Do Exchange que pretende recuperar.
3. Selecione um ponto de recuperação on-line da lista de recuos do tempo de *recuperação.*
4. Selecione **Recuperar** para iniciar o **Assistente de Recuperação**.

Para os pontos de recuperação online, existem cinco tipos de recuperação:

* **Recuperar para a localização original do Servidor de Câmbio:** Os dados serão recuperados para o servidor Exchange original.
* **Recupere para outra base de dados num Servidor de Intercâmbio:** Os dados serão recuperados para outra base de dados noutro servidor da Exchange.
* **Recuperar para uma Base de Dados de Recuperação:** Os dados serão recuperados para uma Base de Dados de Recuperação de Intercâmbio (RDB).
* **Copiar para uma pasta de rede:** Os dados serão recuperados para uma pasta de rede.
* **Copiar para fita:** Se tiver uma biblioteca de fitas ou uma fita adesiva autónoma anexada e configurada no servidor DPM, o ponto de recuperação será copiado para uma fita grátis.

    ![Escolha a replicação online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Passos seguintes

* [Azure Backup FAQ](backup-azure-backup-faq.yml)
