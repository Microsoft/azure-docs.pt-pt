---
title: Início rápido do Azure Sentinel - introdução à pré-visualização de sentinela de Azure | Documentos da Microsoft
description: Início rápido do Azure Sentinel - introdução ao Azure sentinela
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5a4ae93c-d648-41fb-8fb8-96a025d2f73e
ms.service: sentinel
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/4/2019
ms.author: rkarlin
ms.openlocfilehash: 1d54ac521fb8b81ed5fa135f68af420bda261661
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246955"
---
# <a name="quickstart-get-started-with-azure-sentinel-preview"></a>Início rápido: Introdução ao Azure sentinela pré-visualização

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Neste início rápido, aprenderá como conseguir rapidamente ver e monitorizar o que está acontecendo em seu ambiente usando sentinela do Azure. Depois de ligar as origens de dados à sentinela do Azure, que recebe instantânea visualização e análise de dados para que pode saber o que está acontecendo em todas as origens de dados ligadas. Sentinel do Azure dá-lhe dashboards que proporcionam-lhe todo o potencial das ferramentas já disponíveis no Azure, bem como tabelas e gráficos que estão incorporados para lhe fornecer análise para os seus registos e consultas. Pode utilizar os painéis internos ou criar um novo dashboard facilmente, a partir do zero ou com base num dashboard existente. 

## <a name="get-visualization"></a>Obter a visualização

Para visualizar e obter a análise do que está acontecendo em seu ambiente, primeiro, vejamos o dashboard de descrição geral para ter uma idéia da postura de segurança da sua organização. Pode clicar em cada elemento destes mosaicos para desagregar os dados não processados a partir da qual são criados. Para ajudar a reduzir o ruído e minimizar o número de alertas, que terá de rever e investigar, Azure sentinela usa uma técnica de fusion para correlacionar os alertas em casos. **Casos** são grupos de alertas relacionados que juntos criam um incidente passíveis de ação que pode investigar e resolver.

- No portal do Azure, selecione Azure sentinela e, em seguida, selecione a área de trabalho que pretende monitorizar.

  ![Descrição geral de Sentinel do Azure](./media/qs-get-visibility/overview.png)

- A barra de ferramentas na parte superior indica quantos eventos obteve durante o período de tempo selecionado e compara-a 24 horas anteriores. Barra de ferramentas indica desses eventos, os alertas que foram acionados (a pequeno número representa alteração nas últimas 24 horas) e, em seguida, ele diz a para esses eventos, quantas têm aberto, em curso e fechados. Verifique para ver o que não há um aumento drástico ou remova o número de eventos. Se houver uma operação de soltar, é possível que uma ligação não reportam ao sentinela do Azure. Se houver um aumento, pode ter acontecido algo suspeito. Verifique a existência de novos alertas.

   ![Funil de Sentinel do Azure](./media/qs-get-visibility/funnel.png)

O corpo principal da página de descrição geral fornece informações rapidamente o estado de segurança de sua área de trabalho:

- **Eventos e alertas ao longo do tempo**: Lista o número de eventos e quantos alertas foram criadas a partir desses eventos. Se vir um pico de que está fora do comum, que deverá ver alertas para o mesmo - se há algo invulgar se onde existe um pico nos eventos, mas não a vir alertas, pode ser motivo de preocupação.

- **Potenciais eventos maliciosos**: Quando o tráfego é detectado a partir de origens que são conhecidas por serem mal-intencionados, sentinela Azure alerta-o no mapa. Se vir a cor de laranja, é o tráfego de entrada: alguém está a tentar aceder a sua organização a partir de um endereço IP malicioso conhecido. Se vir saída atividade (vermelha), significa que a transmissão de dados da sua rede fora da organização para um endereço IP malicioso conhecido.

   ![Mapas de Sentinel do Azure](./media/qs-get-visibility/map.png)


- **Os casos recentes**: Para ver os seus casos recentes, sua gravidade e o número de alertas associados com o caso. Se vir como picos repentinos num tipo específico de alerta, pode significar que existe um ataque de Active Directory em execução no momento. Por exemplo, se tiver um pico repentino de 20 eventos de Pass-the-hash da ATP do Azure, é possível que alguém está atualmente a tentar atacar.

- **Anomalias de origem de dados**: Analistas de dados da Microsoft criado modelos constantemente pesquisar os dados a partir de origens de dados para anomalias. Se não existem quaisquer anomalias, será apresentado nada. Se as anomalias são detetadas, deve aprofundar informações detalhadas sobre o-lhes para verem o que aconteceu. Por exemplo, clique no pico de atividade do Azure. Pode clicar em **gráfico** para ver quando ocorreu o pico e, em seguida, filtrar atividades que ocorreram durante esse período de tempo para ver o que causou o pico.

   ![Mapas de Sentinel do Azure](./media/qs-get-visibility/anomolies.png)

## Utilize dashboards incorporados<a name="dashboards"></a>

Dashboards incorporados fornecem dados integrada com as origens de dados ligada para permitir que profunda de aprofundar os eventos gerados nesses serviços. Os painéis internos incluem o ID do Azure, os eventos de atividade do Azure e no local, que pode ser dados de eventos do Windows a partir de servidores, da primeira alertas de terceiros, de terceiros ‑ incluindo protocolos inseguros com base no Windows, Office 365 e registos de tráfego de firewall eventos.

1. Sob **configurações**, selecione **Dashboards**. Sob **instalada**, pode ver todos os seus dashboards instalados. Sob **todos os** pode ver a galeria completa de dashboards incorporados que estão disponíveis para instalação. 
2. Procurar num dashboard específico ver o toda a lista e a descrição do que cada oferece. 
3. Supondo que utilize o Azure AD, para assegurar o funcionamento e em execução com o Azure sentinela, recomendamos que instale, pelo menos, os dashboards seguintes:
   - **Azure AD**: Utilize um ou ambos dos seguintes procedimentos:
       - **O Azure AD inícios de sessão** analisa os inícios de sessão ao longo do tempo para ver se existem anomalias. Este dashboard fornece inícios de sessão falhados por aplicativos, dispositivos e as localizações para que pode notar, rapidamente se acontecer alguma coisa excepcional. Preste atenção às vários inícios de sessão falhadas. 
       - **Registos de auditoria do Azure AD** analisa as atividades de administrador, como as alterações dos utilizadores (adicionar, remover, etc.) a criação de grupo e modificações.  

   - Adicione um dashboard para o seu firewall. Por exemplo, adicione o dashboard da Palo Alto. O dashboard analisa o tráfego de firewall, dando-lhe correlações entre eventos de ameaças e dados de seu firewall e destaca os eventos suspeitos em entidades. Dashboards fornece informações sobre as tendências em seu tráfego e permite-lhe desagregar e filtrar os resultados. 

      ![Dashboard de Alto PAL](./media/qs-get-visibility/palo-alto-week-query.png)


Pode personalizar os dashboards ao editar a consulta principal ![botão](./media/qs-get-visibility/edit-query-button.png). Pode clicar no botão ![botão](./media/qs-get-visibility/go-to-la-button.png) para aceder à [Log Analytics para editar a consulta lá](../azure-monitor/log-query/get-started-portal.md), e pode selecionar as reticências (...) e selecione **dados de mosaico de personalizar**, que lhe permite Para editar o filtro de tempo de principal ou remova os mosaicos de específicos a partir do dashboard.

Para obter mais informações sobre como trabalhar com consultas, consulte [Tutorial: Dados Visual no Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Adicionar um novo mosaico

Se pretender adicionar um novo mosaico, pode adicioná-lo a um dashboard existente, um criado por si ou um dashboard incorporado sentinela do Azure. 
1. No Log Analytics, criar um mosaico com as instruções no [Tutorial: Dados Visual no Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Depois do mosaico é criado, em **Pin**, selecione o dashboard no qual pretende que o mosaico a aparecer.

## <a name="create-new-dashboards"></a>Criar novos dashboards
Pode criar um novo dashboard a partir do zero ou utilizar um dashboard incorporado como base para o seu novo dashboard.

1. Para criar um novo dashboard a partir do zero, selecione **Dashboards** e, em seguida **+ novo dashboard**.
2. Selecione a subscrição, que o dashboard é criado no e dê a ele um nome descritivo. Cada dashboard é um recurso do Azure como qualquer outro, e pode atribuí-la funções (RBAC) para definir e limite quem pode aceder. 
3. Para permitir que seja apresentada nos seus dashboards ao afixar visualizações, terá de partilhá-lo. Clique em **partilha** e, em seguida **gerir utilizadores**. 
 
1. Utilize o **verificação de acesso** e **atribuições de funções** tal como faria para qualquer outro recurso do Azure. Para obter mais informações, consulte [dashboards do Azure de partilha com o RBAC](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-dashboard-examples"></a>Exemplos do novo dashboard

A seguinte consulta de exemplo permite-lhe comparar tendências de tráfego em semanas. É possível alternar facilmente quais fornecedor do dispositivo e a origem de dados, execute a consulta na. Este exemplo utiliza SecurityEvent do Windows, pode alternar seja executado no AzureActivity ou CommonSecurityLog em qualquer outra firewall.

     |where DeviceVendor = = "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Pode querer criar uma consulta que incorpore dados de várias origens. Pode criar uma consulta que analisa os registos de auditoria do Azure Active Directory para novos utilizadores que apenas foram criados e, em seguida, verificações de registos de sua do Azure para ver se o utilizador iniciou a fazer alterações de atribuição de função dentro de 24 horas da criação. Atividade suspeita apareceria neste dashboard:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

Pode criar dashboards diferentes com base na função da pessoa que examinar os dados e o estão procurando. Por exemplo, pode criar um dashboard para o administrador de rede que inclui os dados de firewall. Também pode criar dashboards com base na frequência com a qual pretende observá-los, se existem coisas que deseje revisar diariamente e outros itens que deseja verificar uma vez por hora, por exemplo, pode querer examinar sua do Azure AD inícios de sessão a cada hora para procurar anomali es. 


## <a name="next-steps"></a>Passos Seguintes
Neste início rápido, aprendeu como começar a utilizar o Azure sentinela. Avance para o tutorial [como detetar ameaças](tutorial-detect-threats.md).
> [!div class="nextstepaction"]
> [Detete ameaças](tutorial-detect-threats.md) para automatizar suas respostas a ameaças.

