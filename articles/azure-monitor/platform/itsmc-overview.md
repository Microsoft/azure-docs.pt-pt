---
title: Conector de gestão de serviços de TI em Azure Log Analytics  Microsoft Docs
description: Este artigo fornece uma visão geral do Conector de Gestão de Serviços de TI (ITSMC) e informações sobre como usar esta solução para monitorizar e gerir centralmente os itens de trabalho ITSM no Azure Log Analytics, e resolver quaisquer problemas rapidamente.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: 50bab4c26046059b993c19a030a8f840ae336ef2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274544"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Ligue o Azure às ferramentas ITSM utilizando o Conector de Gestão de Serviços DE TI

![Símbolo do conector de gestão do serviço de TI](media/itsmc-overview/itsmc-symbol.png)

O Conector de Gestão de Serviços de TI (ITSMC) permite ligar o Azure e um produto/serviço de gestão de serviços de TI suportado (ITSM).

Serviços Azure como log analytics e Azure Monitor fornecem ferramentas para detetar, analisar e resolver problemas com os seus recursos Azure e não-Azure. No entanto, os itens de trabalho relacionados com um problema residem tipicamente num produto/serviço ITSM. O conector ITSM fornece uma ligação bidirecional entre as ferramentas Azure e ITSM para ajudá-lo a resolver os problemas mais rapidamente.

A ITSMC suporta ligações com as seguintes ferramentas ITSM:

-   ServiceNow
-   Gestor de serviços do Centro de Sistema
-   Provance
-   Cherwell

Com o ITSMC, pode:

-  Crie itens de trabalho na ferramenta ITSM, com base nos seus alertas Azure (alertas métricos, alertas de Registo de Atividade e alertas de Log Analytics).
-  Opcionalmente, pode sincronizar o seu incidente e alterar os dados de pedido da sua ferramenta ITSM para um espaço de trabalho Azure Log Analytics.

Leia mais sobre os [termos legais e a política](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)de privacidade.

Pode começar a utilizar o Conector ITSM utilizando os seguintes passos:

1.  [Adicione a solução de conector ITSM](#adding-the-it-service-management-connector-solution)
2.  Criar uma ligação ITSM
3.  [Use a ligação](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Adicionar a solução de conector de gestão de serviços de TI

Antes de criar uma ligação, tem de adicionar a Solução de Conector ITSM.

1. No portal Azure, clique **+ Novo** ícone.

   ![Novo recurso azure](media/itsmc-overview/azure-add-new-resource.png)

2. Procure **conector** de gestão de serviços de TI no Mercado e clique em **Criar**.

   ![Adicionar solução ITSMC](media/itsmc-overview/add-itsmc-solution.png)

3. Na secção **Workspace OMS,** selecione o espaço de trabalho Azure Log Analytics onde pretende instalar a solução.
   >[!NOTE]
   > * Como parte da transição em curso da Microsoft Operations Management Suite (OMS) para o Monitor Azure, os espaços de trabalho oMS são agora referidos como espaços de trabalho de Log Analytics.
   > * O Conector ITSM só pode ser instalado em espaços de trabalho log Analytics nas seguintes regiões: Leste dos EUA, US2 Ocidental, Centro-Sul dos EUA, Oeste Dos EUA, Canadá Central, Europa Ocidental, Reino Unido do Sul, Sudeste Asiático, Japão Oriental, Índia Central, Sudeste da Austrália.

4. Na secção Definições do Espaço de **Trabalho OMS,** selecione o Grupo de Recursos onde pretende criar o recurso de solução.

   ![Espaço de trabalho ITSMC](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Como parte da transição em curso da Microsoft Operations Management Suite (OMS) para o Monitor Azure, os espaços de trabalho oMS são agora referidos como espaços de trabalho de Log Analytics.

5. Clique em **Criar**.

Quando o recurso de solução é implantado, uma notificação aparece na parte superior direita da janela.


## <a name="creating-an-itsm--connection"></a>Criar uma ligação ITSM

Uma vez instalado a solução, pode criar uma ligação.

Para criar uma ligação, terá de preparar a sua ferramenta ITSM para permitir a ligação a partir da solução de Conector ITSM.  

Dependendo do produto ITSM a que está a ligar, utilize os seguintes passos:

- [Gestor de Serviço scsm (SCSM)](../../azure-monitor/platform/itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](../../azure-monitor/platform/itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](../../azure-monitor/platform/itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Rio Cherwell](../../azure-monitor/platform/itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Depois de ter preparado as suas ferramentas ITSM, siga os passos abaixo para criar uma ligação:

1. Ir a **Todos os Recursos**, procure **ServiceDesk(YourWorkspaceName)** .
2. Em fontes de **dados workspace** no painel esquerdo, clique em **Conexões ITSM**.
   ![ligações ITSM](media/itsmc-overview/itsm-connections.png)

   Esta página apresenta a lista de ligações.
3. Clique em **adicionar ligação**.

   ![Adicione a ligação ITSM](media/itsmc-overview/add-new-itsm-connection.png)

4. Especifique as definições de ligação descritas na [configuração da ligação ITSMC com o artigo produtos/serviços ITSM](../../azure-monitor/platform/itsmc-connections.md).

   > [!NOTE]
   >
   > Por predefinição, o ITSMC atualiza os dados de configuração da ligação uma vez em cada 24 horas. Para atualizar os dados da sua ligação instantaneamente para quaisquer edits ou atualizações de modelos que efetua, clique no botão **Sync** na lâmina da sua ligação.

   ![Atualização de ligação](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Utilizar a solução
   Utilizando a solução de Conector ITSM, pode criar itens de trabalho a partir de alertas Azure, alertas De Log Analytics e registos de registos de registo slog Analytics.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Criar itens de trabalho ITSM a partir de alertas Azure

Assim que tiver a ligação ITSM criada, pode criar um item de trabalho na sua ferramenta ITSM com base em alertas Azure, utilizando a **Ação ITSM** em Grupos de **Ação.**

Os Grupos de Ação fornecem uma forma modular e reutilizável de desencadear ações para os seus Alertas Azure. Pode utilizar Grupos de Ação com alertas métricos, alertas de Registo de Atividades e alertas Azure Log Analytics no portal Azure.

Utilize o seguinte procedimento:

1. No portal Azure, clique no **Monitor**.
2. No painel esquerdo, clique em **grupos de ação**. A janela do grupo de **ação Add** aparece.

    ![Grupos de Ação](media/itsmc-overview/action-groups.png)

3. Forneça **nome** e **nome curto** para o seu grupo de ação. Selecione o **Grupo de Recursos** e **Subscrição** onde pretende criar o seu grupo de ação.

    ![Pormenores dos Grupos de Ação](media/itsmc-overview/action-groups-details.png)

4. Na lista de Ações, selecione **ITSM** do menu suspenso para **O Tipo**de Ação . Forneça um **Nome** para a ação e clique em **editar detalhes**.
5. Selecione a **Subscrição** onde está localizado o seu espaço de trabalho Log Analytics. Selecione o nome **Ligação** (o seu nome de conector ITSM) seguido do seu nome Workspace. Por exemplo, "MyITSMMConnector(MyWorkspace)".

    ![Detalhes da Ação ITSM](media/itsmc-overview/itsm-action-details.png)

6. Selecione o tipo **de artigo** de trabalho a partir do menu suspenso.
   Opte por utilizar um modelo existente ou preencha os campos exigidos pelo seu produto ITSM.
7. Clique em **OK**.

Ao criar/editar uma regra de alerta Azure, utilize um grupo de Ação, que tenha uma Ação ITSM. Quando o alerta dispara, o item de trabalho é criado/atualizado na ferramenta ITSM.

> [!NOTE]
>
> Para obter informações sobre os preços da AÇÃO ITSM, consulte a [página de preços](https://azure.microsoft.com/pricing/details/monitor/) dos Grupos de Ação.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualizar e analisar os dados do incidente e alterar os dados do pedido

Com base na sua configuração ao configurar uma ligação, o conector ITSM pode sincronizar até 120 dias de dados de pedidos de incidentes e alterações. O esquema de registo de registo para estes dados é fornecido na [secção seguinte](#additional-information).

Os dados do incidente e alteração podem ser visualizados utilizando o painel de conector ITSM na solução.

![Tela de Log Analytics](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

O painel também fornece informações sobre o estado do conector que podem ser usados como ponto de partida para analisar quaisquer problemas com as ligações.

Também pode visualizar os incidentes sincronizados com os computadores impactados, dentro da solução Service Map.

O Service Map descobre automaticamente os componentes da aplicação nos sistemas Windows e Linux e mapeia a comunicação entre os serviços. Permite-lhe visualizar os seus servidores tal como os pensa – como sistemas interligados que prestam serviços críticos. O Mapa de Serviço mostra ligações entre servidores, processos e portas em qualquer arquitetura ligada ao TCP sem qualquer configuração necessária que não seja a instalação de um agente. [Saiba mais](../../azure-monitor/insights/service-map.md).

Se estiver a utilizar a solução Service Map, pode ver os itens de balcão de serviço criados nas soluções ITSM, como mostra o seguinte exemplo:

![Tela de Log Analytics](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

Mais informações: [Mapa do Serviço](../../azure-monitor/insights/service-map.md)


## <a name="additional-information"></a>Informações adicionais

### <a name="data-synced-from-itsm-product"></a>Dados sincronizados com o produto ITSM
Incidentes e pedidos de alteração são sincronizados do seu produto ITSM para o seu espaço de trabalho Log Analytics com base na configuração da ligação.

As seguintes informações mostram exemplos de dados recolhidos pelo ITSMC:

> [!NOTE]
>
> Dependendo do tipo de artigo de trabalho importado para o Log Analytics, **ServiceDesk_CL** contém os seguintes campos:

**Artigo de trabalho:** **Incidentes**  
ServiceDeskWorkItemType_s="Incident"

**Campos**

- ServiceDeskConnectionName
- ID do balcão de serviço
- Estado
- Urgência
- Impacto
- Prioridade
- Escalamento
- Criado por
- Resolvido Por
- Fechado por
- Origem
- Atribuído a
- Categoria
- Título
- Descrição
- Data de criação
- Data de fecho
- Data de resolução
- Data da última modificação
- Computador


**Artigo de trabalho:** **Alterar pedidos**

ServiceDeskWorkItemType_s="ChangeRequest"

**Campos**
- ServiceDeskConnectionName
- ID do balcão de serviço
- Criado por
- Fechado por
- Origem
- Atribuído a
- Título
- Tipo
- Categoria
- Estado
- Escalamento
- Estado do Conflito
- Urgência
- Prioridade
- Risco
- Impacto
- Atribuído a
- Data de criação
- Data de fecho
- Data da última modificação
- Data solicitada
- Data de início planeada
- Data final planeada
- Data de início do trabalho
- Data final do trabalho
- Descrição
- Computador

## <a name="output-data-for-a-servicenow-incident"></a>Dados de saída para um incidente serviceNow

| Campo de Log Analytics | Campo ServiceNow |
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
| Title_s|  Descrição curta |
| Description_s|  Notas |
| CreatedDate_t|  Aberto |
| ClosedDate_t| Fechado|
| ResolvedDate_t|Resolvido|
| Computador  | Item de configuração |

## <a name="output-data-for-a-servicenow-change-request"></a>Dados de saída para um pedido de alteração ServiceNow

| Log Analytics | Campo ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | Solicitado por |
| ClosedBy_s | Fechado por |
| AssignedTo_s | Atribuído a  |
| Title_s|  Descrição curta |
| Type_s|  Tipo |
| Category_s|  Categoria |
| CRState_s|  Estado|
| Urgency_s|  Urgência |
| Priority_s| Prioridade|
| Risk_s| Risco|
| Impact_s| Impacto|
| RequestedDate_t  | Solicitado por data |
| ClosedDate_t | Data de fecho |
| PlannedStartDate_t  |     Data de início planeada |
| PlannedEndDate_t  |   Data final planeada |
| WorkStartDate_t  | Data de início real |
| WorkEndDate_t | Data de fim real|
| Description_s | Descrição |
| Computador  | Item de Configuração |


## <a name="troubleshoot-itsm-connections"></a>Ligações ITSM de resolução de problemas
1. Se a ligação falhar no UI da fonte conectada com um Erro na mensagem de **ligação de salvamento,** tome os seguintes passos:
   - Para ligações ServiceNow, Cherwell e Provance,  
   - certifique-se de que inseriu corretamente o nome de utilizador, palavra-passe, ID do cliente e segredo do cliente para cada uma das ligações.  
   - verifique se tem privilégios suficientes no produto ITSM correspondente para efazer a ligação.  
   - Para ligações de Gestor de Serviços,  
   - garantir que a aplicação Web é implementada com sucesso e a conexão híbrida é criada. Para verificar se a ligação é estabelecida com sucesso com a máquina de Gestor de Serviços no local, visite o URL da aplicação Web conforme detalhado na documentação para fazer a [ligação híbrida](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection).  

2. Se os dados do ServiceNow não estiverem a ser sincronizados no Log Analytics, certifique-se de que a instância ServiceNow não está a dormir. ServiceNow Dev Instances às vezes vão dormir quando estão inativos por um longo período. Caso contrário, informe o assunto.
3. Se o Log Analytics alertar o fogo, mas os itens de trabalho não forem criados em produtos ITSM ou itens de configuração não são criados/ligados a itens de trabalho ou para qualquer outra informação genérica, procure nos seguintes locais:
   -  ITSMC: A solução mostra um resumo de ligações/itens de trabalho/computadores, etc. Clique no azulejo que mostra o Estado do **Conector,** que o leva a **Registar Pesquisa** com a consulta relevante. Veja os registos de registo com LogType_S como ERROR para obter mais informações.
   - **Página de pesquisa de registo:** consulte os erros/informações relacionadas diretamente utilizando a consulta `*`ServiceDeskLog_CL`*`.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Implementação da Aplicação Web do Gestor de Serviços de Resolução de Problemas
1.  Em caso de problemas com a implementação de aplicações web, certifique-se de que tem permissões suficientes na subscrição mencionada para criar/implementar recursos.
2.  Se obtém um erro de "referência do **objeto não definido para a instância de um objeto"** quando executa a [script](itsmc-service-manager-script.md), certifique-se de que introduziu valores válidos na secção de Configuração do **Utilizador.**
3.  Se não criar o espaço de nome de retransmissão de ônibus de serviço, certifique-se de que o fornecedor de recursos necessário está registado na subscrição. Se não estiver registado, crie manualmente o espaço de nome de retransmissão de ônibus de serviço a partir do portal Azure. Também pode criá-lo ao mesmo tempo [que cria a ligação híbrida](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection) a partir do portal Azure.


## <a name="contact-us"></a>Contacte-nos

Para quaisquer dúvidas ou comentários sobre o Conector de Gestão de Serviços de TI, contacte-nos em [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Passos seguintes
[Adicione produtos/serviços ITSM ao Conector](../../azure-monitor/platform/itsmc-connections.md)de Gestão de Serviços de TI.
