---
title: Monitor SQL Data Sync com registos do Monitor Azure
description: Saiba como monitorizar o SQL Data Sync do Azure utilizando registos do Monitor Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 0ed0bd3544fff89c8230267e3d6d8826c5ae3c7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74114614"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Monitor SQL Data Sync com registos do Monitor Azure 

Para verificar o registo de atividades do SQL Data Sync e detetar erros e avisos, teve de verificar manualmente o SQL Data Sync no portal Azure ou utilizar o PowerShell ou o REST API. Siga os passos deste artigo para configurar uma solução personalizada que melhore a experiência de monitorização do Data Sync. Pode personalizar esta solução para se adaptar ao seu cenário.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Para obter uma descrição geral da Sincronização de Dados SQL, veja [Sincronizar dados em várias bases de dados na cloud e no local com a Sincronização de Dados SQL do Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> O Azure SQL Data Sync **não** suporta a Instância Gerida pela Base de Dados Azure SQL neste momento.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Painel de monitorização para todos os seus Grupos de Sincronização 

Já não é necessário olhar através dos registos de cada Sync Group individualmente para procurar problemas. Pode monitorizar todos os seus Sync Groups a partir de qualquer uma das suas subscrições num só local, utilizando uma vista personalizada do Monitor Azure. Esta vista surge a informação que interessa aos clientes SQL Data Sync.

![Painel de monitorização de Data Sync](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Notificações automáticas de e-mail

Já não é necessário verificar o registo manualmente no portal Azure ou através da PowerShell ou da API REST. Com [os registos do Monitor Azure,](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)pode criar alertas que vão diretamente para os endereços de e-mail das pessoas que precisam de os ver quando ocorre um erro.

![Notificações de e-mail sincronizadas de dados](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Como configura estas funcionalidades de monitorização? 

Implemente uma solução personalizada de monitorização de registos do Monitor Azure para o SQL Data Sync em menos de uma hora, fazendo as seguintes coisas:

É necessário configurar três componentes:

-   Um livro de execução powerShell para alimentar os dados de registo do SQL Data Sync para os registos do Monitor Azure.

-   Um alerta do Monitor Azure para notificações por e-mail.

-   Uma visão de monitor Azure para monitorização.

### <a name="samples-to-download"></a>Amostras para descarregar

Faça o download das duas amostras seguintes:

-   [Livro de execução powershell de sincronização de dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Visão de monitor de data Sync Azure](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Pré-requisitos

Certifique-se de ter configurado as seguintes coisas:

-   Uma conta de Automação Azure

-   Área de trabalho do Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>PowerShell Runbook para obter Registo de Sincronização de Dados SQL 

Utilize um livro de execução PowerShell hospedado na Automatização Azure para retirar os dados de registo do SQL Data Sync e enviá-lo para registos do Monitor Azure. Um script de amostra está incluído. Como pré-requisito, é necessário ter uma conta De Automação Azure. Então tens de criar um livro de corridas e programar para correr. 

### <a name="create-a-runbook"></a>Criar um runbook

Para mais informações sobre a criação de um livro de corridas, consulte o meu primeiro livro de [corridas PowerShell.](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell)

1.  Na sua conta De Automação Azure, selecione o separador **Runbooks** em fase de automatização de processos.

2.  **Selecione Adicionar um Livro de Ensaios** no canto superior esquerdo da página Runbooks.

3.  Selecione Importar um Livro de **Execução existente**.

4.  No **ficheiro Runbook,** `DataSyncLogPowerShellRunbook` utilize o ficheiro dado. Desloque o `PowerShell` **tipo DeRlivro** como . Dê um nome ao livro de corridas.

5.  Selecione **Criar**. Agora tem um livro de corridas.

6.  Na sua Conta de Automação Azure, selecione o separador **Variáveis** em Recursos Partilhados.

7.  Selecione **Adicionar uma variável** na página Variáveis. Crie uma variável para armazenar o último tempo de execução para o livro de corridas. Se tiver vários livros de execução, precisa de uma variável para cada livro de execução.

8.  Dete te `DataSyncLogLastUpdatedTime` o nome variável como e desloque o seu Tipo como DataTime.

9.  Selecione o livro de execução e clique no botão de edição na parte superior da página.

10. Faça as alterações necessárias para a sua conta e a configuração SQL Data Sync. (Para obter informações mais detalhadas, consulte o script da amostra.)

    1.  Informação azure.

    2.  Informação do Grupo Sync.

    3.  A Azure Monitor regista informações. Encontre esta informação no Portal Azure [ Definições / Fontes Ligadas. Para obter mais informações sobre o envio de dados para os registos do Monitor Do Azure, consulte Enviar dados para os registos do [Monitor Azure com a API de Recolha de Dados HTTP (pré-visualização)](../azure-monitor/platform/data-collector-api.md).

11. Execute o livro de corridas no painel de teste. Verifique se foi bem sucedido.

    Se tiver erros, certifique-se de que tem o mais recente módulo PowerShell instalado. Pode instalar o mais recente módulo PowerShell na Galeria de **Módulos** na sua Conta de Automação.

12. Clique em **Publicar**

### <a name="schedule-the-runbook"></a>Agende o livro de corridas

Para agendar o livro de corridas:

1.  No livro de execução, selecione o separador **'Agendas'** em Recursos.

2.  Selecione **Adicionar um Horário** na página de Horários.

3.  Selecione Ligar um Horário ao seu livro de **execução**.

4.  Selecione **Criar um novo horário.**

5.  **Delineie a Recorrência** para Recorrente e delineie o intervalo que desejar. Use o mesmo intervalo aqui, no script, e nos registos do Monitor Azure.

6.  Selecione **Criar**.

### <a name="check-the-automation"></a>Verifique a automatização

Para monitorizar se a sua automatização está a funcionar como esperado, sob **a visão geral** da sua conta de automação, encontre a visão de Estatísticas de **Emprego** em **Monitorização**. Coloque esta vista no seu painel para uma visualização fácil. As corridas bem sucedidas do programa de runbook como "Completed" e Failed runs mostram como "Falhado".

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Criar um Alerta de Leitor de Monitores Azure para notificações de e-mail

Para criar um alerta que utilize registos do Monitor Azure, faça as seguintes coisas. Como pré-requisito, é necessário ter registos do Monitor Azure ligados a um espaço de trabalho do Log Analytics.

1.  No portal Azure, selecione **Pesquisa de Registo**.

2.  Crie uma consulta para selecionar os erros e avisos por grupo sincronizado dentro do intervalo selecionado. Por exemplo:

    `DataSyncLog_CL | where LogLevel_s != "Success" | summarize AggregatedValue = count() by bin(TimeGenerated,60m),SyncGroupName_s`

3.  Depois de executar a consulta, selecione o sino que diz **Alerta**.

4.  Sob **o alerta De Gerar com base em**, selecione Métrica **.**

    1.  Defina o Valor Agregado para **Maior do que**.

    2.  Depois **de maior do que**, insira o limiar para decorrido antes de receber notificações. Esperam-se erros transitórios no Data Sync. Para reduzir o ruído, desloque o limiar para 5.

5.  No âmbito **de Ações**, deteteto a notificação por **e-mail** para "Sim". Insira os destinatários de e-mail pretendidos.

6.  Clique em **Guardar**. Os destinatários especificados recebem agora notificações por e-mail quando ocorrem erros.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Criar uma visão de monitor Azure para monitorização

Este passo cria uma visão do Monitor Azure para monitorizar visualmente todos os grupos de sincronização especificados. A vista inclui vários componentes:

-   Um azulejo de visão geral, que mostra quantos erros, sucessos e avisos todos os grupos de sincronização têm.

-   Um azulejo para todos os grupos de sincronização, que mostra a contagem de erros e avisos por grupo de sincronização. Grupos sem problemas não aparecem neste azulejo.

-   Um azulejo para cada Sync Group, que mostra o número de erros, sucessos e avisos, e as recentes mensagens de erro.

Para configurar a vista Do Monitor Azure, faça as seguintes coisas:

1.  Na página inicial do espaço de trabalho Log Analytics, selecione o plus à esquerda para abrir o designer de **visualização**.

2.  Selecione **Importar** na barra superior do designer de visualização. Em seguida, selecione o ficheiro de amostra "DataSyncLogOMSView".

3.  A visão da amostra é para gerir dois grupos de sincronização. Edite esta vista para se adaptar ao seu cenário. Clique em **editar** e fazer as seguintes alterações:

    1.  Crie novos objetos "Donut & List" da Galeria, conforme necessário.

    2.  Em cada azulejo, atualize as consultas com as suas informações.

        1.  Em cada azulejo, altere o intervalo de TimeStamp_t conforme desejado.

        2.  Nos azulejos de cada Sync Group, atualize os nomes do Sync Group.

    3.  Em cada azulejo, atualize o título conforme necessário.

4.  Clique em **Guardar** e a vista está pronta.

## <a name="cost-of-this-solution"></a>Custo desta solução

Na maioria dos casos, esta solução é gratuita.

**Automação Azure:** Pode haver um custo incorrido com a conta Azure Automation, dependendo da sua utilização. Os primeiros 500 minutos de trabalho por mês são gratuitos. Na maioria dos casos, espera-se que esta solução utilize menos de 500 minutos por mês. Para evitar encargos, agende o livro de execução para funcionar num intervalo de duas horas ou mais. Para mais informações, consulte [preços de Automação.](https://azure.microsoft.com/pricing/details/automation/)

**Registos do Monitor Azure:** Pode haver um custo associado aos registos do Monitor Azure dependendo da sua utilização. O nível livre inclui 500 MB de dados ingeridos por dia. Na maioria dos casos, espera-se que esta solução ingere menos de 500 MB por dia. Para diminuir a utilização, utilize a filtragem apenas por falha incluída no livro de execução. Se estiver a utilizar mais de 500 MB por dia, atualize para o nível pago para evitar o risco de paragem da análise quando a limitação é atingida. Para mais informações, consulte os preços dos [registos do Monitor Azure.](https://azure.microsoft.com/pricing/details/log-analytics/)

## <a name="code-samples"></a>Exemplos de código

Faça o download das amostras de código descritas neste artigo a partir dos seguintes locais:

-   [Livro de execução powershell de sincronização de dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Visão de monitor de data Sync Azure](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a Sincronização de Dados SQL, veja:

-   Visão geral - Sync dados em várias bases de dados de nuvem e no local com O Sincronizado de [Dados Azure SQL](sql-database-sync-data.md)
-   Configurar o Sincronizado de Dados
    - No portal - [Tutorial: Configurar o SQL Data Sync para sincronizar dados entre a Base de Dados Azure SQL e o Servidor SQL no local](sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        -  [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Data Sync Agent - [Data Sync Agent for Azure SQL Data Sync](sql-database-data-sync-agent.md)
-   Boas práticas - [Boas práticas para O Sincronização de Dados Azure SQL](sql-database-best-practices-data-sync.md)
-   Troubleshoot - [Problemas com O Sincronizado de Dados Azure SQL](sql-database-troubleshoot-data-sync.md)
-   Atualizar o esquema de sincronização
    -   Com a Transact-SQL - [Automatizar a replicação de alterações de esquemas no Azure SQL Data Sync](sql-database-update-sync-schema.md)
    -   Com powerShell - [Use powerShell para atualizar o esquema de sincronização num grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)

Para obter mais informações sobre a Base de Dados SQL, veja:

-   [Descrição Geral da Base de Dados SQL](sql-database-technical-overview.md)
-   [Gestão do Ciclo de Vida da Base de Dados](https://msdn.microsoft.com/library/jj907294.aspx)
