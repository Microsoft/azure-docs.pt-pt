---
title: Fazer cópia de segurança de um servidor do Exchange para o Azure Backup com o System Center 2012 R2 DPM
description: Saiba como fazer backup de um servidor Exchange para o backup do Azure usando o System Center 2012 R2 DPM
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: kasinh
ms.openlocfilehash: 2d3670e2120e7c203e40d39ba9d82537da877ee5
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466735"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Fazer cópia de segurança de um servidor do Exchange para o Azure Backup com o System Center 2012 R2 DPM
Este artigo descreve como configurar um servidor de Data Protection Manager do System Center 2012 R2 (DPM) para fazer backup de um servidor do Microsoft Exchange no backup do Azure.  

## <a name="updates"></a>Atualizações
Para registrar com êxito o servidor DPM com o backup do Azure, você deve instalar o pacote cumulativo de atualizações mais recente para o System Center 2012 R2 DPM e a versão mais recente do agente de backup do Azure. Obtenha o pacote cumulativo de atualizações mais recente do [catálogo da Microsoft](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> Para os exemplos neste artigo, a versão 2.0.8719.0 do agente de backup do Azure está instalada e o pacote cumulativo de atualizações 6 está instalado no System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Pré-requisitos
Antes de continuar, certifique-se de que todos os [pré-requisitos](backup-azure-dpm-introduction.md#prerequisites-and-limitations) para usar backup do Microsoft Azure para proteger as cargas de trabalho foram atendidos. Esses pré-requisitos incluem o seguinte:

* Um cofre de backup no site do Azure foi criado.
* As credenciais do agente e do cofre foram baixadas no servidor DPM.
* O agente está instalado no servidor DPM.
* As credenciais do cofre foram usadas para registrar o servidor DPM.
* Se você estiver protegendo o Exchange 2016, atualize para o DPM 2012 R2 UR9 ou posterior

## <a name="dpm-protection-agent"></a>Agente de proteção do DPM
Para instalar o agente de proteção do DPM no Exchange Server, siga estas etapas:

1. Verifique se os firewalls estão configurados corretamente. Consulte [configurar exceções de firewall para o agente](https://technet.microsoft.com/library/Hh758204.aspx).
2. Instale o agente no servidor Exchange clicando em **Management > agents > instalar** no console do administrador do DPM. Consulte [instalar o agente de proteção do DPM](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) para obter etapas detalhadas.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Criar um grupo de proteção para o Exchange Server
1. No Console do Administrador do DPM, clique em **proteção**e, em seguida, clique em **novo** na faixa de ferramentas da ferramenta para abrir o assistente para **criar novo grupo de proteção** .
2. Na tela de **boas-vindas** do assistente, clique em **Avançar**.
3. Na tela **Selecionar tipo de grupo de proteção** , selecione **servidores** e clique em **Avançar**.
4. Selecione o banco de dados do Exchange Server que você deseja proteger e clique em **Avançar**.

   > [!NOTE]
   > Se você estiver protegendo o Exchange 2013, verifique os [pré-requisitos do exchange 2013](https://technet.microsoft.com/library/dn751029.aspx).
   >
   >

    No exemplo a seguir, o banco de dados do Exchange 2010 está selecionado.

    ![Selecionar membros do grupo](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Selecione o método de proteção de dados.

    Nomeie o grupo de proteção e, em seguida, selecione as seguintes opções:

   * Quero proteção de curto prazo usando disco.
   * Quero proteção online.
6. Clique em **Seguinte**.
7. Selecione a opção **executar Eseutil para verificar a integridade dos dados** se desejar verificar a integridade dos bancos de dado do Exchange Server.

    Depois de selecionar essa opção, a verificação de consistência do backup será executada no servidor DPM para evitar o tráfego de e/s gerado com a execução do comando **Eseutil** no servidor Exchange.

   > [!NOTE]
   > Para usar essa opção, você deve copiar os arquivos ESE. dll e Eseutil. exe para o diretório C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin no servidor DPM. Caso contrário, o seguinte erro será disparado:  
   > ![erro de Eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Clique em **Seguinte**.
9. Selecione o banco de dados para **backup de cópia**e clique em **Avançar**.

   > [!NOTE]
   > Se você não selecionar "backup completo" para pelo menos uma cópia DAG de um banco de dados, os logs não serão truncados.
   >
   >
10. Configure as metas de **backup de curto prazo**e clique em **Avançar**.
11. Examine o espaço em disco disponível e clique em **Avançar**.
12. Selecione a hora em que o servidor DPM criará a replicação inicial e clique em **Avançar**.
13. Selecione as opções de verificação de consistência e clique em **Avançar**.
14. Escolha o banco de dados do qual você deseja fazer backup no Azure e clique em **Avançar**. Por exemplo:

    ![Especificar dados de proteção online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Defina a agenda para o **backup do Azure**e clique em **Avançar**. Por exemplo:

    ![Especificar agendamento de backup online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Observação os pontos de recuperação online são baseados em pontos de recuperação completos expressos. Portanto, você deve agendar o ponto de recuperação online após o horário especificado para o ponto de recuperação completo expresso.
    >
    >
16. Configure a política de retenção para o **backup do Azure**e clique em **Avançar**.
17. Escolha uma opção de replicação online e clique em **Avançar**.

    Se você tiver um banco de dados grande, pode levar muito tempo para que o backup inicial seja criado pela rede. Para evitar esse problema, você pode criar um backup offline.  

    ![Especificar política de retenção online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Confirme as configurações e clique em **Criar grupo**.
19. Clique em **Fechar**.

## <a name="recover-the-exchange-database"></a>Recuperar o banco de dados do Exchange
1. Para recuperar um banco de dados do Exchange, clique em **recuperação** no console do administrador do DPM.
2. Localize o banco de dados do Exchange que você deseja recuperar.
3. Selecione um ponto de recuperação online na lista suspensa *tempo de recuperação* .
4. Clique em **recuperar** para iniciar o **Assistente de recuperação**.

Para pontos de recuperação online, há cinco tipos de recuperação:

* **Recuperar no local original do Exchange Server:** Os dados serão recuperados para o servidor Exchange original.
* **Recuperar em outro banco de dados em um servidor Exchange:** Os dados serão recuperados em outro servidor do Exchange.
* **Recuperar em um banco de dados de recuperação:** Os dados serão recuperados em um RDB (Exchange Recovery Database).
* **Copiar para uma pasta de rede:** Os dados serão recuperados em uma pasta de rede.
* **Copiar para fita:** Se você tiver uma biblioteca de fitas ou uma unidade de fita autônoma anexada e configurada no servidor DPM, o ponto de recuperação será copiado para uma fita livre.

    ![Escolher replicação online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Passos Seguintes
* [Perguntas frequentes do backup do Azure](backup-azure-backup-faq.md)
