---
title: Guia de início rápido do Sentinela do Azure-introdução à visualização do Azure Sentinel | Microsoft Docs
description: Guia de início rápido do Sentinela do Azure-introdução ao Azure Sentinel
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5a4ae93c-d648-41fb-8fb8-96a025d2f73e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: c6434b4f5808c349d5e0ab04dafc5233f6ec63ac
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780475"
---
# <a name="quickstart-get-started-with-azure-sentinel-preview"></a>Início rápido: Introdução à visualização do Azure Sentinel

> [!IMPORTANT]
> O Azure Sentinel está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Neste início rápido, você aprenderá a exibir e monitorar rapidamente o que está acontecendo em seu ambiente usando o Azure Sentinel. Depois de conectar suas fontes de dados ao Azure Sentinel, você obtém visualização instantânea e análise de dados para que possa saber o que está acontecendo em todas as suas fontes de dados conectadas. O Azure Sentinel oferece painéis que fornecem todo o poder das ferramentas já disponíveis no Azure, bem como tabelas e gráficos que são criados para fornecer análises para seus logs e consultas. Você pode usar painéis internos ou criar um novo painel facilmente, do zero ou com base em um painel existente. 

## <a name="get-visualization"></a>Obter visualização

Para visualizar e obter a análise do que está acontecendo em seu ambiente, primeiro, dê uma olhada no painel Visão geral para ter uma ideia da postura de segurança da sua organização. Você pode clicar em cada elemento desses blocos para fazer uma busca detalhada nos dados brutos dos quais eles são criados. Para ajudá-lo a reduzir o ruído e minimizar o número de alertas que você precisa examinar e investigar, o Azure Sentinel usa uma técnica de fusão para correlacionar alertas em incidentes. **incidentes** são grupos de alertas relacionados que criam, juntos, um incidente acionável que você pode investigar e resolver.

- Na portal do Azure, selecione Azure Sentinel e, em seguida, selecione o espaço de trabalho que você deseja monitorar.

  ![Visão geral do Sentinela do Azure](./media/qs-get-visibility/overview.png)

- A barra de ferramentas na parte superior informa quantos eventos você obteve ao longo do período de tempo selecionado e os compara com as 24 horas anteriores. A barra de ferramentas informa a você sobre esses eventos, os alertas que foram disparados (o pequeno número representa alterações nas últimas 24 horas) e, em seguida, ele informa a você os eventos, quantas estão abertas, em andamento e fechadas. Verifique se não há um aumento drástico ou queda no número de eventos. Se houver uma queda, pode ser que uma conexão pare de relatar para o Azure Sentinel. Se houver um aumento, algo suspeito poderá ter ocorrido. Verifique se você tem novos alertas.

   ![Funil do Azure Sentinel](./media/qs-get-visibility/funnel.png)

O corpo principal da página de visão geral fornece uma visão geral do status de segurança do seu espaço de trabalho:

- **Eventos e alertas ao longo do tempo**: Lista o número de eventos e quantos alertas foram criados a partir desses eventos. Se você vir um pico incomum, verá alertas para ele – se houver algo incomum em que há um pico nos eventos, mas você não vir os alertas, isso poderá causar uma preocupação.

- **Eventos mal-intencionados potenciais**: Quando o tráfego é detectado de fontes que são conhecidas como mal-intencionados, o Azure Sentinel alerta você sobre o mapa. Se você vir laranja, é o tráfego de entrada: alguém está tentando acessar sua organização a partir de um endereço IP mal-intencionado conhecido. Se você vir atividade de saída (vermelho), significa que os dados de sua rede estão sendo transmitidos da sua organização para um endereço IP mal-intencionado conhecido.

   ![Mapa do Azure Sentinel](./media/qs-get-visibility/map.png)


- **Incidentes recentes**: Para exibir seus incidentes recentes, sua gravidade e o número de alertas associados ao incidente. Se você vir como um pico repentino em um tipo específico de alerta, isso pode significar que há um ataque ativo em execução no momento. Por exemplo, se você tiver um pico repentino de 20 eventos Pass-the-hash do Azure ATP, é possível que alguém esteja atualmente tentando atacar você.

- **Anomalias da fonte de dados**: Os analistas de dados da Microsoft criaram modelos que pesquisam constantemente os dados de suas fontes de dados em busca de anomalias. Se não houver anomalias, nada será exibido. Se forem detectadas anomalias, você deve aprofundar-se nelas para ver o que aconteceu. Por exemplo, clique no pico na atividade do Azure. Você pode clicar no **gráfico** para ver quando o pico aconteceu e, em seguida, filtrar as atividades que ocorreram durante esse período de tempo para ver o que causou o pico.

   ![Mapa do Azure Sentinel](./media/qs-get-visibility/anomolies.png)

## Usar painéis internos<a name="dashboards"></a>

Os painéis internos fornecem dados integrados de suas fontes de dados conectadas para permitir que você aprofunde os eventos gerados nesses serviços. Os painéis internos incluem ID do Azure, eventos de atividade do Azure e locais, que podem ser dados de eventos do Windows de servidores, de alertas de primeira parte, de terceiros, incluindo logs de tráfego de firewall, Office 365 e protocolos inseguros baseados no Windows LostFocus.

1. Em **configurações**, selecione **painéis**. Em **instalado**, você pode ver todos os painéis instalados. Em **tudo** , você pode ver toda a Galeria de painéis internos que estão disponíveis para instalação. 
2. Procure um painel específico para ver a lista e a descrição completas do que cada uma oferece. 
3. Supondo que você use o Azure AD para colocar em funcionamento o Azure Sentinel, recomendamos que você instale pelo menos os seguintes painéis:
   - **Azure ad**: Use um ou ambos os itens a seguir:
       - As **entradas do Azure ad** analisam as entradas ao longo do tempo para ver se há anomalias. Esse painel fornece entradas com falha por aplicativos, dispositivos e locais para que você possa observar rapidamente se algo incomum acontece. Preste atenção em várias entradas com falha. 
       - **Os logs de auditoria do Azure ad** analisam atividades administrativas, como alterações em usuários (adicionar, remover, etc.), criação de grupo e modificações.  

   - Adicione um painel para o firewall. Por exemplo, adicione o painel Palo Alto. O painel analisa o tráfego do firewall, fornecendo correlações entre os dados do firewall e os eventos de ameaça e realça eventos suspeitos entre entidades. Os painéis fornecem informações sobre tendências em seu tráfego e permitem que você faça uma busca detalhada e filtre os resultados. 

      ![Painel PAL alto](./media/qs-get-visibility/palo-alto-week-query.png)


Você pode personalizar os painéis editando o botão ![](./media/qs-get-visibility/edit-query-button.png)de consulta principal. Você pode clicar no botão ![](./media/qs-get-visibility/go-to-la-button.png) de botão para ir para [log Analytics para editar a consulta lá](../azure-monitor/log-query/get-started-portal.md)e pode selecionar as reticências (...) e selecionar **personalizar dados de bloco**, o que permite editar o filtro de tempo principal ou remover o blocos específicos do painel.

Para obter mais informações sobre como trabalhar com consultas [, consulte Tutorial: Dados visuais no Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Adicionar um novo bloco

Se você quiser adicionar um novo bloco, poderá adicioná-lo a um painel existente, um que você criar ou um painel interno do Azure Sentinel. 
1. Em log Analytics, crie um bloco usando as instruções encontradas no [tutorial: Dados visuais em Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Depois que o bloco for criado, em **PIN**, selecione o painel no qual você deseja que o bloco apareça.

## <a name="create-new-dashboards"></a>Criar novos painéis
Você pode criar um novo painel do zero ou usar um painel interno como base para o novo painel.

1. Para criar um novo painel do zero, selecione **painéis** e **+ novo painel**.
2. Selecione a assinatura na qual o painel é criado e dê a ele um nome descritivo. Cada painel é um recurso do Azure como qualquer outro, e você pode atribuir funções de ti (RBAC) para definir e limitar quem pode acessar. 
3. Para permitir que ele apareça em seus painéis para fixar visualizações no, você precisa compartilhá-lo. Clique em **compartilhar** e em **gerenciar usuários**. 
 
1. Use o **acesso de verificação** e as atribuições de **função** como você faria para qualquer outro recurso do Azure. Para obter mais informações, consulte [compartilhar painéis do Azure usando o RBAC](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-dashboard-examples"></a>Novos exemplos de painel

A consulta de exemplo a seguir permite que você compare as tendências de tráfego em semanas. Você pode facilmente alternar qual fornecedor de dispositivo e fonte de dados em que você executa a consulta. Este exemplo usa SecurityEvent do Windows, você pode alternar para executar em AzureActivity ou CommonSecurityLog em qualquer outro firewall.

     |where DeviceVendor = = "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Talvez você queira criar uma consulta que incorpore dados de várias fontes. Você pode criar uma consulta que examina Azure Active Directory logs de auditoria para novos usuários que acabaram de ser criados e, em seguida, verifica os logs do Azure para ver se o usuário começou a fazer alterações de atribuição de função dentro de 24 horas após a criação. Essa atividade suspeita apareceria neste painel:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

Você pode criar painéis diferentes com base na função de pessoa que examina os dados e o que eles estão procurando. Por exemplo, você pode criar um painel para o administrador de rede que inclui os dados do firewall. Você também pode criar painéis com base na frequência com que deseja examiná-los, se há coisas que deseja revisar diariamente e outros itens que deseja verificar uma vez por hora, por exemplo, talvez você queira examinar suas entradas do Azure AD a cada hora para pesquisar anomalias espanhol. 

## <a name="create-new-detections"></a>Criar novas detecções

Gere detecções nas fontes de [dados que você conectou ao Azure Sentinel](connect-data-sources.md) para investigar ameaças em sua organização.

Ao criar uma nova detecção, aproveite as detecções internas criadas pelos pesquisadores de segurança da Microsoft que são adaptadas às fontes de dados conectadas.

1. [Na Comunidade do GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Detections) , vá para a pasta **detecções** e selecione as pastas relevantes.
   ![pastas relevantes](./media/qs-get-visibility/detection-folders.png)
 
3.  Vá para a guia **análise** e selecione **Adicionar**.
   ![Criar regra no Log Analytics](./media/qs-get-visibility/query-params.png)

3.  Copie todos os parâmetros para a regra e clique em **criar**.
   ![Criar regra de alerta](./media/qs-get-visibility/create-alert-rule.png)

 
## <a name="next-steps"></a>Passos seguintes
Neste guia de início rápido, você aprendeu a começar a usar o Azure Sentinel. Continue no tutorial sobre [como detectar ameaças](tutorial-detect-threats.md).
> [!div class="nextstepaction"]
> [Detecte ameaças](tutorial-detect-threats.md) para automatizar suas respostas a ameaças.

