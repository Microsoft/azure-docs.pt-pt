---
title: Conector de Gestão de Serviços de TI em Azure Log Analytics / Microsoft Docs
description: Este artigo fornece uma visão geral do It Service Management Connector (ITSMC) e informações sobre como usar esta solução para monitorizar e gerir centralmente os itens de trabalho itsm em Azure Log Analytics, e resolver quaisquer problemas rapidamente.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: 344e15d3c15474fc8959b120f86bb86f22217ef6
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676974"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Ligar o Azure às ferramentas do ITSM com o Conector de Gestão de Serviços de TI

![Símbolo do conector de gestão de serviço de TI](media/itsmc-overview/itsmc-symbol.png)

O Conector de Gestão de Serviços de TI (ITSMC) permite ligar o Azure a um produto/serviço de Gestão de Serviços de TI (ITSM) suportado.

Serviços Azure como o Log Analytics e o Azure Monitor fornecem ferramentas para detetar, analisar e resolver problemas com os seus recursos Azure e não-Azure. No entanto, os artigos de trabalho relacionados com um problema residem normalmente num produto/serviço ITSM. O conector ITSM fornece uma ligação bidis entre as ferramentas Azure e ITSM para ajudá-lo a resolver problemas mais rapidamente.

A ITSMC suporta ligações com as seguintes ferramentas ITSM:

-   ServiceNow
-   Gestor de serviços do Centro de Sistema
-   Provance
-   Cherwell

Com o ITSMC, pode:

-  Crie itens de trabalho na ferramenta ITSM, com base nos seus alertas Azure (alertas métricos, alertas de Registo de Atividade e alertas de Log Analytics).
-  Opcionalmente, pode sincronizar o seu incidente e alterar os dados de pedido da sua ferramenta ITSM para um espaço de trabalho Azure Log Analytics.

Leia mais sobre os [termos legais e a política de privacidade.](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)

Pode começar a utilizar o conector ITSM utilizando os seguintes passos:

1.  [Adicione a solução de conector ITSM](#adding-the-it-service-management-connector-solution)
2.  Criar uma ligação ITSM
3.  [Utilize a ligação](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Adicionar a Solução de Conector de Gestão de Serviços de TI

Antes de poder criar uma ligação, tem de adicionar a Solução de Conector ITSM.

1. No portal Azure, clique **+ Novo** ícone.

   ![Novo recurso Azure](media/itsmc-overview/azure-add-new-resource.png)

2. Procure o **Conector de Gestão de Serviços de TI** no Mercado e clique em **Criar** .

   ![Adicionar solução ITSMC](media/itsmc-overview/add-itsmc-solution.png)

3. Na secção **espaço de trabalho OMS,** selecione o espaço de trabalho Azure Log Analytics onde pretende instalar a solução.
   >[!NOTE]
   > * Como parte da transição em curso do Microsoft Operations Management Suite (OMS) para o Azure Monitor, os espaços de trabalho da OMS são agora referidos como espaços de trabalho Log Analytics.
   > * O CONECTOR ITSM só pode ser instalado em espaços de trabalho log Analytics nas seguintes regiões: East US, West US2, South Central US, West Central US, Fairfax, Central Canada, West Europe, South UK, Southeast Asia, East Japan, Central India, Southeast Australia.

4. Na secção Definições do **Espaço de Trabalho OMS,** selecione o Grupo de Recursos onde pretende criar o recurso de solução.

   ![Espaço de trabalho ITSMC](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Como parte da transição em curso do Microsoft Operations Management Suite (OMS) para o Azure Monitor, os espaços de trabalho da OMS são agora referidos como espaços de trabalho Log Analytics.

5. Clique em **OK** .

Quando o recurso de solução é implantado, aparece uma notificação no topo direito da janela.


## <a name="creating-an-itsm--connection"></a>Criação de uma ligação ITSM

Depois de ter instalado a solução, pode criar uma ligação.

Para criar uma ligação, terá de preparar a sua ferramenta ITSM para permitir a ligação a partir da solução ITSM Connector.  

Dependendo do produto ITSM a que está a ligar, utilize os seguintes passos:

- [Gestor de Serviços do Centro de Sistema (SCSM)](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Depois de ter preparado as suas ferramentas ITSM, siga os passos abaixo para criar uma ligação:

1. Ir a **Todos os Recursos,** procurar **ServiçoDesk (YourWorkspaceName)** .
2. Em **FONTES DE DADOS DO ESPAÇO DE TRABALHO** no painel esquerdo, clique em **ItsM Connections** .
   ![Ligações ITSM](media/itsmc-overview/itsm-connections.png)

   Esta página apresenta a lista de ligações.
3. Clique **em Adicionar Ligação** .

   ![Adicionar ligação ITSM](media/itsmc-overview/add-new-itsm-connection.png)

4. Especifique as definições de ligação descritas na [configuração da ligação ITSMC com o seu artigo de produtos/serviços ITSM](./itsmc-connections.md).

   > [!NOTE]
   >
   > Por predefinição, o ITSMC atualiza os dados de configuração da ligação uma vez em cada 24 horas. Para atualizar os dados da sua ligação instantaneamente para quaisquer edições ou atualizações de modelos que escote, clique no botão **Sync** na lâmina da sua ligação.

   ![Atualização de conexão](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Utilizar a solução
   Ao utilizar a solução ITSM Connector, pode criar itens de trabalho a partir de alertas Azure, alertas de Log Analytics e registos de registos do Log Analytics.

## <a name="template-definitions"></a>Definições de modelo
   Existem tipos de Itens de Trabalho que podem usar modelos que são definidos pela ferramenta ITSM.
A utilização de modelos permite ao cliente definir campos que serão povoados automaticamente de acordo com valores fixos que são definidos como parte do grupo de ação. A definição dos modelos é feita na ferramenta ITSM.
      
## <a name="create-itsm-work-items-from-azure-alerts"></a>Crie itens de trabalho ITSM a partir de alertas Azure

Assim que tiver a sua ligação ITSM criada, pode criar artigos de trabalho na sua ferramenta ITSM com base em alertas Azure, utilizando a **Ação ITSM** em **Grupos de Ação** .

Os Grupos de Ação fornecem uma forma modular e reutilizável de desencadear ações para os seus Alertas Azure. Pode utilizar grupos de ação com alertas métricos, alertas de registo de atividade e alertas Azure Log Analytics no portal Azure.

> [!NOTE]
> Uma vez criada a ligação ITSM, é necessário esperar 30 min após o conector criado para o processo de sincronização terminar
> 

Utilize o seguinte procedimento:

1. No portal Azure, clique em **Alertas.**
2. No painel superior, clique em **Gerir Ações.** Aparece a janela **do grupo de ação Add.**

    ![Grupos de Ação](media/itsmc-overview/action-groups.png)

3. Selecione o Grupo **de Subscrição** e **Recursos** onde pretende criar o seu grupo de ação. Forneça o nome e o nome **do grupo** de **ação** para o seu grupo de ação. Clique em **Seguinte: Notificações** .

    ![Detalhe de grupos de ação](media/itsmc-overview/action-groups-details.png)

4. Na lista de notificações clique no **Ninho: Ações** .
5. Na lista de Ações, selecione **ITSM** do menu suspenso para **o Tipo** de Ação . Forneça um **Nome** para a ação e clique na caneta que representa **detalhes de Edição.**
6. Selecione a **Subscrição** onde está localizado o seu espaço de trabalho Log Analytics. Selecione o nome **De Ligação** (o nome ITSM Connector) seguido do nome do espaço de trabalho. Por exemplo, "MyITSMMConnector (MyWorkspace)."

    ![Detalhes da ação itsm](media/itsmc-overview/itsm-action-details.png)

7. Selecione o item de **trabalho** do menu suspenso.

8. Se pretender preencher os campos de caixa com valores fixos, deve selecionar a caixa de verificação de "Use Custom Template" caso contrário, optar por utilizar um [modelo](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#template-definitions) existente a partir do dropdown e preencher os campos de modelo com valores fixos.

9. Ao selecionar a caixa de verificação **Criar itens de trabalho individuais para cada Item de configuração** cada item de configuração terá o seu próprio item de trabalho. Ou seja, haverá um item de trabalho por item de configuração e será atualizado de acordo com os alertas que serão criados.
Se descosistada a caixa de verificação **Criar itens de trabalho individuais para cada Configuração Item** cada alerta criará um novo item de trabalho, o que significa que pode haver mais de 1 alerta por item de configuração.

10. Clique em **OK** .

Ao criar/editar uma regra de alerta Azure, utilize um grupo action, que tem uma ação ITSM. Quando o alerta dispara, o item de trabalho é criado/atualizado na ferramenta ITSM.

> [!NOTE]
>
> Para obter informações sobre os preços da ITSM Action, consulte [a página de preços dos](https://azure.microsoft.com/pricing/details/monitor/) Grupos de Ação.

> [!NOTE]
>
> O campo de descrição curta na definição de regra de alerta é limitado a 40 caracteres quando é enviado usando ação ITSM.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualizar e analisar o incidente e alterar os dados do pedido

Com base na sua configuração ao configurar uma ligação, o conector ITSM pode sincronizar até 120 dias de dados de pedido de Incidente e Alteração. O esquema de registo de registo destes dados é fornecido na [secção seguinte](#additional-information).

Os dados de pedido de incidente e alteração podem ser visualizados utilizando o painel de instrumentos itsm connector na solução.

![Screenshot que mostra o painel de conector ITSM.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

O painel também fornece informações sobre o estado do conector que podem ser usadas como ponto de partida para analisar quaisquer problemas com as ligações.

Também pode visualizar os incidentes sincronizados com os computadores impactados, dentro da solução 'Mapa de Serviço'.

O Mapa de Serviços descobre automaticamente os componentes da aplicação nos sistemas Windows e Linux e mapeia a comunicação entre os serviços. Permite-lhe visualizar os seus servidores, como os concebe: sistemas interligados que fornecem serviços críticos. O Mapa de Serviços mostra as ligações entre os servidores, processos e portas em qualquer arquitetura com ligação TCP, e não requer qualquer configuração para além da instalação de um agente. [Saiba mais](../insights/service-map.md).

Se estiver a utilizar a solução 'Mapa de Serviços', pode visualizar os itens de balcão de assistência criados nas soluções ITSM, como mostra o seguinte exemplo:

![Rastreio de Log Analytics](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

Mais informações: [Mapa de Serviços](../insights/service-map.md)


## <a name="additional-information"></a>Informações adicionais

### <a name="data-synced-from-itsm-product"></a>Dados sincronizados com o produto ITSM
Incidentes e pedidos de alteração são sincronizados do seu produto ITSM para o seu espaço de trabalho Log Analytics com base na configuração da ligação.

As seguintes informações mostram exemplos de dados recolhidos pela ITSMC:

> [!NOTE]
>
> Dependendo do tipo de artigo de trabalho importado para o Log Analytics, **ServiceDesk_CL** contém os seguintes campos:

**Artigo de trabalho:** **Incidentes**  
ServiceDeskWorkItemType_s="Incidente"

**Campos**

- Nome de ServiçoDeskConnectionName
- ID de serviço
- Estado
- Urgência
- Impacto
- Prioridade
- Escalamento
- Criada Por
- Resolvido por
- Fechado por
- Fonte
- Atribuído a
- Categoria
- Título
- Descrição
- Data de Criação
- Data de Fecho
- Data de Resolução
- Data da Última Modificação
- Computador


**Artigo de trabalho:** **Alterar pedidos**

ServiceDeskWorkItemType_s="ChangeRequest"

**Campos**
- Nome de ServiçoDeskConnectionName
- ID de serviço
- Criada Por
- Fechado por
- Fonte
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
- Data de Criação
- Data de Fecho
- Data da Última Modificação
- Data Solicitada
- Data de início planeada
- Data de fim planeada
- Data de início do trabalho
- Data de fim do trabalho
- Descrição
- Computador

## <a name="output-data-for-a-servicenow-incident"></a>Dados de saída para um incidente do ServiceNow

| Log Analytics | Campo ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Número |
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
| CreatedDate_t|  Inaugurado |
| ClosedDate_t| fechado|
| ResolvedDate_t|Resolvido|
| Computador  | Item de configuração |

## <a name="output-data-for-a-servicenow-change-request"></a>Dados de saída para um pedido de alteração serviceNow

| Log Analytics | Campo ServiceNow |
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
| ClosedDate_t | Data fechada |
| PlannedStartDate_t  |     Data de início prevista |
| PlannedEndDate_t  |   Data de fim prevista |
| WorkStartDate_t  | Data de início real |
| WorkEndDate_t | Data de fim real|
| Description_s | Descrição |
| Computador  | Item de configuração |


## <a name="troubleshoot-itsm-connections"></a>Resolução de problemas ligações ITSM
1. Se a ligação falhar da UI da fonte ligada com um **Erro na mensagem de ligação de poupança,** tome os seguintes passos:
   - Para ligações ServiceNow, Cherwell e Provance,  
   - certifique-se de que inseriu corretamente o nome de utilizador, palavra-passe, identificação do cliente e segredo do cliente para cada uma das ligações.  
   - verifique se tem privilégios suficientes no produto ITSM correspondente para escoar a ligação.  
   - Para ligações de Gestor de Serviço,  
   - certifique-se de que a aplicação Web é implementada com sucesso e a ligação híbrida é criada. Para verificar se a ligação está estabelecida com sucesso com a máquina de Service Manager nas instalações, visite o URL da aplicação Web conforme detalhado na documentação para a realização da [ligação híbrida](./itsmc-connections.md#configure-the-hybrid-connection).  

2. Se os dados do ServiceNow não estiverem a ser sincronizados com o Log Analytics, certifique-se de que a instância ServiceNow não está a dormir. ServiceNow Dev Instances às vezes dorme quando ocioso por um longo período. Caso contrário, informe o assunto.
3. Se o Log Analytics alertar o fogo mas os itens de trabalho não forem criados em produtos ou itens de configuração ITSM não forem criados/ligados a itens de trabalho ou a qualquer outra informação genérica, procure nos seguintes locais:
   -  ITSMC: A solução mostra um resumo de ligações/artigos de trabalho/computadores, etc. Clique no azulejo que mostra o **Estado do Conector,** que o leva a **registar pesquisa**  com a consulta relevante. Veja os registos de registos com LogType_S como ERROR para obter mais informações.
   - **Registar Página de pesquisa:** ver os erros/informações relacionadas usando a consulta `*` ServiceDeskLog_CL `*` .

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Implementação de Aplicativo Web do Gestor de Serviços de Resolução de Problemas
1.  Em caso de problemas com a implementação de aplicações web, certifique-se de que tem permissões suficientes na subscrição mencionada para criar/implementar recursos.
2.  Se obter uma **referência de "Objeto não definida como uma instância de um objeto"** quando executar o [script](itsmc-service-manager-script.md), certifique-se de que introduziu valores válidos na secção **de Configuração do Utilizador.**
3.  Se não criar o espaço de nome de retransmissão de ônibus de serviço, certifique-se de que o fornecedor de recursos necessário está registado na subscrição. Se não estiver registado, crie manualmente o espaço de nome do retransmissor de autocarros a partir do portal Azure. Também pode criá-lo ao [criar a ligação híbrida](./itsmc-connections.md#configure-the-hybrid-connection) a partir do portal Azure.


## <a name="contact-us"></a>Contacte-nos

Para quaisquer consultas ou feedback sobre o Conector de Gestão de Serviços de TI, contacte-nos em [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) .

## <a name="next-steps"></a>Passos seguintes
[Adicione produtos/serviços ITSM ao It Service Management Connector](./itsmc-connections.md).

