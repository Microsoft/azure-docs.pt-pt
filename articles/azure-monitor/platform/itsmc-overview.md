---
title: IT Service conector de gestão no Log Analytics do Azure | Documentos da Microsoft
description: Este artigo fornece uma descrição geral do conector de gestão de serviço de TI (ITSMC) e informações sobre como utilizar esta solução para monitorizar e gerir o ITSM centralmente itens de trabalho no Azure Log Analytics e resolver quaisquer problemas rapidamente.
services: log-analytics
documentationcenter: ''
author: jyothirmaisuri
manager: riyazp
editor: ''
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: v-jysur
ms.openlocfilehash: abbd26779cefaf52c6f2247a5d27db25f280c930
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58118055"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Ligar o Azure para ferramentas ITSM com o conector de gestão do serviço de TI

![Símbolo de conector de gestão do serviço IT](media/itsmc-overview/itsmc-symbol.png)

O conector de gestão de serviço de TI (ITSMC) permite-lhe ligar o Azure e um suportados produtos/serviços de gestão de serviço de TI (ITSM).

Serviços do Azure como o Log Analytics e o Azure Monitor fornecem ferramentas para detetar, analisar e resolver problemas com o Azure e os recursos não Azure. No entanto, os itens de trabalho relacionados com um problema normalmente residem num produto/serviço ITSM. O conector ITSM fornece uma ligação bidirecional entre as ferramentas do Azure e ITSM para o ajudar a resolver problemas de forma mais rápida.

ITSMC oferece suporte a conexões com as seguintes ferramentas ITSM:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

Com o ITSMC, pode

-  Crie itens de trabalho na ferramenta ITSM, com base nos seus alertas do Azure (alertas de métricas, alertas de registo de atividades e alertas do Log Analytics).
-  Opcionalmente, pode sincronizar o seu incidente e alterar dados de pedidos a partir da sua ferramenta ITSM numa área de trabalho do Log Analytics do Azure.


Pode começar a utilizar o conector de ITSM através dos seguintes passos:

1.  [Adicionar a solução de conector ITSM](#adding-the-it-service-management-connector-solution)
2.  Crie uma ligação de ITSM
3.  [Utilizar a ligação](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Adicionar o IT a solução de conector de gestão de serviço

Antes de poder criar uma ligação, terá de adicionar a solução de conector ITSM.

1. No portal do Azure, clique em **+ novo** ícone.

   ![Recurso novo do Azure](media/itsmc-overview/azure-add-new-resource.png)

2. Procure **conector de gestão do serviço de TI** no Marketplace e clique **criar**.

   ![Adicionar solução ITSMC](media/itsmc-overview/add-itsmc-solution.png)

3. Na **área de trabalho OMS** secção, selecione a área de trabalho do Log Analytics do Azure onde pretende instalar a solução.
   >[!NOTE]
   >Como parte da transição em curso do Microsoft Operations Management Suite (OMS) para o Azure Monitor, áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics.
4. Na **definições de área de trabalho do OMS** secção, selecione o ResourceGroup onde pretende criar o recurso de solução.

   ![Área de trabalho ITSMC](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Como parte da transição em curso do Microsoft Operations Management Suite (OMS) para o Azure Monitor, áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics.

5. Clique em **Criar**.

Quando o recurso de solução é implementado, é apresentada uma notificação na parte superior direita da janela.


## <a name="creating-an-itsm--connection"></a>Criar uma ligação de ITSM

Depois de instalar a solução, pode criar uma ligação.

Para criar uma ligação, terá de preparar a sua ferramenta ITSM para permitir a ligação da solução do conector ITSM.  

Dependendo do produto ITSM que está a ligar, utilize os seguintes passos:

- [System Center Service Manager (SCSM)](../../azure-monitor/platform/itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](../../azure-monitor/platform/itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](../../azure-monitor/platform/itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](../../azure-monitor/platform/itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Depois de ter preparado suas ferramentas ITSM, siga os passos abaixo para criar uma ligação:

1. Aceda a **todos os recursos**, procure **ServiceDesk(YourWorkspaceName)**.
2. Sob **ORIGENS de dados de área de trabalho** no painel esquerdo, clique em **ligações de ITSM**.
   ![Ligações de ITSM](media/itsmc-overview/itsm-connections.png)

   Esta página apresenta a lista de ligações.
3. Clique em **adicionar ligação**.

   ![Adicionar a ligação ITSM](media/itsmc-overview/add-new-itsm-connection.png)

4. Especifique as definições de ligação, conforme descrito em [configurar a ligação de ITSMC com o seu artigo de produtos/serviços ITSM](../../azure-monitor/platform/itsmc-connections.md).

   > [!NOTE]
   > 
   > Por predefinição, o ITSMC atualiza os dados de configuração da ligação uma vez em cada 24 horas. Para atualizar os dados de sua ligação instantaneamente para qualquer edições ou modelo de atualização que fizer, clique nas **sincronização** botão no painel da sua ligação.

   ![Atualização de ligação](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Utilizar a solução
   Ao utilizar a solução de conector ITSM, pode criar itens de trabalho de alertas do Azure, os alertas do Log Analytics e registros de log do Log Analytics.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Criar itens de trabalho ITSM de alertas do Azure

Depois de ter a sua ligação de ITSM criada, pode criar itens de trabalho na sua ferramenta ITSM com base em alertas do Azure, utilizando o **ação de ITSM** na **grupos de ação**.

Grupos de ação proporcionam uma forma de modular e reutilizável de acionar ações para os alertas do Azure. Pode utilizar grupos de ação com alertas de métricas, alertas de registo de atividades e alertas do Log Analytics do Azure no portal do Azure.

Utilize o seguinte procedimento:

1. No portal do Azure, clique em **Monitor**.
2. No painel esquerdo, clique em **grupos de ação**. O **grupo de ação de adicionar** é apresentada a janela.

    ![Grupos de Ação](media/itsmc-overview/action-groups.png)

3. Fornecer **Name** e **ShortName** para o seu grupo de ação. Selecione o **grupo de recursos** e **subscrição** onde pretende criar um grupo de ação.

    ![Detalhes de grupos de ação](media/itsmc-overview/action-groups-details.png)

4. Na lista de ações, selecione **ITSM** no menu pendente para **tipo de ação**. Fornecer um **Name** da ação e clique em **editar detalhes**.
5. Selecione o **subscrição** onde está localizada a sua área de trabalho do Log Analytics. Selecione o **ligação** nome (o nome do conector ITSM) seguido pelo seu nome de área de trabalho. Por exemplo, "MyITSMMConnector(MyWorkspace)".

    ![Detalhes da ação de ITSM](media/itsmc-overview/itsm-action-details.png)

6. Selecione **Item de trabalho** tipo no menu pendente.
   Opte por utilizar um modelo existente ou preencher os campos necessários pelo seu produto ITSM.
7. Clique em **OK**.

Quando a criação/edição de uma regra de alerta do Azure, utilize um grupo de ação, o que tem uma ação de ITSM. Quando o alerta for acionado, o item de trabalho é criado/atualizado na ferramenta de ITSM.

> [!NOTE]
> 
> Para obter informações sobre os preços da ação de ITSM, consulte a [página de preços](https://azure.microsoft.com/pricing/details/monitor/) para grupos de ação.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualize e analise o incidente e dados de pedido de alteração

Com base na sua configuração ao configurar uma ligação, conector ITSM pode sincronizar até 120 dias de dados de pedido de incidentes e alterações. O esquema de registo do registo para estes dados é fornecido na [próxima seção](#additional-information).

Os dados de pedido de incidentes e alterações podem ser visualizados com o dashboard de conector ITSM na solução.

![Ecrã do log Analytics](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

O dashboard também fornece informações sobre o estado do conector que pode ser utilizado como um ponto de partida para analisar quaisquer problemas com as ligações.

Também é possível visualizar os incidentes sincronizados em relação aos computadores afetados, dentro da solução mapa de serviço.

Mapa de serviço Deteta os componentes da aplicação em sistemas Windows e Linux e mapeia a comunicação entre serviços automaticamente. Permite-lhe ver seus servidores, à medida que considerá-los – como sistemas interconectados que fornecem serviços críticos. Mapa de serviço mostra ligações entre servidores, processos, e as portas em qualquer arquitetura ligado a TCP sem qualquer configuração necessária que a instalação de um agente. [Saiba mais](../../azure-monitor/insights/service-map.md).

Se estiver a utilizar a solução mapa de serviço, pode ver os itens de suporte técnico de serviço criados nas soluções ITSM, conforme mostrado no exemplo a seguir:

![Ecrã do log Analytics](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

Mais informações: [Mapa do Serviço](../../azure-monitor/insights/service-map.md)


## <a name="additional-information"></a>Informações adicionais

### <a name="data-synced-from-itsm-product"></a>Dados sincronizados no produto ITSM
Incidentes e pedidos de alteração são sincronizados a partir de seu produto ITSM à sua área de trabalho do Log Analytics com base na configuração da ligação.

As seguintes informações mostram exemplos de dados coletados pela ITSMC:

> [!NOTE]
> 
> Dependendo do tipo de item de trabalho importados para o Log Analytics, **ServiceDesk_CL** contém os seguintes campos:

**Item de trabalho:** **Incidentes**  
ServiceDeskWorkItemType_s="Incident"

**Campos**

- ServiceDeskConnectionName
- ID do Service Desk
- Estado
- Urgência
- Impacto
- Prioridade
- Escalamento
- Criado Por
- Resolvido por
- Fechado por
- Origem
- Atribuído a
- Categoria
- Título
- Descrição
- Data de Criação
- Data de fecho
- Data de Resolução
- Data da Última Modificação
- Computador


**Item de trabalho:** **Pedidos de alteração**

ServiceDeskWorkItemType_s="ChangeRequest"

**Campos**
- ServiceDeskConnectionName
- ID do Service Desk
- Criado Por
- Fechado por
- Origem
- Atribuído a
- Título
- Type
- Categoria
- Estado
- Escalamento
- Estado de conflito
- Urgência
- Prioridade
- Risco
- Impacto
- Atribuído a
- Data de Criação
- Data de fecho
- Data da Última Modificação
- Data solicitada
- Data de início planeada
- Data de fim de planeada
- Data de início do trabalho
- Data de fim de trabalho
- Descrição
- Computador

## <a name="output-data-for-a-servicenow-incident"></a>Dados de saída para um incidente de ServiceNow

| Campo do log Analytics | Campo do ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | Estado |
| Urgency_s |Urgência |
| Impact_s |Impacto|
| Priority_s | Prioridade |
| CreatedBy_s | Aberto por |
| ResolvedBy_s | Resolvido por|
| ClosedBy_s  | Fechado por |
| Source_s| Tipo de contacto |
| AssignedTo_s | Atribuído a  |
| Category_s | Categoria |
| Title_s|  Breve descrição |
| Description_s|  Notas |
| CreatedDate_t|  Aberturas |
| ClosedDate_t| Fechado|
| ResolvedDate_t|Resolvido|
| Computador  | Item de configuração |

## <a name="output-data-for-a-servicenow-change-request"></a>Pedido de alteração de dados de saída para um ServiceNow

| Log Analytics | Campo do ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | Pedido por |
| ClosedBy_s | Fechado por |
| AssignedTo_s | Atribuído a  |
| Title_s|  Breve descrição |
| Type_s|  Type |
| Category_s|  Categoria |
| CRState_s|  Estado|
| Urgency_s|  Urgência |
| Priority_s| Prioridade|
| Risk_s| Risco|
| Impact_s| Impacto|
| RequestedDate_t  | Requerido por data |
| ClosedDate_t | Data de fecho |
| PlannedStartDate_t  |     Data de início planeada |
| PlannedEndDate_t  |   Data de fim planeada |
| WorkStartDate_t  | Data de início real |
| WorkEndDate_t | Data de fim real|
| Description_s | Descrição |
| Computador  | Item de configuração |


## <a name="troubleshoot-itsm-connections"></a>Resolver problemas de ligações de ITSM
1. Se a falha de ligação da interface do Usuário de origem ligada com um **erro ao guardar ligação** da mensagem, siga os passos seguintes:
   - Para ligações de ServiceNow, Cherwell e Provance,  
   - Certifique-se de que introduziu corretamente o nome de utilizador, palavra-passe, ID de cliente e segredo do cliente para cada uma das ligações.  
   - Verifique se tem privilégios suficientes no produto ITSM correspondente para fazer a conexão.  
   - Para as ligações do Service Manager,  
   - Certifique-se de que a aplicação Web é implementada com êxito e ligação híbrida é criada. Para verificar a ligação é estabelecida com êxito com a máquina de Service Manager no local, visite o URL da aplicação Web conforme detalhado na documentação de fazer a [ligação híbrida](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection).  

2. Se não estiver a obter sincronizados dados a partir de ServiceNow para o Log Analytics, certifique-se de que o ServiceNow instância não está a ser suspenso. Instâncias de desenvolvimento do ServiceNow, às vezes, vá em suspensão quando está ocioso por um longo período. Caso contrário, relate o problema.
3. Se os alertas do Log Analytics são disparados, mas funcionam itens não são criados no produto ITSM ou itens de configuração não são criados/ligados a itens de trabalho ou para quaisquer outras informações genéricas, procure nos seguintes locais:
   -  ITSMC: A solução mostra um resumo dos itens de ligações/trabalho/computadores etc. Clique em Mostrar o mosaico **estado do conector**, que leva-o para **pesquisa de registos** com a consulta relevante. Examinar os registros de log com LogType_S como erro para obter mais informações.
   - **Pesquisa de registos** página: ver as informações de erros/relacionados diretamente com a consulta `*`ServiceDeskLog_CL`*`.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Resolver problemas de implementação de aplicação de Web do Service Manager
1.  Em caso de problemas de implementação de aplicações web, certifique-se de que tem permissões suficientes na subscrição mencionada para criar/implementar recursos.
2.  Se obtiver um **"Não foi definida de referência para a instância de um objeto de objeto"** Ocorreu um erro ao executar o [script](itsmc-service-manager-script.md), certifique-se de que introduziu valores válidos em **configuração do usuário** secção .
3.  Se não conseguir criar o espaço de nomes de reencaminhamento do service bus, certifique-se de que o fornecedor de recursos necessária está registado na subscrição. Se não registada, crie manualmente o espaço de nomes de reencaminhamento do service bus no portal do Azure. Também pode criá-la ao [criação da ligação híbrida](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection) do portal do Azure.


## <a name="contact-us"></a>Contacte-nos

Para quaisquer consultas ou comentários sobre o conector de gestão do serviço de TI, contacte-nos [ omsitsmfeedback@microsoft.com ](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Passos Seguintes
[Adicionar produtos/serviços ITSM ao conector de gestão do serviço de TI](../../azure-monitor/platform/itsmc-connections.md).
