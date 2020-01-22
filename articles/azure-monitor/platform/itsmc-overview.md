---
title: Conector de Gerenciamento de Serviços de TI no Azure Log Analytics | Microsoft Docs
description: Este artigo fornece uma visão geral de Conector de Gerenciamento de Serviços de TI (ITSMC) e informações sobre como usar essa solução para monitorar e gerenciar centralmente os itens de trabalho de ITSM no Azure Log Analytics e resolver problemas rapidamente.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: JYOTHIRMAISURI
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: 83d5b7ffb49a08d02d5dd34ad561ce725ead7e0e
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289140"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Conectar o Azure a ferramentas de ITSM usando o Conector de Gerenciamento de Serviços de TI

![Símbolo de Conector de Gerenciamento de Serviços de TI](media/itsmc-overview/itsmc-symbol.png)

O Conector de Gerenciamento de Serviços de TI (ITSMC) permite que você conecte o Azure e um produto/serviço de ITSM (gerenciamento de serviços de ti) com suporte.

Os serviços do Azure, como Log Analytics e Azure Monitor fornecem ferramentas para detectar, analisar e solucionar problemas com seus recursos do Azure e não Azure. No entanto, os itens de trabalho relacionados a um problema normalmente residem em um produto/serviço de ITSM. O conector ITSM fornece uma conexão bidirecional entre o Azure e as ferramentas de ITSM para ajudá-lo a resolver problemas com mais rapidez.

O ITSMC dá suporte a conexões com as seguintes ferramentas de ITSM:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

Com o ITSMC, você pode:

-  Crie itens de trabalho na ferramenta de ITSM, com base em seus alertas do Azure (alertas de métrica, alertas do log de atividades e alertas de Log Analytics).
-  Opcionalmente, você pode sincronizar seu incidente e alterar os dados de solicitação de sua ferramenta de ITSM para um espaço de trabalho do Azure Log Analytics.

Leia mais sobre os [termos legais e a política de privacidade](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Você pode começar a usar o Conector ITSM usando as seguintes etapas:

1.  [Adicionar a solução de Conector ITSM](#adding-the-it-service-management-connector-solution)
2.  Criar uma conexão de ITSM
3.  [Usar a conexão](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Adicionando a solução de Conector de Gerenciamento de Serviços de TI

Para poder criar uma conexão, você precisa adicionar a solução Conector ITSM.

1. Em portal do Azure, clique em **+ novo** ícone.

   ![Novo recurso do Azure](media/itsmc-overview/azure-add-new-resource.png)

2. Pesquise **conector de gerenciamento de serviços de ti** no Marketplace e clique em **criar**.

   ![Adicionar solução ITSMC](media/itsmc-overview/add-itsmc-solution.png)

3. Na seção **espaço de trabalho do OMS** , selecione o espaço de trabalho log Analytics do Azure onde você deseja instalar a solução.
   >[!NOTE]
   > * Como parte da transição contínua do Microsoft Operations Management Suite (OMS) para Azure Monitor, os espaços de trabalho do OMS agora são chamados de espaços de trabalho Log Analytics.
   > * O Conector ITSM só pode ser instalado nos espaços de trabalho Log Analytics nas seguintes regiões: leste dos EUA, oeste dos EUA 2, Sul EUA Central, Oeste EUA Central, centro-Canadá, Europa Ocidental, sul do Reino Unido, Sudeste Asiático, leste do Japão, Índia central, Austrália oriental.

4. Na seção **configurações do espaço de trabalho do OMS** , selecione o resourcegroup no qual você deseja criar o recurso de solução.

   ![Espaço de trabalho ITSMC](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Como parte da transição contínua do Microsoft Operations Management Suite (OMS) para Azure Monitor, os espaços de trabalho do OMS agora são chamados de espaços de trabalho Log Analytics.

5. Clique em **Criar**.

Quando o recurso de solução é implantado, uma notificação é exibida na parte superior direita da janela.


## <a name="creating-an-itsm--connection"></a>Criando uma conexão de ITSM

Depois de instalar a solução, você pode criar uma conexão.

Para criar uma conexão, você precisará preparar sua ferramenta de ITSM para permitir a conexão da solução de Conector ITSM.  

Dependendo do produto de ITSM ao qual você está se conectando, use as seguintes etapas:

- [System Center Service Manager (SCSM)](../../azure-monitor/platform/itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](../../azure-monitor/platform/itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](../../azure-monitor/platform/itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](../../azure-monitor/platform/itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Depois de ter preparado suas ferramentas de ITSM, siga as etapas abaixo para criar uma conexão:

1. Vá para **todos os recursos**, procure por **YourWorkspaceName (assistência técnica)** .
2. Em **fontes de dados do espaço de trabalho** no painel esquerdo, clique em **conexões de ITSM**.
   ![conexões de ITSM](media/itsmc-overview/itsm-connections.png)

   Esta página exibe a lista de conexões.
3. Clique em **adicionar ligação**.

   ![Adicionar conexão de ITSM](media/itsmc-overview/add-new-itsm-connection.png)

4. Especifique as configurações de conexão, conforme descrito em [Configurando o artigo conexão ITSMC com seus produtos/serviços de ITSM](../../azure-monitor/platform/itsmc-connections.md).

   > [!NOTE]
   >
   > Por padrão, o ITSMC atualiza os dados de configuração da conexão uma vez a cada 24 horas. Para atualizar os dados da conexão instantaneamente para quaisquer edições ou atualizações de modelo feitas, clique no botão **sincronizar** na folha da sua conexão.

   ![Atualização de conexão](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Utilizar a solução
   Usando a solução Conector ITSM, você pode criar itens de trabalho de alertas do Azure, alertas de Log Analytics e registros de log do Log Analytics.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Criar itens de trabalho de ITSM de alertas do Azure

Depois de criar a conexão de ITSM, você poderá criar itens de trabalho em sua ferramenta de ITSM com base nos alertas do Azure, usando a **ação de ITSM** em **grupos de ação**.

Os grupos de ação fornecem uma maneira modular e reutilizável de disparar ações para seus alertas do Azure. Você pode usar grupos de ação com alertas de métrica, alertas do log de atividades e alertas do Log Analytics do Azure no portal do Azure.

Utilize o seguinte procedimento:

1. Em portal do Azure, clique em **monitorar**.
2. No painel esquerdo, clique em **grupos de ação**. A janela **Adicionar grupo de ações** é exibida.

    ![Grupos de Ações](media/itsmc-overview/action-groups.png)

3. Forneça o **nome** e o **curtoname** para o seu grupo de ação. Selecione o **grupo de recursos** e a **assinatura** em que você deseja criar o grupo de ações.

    ![Detalhes de grupos de ação](media/itsmc-overview/action-groups-details.png)

4. Na lista ações, selecione **ITSM** no menu suspenso para o **tipo de ação**. Forneça um **nome** para a ação e clique em **Editar detalhes**.
5. Selecione a **assinatura** na qual seu espaço de trabalho do log Analytics está localizado. Selecione o nome da **conexão** (seu nome de conector ITSM) seguido pelo nome do espaço de trabalho. Por exemplo, "MyITSMMConnector (MyWorkspace)".

    ![Detalhes da ação de ITSM](media/itsmc-overview/itsm-action-details.png)

6. Selecione tipo de **item de trabalho** no menu suspenso.
   Escolha usar um modelo existente ou preencha os campos exigidos por seu produto de ITSM.
7. Clique em **OK**.

Ao criar/editar uma regra de alerta do Azure, use um grupo de ações que tenha uma ação de ITSM. Quando o alerta é disparado, o item de trabalho é criado/atualizado na ferramenta ITSM.

> [!NOTE]
>
> Para obter informações sobre o preço da ação de ITSM, consulte a [página de preços](https://azure.microsoft.com/pricing/details/monitor/) para grupos de ações.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualizar e analisar os dados de incidente e de solicitação de alteração

Com base na sua configuração ao configurar uma conexão, o conector ITSM pode sincronizar até 120 dias de incidentes e dados de solicitação de alteração. O esquema de registro de log para esses dados é fornecido na [próxima seção](#additional-information).

Os dados de incidente e de solicitação de alteração podem ser visualizados usando o painel Conector ITSM na solução.

![Tela de Log Analytics](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

O painel também fornece informações sobre o status do conector que pode ser usado como um ponto de partida para analisar quaisquer problemas com as conexões.

Você também pode visualizar os incidentes sincronizados em relação aos computadores afetados, dentro da solução Mapa do Serviço.

Mapa do Serviço descobre automaticamente os componentes do aplicativo em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Permite-lhe visualizar os seus servidores, como os concebe: sistemas interligados que fornecem serviços críticos. O Mapa de Serviços mostra as ligações entre os servidores, processos e portas em qualquer arquitetura com ligação TCP, e não requer qualquer configuração para além da instalação de um agente. [Saiba mais](../../azure-monitor/insights/service-map.md).

Se você estiver usando a solução Mapa do Serviço, poderá exibir os itens da central de serviços criados nas soluções de ITSM, conforme mostrado no exemplo a seguir:

![Tela de Log Analytics](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

Mais informações: [mapa do serviço](../../azure-monitor/insights/service-map.md)


## <a name="additional-information"></a>Informação adicional

### <a name="data-synced-from-itsm-product"></a>Dados sincronizados do produto de ITSM
Incidentes e solicitações de alteração são sincronizados de seu produto ITSM para seu espaço de trabalho Log Analytics com base na configuração da conexão.

As informações a seguir mostram exemplos de dados coletados pelo ITSMC:

> [!NOTE]
>
> Dependendo do tipo de item de trabalho importado para Log Analytics, **ServiceDesk_CL** contém os seguintes campos:

**Item de trabalho:** **incidentes**  
ServiceDeskWorkItemType_s="Incident"

**Campos**

- ServiceDeskConnectionName
- ID da central de serviços
- Estado
- Urgência
- Impacto
- Prioridade
- Escalamento
- Criado por
- Resolvido Por
- Fechado por
- Origem
- Atribuído A
- Categoria
- Título
- Descrição
- Data de criação
- Data de fecho
- Data de resolução
- Data da última modificação
- Computador


**Item de trabalho:** **solicitações de alteração**

ServiceDeskWorkItemType_s="ChangeRequest"

**Campos**
- ServiceDeskConnectionName
- ID da central de serviços
- Criado por
- Fechado por
- Origem
- Atribuído A
- Título
- Tipo
- Categoria
- Estado
- Escalamento
- Status de conflito
- Urgência
- Prioridade
- Risco
- Impacto
- Atribuído A
- Data de criação
- Data de fecho
- Data da última modificação
- Data solicitada
- Data de início planejada
- Data de término planejada
- Data de início do trabalho
- Data de término do trabalho
- Descrição
- Computador

## <a name="output-data-for-a-servicenow-incident"></a>Dados de saída para um incidente do ServiceNow

| Campo de Log Analytics | Campo do ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Número |
| IncidentState_s | Estado |
| Urgency_s |Urgência |
| Impact_s |Impacto|
| Priority_s | Prioridade |
| CreatedBy_s | Aberto por |
| ResolvedBy_s | Resolvido por|
| ClosedBy_s  | Fechado por |
| Source_s| Tipo de contato |
| AssignedTo_s | Atribuído a  |
| Category_s | Categoria |
| Title_s|  Breve descrição |
| Description_s|  Notas |
| CreatedDate_t|  Aberto |
| ClosedDate_t| Fechado|
| ResolvedDate_t|Resolvido|
| Computador  | Item de configuração |

## <a name="output-data-for-a-servicenow-change-request"></a>Dados de saída para uma solicitação de alteração do ServiceNow

| Log Analytics | Campo do ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Número |
| CreatedBy_s | Solicitado por |
| ClosedBy_s | Fechado por |
| AssignedTo_s | Atribuído a  |
| Title_s|  Breve descrição |
| Type_s|  Tipo |
| Category_s|  Categoria |
| CRState_s|  Estado|
| Urgency_s|  Urgência |
| Priority_s| Prioridade|
| Risk_s| Risco|
| Impact_s| Impacto|
| RequestedDate_t  | Solicitado por data |
| ClosedDate_t | Data de fecho |
| PlannedStartDate_t  |     Data de início planejada |
| PlannedEndDate_t  |   Data de término planejada |
| WorkStartDate_t  | Data de início real |
| WorkEndDate_t | Data de fim real|
| Description_s | Descrição |
| Computador  | Item de Configuração |


## <a name="troubleshoot-itsm-connections"></a>Solucionar problemas de conexões de ITSM
1. Se a conexão falhar na interface do usuário da fonte conectada com um **erro ao salvar** a mensagem de conexão, execute as seguintes etapas:
   - Para conexões do ServiceNow, Cherwell e Provance,  
   - Verifique se você inseriu corretamente o nome de usuário, a senha, a ID do cliente e o segredo do cliente para cada uma das conexões.  
   - Verifique se você tem privilégios suficientes no produto de ITSM correspondente para fazer a conexão.  
   - Para conexões Service Manager,  
   - Verifique se o aplicativo Web foi implantado com êxito e se a conexão híbrida foi criada. Para verificar se a conexão foi estabelecida com êxito com o computador Service Manager local, visite a URL do aplicativo Web conforme detalhado na documentação para fazer a [conexão híbrida](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection).  

2. Se os dados do ServiceNow não estiverem sendo sincronizados com o Log Analytics, verifique se a instância do ServiceNow não está em suspensão. As instâncias de desenvolvimento do ServiceNow às vezes entram em suspensão quando ociosas por um longo período. Caso contrário, relate o problema.
3. Se Log Analytics alertas forem acionados, mas os itens de trabalho não forem criados em itens de configuração ou produtos de ITSM não forem criados/vinculados a itens de trabalho ou para outras informações genéricas, procure nos seguintes locais:
   -  ITSMC: a solução mostra um resumo de conexões/itens de trabalho/computadores, etc. Clique no bloco mostrando o **status do conector**, que o leva para a pesquisa de **log** com a consulta relevante. Examine os registros de log com LogType_S como erro para obter mais informações.
   - Página **pesquisa de logs** : exiba os erros/informações relacionadas diretamente usando o ServiceDeskLog_CL de consulta `*``*`.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Solucionar problemas Service Manager implantação de aplicativo Web
1.  No caso de problemas com a implantação de aplicativo Web, verifique se você tem permissões suficientes na assinatura mencionada para criar/implantar recursos.
2.  Se você receber um erro **"referência de objeto não definida para a instância de um objeto"** ao executar o [script](itsmc-service-manager-script.md), certifique-se de que você inseriu valores válidos na seção **configuração do usuário** .
3.  Se você não conseguir criar o namespace de retransmissão do barramento de serviço, verifique se o provedor de recursos necessário está registrado na assinatura. Se não estiver registrado, crie manualmente o namespace de retransmissão do barramento de serviço do portal do Azure. Você também pode criá-lo ao [criar a conexão híbrida](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection) do portal do Azure.


## <a name="contact-us"></a>Contacte-nos

Para quaisquer consultas ou comentários sobre o Conector de Gerenciamento de Serviços de TI, entre em contato conosco em [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Passos seguintes
[Adicione produtos/serviços de ITSM a conector de gerenciamento de serviços de ti](../../azure-monitor/platform/itsmc-connections.md).
