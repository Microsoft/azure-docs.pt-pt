---
title: Monitorar Sincronização de Dados SQL do Azure com logs de Azure Monitor | Microsoft Docs
description: Saiba como monitorar Sincronização de Dados SQL do Azure usando logs de Azure Monitor
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
ms.openlocfilehash: 26dc1ebef1c627ed2b20eb0fda68b2ca2d01b82a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791748"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Monitorar Sincronização de Dados SQL com logs de Azure Monitor 

Para verificar o log de atividades do Sincronização de Dados SQL e detectar erros e avisos, antes você tinha que verificar Sincronização de Dados SQL manualmente no portal do Azure ou usar o PowerShell ou a API REST. Siga as etapas neste artigo para configurar uma solução personalizada que melhora a experiência de monitoramento da sincronização de dados. Você pode personalizar essa solução para se ajustar ao seu cenário.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Para obter uma descrição geral da Sincronização de Dados SQL, veja [Sincronizar dados em várias bases de dados na cloud e no local com a Sincronização de Dados SQL do Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> O Azure Sincronização de Dados SQL **não oferece suporte a** instância gerenciada do banco de dados SQL do Azure no momento.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Painel de monitoramento para todos os seus grupos de sincronização 

Você não precisa mais examinar os logs de cada grupo de sincronização individualmente para procurar por problemas. Você pode monitorar todos os seus grupos de sincronização de qualquer uma de suas assinaturas em um único lugar usando uma exibição de Azure Monitor personalizada. Essa exibição mostra as informações que são importantes para Sincronização de Dados SQL clientes.

![Painel de monitoramento de sincronização de dados](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Notificações por email automatizadas

Você não precisa mais verificar o log manualmente no portal do Azure ou por meio do PowerShell ou da API REST. Com [os logs de Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), você pode criar alertas que vão diretamente para os endereços de email das pessoas que precisam vê-los quando ocorrer um erro.

![Notificações por email de sincronização de dados](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Como configurar esses recursos de monitoramento? 

Implemente uma solução de monitoramento de logs de Azure Monitor personalizada para Sincronização de Dados SQL em menos de uma hora, fazendo o seguinte:

Você precisa configurar três componentes:

-   Um runbook do PowerShell para alimentar Sincronização de Dados SQL dados de log para Azure Monitor logs.

-   Um alerta de Azure Monitor para notificações por email.

-   Uma exibição Azure Monitor para monitoramento.

### <a name="samples-to-download"></a>Exemplos para baixar

Baixe os dois exemplos a seguir:

-   [Runbook do PowerShell de log de sincronização de dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Exibição de Azure Monitor de sincronização de dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Pré-requisitos

Verifique se você configurou os seguintes itens:

-   Uma conta de automação do Azure

-   Espaço de trabalho Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Runbook do PowerShell para obter Sincronização de Dados SQL log 

Use um runbook do PowerShell hospedado na automação do Azure para efetuar pull do Sincronização de Dados SQL dados de log e enviá-los para Azure Monitor logs. Um script de exemplo está incluído. Como pré-requisito, você precisa ter uma conta de automação do Azure. Em seguida, você precisa criar um runbook e agendá-lo para execução. 

### <a name="create-a-runbook"></a>Criar um runbook

Para obter mais informações sobre como criar um runbook, consulte [meu primeiro runbook do PowerShell](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Na sua conta de automação do Azure, selecione a guia **Runbooks** em automação de processo.

2.  Selecione **Adicionar um runbook** no canto superior esquerdo da página Runbooks.

3.  Selecione **importar um runbook existente**.

4.  Em **arquivo de runbook**, use o arquivo de `DataSyncLogPowerShellRunbook` fornecido. Defina o **tipo de runbook** como `PowerShell`. Dê um nome ao runbook.

5.  Selecione **Criar**. Agora você tem um runbook.

6.  Na sua conta de automação do Azure, selecione a guia **variáveis** em recursos compartilhados.

7.  Selecione **Adicionar uma variável** na página variáveis. Crie uma variável para armazenar a hora da última execução para o runbook. Se você tiver vários runbooks, precisará de uma variável para cada runbook.

8.  Defina o nome da variável como `DataSyncLogLastUpdatedTime` e defina seu tipo como DateTime.

9.  Selecione o runbook e clique no botão Editar na parte superior da página.

10. Faça as alterações necessárias para sua conta e sua configuração de Sincronização de Dados SQL. (Para obter informações mais detalhadas, consulte o script de exemplo.)

    1.  Informações do Azure.

    2.  Informações do grupo de sincronização.

    3.  Azure Monitor registra as informações. Localizar essas informações no portal do Azure | Configurações | Fontes conectadas. Para obter mais informações sobre como enviar dados para logs de Azure Monitor, consulte [enviar dados para logs de Azure monitor com a API do coletor de dados http (versão prévia)](../azure-monitor/platform/data-collector-api.md).

11. Execute o runbook no painel de teste. Verifique se foi bem-sucedido.

    Se você tiver erros, verifique se você tem o módulo mais recente do PowerShell instalado. Você pode instalar o módulo mais recente do PowerShell na **Galeria de módulos** em sua conta de automação.

12. Clique em **publicar**

### <a name="schedule-the-runbook"></a>Agendar o runbook

Para agendar o runbook:

1.  No runbook, selecione a guia **agendas** em recursos.

2.  Selecione **Adicionar um agendamento** na página agendas.

3.  Selecione **vincular um agendamento ao seu runbook**.

4.  Selecione **criar um novo agendamento.**

5.  Defina **recorrência** como recorrente e defina o intervalo desejado. Use o mesmo intervalo aqui, no script e em logs de Azure Monitor.

6.  Selecione **Criar**.

### <a name="check-the-automation"></a>Verificar a automação

Para monitorar se a automação está sendo executada conforme o esperado, em **visão geral** da sua conta de automação, localize a exibição **Estatísticas de trabalho** em **monitoramento**. Fixe essa exibição ao seu painel para facilitar a exibição. Execuções com êxito do runbook mostram como "concluído" e execuções com falha mostram como "falha".

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Criar um alerta do leitor de Azure Monitor para notificações por email

Para criar um alerta que usa logs de Azure Monitor, faça o seguinte. Como pré-requisito, você precisa ter Azure Monitor logs vinculados a um espaço de trabalho Log Analytics.

1.  Na portal do Azure, selecione **pesquisa de logs**.

2.  Crie uma consulta para selecionar os erros e avisos por grupo de sincronização dentro do intervalo selecionado. Por exemplo:

    `DataSyncLog_CL | where TimeGenerated > ago(60m) | where LogLevel_s != "Success" | summarize count() by SyncGroupName_s`

3.  Depois de executar a consulta, selecione o sino que diz **alerta**.

4.  Em **gerar alerta com base em**, selecione **medição de métrica**.

    1.  Defina o valor de agregação para **maior que**.

    2.  Após **maior que**, insira o limite a ser decorrido antes de receber notificações. Erros transitórios são esperados na sincronização de dados. Para reduzir o ruído, defina o limite como 5.

5.  Em **ações**, defina **notificação por email** como "Sim". Insira os destinatários de email desejados.

6.  Clique em **Guardar**. Os destinatários especificados agora recebem notificações por email quando ocorrem erros.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Criar uma exibição de Azure Monitor para monitoramento

Esta etapa cria uma exibição Azure Monitor para monitorar visualmente todos os grupos de sincronização especificados. A exibição inclui vários componentes:

-   Um bloco de visão geral, que mostra quantos erros, êxitos e avisos todos os grupos de sincronização têm.

-   Um bloco para todos os grupos de sincronização, que mostra a contagem de erros e avisos por grupo de sincronização. Grupos sem problemas não aparecem neste bloco.

-   Um bloco para cada grupo de sincronização, que mostra o número de erros, êxitos e avisos e as mensagens de erro recentes.

Para configurar o Azure Monitor exibição, faça o seguinte:

1.  Na home page do espaço de trabalho Log Analytics, selecione o mais à esquerda para abrir o **Designer de exibição**.

2.  Selecione **importar** na barra superior do designer de exibição. Em seguida, selecione o arquivo de exemplo "DataSyncLogOMSView".

3.  A exibição de exemplo é para o gerenciamento de dois grupos de sincronização. Edite essa exibição para se ajustar ao seu cenário. Clique em **Editar** e faça as seguintes alterações:

    1.  Crie novos objetos de "lista de & de rosca" da galeria, conforme necessário.

    2.  Em cada bloco, atualize as consultas com suas informações.

        1.  Em cada bloco, altere o intervalo de TimeStamp_t conforme desejado.

        2.  Nos blocos de cada grupo de sincronização, atualize os nomes dos grupos de sincronização.

    3.  Em cada bloco, atualize o título conforme necessário.

4.  Clique em **salvar** e a exibição estará pronta.

## <a name="cost-of-this-solution"></a>Custo desta solução

Na maioria dos casos, essa solução é gratuita.

**Automação do Azure:** Pode haver um custo incorrido com a conta de automação do Azure, dependendo do seu uso. Os primeiros 500 minutos de tempo de execução do trabalho por mês são gratuitos. Na maioria dos casos, espera-se que essa solução use menos de 500 minutos por mês. Para evitar encargos, agende o runbook para ser executado em um intervalo de duas horas ou mais. Para obter mais informações, consulte [preços de automação](https://azure.microsoft.com/pricing/details/automation/).

**Logs de Azure monitor:** Pode haver um custo associado a Azure Monitor logs, dependendo do seu uso. A camada gratuita inclui 500 MB de dados ingeridos por dia. Na maioria dos casos, espera-se que essa solução insome menos de 500 MB por dia. Para diminuir o uso, use a filtragem somente de falha incluída no runbook. Se você estiver usando mais de 500 MB por dia, atualize para a camada paga para evitar o risco de a análise parar quando a limitação for atingida. Para obter mais informações, consulte [preços de Azure monitor logs](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Exemplos de código

Baixe os exemplos de código descritos neste artigo nos seguintes locais:

-   [Runbook do PowerShell de log de sincronização de dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Exibição de Azure Monitor de sincronização de dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a Sincronização de Dados SQL, veja:

-   Visão geral – [sincronizar dados entre vários bancos de dados locais e de nuvem com o Azure sincronização de dados SQL](sql-database-sync-data.md)
-   Configurar a sincronização de dados
    - No portal- [tutorial: configurar sincronização de dados SQL para sincronizar os dados entre o Azure SQL Database e o SQL Server local](sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        -  [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agente de sincronização de dados- [agente de sincronização de dados para Azure sincronização de dados SQL](sql-database-data-sync-agent.md)
-   Práticas recomendadas- [práticas recomendadas para o Azure sincronização de dados SQL](sql-database-best-practices-data-sync.md)
-   Solucionar problemas- [solucionar problema com o Azure sincronização de dados SQL](sql-database-troubleshoot-data-sync.md)
-   Atualizar o esquema de sincronização
    -   Com o Transact-SQL – [Automatize a replicação de alterações de esquema no Azure sincronização de dados SQL](sql-database-update-sync-schema.md)
    -   Com o PowerShell- [use o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)

Para obter mais informações sobre a Base de Dados SQL, veja:

-   [Descrição Geral da Base de Dados SQL](sql-database-technical-overview.md)
-   [Gestão do Ciclo de Vida da Base de Dados](https://msdn.microsoft.com/library/jj907294.aspx)
