---
title: Monitor SQL Data Sync com registos do Monitor Azure
description: Saiba como monitorizar o SQL Data Sync utilizando registos do Monitor Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: b7c801d75d778deccae645e0945fba557dbc6782
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84188800"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Monitor SQL Data Sync com registos do Monitor Azure 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Para verificar o registo de atividade do SQL Data Sync e detetar erros e avisos, teve anteriormente de verificar o SQL Data Sync manualmente no portal Azure ou utilizar o PowerShell ou a API REST. Siga os passos deste artigo para configurar uma solução personalizada que melhore a experiência de monitorização do Data Sync. Pode personalizar esta solução para se adaptar ao seu cenário.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Para obter uma visão geral do SQL Data Sync, consulte [os dados do Sync em várias bases de dados de nuvem e no local com SQL Data Sync em Azure](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> O SQL Data Sync **não** suporta a Azure SQL Managed Instance neste momento.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Painel de monitorização para todos os seus Grupos de Sincronização 

Já não é necessário olhar os registos de cada Grupo Sync individualmente para procurar problemas. Pode monitorizar todos os seus Sync Groups a partir de qualquer uma das suas subscrições num só local, utilizando uma vista personalizada do Azure Monitor. Esta visualização surge na informação que interessa aos clientes sql Data Sync.

![Painel de monitorização do Data Sync](./media/sql-data-sync-monitor-sync/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Notificações automáticas de email

Já não é necessário verificar o registo manualmente no portal Azure ou através do PowerShell ou da API REST. Com [os registos do Azure Monitor,](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)pode criar alertas que vão diretamente para os endereços de e-mail das pessoas que precisam de os ver quando ocorre um erro.

![Notificações de email de Data Sync](./media/sql-data-sync-monitor-sync/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Como configura estas funcionalidades de monitorização? 

Implementar uma solução de monitorização de registos Azure Monitor personalizado para o SQL Data Sync em menos de uma hora, fazendo as seguintes coisas:

É necessário configurar três componentes:

-   Um livro de execução PowerShell para alimentar os dados de registo do SQL Data Sync para registos do Monitor Azure.

-   Um alerta do Azure Monitor para notificações por e-mail.

-   Uma vista do monitor Azure para monitorização.

### <a name="samples-to-download"></a>Amostras para descarregar

Faça o download das duas seguintes amostras:

-   [Data Sync Log PowerShell Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Vista do monitor Azure de Sincronização de Dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que configura as seguintes coisas:

-   Uma conta de Automação Azure

-   Área de trabalho do Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>PowerShell Runbook para obter registo sincronizado de dados SQL 

Utilize um livro de execução PowerShell alojado na Azure Automation para retirar os dados de registo de sincronização de dados SQL e enviá-lo para os registos do Azure Monitor. Um guião de amostra está incluído. Como pré-requisito, precisa de ter uma conta Azure Automation. Então precisa criar um livro de bordo e programar para ser executado. 

### <a name="create-a-runbook"></a>Criar um runbook

Para obter mais informações sobre a criação de um livro de recortes, consulte [o meu primeiro livro de execução PowerShell](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Na sua conta Azure Automation, selecione o **separador Runbooks** em Automação de Processos.

2.  **Selecione Adicionar um Runbook** no canto superior esquerdo da página Runbooks.

3.  Selecione **Importe um Runbook existente.**

4.  No **ficheiro Runbook,** utilize o `DataSyncLogPowerShellRunbook` ficheiro dado. Desa esta medida, o **tipo Runbook** `PowerShell` . Dê um nome ao livro de corridas.

5.  Selecione **Criar**. Agora tem um livro de bordo.

6.  Na sua Conta de Automação Azure, selecione o **separador Variáveis** em Recursos Partilhados.

7.  **Selecione Adicionar uma variável** na página 'Variáveis'. Crie uma variável para armazenar o último tempo de execução para o livro de recortes. Se tiver vários runbooks, precisa de uma variável para cada livro de formulários.

8.  Desa esta medida o nome variável `DataSyncLogLastUpdatedTime` e define o seu Tipo como DataTime.

9.  Selecione o runbook e clique no botão de edição na parte superior da página.

10. Faça as alterações necessárias para a sua conta e a sua configuração SQL Data Sync. (Para obter informações mais detalhadas, consulte o guião da amostra.)

    1.  Informação do Azure.

    2.  Informação do Grupo Sync.

    3.  O Monitor Azure regista informações. Encontre esta informação no portal Azure Configurações / Fontes ligadas. Para obter mais informações sobre o envio de dados para registos do Azure Monitor, consulte [Enviar dados para registos do Azure Monitor com a API do Colecionador de Dados HTTP (pré-visualização)](../../azure-monitor/platform/data-collector-api.md).

11. Coloque o livro de execução no painel de teste. Verifique se foi bem sucedido.

    Se tiver erros, certifique-se de que tem o módulo PowerShell mais recente instalado. Pode instalar o mais recente módulo PowerShell na Galeria de **Módulos** na sua Conta de Automação.

12. Clique **em Publicar**

### <a name="schedule-the-runbook"></a>Agende o livro de corridas

Para agendar o livro de recortes:

1.  No manual, selecione o **separador Agendas** em Recursos.

2.  **Selecione Adicionar uma Agenda** na página Agendas.

3.  Selecione **Link a Schedule para o seu runbook**.

4.  **Selecione Criar um novo horário.**

5.  **Desacordo a Recorrência** para Recorrente e desaprote o intervalo que deseja. Use o mesmo intervalo aqui, no script e nos registos do Monitor Azure.

6.  Selecione **Criar**.

### <a name="check-the-automation"></a>Verifique a automatização

Para monitorizar se a sua automatização está a funcionar como esperado, sob **visão geral** da sua conta de automação, encontre a vista **estatísticas de emprego** em **Monitorização**. Pina esta vista no seu painel de instrumentos para uma visualização fácil. As séries bem sucedidas do runbook mostram como "Completed" e "Failed runs show" como "Failed".

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Criar um alerta de leitor de monitores Azure para notificações de email

Para criar um alerta que utilize registos do Azure Monitor, faça as seguintes coisas. Como pré-requisito, precisa de ter registos do Monitor Azure ligados a um espaço de trabalho log analytics.

1.  No portal Azure, selecione **Registar-se .**

2.  Crie uma consulta para selecionar os erros e avisos por grupo sincronizado dentro do intervalo selecionado. Por exemplo:

    `DataSyncLog_CL | where LogLevel_s != "Success" | summarize AggregatedValue = count() by bin(TimeGenerated,60m),SyncGroupName_s`

3.  Depois de executar a consulta, selecione a campainha que diz **Alerta**.

4.  Em **Alerta de Gerar com base em**, **selecione Métrica Medida**.

    1.  Defina o Valor Agregado para **Maior do que**.

    2.  Depois de **maior do que,** introduza o limiar para decorrer antes de receber notificações. São esperados erros transitórios no Data Sync. Para reduzir o ruído, desa um limite para 5.

5.  Em **Ações,** desa um **aviso de e-mail** para "Sim". Insira os destinatários de e-mail pretendidos.

6.  Clique em **Guardar**. Os destinatários especificados recebem agora notificações por e-mail quando ocorrem erros.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Criar uma vista de monitor azure para monitorização

Este passo cria uma visão do Azure Monitor para monitorizar visualmente todos os grupos de sincronização especificados. A vista inclui vários componentes:

-   Um azulejo geral, que mostra quantos erros, sucessos e avisos todos os grupos de sincronização têm.

-   Um azulejo para todos os grupos de sincronização, que mostra a contagem de erros e avisos por grupo de sincronização. Grupos sem problemas não aparecem neste azulejo.

-   Um azulejo para cada Sync Group, que mostra o número de erros, sucessos e avisos, e as recentes mensagens de erro.

Para configurar a vista do Monitor Azure, faça as seguintes coisas:

1.  Na página inicial do espaço de trabalho Log Analytics, selecione o mais à esquerda para abrir o **designer de visualização**.

2.  **Selecione Import** na barra superior do designer de visualização. Em seguida, selecione o ficheiro de amostra "DataSyncLogOMSView".

3.  A amostra é para gerir dois grupos de sincronização. Edite esta vista para se encaixar no seu cenário. Clique **em editar** e faça as seguintes alterações:

    1.  Crie novos objetos "Donut & List" da Galeria, conforme necessário.

    2.  Em cada azulejo, atualize as consultas com as suas informações.

        1.  Em cada azulejo, altere o intervalo de TimeStamp_t conforme desejado.

        2.  Nos azulejos de cada Grupo Sync, atualize os nomes do Grupo Sync.

    3.  Em cada azulejo, atualize o título conforme necessário.

4.  Clique **em Guardar** e a vista está pronta.

## <a name="cost-of-this-solution"></a>Custo desta solução

Na maioria dos casos, esta solução é gratuita.

**Azure Automation:** Pode haver um custo incorrido com a conta Azure Automation, dependendo da sua utilização. Os primeiros 500 minutos de trabalho por mês são gratuitos. Na maioria dos casos, esta solução deverá utilizar menos de 500 minutos por mês. Para evitar encargos, agende o livro de execução para funcionar com um intervalo de duas horas ou mais. Para mais informações, consulte [os preços da Automação.](https://azure.microsoft.com/pricing/details/automation/)

**Registos do Monitor Azure:** Pode haver um custo associado aos registos do Azure Monitor dependendo da sua utilização. O nível livre inclui 500 MB de dados ingeridos por dia. Na maioria dos casos, esta solução deverá ingerir menos de 500 MB por dia. Para diminuir a utilização, utilize a filtragem apenas por falha incluída no livro de recortes. Se estiver a utilizar mais de 500 MB por dia, atualize para o nível pago para evitar o risco de paragem da análise quando a limitação for atingida. Para obter mais informações, consulte [os preços dos registos do Azure Monitor](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Exemplos de código

Descarregue as amostras de código descritas neste artigo a partir dos seguintes locais:

-   [Data Sync Log PowerShell Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Vista do monitor Azure de Sincronização de Dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a Sincronização de Dados SQL, veja:

-   Visão geral - [Sync dados em várias bases de dados de nuvem e no local com SQL Data Sync em Azure](sql-data-sync-data-sql-server-sql-database.md)
-   Configurar o Data Sync
    - No portal - [Tutorial: Configurar o SQL Data Sync para sincronizar dados entre a Base de Dados SQL do Azure e o SqL Server](sql-data-sync-sql-server-configure.md)
    - Com o PowerShell
        -  [Utilize o PowerShell para sincronizar entre várias bases de dados na Base de Dados Azure SQL](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Utilize o PowerShell para sincronizar entre uma base de dados na Base de Dados Azure SQL e uma base de dados num caso de Servidor SQL](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Data Sync Agent - [Data Sync Agent for SQL Data Sync in Azure](sql-data-sync-agent-overview.md)
-   Melhores práticas - [Melhores práticas para SQL Data Sync em Azure](sql-data-sync-best-practices.md)
-   Resolução de problemas - [Problemas de resolução de problemas com SQL Data Sync em Azure](sql-data-sync-troubleshoot.md)
-   Atualizar o esquema de sincronização
    -   Com a Transact-SQL - [Automatizar a replicação de alterações de esquema no SQL Data Sync em Azure](sql-data-sync-update-sync-schema.md)
    -   Com o PowerShell - [Use o PowerShell para atualizar o esquema de sincronização num grupo de sincronização existente](scripts/update-sync-schema-in-sync-group.md)

Para obter mais informações sobre a Base de Dados SQL, veja:

-   [Descrição Geral da Base de Dados SQL](../../sql-database/sql-database-technical-overview.md)
-   [Gestão do Ciclo de Vida da Base de Dados](https://msdn.microsoft.com/library/jj907294.aspx)
