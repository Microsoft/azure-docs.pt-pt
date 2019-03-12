---
title: Monitorizar a sincronização de dados SQL do Azure com os registos do Azure Monitor | Documentos da Microsoft
description: Saiba como monitorizar a sincronização de dados SQL do Azure através de registos do Azure Monitor
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: 1417907bf9472137677a090906fa173c3d1ea571
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539297"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Monitorizar a sincronização de dados SQL com os registos do Azure Monitor 

Verificar o registo de atividade de sincronização de dados SQL e detetar erros e avisos, tinha anteriormente verificar a sincronização de dados SQL manualmente no portal do Azure ou utilizar o PowerShell ou a API REST. Siga os passos neste artigo para configurar uma solução personalizada que melhora a experiência de monitorização de sincronização de dados. Pode personalizar esta solução para se ajustarem ao seu cenário.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Para obter uma descrição geral da Sincronização de Dados SQL, veja [Sincronizar dados em várias bases de dados na cloud e no local com a Sincronização de Dados SQL do Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> Sincronização de dados SQL do Azure faz **não** suporta a instância gerida da base de dados SQL do Azure neste momento.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Dashboard de monitorização para todos os grupos de sincronização 

Já não terá de consultar os registos de cada grupo de sincronização individualmente para verificar se há problemas. Pode monitorizar todos os grupos de sincronização de qualquer uma das suas subscrições num único local, utilizando uma vista personalizada do Azure Monitor. Esta vista apresenta as informações que é importante para os clientes de sincronização de dados SQL.

![Dashboard de monitorização de sincronização de dados](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Notificações de E-Mail automatizadas

Já não terá de verificar o registo manualmente no portal do Azure ou através do PowerShell ou a API REST. Com o [registos do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), pode criar alertas que vão diretamente para os endereços de e-mail das pessoas que precisam para vê-los quando ocorre um erro.

![Notificações de e-mail de sincronização de dados](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Como configurar estas funcionalidades de monitorização? 

Implementar um Monitor personalizado do Azure registos de solução de monitorização para sincronização de dados SQL em menos de uma hora, efetuando os seguintes procedimentos:

Tem de configurar três componentes:

-   Um runbook do PowerShell para alimentar dados de registo de sincronização de dados SQL para registos do Azure Monitor.

-   Um alerta de Monitor do Azure para notificações por e-mail.

-   Uma vista de Monitor do Azure para monitorização.

### <a name="samples-to-download"></a>Exemplos para transferir

Transferir os exemplos de duas seguintes:

-   [Runbook do PowerShell de Log de sincronização de dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Modo de exibição de Monitor do Azure de sincronização de dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que configurou os seguintes procedimentos:

-   Uma conta de automatização do Azure

-   Área de trabalho do Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Runbook do PowerShell para obter o Log de sincronização de dados SQL 

Utilize um runbook do PowerShell alojado na automatização do Azure para extrair os dados de log de sincronização de dados SQL e enviá-lo para registos do Azure Monitor. Um script de exemplo está incluído. Como pré-requisito, tem de ter uma conta de automatização do Azure. Em seguida, terá de criar um runbook e agendar sua execução. 

### <a name="create-a-runbook"></a>Criar um runbook

Para obter mais informações sobre a criação de um runbook, consulte [meu primeiro runbook do PowerShell](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Em sua conta de automatização do Azure, selecione o **Runbooks** separador em automatização de processos.

2.  Selecione **adicionar um Runbook** no canto superior esquerdo da página de Runbooks.

3.  Selecione **importar um Runbook existente**.

4.  Sob **o ficheiro de Runbook**, utilize o determinado `DataSyncLogPowerShellRunbook` ficheiro. Definir o **tipo de Runbook** como `PowerShell`. Dê um nome de runbook.

5.  Selecione **Criar**. Tem agora um runbook.

6.  Em sua conta de automatização do Azure, selecione o **variáveis** separador sob recursos partilhados.

7.  Selecione **adicionar uma variável** na página de variáveis. Crie uma variável para armazenar o último tempo de execução do runbook. Se tiver vários runbooks, precisa de uma variável para cada runbook.

8.  Definir o nome da variável como `DataSyncLogLastUpdatedTime` e definir o tipo como DateTime.

9.  Selecione o runbook e clique no botão de edição na parte superior da página.

10. Faça as alterações necessárias para a sua conta e a configuração de sincronização de dados SQL. (Para obter informações mais detalhadas, consulte o script de exemplo.)

    1.  Informações do Azure.

    2.  Informações do grupo de sincronização.

    3.  Monitor do Azure regista as informações. Pode encontrar estas informações no Portal do Azure | Definições | Origens ligadas. Para obter mais informações sobre como enviar dados para os registos do Azure Monitor, consulte [enviar dados para logs de Monitor do Azure com a API de Recoletor de dados HTTP (pré-visualização)](../azure-monitor/platform/data-collector-api.md).

11. Execute o runbook no painel de teste. Verifique para se certificar de que ele foi concluída com êxito.

    Se tiver erros, certifique-se de que tem o módulo do PowerShell mais recente instalado. Pode instalar o módulo do PowerShell mais recente na **Galeria de módulos** na conta de automatização.

12. Clique em **publicar**

### <a name="schedule-the-runbook"></a>Agendar o runbook

Para agendar o runbook:

1.  Sob o runbook, selecione o **agendas** separador em recursos.

2.  Selecione **adicionar uma agenda** na página de agendas.

3.  Selecione **ligar uma agenda ao runbook**.

4.  Selecione **criar uma nova agenda.**

5.  Definir **periodicidade** para periódico e defina o intervalo que pretende. Utilize o mesmo intervalo aqui, no script e nos registos do Azure Monitor.

6.  Selecione **Criar**.

### <a name="check-the-automation"></a>Verifique a Automação

Para monitorar sua automação está em execução conforme esperado, em **descrição geral** para a sua conta de automatização, localizar o **estatísticas de tarefas** visualizado sob **monitorização**. Afixe esta vista ao dashboard para uma visualização mais fácil. Execuções com êxito o programa de runbook como "Concluído" e execuções com falhas mostram como "Falhado".

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Criar um alerta do leitor de Monitor do Azure para notificações por E-Mail

Para criar um alerta que utiliza registos do Azure Monitor, efetue os seguintes procedimentos. Como pré-requisito, tem de ter os logs de Monitor do Azure associados a uma área de trabalho do Log Analytics.

1.  No portal do Azure, selecione **pesquisa de registos**.

2.  Crie uma consulta para selecionar os erros e avisos por grupo de sincronização dentro do intervalo que selecionou. Por exemplo:

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  Depois de executar a consulta, selecione o Sino que diz **alerta**.

4.  Sob **gerar um alerta com base nos**, selecione **medida da métrica**.

    1.  Defina o valor agregado para **superior a**.

    2.  Após **superior a**, introduza o limiar devem decorrer antes de poder receber notificações. Espera-se erros transitórios na sincronização de dados. Para reduzir o ruído, defina o limiar para 5.

5.  Sob **ações**, defina **notificação por E-Mail** como "Sim". Introduza os destinatários de e-mail pretendido.

6.  Clique em **Guardar**. Agora, os destinatários especificados recebem notificações por e-mail quando ocorrerem erros.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Criar uma vista de Monitor do Azure para monitorização

Este passo cria uma vista de Azure Monitor para monitorizar visualmente todos os grupos de sincronização especificado. A vista inclui vários componentes:

-   Um mosaico de descrição geral, que mostra o número de erros, sucessos e avisos de todos os grupos de sincronização tem.

-   Um mosaico para todos os grupos de sincronização, que mostra a contagem de erros e avisos por grupo de sincronização. Grupos sem problemas não são apresentados neste mosaico.

-   Um mosaico para cada grupo de sincronização, que mostra o número de erros, êxitos e avisos e as mensagens de erro recentes.

Para configurar o modo de exibição de Monitor do Azure, efetue os seguintes procedimentos:

1.  Na home page de área de trabalho do Log Analytics, selecione o sinal de adição no lado esquerdo para abrir o **estruturador de vistas**.

2.  Selecione **importação** na barra superior do designer de vista. Em seguida, selecione o ficheiro de exemplo "DataSyncLogOMSView".

3.  A visualização de exemplo é para o gerenciamento de dois grupos de sincronização. Edite esta vista para se ajustarem ao seu cenário. Clique em **editar** e efetue as seguintes alterações:

    1.  Crie "nova anel e lista" objetos a partir da Galeria conforme necessário.

    2.  Em cada mosaico, Atualize as consultas com as suas informações.

        1.  Em cada mosaico, altere o intervalo de TimeStamp_t conforme pretendido.

        2.  Nos mosaicos para cada grupo de sincronização, atualize os nomes de grupo de sincronização.

    3.  Em cada mosaico, atualize o título, conforme necessário.

4.  Clique em **guardar** e a vista está pronta.

## <a name="cost-of-this-solution"></a>Custo desta solução

Na maioria dos casos, essa solução é gratuita.

**Automatização do Azure:** Pode haver um custo incorrido com a conta de automatização do Azure, dependendo da sua utilização. Os primeiros 500 minutos de tempo por mês de execução de tarefas são gratuitos. Na maioria dos casos, esta solução deverá utilizar menos de 500 minutos por mês. Para evitar encargos, agende o runbook seja executado num intervalo de duas horas ou mais. Para mais informações, veja [os preços de automatização](https://azure.microsoft.com/pricing/details/automation/).

**Registos de Monitor do Azure:** Pode haver um custo associado a registos do Azure Monitor, dependendo da sua utilização. O escalão gratuito inclui 500 MB de dados ingeridos por dia. Na maioria dos casos, esta solução é esperada para a ingestão de menos de 500 MB por dia. Para reduzir a utilização, utilize à apenas de falha de filtragem incluídos no runbook. Se estiver a utilizar mais de 500 MB por dia, atualize para o escalão pago para evitar o risco de análise a parar quando for atingida a limitação. Para mais informações, veja [registos do Azure Monitor preços](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Exemplos de código

Transfira os exemplos de código descritos neste artigo a partir das seguintes localizações:

-   [Runbook do PowerShell de Log de sincronização de dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Modo de exibição de Monitor do Azure de sincronização de dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre a Sincronização de Dados SQL, veja:

-   Descrição geral - [sincronizar dados em várias bases de dados na cloud e no local com sincronização de dados SQL do Azure](sql-database-sync-data.md)
-   Configurar a sincronização de dados
    - No portal - [Tutorial: Configurar a sincronização de dados SQL para sincronizar dados entre a base de dados do Azure SQL e SQL Server no local](sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        -  [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agente de - de sincronização de dados [agente de sincronização de dados SQL do Azure de sincronização de dados](sql-database-data-sync-agent.md)
-   Melhores práticas - [melhores práticas para a sincronização de dados SQL do Azure](sql-database-best-practices-data-sync.md)
-   Resolução de problemas - [solucionar problemas com a sincronização de dados SQL do Azure](sql-database-troubleshoot-data-sync.md)
-   Atualizar o esquema de sincronização
    -   Com o Transact-SQL - [automatizar a replicação das alterações de esquema na sincronização de dados SQL do Azure](sql-database-update-sync-schema.md)
    -   Com o PowerShell - [utilize o PowerShell para atualizar o esquema de sincronização num grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)

Para obter mais informações sobre a Base de Dados SQL, veja:

-   [Descrição Geral da Base de Dados SQL](sql-database-technical-overview.md)
-   [Gestão do Ciclo de Vida da Base de Dados](https://msdn.microsoft.com/library/jj907294.aspx)
