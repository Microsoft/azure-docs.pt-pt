---
title: 'Quickstart: Comece com Azure Sentinel'
description: Azure Sentinel Quickstart - Comece com O Sentinela
services: sentinel
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 4bd01b737c4a80ff286458c122f3b0ab9573a40b
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798679"
---
# <a name="quickstart-get-started-with-azure-sentinel"></a>Quickstart: Comece com Azure Sentinel




Neste arranque rápido, você vai aprender a ser rapidamente capaz de ver e monitorizar o que está acontecendo em todo o seu ambiente usando O Sentinel apiná-lo. Depois de ligar as suas fontes de dados ao Azure Sentinel, obtém visualização instantânea e análise de dados para que possa saber o que está a acontecer em todas as suas fontes de dados conectadas. O Azure Sentinel dá-lhe livros que lhe fornecem toda a potência das ferramentas já disponíveis no Azure, bem como tabelas e gráficos que estão integrados para lhe fornecer análises para os seus registos e consultas. Pode utilizar livros incorporados ou criar facilmente um novo livro de trabalho, desde o zero ou baseado num livro existente. 

## <a name="get-visualization"></a>Obter visualização

Para visualizar e obter uma análise do que está a acontecer no seu ambiente, primeiro, dê uma olhada no painel de visão geral para ter uma ideia da postura de segurança da sua organização. Pode clicar em cada elemento destes azulejos para perfurar até aos dados brutos a partir dos quais são criados. Para o ajudar a reduzir o ruído e minimizar o número de alertas que tem de rever e investigar, o Azure Sentinel utiliza uma técnica de fusão para correlacionar os alertas em incidentes. **incidentes** são grupos de alertas relacionados que juntos criam um incidente atorável que você pode investigar e resolver.

- No portal Azure, selecione O Sentinel a pino e, em seguida, selecione o espaço de trabalho que pretende monitorizar.

  ![Descrição Geral do Azure Sentinel](./media/qs-get-visibility/overview.png)

- A barra de ferramentas em cima diz-lhe quantos eventos obteve ao longo do período de tempo selecionado, e compara-o com as 24 horas anteriores. A barra de ferramentas conta-lhe destes eventos, os alertas que foram desencadeados (o pequeno número representa a mudança nas últimas 24 horas), e depois diz-lhe para esses eventos, quantos estão abertos, em andamento e fechados. Verifique se não há um aumento dramático ou uma queda no número de eventos. Se houver uma queda, pode ser que uma ligação pare de reportar ao Azure Sentinel. Se houver um aumento, algo suspeito pode ter acontecido. Verifique se tem novos alertas.

   ![Funil Azure Sentinel](./media/qs-get-visibility/funnel.png)

O corpo principal da página geral dá uma visão sobre o estado de segurança do seu espaço de trabalho:

- **Eventos e alertas ao longo**do tempo : Lista o número de eventos e quantos alertas foram criados a partir desses eventos. Se vires um pico incomum, devias ver alertas para ele - se há algo incomum onde há um pico em eventos mas não vês alertas, pode ser motivo de preocupação.

- **Potenciais eventos maliciosos**: Quando o tráfego é detetado a partir de fontes que são conhecidas por serem maliciosas, o Azure Sentinel alerta-o no mapa. Se vir laranja, é tráfego de entrada: alguém está a tentar aceder à sua organização a partir de um endereço IP malicioso conhecido. Se vir a atividade outbound (vermelha), significa que os dados da sua rede estão a ser transmitidos da sua organização para um endereço IP malicioso conhecido.

   ![Mapa do Sentinela Azure](./media/qs-get-visibility/map.png)


- **Incidentes recentes**: Para ver os seus incidentes recentes, a sua gravidade e o número de alertas associados ao incidente. Se vires como um pico repentino num tipo específico de alerta, pode significar que há um ataque ativo a decorrer. Por exemplo, se tiver um pico repentino de 20 eventos pass-the-hash do Azure ATP, é possível que alguém esteja a tentar atacá-lo.

- **Anomalias**na fonte de dados : Os analistas de dados da Microsoft criaram modelos que pesquisam constantemente os dados a partir das suas fontes de dados por anomalias. Se não houver anomalias, nada é exibido. Se forem detetadas anomalias, deve mergulhar profundamente nelas para ver o que aconteceu. Por exemplo, clique no pico na Atividade Azure. Pode clicar no **Gráfico** para ver quando o pico aconteceu e, em seguida, filtrar para atividades que ocorreram durante esse período de tempo para ver o que causou o pico.

   ![Mapa do Sentinela Azure](./media/qs-get-visibility/anomolies.png)

## <a name="use-built-in-workbooks"></a>Utilize livros embutidos<a name="dashboards"></a>

Os livros incorporados fornecem dados integrados a partir das suas fontes de dados conectadas para permitir que você mergulhe profundamente nos eventos gerados nesses serviços. Os livros incorporados incluem Azure AD, eventos de atividade azure e no local, que podem ser dados de Eventos Windows a partir de servidores, a partir de alertas de primeira parte, de qualquer terceiro, incluindo registos de tráfego de firewall, Office 365, e protocolos inseguros baseados em eventos windows. Os livros baseiam-se em livros de trabalho do Monitor Azure para lhe proporcionar uma maior personalizabilidade e flexibilidade na conceção do seu próprio livro. Para mais informações, consulte [Livros de Trabalho.](../azure-monitor/platform/workbooks-overview.md)

1. Em **Definições,** selecione Livros de **Trabalho**. Em **Instalação,** pode ver todo o seu livro de trabalho instalado. Em **Tudo,** pode ver toda a galeria de livros embutidos que estão disponíveis para instalação. 
2. Procure um livro específico para ver toda a lista e descrição do que cada um oferece. 
3. Assumindo que utiliza o Azure AD, para se levantar e funcionar com o Azure Sentinel, recomendamos que instale pelo menos os seguintes livros de trabalho:
   - **Azure AD**: Utilize ambos ou ambos os seguintes:
       - **Os sign-ins Azure AD** analisam os sign-ins ao longo do tempo para ver se existem anomalias. Estes livros de livros fornecem inscrições falhadas por aplicações, dispositivos e locais para que possa notar, num ápice, se algo incomum acontecer. Preste atenção a vários insins falhados. 
       - Os registos de **auditoria da Azure AD** analisam as atividades de administração, tais como alterações nos utilizadores (adicionar, remover, etc.), criação de grupo e modificações.  

   - Adicione um livro de trabalho para a sua firewall. Por exemplo, adicione o livro de Palo Alto. O livro analisa o tráfego de firewall, fornecendo-lhe correlações entre os dados da firewall e eventos de ameaça, e destaca eventos suspeitos entre entidades. Os livros de reções fornecem-lhe informações sobre as tendências do seu tráfego e permitem-lhe aprofundar e filtrar os resultados. 

      ![Painel de pal Alto](./media/qs-get-visibility/palo-alto-week-query.png)


Pode personalizar os livros de trabalho ou editando o botão de consulta principal ![ ](./media/qs-get-visibility/edit-query-button.png) . Pode clicar no botão botão ![ para ir ao Log Analytics para editar a consulta ](./media/qs-get-visibility/go-to-la-button.png) [lá](../azure-monitor/log-query/get-started-portal.md), e pode selecionar a elipse (...) e selecionar dados de **azulejos Personalizados,** que lhe permite editar o filtro de tempo principal, ou remover os azulejos específicos do livro.

Para obter mais informações sobre o trabalho com consultas, consulte [Tutorial: Dados visuais em Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Adicione um novo azulejo

Se quiser adicionar um azulejo novo, pode adicioná-lo a um livro existente, seja um que crie ou um livro integrado Azure Sentinel. 
1. No Log Analytics, crie um azulejo utilizando as instruções encontradas no [Tutorial: Dados visuais em Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Depois de criado o azulejo, sob **pin,** selecione o livro de trabalho no qual pretende que o azulejo apareça.

## <a name="create-new-workbooks"></a>Criar novos livros
Pode criar um novo livro de raiz ou usar um livro incorporado como base para o seu novo livro.

1. Para criar um novo livro de raiz, selecione **Livros** e, em seguida, **+Novo livro.**
2. Selecione a subscrição em que o livro é criado e dê-lhe um nome descritivo. Cada livro é um recurso Azure como qualquer outro, e você pode atribuir-lhe papéis (RBAC) para definir e limitar quem pode aceder. 
3. Para permitir que apareça nos seus livros para fixar visualizações, tem de o partilhar. Clique em **Partilhar** **e,** em seguida, gerir os utilizadores . 
 
1. Utilize as **atribuições** **de acesso** ao Cheque e Role como faria para qualquer outro recurso Azure. Para mais informações, consulte os livros de [trabalho Share Azure utilizando o RBAC](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-workbook-examples"></a>Novos exemplos de livros

A seguinte consulta de amostra permite comparar tendências de tráfego ao longo de semanas. Pode facilmente mudar o fornecedor do dispositivo e a fonte de dados em que executa a consulta. Este exemplo utiliza o SecurityEvent a partir do Windows, pode trocá-lo para executar em AzureActivity ou CommonSecurityLog em qualquer outra firewall.

     |where DeviceVendor == "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Pode querer criar uma consulta que incorpore dados de múltiplas fontes. Pode criar uma consulta que analise os registos de auditoria do Azure Ative Directory para novos utilizadores que acabaram de ser criados e, em seguida, verifica os seus registos Azure para ver se o utilizador começou a fazer alterações de atribuição de papéis no prazo de 24 horas após a criação. Aquela atividade suspeita apareceria neste painel de instrumentos:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

Pode criar diferentes livros baseados no papel da pessoa que olha para os dados e o que procura. Por exemplo, pode criar um livro de trabalho para a sua administração de rede que inclua os dados de firewall. Também pode criar livros baseados na frequência com que pretende olhar para eles, se há coisas que pretende rever diariamente, e outros itens que pretende verificar uma vez por hora, por exemplo, talvez queira ver os seus sign-ins Azure AD a cada hora para procurar anomalias. 

## <a name="create-new-detections"></a>Criar novas deteções

Gere deteções nas fontes de [dados que ligou ao Azure Sentinel](connect-data-sources.md) para investigar ameaças na sua organização.

Ao criar uma nova deteção, aproveite as deteções incorporadas feitas por investigadores de segurança da Microsoft que são adaptados às fontes de dados que ligou.

Para ver todas as deteções fora da caixa, vá ao **Analytics** e, em seguida, **reine os modelos**. Este separador contém todas as regras incorporadas do Azure Sentinel.

   ![Utilize deteções incorporadas para encontrar ameaças com o Azure Sentinel](media/tutorial-detect-built-in/view-oob-detections.png)

Para obter mais informações sobre a deteção fora da caixa, consulte [Tutorial: Get in-analytics](tutorial-detect-threats-built-in.md).
 
## <a name="next-steps"></a>Passos seguintes
Neste início rápido, aprendeu a começar a usar o Azure Sentinel. Continue ao tutorial sobre [como detetar ameaças.](tutorial-detect-threats-built-in.md)
> [!div class="nextstepaction"]
> [Crie regras personalizadas](tutorial-detect-threats-custom.md) de deteção de ameaças para automatizar as suas respostas a ameaças.

