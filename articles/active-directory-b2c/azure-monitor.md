---
title: Monitor Azure AD B2C com Monitor Azure
titleSuffix: Azure AD B2C
description: Saiba como registar eventos Azure AD B2C com o Azure Monitor utilizando a gestão de recursos delegada.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 01/29/2021
ms.openlocfilehash: bc1dea8121d7986b8394adf6545a0b2c30afb133
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580186"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Monitor Azure AD B2C com Monitor Azure

Utilize o Azure Monitor para encaminhar o Azure Ative Directory B2C (Azure AD B2C) para iniciar sessão de registos e [auditorias](view-audit-logs.md) para diferentes soluções de monitorização. Em seguida, pode retê-los para utilização a longo prazo ou integrá-los com ferramentas de gestão de informações e eventos de segurança (SIEM) de terceiros para obter informações sobre o ambiente.

Pode encaminhar eventos de registo para:

* Uma conta [de armazenamento](../storage/blobs/storage-blobs-introduction.md)Azure.
* Um [espaço de trabalho Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) (para analisar dados, criar dashboards e alertar para eventos específicos).
* Um hub [de eventos](../event-hubs/event-hubs-about.md) Azure (e integrar-se com as suas instâncias Splunk e Sumo Logic).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

Neste artigo, aprende-se a transferir os registos para um espaço de trabalho Azure Log Analytics. Em seguida, pode criar um dashboard ou criar alertas baseados nas atividades dos utilizadores do Azure AD B2C.

> [!IMPORTANT]
> Quando planeia transferir registos Azure AD B2C para diferentes soluções de monitorização, ou repositório, considere o seguinte. Os registos Azure AD B2C contêm dados pessoais. Esses dados devem ser tratados de forma a garantir a segurança adequada dos dados pessoais, incluindo a proteção contra o tratamento não autorizado ou ilícito, utilizando medidas técnicas ou organizativas adequadas.


## <a name="deployment-overview"></a>Descrição geral da implementação

Azure AD B2C aproveita [a monitorização do Azure Ative Directory](../active-directory/reports-monitoring/overview-monitoring.md). Para ativar *as definições de Diagnóstico* no Azure Ative Directory dentro do seu inquilino Azure AD B2C, utiliza o Farol [Azure](../lighthouse/concepts/azure-delegated-resource-management.md) para [delegar um recurso,](../lighthouse/concepts/azure-delegated-resource-management.md)que permite ao seu Azure AD B2C (o **Fornecedor de Serviços)** gerir um recurso Azure AD (o **Cliente).** Depois de completar os passos deste artigo, terá acesso ao grupo de recursos *ad-b2c-monitor azure-ad-b2c* que contém o espaço de [trabalho Log Analytics](../azure-monitor/logs/quick-create-workspace.md) no seu portal **Azure AD B2C.** Também poderá transferir os registos do Azure AD B2C para o seu espaço de trabalho Log Analytics.

Durante esta implementação, você autorizará um utilizador ou grupo no seu diretório Azure AD B2C para configurar a instância do espaço de trabalho Log Analytics dentro do inquilino que contém a sua assinatura Azure. Para criar a autorização, você implementa um modelo [de Gestor de Recursos Azure](../azure-resource-manager/index.yml) para o seu inquilino AZure AD contendo a subscrição.

O diagrama que se segue retrata os componentes que irá configurar nos seus inquilinos AZure AD e Azure AD B2C.

![Projeção do grupo de recursos](./media/azure-monitor/resource-group-projection.png)

Durante esta implantação, você irá configurar tanto o seu inquilino Azure AD B2C como o inquilino AD AD Azure onde o espaço de trabalho Log Analytics será hospedado. A conta Azure AD B2C deve ser atribuída ao [administrador global](../active-directory/roles/permissions-reference.md#limit-use-of-global-administrator) do inquilino Azure AD B2C. A conta Azure AD utilizada para executar a implementação deve ser atribuída a função [de Proprietário](../role-based-access-control/built-in-roles.md#owner) na subscrição AZure AD. Também é importante ter a certeza de que está inscrito no diretório correto à medida que completa cada passo descrito.

## <a name="1-create-or-choose-resource-group"></a>1. Criar ou escolher grupo de recursos

Em primeiro lugar, crie ou escolha um grupo de recursos que contenha o espaço de trabalho Log Analytics de destino que receberá dados do Azure AD B2C. Especificará o nome do grupo de recursos quando implementar o modelo do Gestor de Recursos Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu **inquilino AD Azure**.
1. [Crie um grupo de recursos](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) ou escolha um existente. Este exemplo utiliza um grupo de recursos chamado *azure-ad-b2c-monitor*.

## <a name="2-create-a-log-analytics-workspace"></a>2. Criar um espaço de trabalho Log Analytics

Um **espaço de trabalho Log Analytics** é um ambiente único para os dados de registo do Azure Monitor. Você usará este espaço de trabalho log Analytics para recolher dados de [registos](view-audit-logs.md)de auditoria AD B2C Azure , e depois visualizá-lo com consultas e livros de trabalho, ou criar alertas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu **inquilino AD Azure**.
1. [Criar um espaço de trabalho Log Analytics](../azure-monitor/logs/quick-create-workspace.md). Este exemplo utiliza um espaço de trabalho Log Analytics chamado *AzureAdB2C,* num grupo de recursos chamado *azure-ad-b2c-monitor*.

## <a name="3-delegate-resource-management"></a>3. Gestão de recursos delegados

Neste passo, você escolhe o seu inquilino Azure AD B2C como prestador de **serviços.** Você também define as autorizações que você precisa para atribuir as funções adequadas Azure incorporados a grupos no seu inquilino AZure AD.

### <a name="31-get-your-azure-ad-b2c-tenant-id"></a>3.1 Obtenha o seu ID do inquilino Azure AD B2C

Primeiro, obtenha a **ID** do inquilino do seu diretório Azure AD B2C (também conhecido como iD do diretório).

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino **Azure AD B2C.**
1. Selecione **Azure Ative Directory**, selecione **Overview**.
1. Grave a identificação do **inquilino.**

### <a name="32-select-a-security-group"></a>3.2 Selecione um grupo de segurança

Agora selecione um grupo AD B2C Azure ou utilizador ao qual pretende dar permissão ao grupo de recursos que criou anteriormente no diretório que contém a sua subscrição.  

Para facilitar a gestão, recomendamos a utilização de *grupos* de utilizadores AZure AD para cada função, permitindo-lhe adicionar ou remover utilizadores individuais ao grupo em vez de atribuir permissões diretamente a esse utilizador. Nesta passagem, vamos adicionar um grupo de segurança.

> [!IMPORTANT]
> Para adicionar permissões para um grupo AD Azure, o **tipo de grupo** deve ser definido como **Segurança**. Esta opção é selecionada quando o grupo é criado. Para obter mais informações, consulte [Criar um grupo básico e adicionar membros utilizando o Azure Ative Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. Com **o Azure Ative Directory** ainda selecionado no seu diretório **Azure AD B2C,** selecione **Grupos** e, em seguida, selecione um grupo. Se não tiver um grupo existente, crie um grupo **de Segurança** e adicione membros. Para mais informações, siga o procedimento [Crie um grupo básico e adicione membros usando o Azure Ative Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 
1. Selecione **Overview** e grave o **ID** do objeto do grupo .

### <a name="33-create-an-azure-resource-manager-template"></a>3.3 Criar um modelo de gestor de recursos Azure

Em seguida, irá criar um modelo de Gestor de Recursos Azure que concede acesso Azure AD B2C ao grupo de recursos Azure AD que criou anteriormente (por exemplo, *azure-ad-b2c-monitor).* Implemente o modelo a partir da amostra do GitHub utilizando o botão **Implementar para Azure,** que abre o portal Azure e permite configurar e implementar o modelo diretamente no portal. Para estes passos, certifique-se de que está inscrito no seu inquilino AZure AD (não o inquilino Azure AD B2C).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino **AD Azure.**
3. Utilize o botão **Implementar para Azure** para abrir o portal Azure e implementar o modelo diretamente no portal. Para obter mais informações, consulte [criar um modelo de Gestor de Recursos Azure](../lighthouse/how-to/onboard-customer.md#create-an-azure-resource-manager-template).

   [![Implementar no Azure](https://aka.ms/deploytoazurebutton)](   https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure-ad-b2c%2Fsiem%2Fmaster%2Ftemplates%2FrgDelegatedResourceManagement.json)

5. Na página **de implementação personalizada,** introduza as seguintes informações:

   | Campo   | Definição |
   |---------|------------|
   | Subscrição |  Selecione o diretório que contém a subscrição Azure onde foi criado o grupo de recursos *ad-b2c-monitor.* |
   | Region| Selecione a região onde o recurso será implantado.  | 
   | Nome da oferta de msp| Um nome descrevendo esta definição. Por exemplo, *Monitorização Azure AD B2C*.  |
   | Descrição da oferta de msp| Uma breve descrição da sua oferta. Por exemplo, *Ativa o Monitor Azure em Azure AD B2C*.|
   | Gerido por Id inquilino| O **ID** do Inquilino do seu inquilino Azure AD B2C (também conhecido como iD do diretório). |
   |Autorizações|Especifique uma matriz de objetos JSON que incluem o Azure AD `principalId` `principalIdDisplayName` , e Azure `roleDefinitionId` . O `principalId` **ID do Objeto** do grupo B2C ou utilizador que terá acesso a recursos nesta subscrição do Azure. Para esta passagem, especifique o ID do objeto do grupo que gravou anteriormente. Para o `roleDefinitionId` , use o valor de [função incorporado](../role-based-access-control/built-in-roles.md) para o papel *contribuinte,* `b24988ac-6180-42a0-ab88-20f7382dd24c` .|
   | Nome Rg | O nome do grupo de recursos que cria anteriormente no seu inquilino AZure AD. Por exemplo, *azure-ad-b2c-monitor*. |

   O exemplo a seguir demonstra um conjunto de autorizações com um grupo de segurança.

   ```json
   [
       {
           "principalId": "<Replace with group's OBJECT ID>",
           "principalIdDisplayName": "Azure AD B2C tenant administrators",
           "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
       }
   ]
   ```

Depois de implementar o modelo, pode demorar alguns minutos (normalmente não mais de cinco) para que a projeção do recurso esteja concluída. Você pode verificar a implantação no seu inquilino Azure AD e obter os detalhes da projeção de recursos. Para mais informações, consulte [Ver e gerir os prestadores de serviços.](../lighthouse/how-to/view-manage-service-providers.md)

## <a name="4-select-your-subscription"></a>4. Selecione a sua subscrição

Depois de ter implantado o modelo e de ter esperado alguns minutos para que a projeção do recurso se completasse, siga estes passos para associar a sua subscrição ao seu diretório Azure AD B2C.

1. Assine fora do portal Azure se estiver atualmente assinado (isto permite que as suas credenciais de sessão sejam atualizadas no passo seguinte).
2. Inscreva-se no [portal Azure](https://portal.azure.com) com a sua conta administrativa **Azure AD B2C.** Esta conta deve ser um membro do grupo de segurança especificado na etapa [de gestão de recursos delegado.](#3-delegate-resource-management)
3. Selecione o ícone **de inscrição + Diretório** na barra de ferramentas do portal.
4. Selecione o diretório AD Ad Azure que contém a subscrição Azure e o grupo de recursos *ad-b2c monitor* que criou.

    ![Trocar diretório](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)

1. Verifique se selecionou o diretório e subscrição corretos. Neste exemplo, todos os diretórios e todas as subscrições são selecionados.

    ![Todos os diretórios selecionados no filtro de subscrição de & do Diretório](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="5-configure-diagnostic-settings"></a>5. Configurar as definições de diagnóstico

As definições de diagnóstico definem onde devem ser enviados registos e métricas de um recurso. Os destinos possíveis são:

- [Conta de armazenamento Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)
- [Soluções de centros](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) de eventos
- [Log Analytics espaço de trabalho](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)

Neste exemplo, usamos o espaço de trabalho Log Analytics para criar um dashboard.

### <a name="51-create-diagnostic-settings"></a>5.1 Criar definições de diagnóstico

Está pronto para [criar definições de diagnóstico](../active-directory/reports-monitoring/overview-monitoring.md) no portal Azure.

Para configurar as definições de monitorização dos registos de atividade Azure AD B2C:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com a sua conta administrativa Azure AD B2C. Esta conta deve ser um membro do grupo de segurança especificado no [passo de grupo de segurança Select.](#32-select-a-security-group)
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. Selecione **Azure Ative Directory**
1. Em **Monitorização**, selecione **Definições de diagnóstico**.
1. Se existirem definições existentes para o recurso, verá uma lista de definições já configuradas. **Selecione Adicionar a definição de diagnóstico** para adicionar uma nova definição ou selecione **Editar** para editar uma definição existente. Cada configuração não pode ter mais do que um dos tipos de destino.

    ![Painel de definições de diagnóstico no portal Azure](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Dê um nome se já não tiver um.
1. Verifique a caixa para cada destino para enviar os registos. Selecione **Configurar** para especificar as suas definições **conforme descrito no quadro seguinte**.
1. Selecione **Enviar para Registar Análises** e, em seguida, selecione o **Nome do espaço de trabalho** que criou anteriormente `AzureAdB2C` ().
1. Selecione **AuditLogs** e **SignInLogs**.
1. Selecione **Guardar**.

> [!NOTE]
> Pode levar até 15 minutos após a emissão de um evento para que [apareça num espaço de trabalho do Log Analytics](../azure-monitor/logs/data-ingestion-time.md). Além disso, saiba mais sobre [o Ative Directory reportando latências,](../active-directory/reports-monitoring/reference-reports-latencies.md)que podem impactar a estagnação dos dados e desempenhar um papel importante na reportagem.

Se vir a mensagem de erro "Para configurar as definições de Diagnóstico para utilizar o Azure Monitor para o seu diretório Azure AD B2C, tem de configurar a gestão de recursos delegada", certifique-se de que faz o seu s-in com um utilizador que é membro do grupo de [segurança](#32-select-a-security-group) e [selecione](#4-select-your-subscription)a sua subscrição .

## <a name="6-visualize-your-data"></a>6. Visualizar os seus dados

Agora pode configurar o seu espaço de trabalho Log Analytics para visualizar os seus dados e configurar alertas. Estas configurações podem ser feitas tanto no seu inquilino AZURE AD como no seu inquilino Azure AD B2C.

### <a name="61-create-a-query"></a>6.1 Criar uma consulta

As consultas de registo ajudam-no a aproveitar totalmente o valor dos dados recolhidos nos Registos do Monitor Azure. Uma linguagem de consulta poderosa permite-lhe juntar dados de várias tabelas, agregar grandes conjuntos de dados e realizar operações complexas com código mínimo. Praticamente qualquer pergunta pode ser respondida e a análise realizada desde que os dados de suporte sejam recolhidos, e você entende como construir a consulta correta. Para obter mais informações, consulte [Começar com consultas de registo no Azure Monitor](../azure-monitor/logs/get-started-queries.md).

1. A partir do **espaço de trabalho do Log Analytics**, selecione **Logs**
1. No editor de consulta, cole a seguinte consulta [de linguagem de consulta kusto.](/azure/data-explorer/kusto/query/) Esta consulta mostra o uso da política por operação nos últimos x dias. A duração por defeito é definida para 90 dias (90d). Note que a consulta se centra apenas na operação em que um token/código é emitido por política.

    ```kusto
    AuditLogs
    | where TimeGenerated  > ago(90d)
    | where OperationName contains "issue"
    | extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
    | extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
    | summarize SignInCount = count() by Policy, OperationName
    | order by SignInCount desc  nulls last
    ```

1. Selecione **Executar**. Os resultados da consulta são apresentados na parte inferior do ecrã.
1. Para guardar a sua consulta para utilização posterior, **selecione Guardar**.

   ![Log Analytics editor de registo](./media/azure-monitor/query-policy-usage.png)

1. Preencha os seguintes detalhes:

    - **Nome** - Insira o nome da sua consulta.
    - **Guardar como** - Selecione `query` .
    - **Categoria** - Selecione `Log` .

1. Selecione **Guardar**.

Também pode alterar a sua consulta para visualizar os dados utilizando o operador [de renderização.](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor)

```kusto
AuditLogs
| where TimeGenerated  > ago(90d)
| where OperationName contains "issue"
| extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
| extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
| summarize SignInCount = count() by Policy
| order by SignInCount desc  nulls last
| render  piechart
```

![Torta de editor de log de log do Log Analytics](./media/azure-monitor/query-policy-usage-pie.png)

Para obter mais amostras, consulte o Azure AD B2C [SIEM GitHub repo](https://aka.ms/b2csiem).

### <a name="62-create-a-workbook"></a>6.2 Criar um livro

Os livros fornecem uma tela flexível para a análise de dados e a criação de relatórios visuais avançados no portal do Azure. Permitem-lhe aceder a várias fontes de dados de todo o Azure e combiná-las em experiências interativas unificadas. Para mais informações, consulte [os livros de trabalho do Monitor Azure.](../azure-monitor/visualize/workbooks-overview.md)

Siga as instruções abaixo para criar um novo livro utilizando um modelo de galeria JSON. Este livro fornece um painel **de insights** e **autenticação** do utilizador para o inquilino Azure AD B2C.

1. A partir do **espaço de trabalho Log Analytics,** selecione Livros de **Trabalho**.
1. A partir da barra de ferramentas, selecione **+ Nova** opção para criar um novo livro de trabalho.
1. Na página **novo do livro,** selecione o **Editor Avançado** utilizando a **</>** opção na barra de ferramentas.

     ![Modelo de galeria](./media/azure-monitor/wrkb-adv-editor.png)

1. Selecione **o modelo da galeria.**
1. Substitua o JSON no Modelo de **Galeria**  pelo conteúdo do [livro de base Azure AD B2C](https://raw.githubusercontent.com/azure-ad-b2c/siem/master/workbooks/dashboard.json):
1. Aplique o gabarito utilizando o botão **Aplicar.**
1. Selecione O botão **de edição feito** da barra de ferramentas para terminar a edição do livro.
1. Por fim, guarde o livro utilizando o botão **Guardar** a barra de ferramentas.
1. Fornecer um **título**, como *Azure AD B2C Dashboard*.
1. Selecione **Guardar**.

    ![Guarde o livro](./media/azure-monitor/wrkb-title.png)

O livro apresentará relatórios sob a forma de um painel de instrumentos.

![Painel de trabalho primeiro dashboard](./media/azure-monitor/wkrb-dashboard-1.png)

![Segundo painel de trabalho](./media/azure-monitor/wrkb-dashboard-2.png)

![Terceiro painel de trabalho](./media/azure-monitor/wrkb-dashboard-3.png)


## <a name="create-alerts"></a>Criar alertas

Os alertas são criados por regras de alerta no Azure Monitor e podem executar automaticamente consultas guardadas ou pesquisa de registos personalizadas em intervalos regulares. Pode criar alertas com base em métricas de desempenho específicas ou quando determinados eventos são criados, ausência de um evento ou um número de eventos é criado numa janela de tempo específica. Por exemplo, os alertas podem ser usados para notificá-lo quando o número médio de entrada excede um determinado limiar. Para mais informações, consulte [Criar alertas.](../azure-monitor/alerts/tutorial-response.md)


Utilize as seguintes instruções para criar um novo Alerta Azure, que enviará uma [notificação por e-mail](../azure-monitor/alerts/action-groups.md#configure-notifications) sempre que houver uma queda de 25% no **Total de Pedidos** comparativamente ao período anterior. O alerta será executado a cada 5 minutos e procurará a queda nas janelas das últimas 24 horas. Os alertas são criados usando a linguagem de consulta Kusto.


1. A partir do **espaço de trabalho Log Analytics**, selecione **Logs**. 
1. Crie uma nova **consulta kusto** utilizando a consulta abaixo.

    ```kusto
    let start = ago(24h);
    let end = now();
    let threshold = -25; //25% decrease in total requests.
    AuditLogs
    | serialize TimeGenerated, CorrelationId, Result
    | make-series TotalRequests=dcount(CorrelationId) on TimeGenerated in range(start, end, 1h)
    | mvexpand TimeGenerated, TotalRequests
    | where TotalRequests > 0
    | serialize TotalRequests, TimeGenerated, TimeGeneratedFormatted=format_datetime(todatetime(TimeGenerated), 'yyyy-M-dd [hh:mm:ss tt]')
    | project   TimeGeneratedFormatted, TotalRequests, PercentageChange= ((toreal(TotalRequests) - toreal(prev(TotalRequests,1)))/toreal(prev(TotalRequests,1)))*100
    | order by TimeGeneratedFormatted
    | where PercentageChange <= threshold   //Trigger's alert rule if matched.
    ```

1. Selecione **Executar,** para testar a consulta. Deve ver os resultados se houver uma queda de 25% ou mais no total de pedidos nas últimas 24 horas.
1. Para criar uma regra de alerta com base na consulta acima, utilize a opção **+ Nova regra de alerta** disponível na barra de ferramentas.
1. Na página de regra de **alerta,** selecione **o nome 'Condicionar',** selecione 
1. Na página lógica de **sinal de configuração,** desconfiem de valores seguidos e, em seguida, utilize o botão **'Fazer'** para guardar as alterações.
    * Lógica de alerta: Definir **número de resultados** **superior a** **0**.
    * Avaliação com base em: Selecione **1440** para o período (em minutos) e **5** para frequência (em minutos) 

    ![Criar uma condição de regra de alerta](./media/azure-monitor/alert-create-rule-condition.png)

Após a criação do alerta, vá ao **espaço de trabalho do Log Analytics** e selecione **Alertas**. Esta página apresenta todos os alertas que foram desencadeados na duração definida pela opção **Time Range.**  

### <a name="configure-action-groups"></a>Configure grupos de ação

Os alertas Azure Monitor e Service Health utilizam grupos de ação para notificar os utilizadores de que foi desencadeado um alerta. Pode incluir o envio de uma chamada de voz, SMS, e-mail; ou desencadeando vários tipos de ações automatizadas. Siga as orientações [Criar e gerir grupos de ação no portal Azure](../azure-monitor/alerts/action-groups.md)

Aqui está um exemplo de um e-mail de notificação de alerta. 

   ![Notificação por e-mail](./media/azure-monitor/alert-email-notification.png)

## <a name="multiple-tenants"></a>Vários inquilinos

Para embarcar vários registos de inquilinos Azure AD B2C para o mesmo Log Analytics Workspace (ou conta de armazenamento Azure, ou centro de eventos), você precisará de implementações separadas com diferentes valores **de Msp Offer Name.** Certifique-se de que o seu espaço de trabalho Log Analytics está no mesmo grupo de recursos que configurado em [Criar ou escolher o grupo de recursos](#1-create-or-choose-resource-group).

Ao trabalhar com vários espaços de trabalho do Log Analytics, utilize [a Consulta cross workspace](../azure-monitor/logs/cross-workspace-query.md) para criar consultas que funcionam em vários espaços de trabalho. Por exemplo, a seguinte consulta realiza uma junção de dois registos de auditoria de diferentes inquilinos com base na mesma categoria (por exemplo, Autenticação):

```kusto
workspace("AD-B2C-TENANT1").AuditLogs
| join  workspace("AD-B2C-TENANT2").AuditLogs
  on $left.Category== $right.Category
```

## <a name="change-the-data-retention-period"></a>Change the data retention period (Alterar o período de retenção de dados)

Os Registos Azure Monitor são projetados para escalar e suportar a recolha, indexação e armazenamento de quantidades massivas de dados por dia a partir de qualquer fonte na sua empresa ou implantada em Azure. Por padrão, os registos são mantidos por 30 dias, mas a duração da retenção pode ser aumentada até dois anos. Saiba como gerir a [utilização e os custos com os Registos do Monitor Azure.](../azure-monitor/logs/manage-cost-storage.md) Depois de selecionar o nível de preços, pode alterar o período de [retenção de dados](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period).

## <a name="next-steps"></a>Passos seguintes

* Encontre mais amostras na galeria Azure AD B2C [SIEM.](https://aka.ms/b2csiem) 

* Para obter mais informações sobre a adição e configuração das definições de diagnóstico no Azure Monitor, consulte [Tutorial: Colete e analise os registos de recursos a partir de um recurso Azure](../azure-monitor/essentials/monitor-azure-resource.md).

* Para obter informações sobre o streaming de registos Azure AD para um centro de eventos, consulte [Tutorial: Stream Azure Ative Directory para um centro de eventos Azure](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
