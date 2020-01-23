---
title: 'Início rápido: introdução ao Azure Sentinel'
description: Guia de início rápido do Sentinela do Azure-introdução ao Azure Sentinel
services: sentinel
author: rkarlin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 35ecb5c4e3987676d235fc6fd09f58c046301cda
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548038"
---
# <a name="quickstart-get-started-with-azure-sentinel"></a>Início rápido: introdução ao Azure Sentinel




Neste guia de início rápido, você aprenderá a exibir e monitorar rapidamente o que está acontecendo em seu ambiente usando o Azure Sentinel. Depois de conectar suas fontes de dados ao Azure Sentinel, você obtém visualização instantânea e análise de dados para que possa saber o que está acontecendo em todas as suas fontes de dados conectadas. O Azure Sentinel fornece pastas de trabalho que fornecem todo o poder das ferramentas já disponíveis no Azure, bem como tabelas e gráficos internos para fornecer análises para seus logs e consultas. Você pode usar pastas de trabalho internas ou criar uma nova pasta de trabalho facilmente, do zero ou com base em uma pasta de trabalho existente. 

## <a name="get-visualization"></a>Obter visualização

Para visualizar e obter a análise do que está acontecendo em seu ambiente, primeiro, dê uma olhada no painel Visão geral para ter uma ideia da postura de segurança da sua organização. Você pode clicar em cada elemento desses blocos para fazer uma busca detalhada nos dados brutos dos quais eles são criados. Para ajudá-lo a reduzir o ruído e minimizar o número de alertas que você precisa examinar e investigar, o Azure Sentinel usa uma técnica de fusão para correlacionar alertas em incidentes. **incidentes** são grupos de alertas relacionados que criam, juntos, um incidente acionável que você pode investigar e resolver.

- Na portal do Azure, selecione Azure Sentinel e, em seguida, selecione o espaço de trabalho que você deseja monitorar.

  ![Visão geral do Sentinela do Azure](./media/qs-get-visibility/overview.png)

- A barra de ferramentas na parte superior informa quantos eventos você obteve ao longo do período de tempo selecionado e os compara com as 24 horas anteriores. A barra de ferramentas informa a você sobre esses eventos, os alertas que foram disparados (o pequeno número representa alterações nas últimas 24 horas) e, em seguida, ele informa a você os eventos, quantas estão abertas, em andamento e fechadas. Verifique se não há um aumento drástico ou queda no número de eventos. Se houver uma queda, pode ser que uma conexão pare de relatar para o Azure Sentinel. Se houver um aumento, algo suspeito poderá ter ocorrido. Verifique se você tem novos alertas.

   ![Funil do Azure Sentinel](./media/qs-get-visibility/funnel.png)

O corpo principal da página de visão geral fornece uma visão geral do status de segurança do seu espaço de trabalho:

- **Eventos e alertas ao longo do tempo**: lista o número de eventos e quantos alertas foram criados a partir desses eventos. Se você vir um pico incomum, verá alertas para ele – se houver algo incomum em que há um pico nos eventos, mas você não vir os alertas, isso poderá causar uma preocupação.

- **Eventos mal-intencionados potenciais**: quando o tráfego é detectado de fontes que são conhecidas como mal-intencionadas, o Azure Sentinel alerta você sobre o mapa. Se você vir laranja, é o tráfego de entrada: alguém está tentando acessar sua organização a partir de um endereço IP mal-intencionado conhecido. Se você vir atividade de saída (vermelho), significa que os dados de sua rede estão sendo transmitidos da sua organização para um endereço IP mal-intencionado conhecido.

   ![Mapa do Azure Sentinel](./media/qs-get-visibility/map.png)


- **Incidentes recentes**: para exibir seus incidentes recentes, sua gravidade e o número de alertas associados ao incidente. Se você vir como um pico repentino em um tipo específico de alerta, isso pode significar que há um ataque ativo em execução no momento. Por exemplo, se você tiver um pico repentino de 20 eventos Pass-the-hash do Azure ATP, é possível que alguém esteja atualmente tentando atacar você.

- **Anomalias de fonte de dados**: os analistas de dados da Microsoft criaram modelos que pesquisam constantemente os dados de suas fontes de dados em busca de anomalias. Se não houver anomalias, nada será exibido. Se forem detectadas anomalias, você deve aprofundar-se nelas para ver o que aconteceu. Por exemplo, clique no pico na atividade do Azure. Você pode clicar no **gráfico** para ver quando o pico aconteceu e, em seguida, filtrar as atividades que ocorreram durante esse período de tempo para ver o que causou o pico.

   ![Mapa do Azure Sentinel](./media/qs-get-visibility/anomolies.png)

## Usar pastas de trabalho internas<a name="dashboards"></a>

As pastas de trabalho internas fornecem dados integrados de suas fontes de dados conectadas para permitir que você Aprofunde-se nos eventos gerados nesses serviços. As pastas de trabalho internas incluem ID do Azure, eventos de atividade do Azure e locais, que podem ser dados de eventos do Windows de servidores, de alertas de primeira parte, de qualquer terceiros, incluindo logs de tráfego de firewall, Office 365 e protocolos inseguros com base no Windows LostFocus. As pastas de trabalho do são baseadas em pastas de trabalho Azure Monitor para fornecer a você maior personalização e flexibilidade na criação de sua própria pasta de trabalho. Para obter mais informações, consulte [pastas de trabalho](../azure-monitor/app/usage-workbooks.md).

1. Em **configurações**, selecione **pastas de trabalho**. Em **instalado**, você pode ver todas as pastas de trabalho instaladas. Em **todos**, você pode ver toda a Galeria de pastas de trabalho internas que estão disponíveis para instalação. 
2. Procure uma pasta de trabalho específica para ver a lista e a descrição completas do que cada uma oferece. 
3. Supondo que você use o Azure AD para colocar em funcionamento o Azure Sentinel, recomendamos que você instale pelo menos as seguintes pastas de trabalho:
   - **Azure ad**: Use um dos seguintes ou ambos:
       - As **entradas do Azure ad** analisam as entradas ao longo do tempo para ver se há anomalias. Essas pastas de trabalho fornecem entradas com falha por aplicativos, dispositivos e locais para que você possa observar rapidamente se algo incomum acontece. Preste atenção em várias entradas com falha. 
       - **Os logs de auditoria do Azure ad** analisam atividades administrativas, como alterações em usuários (adicionar, remover, etc.), criação de grupo e modificações.  

   - Adicione uma pasta de trabalho para o firewall. Por exemplo, adicione a pasta de trabalho Palo Alto. A pasta de trabalho analisa o tráfego do firewall, fornecendo correlações entre os dados do firewall e os eventos de ameaça e realça eventos suspeitos entre entidades. As pastas de trabalho fornecem informações sobre tendências em seu tráfego e permitem que você faça uma busca detalhada e filtre os resultados. 

      ![Painel PAL alto](./media/qs-get-visibility/palo-alto-week-query.png)


Você pode personalizar as pastas de trabalho editando a consulta principal ![botão](./media/qs-get-visibility/edit-query-button.png). Você pode clicar no botão ![botão](./media/qs-get-visibility/go-to-la-button.png) para acessar [log Analytics para editar a consulta lá](../azure-monitor/log-query/get-started-portal.md)e pode selecionar as reticências (...) e selecionar **personalizar dados de bloco**, o que permite editar o filtro de tempo principal ou remover os blocos específicos da pasta de trabalho.

Para obter mais informações sobre como trabalhar com consultas, consulte [tutorial: dados visuais no log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Adicionar um novo bloco

Se você quiser adicionar um novo bloco, poderá adicioná-lo a uma pasta de trabalho existente, ou seja, um que você criar ou uma pasta de trabalho interna do Azure Sentinel. 
1. Em Log Analytics, crie um bloco usando as instruções encontradas em [tutorial: dados visuais em log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Depois que o bloco for criado, em **PIN**, selecione a pasta de trabalho na qual você deseja que o bloco apareça.

## <a name="create-new-workbooks"></a>Criar novas pastas de trabalho
Você pode criar uma nova pasta de trabalho do zero ou usar uma pasta de trabalho interna como base para sua nova pasta de trabalho.

1. Para criar uma nova pasta de trabalho do zero, selecione **pastas de trabalho** e **+ nova pasta de trabalho**.
2. Selecione a assinatura na qual a pasta de trabalho é criada e dê a ela um nome descritivo. Cada pasta de trabalho é um recurso do Azure como qualquer outro, e você pode atribuir funções de ti (RBAC) para definir e limitar quem pode acessar. 
3. Para permitir que ele apareça em suas pastas de trabalho para fixar visualizações no, você precisa compartilhá-lo. Clique em **compartilhar** e em **gerenciar usuários**. 
 
1. Use o **acesso de verificação** e as **atribuições de função** como você faria para qualquer outro recurso do Azure. Para obter mais informações, consulte [compartilhar pastas de trabalho do Azure usando o RBAC](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-workbook-examples"></a>Novos exemplos de pasta de trabalho

A consulta de exemplo a seguir permite que você compare as tendências de tráfego em semanas. Você pode facilmente alternar qual fornecedor de dispositivo e fonte de dados em que você executa a consulta. Este exemplo usa SecurityEvent do Windows, você pode alternar para executar em AzureActivity ou CommonSecurityLog em qualquer outro firewall.

     |where DeviceVendor == "Palo Alto Networks":
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

Você pode criar pastas de trabalho diferentes com base na função de pessoa que examina os dados e o que eles estão procurando. Por exemplo, você pode criar uma pasta de trabalho para o administrador de rede que inclui os dados do firewall. Você também pode criar pastas de trabalho com base na frequência com que deseja examiná-las, se há coisas que deseja examinar diariamente e outros itens que deseja verificar uma vez por hora, por exemplo, talvez você queira examinar suas entradas do Azure AD a cada hora para pesquisar anomalias &. 

## <a name="create-new-detections"></a>Criar novas detecções

Gere detecções nas fontes de [dados que você conectou ao Azure Sentinel](connect-data-sources.md) para investigar ameaças em sua organização.

Ao criar uma nova detecção, aproveite as detecções internas criadas pelos pesquisadores de segurança da Microsoft que são adaptadas às fontes de dados conectadas.

Para exibir todas as detecções prontas para uso, vá para **análise** e, em seguida, modelos de **regra**. Esta guia contém todas as regras internas do Azure Sentinel.

   ![Use detecções internas para encontrar ameaças com o Azure Sentinel](media/tutorial-detect-built-in/view-oob-detections.png)

Para obter mais informações sobre como obter as detecções prontas para uso, consulte [tutorial: obter a análise interna](tutorial-detect-threats-built-in.md).
 
## <a name="next-steps"></a>Passos seguintes
Neste guia de início rápido, você aprendeu a começar a usar o Azure Sentinel. Continue no tutorial sobre [como detectar ameaças](tutorial-detect-threats-built-in.md).
> [!div class="nextstepaction"]
> [Crie regras de detecção de ameaças personalizadas](tutorial-detect-threats-custom.md) para automatizar suas respostas a ameaças.

