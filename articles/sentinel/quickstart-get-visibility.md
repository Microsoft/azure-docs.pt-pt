---
title: 'Início Rápido: Introdução ao Azure Sentinel'
description: Use este quickstart para aprender a visualizar e monitorizar rapidamente o que está a acontecer em todo o seu ambiente, utilizando o Azure Sentinel.
services: sentinel
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: affefb302d602e9069a903fa5f6a0fbae78992b9
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516958"
---
# <a name="quickstart-get-started-with-azure-sentinel"></a>Início Rápido: Introdução ao Azure Sentinel




Neste arranque rápido, você vai aprender a ser rapidamente capaz de ver e monitorizar o que está acontecendo em todo o seu ambiente usando Azure Sentinel. Depois de ligar as suas fontes de dados ao Azure Sentinel, obtém visualização e análise instantânea de dados para que possa saber o que está a acontecer em todas as suas fontes de dados conectadas. O Azure Sentinel dá-lhe livros que lhe fornecem todo o poder de ferramentas já disponíveis no Azure, bem como tabelas e gráficos que estão incorporados para lhe fornecer análises para os seus registos e consultas. Pode utilizar livros embutidos ou criar facilmente um novo livro, a partir do zero ou com base num livro existente. 

## <a name="get-visualization"></a>Obter visualização

Para visualizar e analisar o que está a acontecer no seu ambiente, em primeiro lugar, dê uma olhada no painel de visão geral para ter uma ideia da postura de segurança da sua organização. Pode clicar em cada elemento destes azulejos para perfurar até aos dados brutos a partir dos quais são criados. Para ajudá-lo a reduzir o ruído e minimizar o número de alertas que tem de rever e investigar, a Azure Sentinel usa uma técnica de fusão para correlacionar alertas em incidentes. **Incidentes** são grupos de alertas relacionados que juntos criam um incidente acccionável que você pode investigar e resolver.

- No portal Azure, selecione Azure Sentinel e, em seguida, selecione o espaço de trabalho que pretende monitorizar.

  ![Descrição Geral do Azure Sentinel](./media/qs-get-visibility/overview.png)

- A barra de ferramentas em toda a parte superior indica-lhe quantos eventos obteve ao longo do período de tempo selecionado, e compara-o com as 24 horas anteriores. A barra de ferramentas diz-lhe destes eventos, os alertas que foram desencadeados (o pequeno número representa mudança nas últimas 24 horas), e depois diz-lhe para esses eventos, quantos estão abertos, em andamento e fechados. Verifique se não há um aumento dramático ou uma queda no número de eventos. Se houver uma queda, pode ser que uma ligação parou de reportar ao Azure Sentinel. Se houver um aumento, algo suspeito pode ter acontecido. Verifique se tem novos alertas.

   ![Contadores Azure Sentinel](./media/qs-get-visibility/funnel.png)

O corpo principal da página de visão geral dá uma visão de um olhar sobre o estado de segurança do seu espaço de trabalho:

- **Eventos e alertas ao longo**do tempo : Lista o número de eventos e quantos alertas foram criados a partir desses eventos. Se vires um pico que é incomum, deves ver alertas para isso - se há algo de invulgar onde há um pico em eventos mas não vês alertas, pode ser motivo de preocupação.

- **Potenciais eventos maliciosos**: Quando o tráfego é detetado a partir de fontes que são conhecidas por serem maliciosas, o Azure Sentinel alerta-o no mapa. Se vir laranja, é tráfego de entrada: alguém está a tentar aceder à sua organização a partir de um conhecido endereço IP malicioso. Se vir a atividade outbound (vermelha), significa que os dados da sua rede estão a ser transmitidos para fora da sua organização para um endereço IP malicioso conhecido.

   ![Mapa de tráfego malicioso](./media/qs-get-visibility/map.png)

- **Incidentes recentes**: Para ver os seus incidentes recentes, a sua gravidade e o número de alertas associados ao incidente. Se vires um pico repentino num tipo específico de alerta, pode significar que há um ataque ativo atualmente em curso. Por exemplo, se tiver um pico repentino de 20 eventos Pass-the-hash do Microsoft Defender for Identity (ex-Azure ATP), é possível que alguém esteja atualmente a tentar atacá-lo.

- **Anomalias na origem**dos dados : Os analistas de dados da Microsoft criaram modelos que procuram constantemente os dados das suas fontes de dados para anomalias. Se não houver anomalias, nada é mostrado. Se forem detetadas anomalias, deve mergulhar profundamente nelas para ver o que aconteceu. Por exemplo, clique no pico na Atividade Azure. Pode clicar em **Gráfico** para ver quando o pico aconteceu e, em seguida, filtrar as atividades que ocorreram durante esse período de tempo para ver o que causou o pico.

   ![Fontes de dados anómalas](./media/qs-get-visibility/anomolies.png)

## <a name="use-built-in-workbooks"></a>Use livros embutidos<a name="dashboards"></a>

Os livros incorporados fornecem dados integrados a partir das suas fontes de dados conectadas para permitir que mergulhe profundamente nos eventos gerados nesses serviços. Os livros incorporados incluem Azure AD, eventos de atividade do Azure e no local, que podem ser dados de Eventos Windows a partir de servidores, de alertas de primeira parte, de terceiros, incluindo registos de tráfego de firewall, Office 365, e protocolos inseguros baseados em eventos windows. Os livros são baseados em livros de trabalho do Azure Monitor para lhe proporcionar uma maior personalização e flexibilidade na conceção do seu próprio livro. Para mais informações, consulte [os Livros de Trabalho.](../azure-monitor/platform/workbooks-overview.md)

1. Em **Definições**, selecione **Livros de Trabalho**. Em **Instalar,** pode ver todo o seu livro instalado. Em **Tudo,** pode ver toda a galeria de livros embutidos que estão disponíveis para instalação. 
2. Procure um livro específico para ver toda a lista e descrição do que cada um oferece. 
3. Assumindo que usa a Azure AD, para se levantar e funcionar com o Azure Sentinel, recomendamos que instale pelo menos os seguintes livros:
   - **Azure AD**: Utilize ambos ou ambos os seguintes:
       - **A azure AD inscreve-ins** analisa as entradas ao longo do tempo para ver se há anomalias. Estes livros fornecem inserções falhadas por aplicações, dispositivos e locais para que possa notar, num ápice se algo incomum acontecer. Preste atenção a várias entradas falhadas. 
       - **Os registos de auditoria da AD AD** analisam as atividades de administração, tais como alterações nos utilizadores (adicionar, remover, etc.), criação de grupo e modificações.  

   - Adicione um livro para a sua firewall. Por exemplo, adicione o livro de Palo Alto. O livro analisa o tráfego de firewall, fornecendo-lhe correlações entre os seus dados de firewall e eventos de ameaça, e destaca eventos suspeitos entre entidades. Os livros de trabalho fornecem-lhe informações sobre as tendências do seu tráfego e permitem-lhe aprofundar e filtrar os resultados. 

      ![Painel palo Alto](./media/qs-get-visibility/palo-alto-week-query.png)


Pode personalizar os livros de trabalho editando o botão de edição de ![ consulta ](./media/qs-get-visibility/edit-query-button.png) principal. Pode clicar no ![ botão Log Analytics ](./media/qs-get-visibility/go-to-la-button.png) para ir ao Log Analytics para [editar a consulta lá](../azure-monitor/log-query/get-started-portal.md), e pode selecionar a elipse (...) e **selecionar Personalizar dados de azulejos**, o que lhe permite editar o filtro de tempo principal ou remover os azulejos específicos do livro.

Para obter mais informações sobre o trabalho com consultas, consulte [Tutorial: Dados visuais em Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Adicione um novo azulejo

Se quiser adicionar um novo azulejo, pode adicioná-lo a um livro existente, quer um que cria, quer um livro incorporado Azure Sentinel. 
1. No Log Analytics, crie um azulejo utilizando as instruções encontradas no [Tutorial: Dados visuais em Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Depois de criar o azulejo, em **Pin,** selecione o livro em que deseja que o azulejo apareça.

## <a name="create-new-workbooks"></a>Criar novos livros
Pode criar um novo livro de raiz ou usar um livro embutido como base para o seu novo livro.

1. Para criar um novo livro de trabalho do zero, selecione **Workbooks** e, em seguida, **+Novo livro**de trabalho.
2. Selecione a subscrição em que o livro é criado e dê-lhe um nome descritivo. Cada livro é um recurso Azure como qualquer outro, e você pode atribuí-lo papéis (Azure RBAC) para definir e limitar quem pode aceder. 
3. Para que ele apareça nos seus livros para fixar visualizações, você tem que partilhá-lo. Clique **em Partilhar** e, em seguida, Gerir os **utilizadores.** 
 
1. Utilize as atribuições de acesso e **função de** **Verificação** como faria para qualquer outro recurso Azure. Para obter mais informações, consulte [os livros share Azure utilizando o Azure RBAC](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-workbook-examples"></a>Novos exemplos de livros

A seguinte consulta de amostra permite-lhe comparar tendências de tráfego ao longo de semanas. Pode facilmente alternar qual o fornecedor de dispositivo e fonte de dados em que executou a consulta. Este exemplo utiliza SecurityEvent a partir do Windows, pode alterá-lo para funcionar no AzureActivity ou CommonSecurityLog em qualquer outra firewall.

```console
 |where DeviceVendor == "Palo Alto Networks":
  // week over week query
  SecurityEvent
  | where TimeGenerated > ago(14d)
  | summarize count() by bin(TimeGenerated, 1d)
  | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)
```

É melhor criar uma consulta que incorpore dados de múltiplas fontes. Pode criar uma consulta que analise os registos de auditoria do Azure Ative Directory para novos utilizadores que acabam de ser criados e, em seguida, verifique os seus registos Azure para ver se o utilizador começou a fazer alterações na atribuição de funções dentro de 24 horas após a criação. Aquela atividade suspeita apareceria neste painel de instrumentos:

```console
AuditLogs
| where OperationName == "Add user"
| project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
| join (AzureActivity
| where OperationName == "Create role assignment"
| project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
| project-away user1
```

Pode criar diferentes livros baseados no papel da pessoa que olha para os dados e para o que procura. Por exemplo, pode criar um livro de trabalho para a sua administração de rede que inclui os dados de firewall. Também pode criar livros com base na frequência com que pretende olhar para eles, se há coisas que quer rever diariamente, e outros itens que pretende verificar uma vez por hora, por exemplo, que talvez queira ver os seus sign-ins Azure AD a cada hora para procurar anomalias. 

## <a name="create-new-detections"></a>Criar novas deteções

Gere deteções nas [fontes de dados que ligou ao Azure Sentinel](connect-data-sources.md) para investigar ameaças na sua organização.

Quando criar uma nova deteção, aproveite as deteções incorporadas criadas por investigadores de segurança da Microsoft que são adaptadas às fontes de dados que ligou.

Para ver todas as deteções fora da caixa, vá ao **Analytics** e, em seguida, **aos modelos de Regras**. Este separador contém todas as regras incorporadas do Azure Sentinel.

   ![Use deteções incorporadas para encontrar ameaças com Azure Sentinel](media/tutorial-detect-built-in/view-oob-detections.png)

Para obter mais informações sobre a obtenção de deteções fora da caixa, consulte [Tutorial: Obter analíticos incorporados.](tutorial-detect-threats-built-in.md)
 
## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido, aprendeu a começar a usar o Azure Sentinel. Continue ao tutorial para [detetar ameaças.](tutorial-detect-threats-built-in.md)
> [!div class="nextstepaction"]
> [Crie regras de deteção de ameaças personalizadas](tutorial-detect-threats-custom.md) para automatizar as suas respostas a ameaças.

