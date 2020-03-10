---
title: Soluções de monitorização no Monitor Azure / Microsoft Docs
description: As soluções de monitorização no Monitor Azure são uma coleção de regras de lógica, visualização e aquisição de dados que fornecem métricas focadas numa determinada área problemática.  Este artigo fornece informações sobre a instalação e utilização de soluções de monitorização.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: a04ca3768ade6058c59393591c252bc4347a3663
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360904"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Soluções de monitorização no Monitor Azure
A monitorização de soluções de alavancagem de serviços em Azure para fornecer informações adicionais sobre o funcionamento de uma determinada aplicação ou serviço. Este artigo fornece uma breve visão geral das soluções de monitorização no Azure e detalhes sobre a sua utilização e instalação.

> [!NOTE]
> As soluções de monitorização eram anteriormente referidas como soluções de gestão.

As soluções de monitorização normalmente recolhem dados de registo e fornecem consultas e pontos de vista para analisar os dados recolhidos. Eles também podem tirar partido outros serviços, como a automatização do Azure para efetuar ações relacionadas com a aplicação ou serviço.

Pode adicionar soluções de monitorização ao Azure Monitor para quaisquer aplicações e serviços que utilize. Normalmente estão disponíveis sem custos, mas recolhem dados que podem invocar taxas de utilização. Além das soluções fornecidas pela Microsoft, parceiros e clientes podem [criar soluções](solutions-creating.md) de gestão para serem usadas no seu próprio ambiente ou disponibilizadas aos clientes através da comunidade.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Utilizar soluções de monitorização
Abra a página **de visão geral** no Monitor Azure para exibir um azulejo para cada solução instalada no espaço de trabalho. 

1. Aceda ao [Portal do Azure](https://ms.portal.azure.com). Procure e selecione **Monitor**.
1. No menu **Insights,** selecione **Mais**.
1. Utilize as caixas de lista pendente na parte superior do ecrã para alterar a área de trabalho ou o intervalo de tempo utilizada para os mosaicos.
1. Clique no azulejo para obter uma solução para abrir a sua visão que inclua uma análise mais detalhada dos seus dados recolhidos.

![Descrição geral](media/solutions/overview.png)

As soluções de monitorização podem conter vários tipos de recursos Azure, e você pode ver quaisquer recursos incluídos com uma solução como qualquer outro recurso. Por exemplo, quaisquer consultas de registo incluídas na solução estão listadas no **Solution Queries** in [Query explorer](../log-query/get-started-portal.md#load-queries) Pode utilizar essas consultas ao efetuar análises ad hoc com consultas de [registo](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Lista de soluções de monitorização instaladas 
Utilize o seguinte procedimento para listar as soluções de monitorização instaladas na sua subscrição.

1. Aceda ao [Portal do Azure](https://ms.portal.azure.com). Procure e selecione **Soluções**.
1. Soluções instaladas em todas as áreas de trabalho são listadas. O nome da solução é seguido pelo nome do espaço de trabalho em que está instalado.
1. Utilize as caixas de lista pendente na parte superior do ecrã para filtrar por subscrição ou grupo de recursos.


![Listar todas as soluções](media/solutions/list-solutions-all.png)

Clique no nome de uma solução para abrir a página de resumida. Esta página apresenta todas as vistas incluídas na solução e fornece opções diferentes para a solução em si e a sua área de trabalho. Ver a página de resumo para uma solução utilizando um dos procedimentos acima para soluções de lista e, em seguida, clique no nome da solução.

![Propriedades de solução](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Instalar uma solução de monitorização
As soluções de monitorização da Microsoft e dos parceiros estão disponíveis no [Azure Marketplace.](https://azuremarketplace.microsoft.com) Pode procurar as soluções disponíveis e instalá-los utilizando o procedimento seguinte. Quando instalar uma solução, deve selecionar um espaço de [trabalho de Log Analytics](../platform/manage-access.md) onde a solução será instalada e onde os seus dados serão recolhidos.

1. A partir da [lista de soluções para a sua subscrição,](#list-installed-monitoring-solutions)clique em **Adicionar**.
1. Navegue ou procure uma solução. Também pode navegar em soluções a partir [deste link](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)de pesquisa .
1. Localize a solução de monitorização que deseja e leia através da sua descrição.
1. Clique em **Criar** para iniciar o processo de instalação.
1. Quando o processo de instalação começar, é solicitado que especifique o espaço de trabalho do Log Analytics e forneça qualquer configuração necessária para a solução.

![Instalar uma solução](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Instalar uma solução da Comunidade
Membros da Comunidade podem submeter soluções de gestão para modelos de início rápido do Azure. Pode instalar essas soluções diretamente ou transferi-los modelos para posterior instalação.

1. Siga o processo descrito no espaço de [trabalho log Analytics e na conta de Automação](#log-analytics-workspace-and-automation-account) para ligar um espaço de trabalho e conta.
2. Vá aos [modelos Azure Quickstart](https://azure.microsoft.com/documentation/templates/). 
3. Procurar por uma solução que está interessado.
4. Selecione a solução dos resultados para ver os detalhes.
5. Clique no botão **'Enviar' para Azure.**
6. Lhe for pedido para fornecer informações tais como o grupo de recursos e a localização, além de valores de quaisquer parâmetros na solução.
7. Clique em **Comprar** para instalar a solução.


## <a name="log-analytics-workspace-and-automation-account"></a>Área de trabalho do log Analytics e a conta de automatização
Todas as soluções de monitorização requerem um espaço de [trabalho do Log Analytics](../platform/manage-access.md) para armazenar dados recolhidos pela solução e para alojar as suas pesquisas e vistas de registo. Algumas soluções também requerem uma [conta de Automação](../../automation/automation-security-overview.md#automation-account-overview) para conter livros de execução e recursos conexos. A área de trabalho e a conta tem de cumprir os seguintes requisitos.

* Cada instalação de uma solução só pode utilizar uma área de trabalho do Log Analytics e uma conta de automatização. Pode instalar a solução em separado em várias áreas de trabalho.
* Se uma solução requer uma conta de automatização, em seguida, a área de trabalho do Log Analytics e a conta de automatização devem ser vinculados a um do outro. Uma área de trabalho do Log Analytics só pode ser ligada a uma conta de automatização e uma conta de automatização só pode ser ligada a uma área de trabalho do Log Analytics.
* Para ser ligada a área de trabalho do Log Analytics e a conta de automatização tem de ser no mesmo grupo de recursos e região. A exceção é uma área de trabalho na região E.U.A. leste e conta de automatização na região E.U.A. Leste 2.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Crie uma ligação entre uma área de trabalho do Log Analytics e uma conta de automatização
Como especificar a área de trabalho do Log Analytics e a conta de automatização depende do método de instalação para a sua solução.

* Quando instala uma solução através do Mercado Azure, é solicitado para uma conta de espaço de trabalho e Automação. A ligação entre eles é criada se eles já não estão ligados.
* Para soluções de fora do Azure Marketplace, tem de associar a área de trabalho do Log Analytics e a conta de automatização antes de instalar a solução. Pode fazê-lo ao selecionar qualquer solução no Azure Marketplace e ao selecionar a área de trabalho do Log Analytics e a conta de automatização. Não tem de instalar, na verdade, a solução porque a ligação for criada assim que a área de trabalho do Log Analytics e a conta de automatização estão selecionadas. Depois de criar a ligação, em seguida, pode utilizar essa área de trabalho do Log Analytics e a conta de automatização para qualquer solução.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Certifique-se a ligação entre uma área de trabalho do Log Analytics e uma conta de automatização
Pode verificar a ligação entre uma área de trabalho do Log Analytics e uma conta de automatização utilizando o procedimento seguinte.

1. Selecione a conta de automatização no portal do Azure.
1. Percorra a secção **recursos relacionados** do menu.
1. Se a definição **do Espaço de Trabalho** estiver ativada, esta conta está ligada a um espaço de trabalho do Log Analytics. Pode clicar no **Workspace** para ver os detalhes do espaço de trabalho.

## <a name="remove-a-monitoring-solution"></a>Remover uma solução de monitorização
Para remover uma solução instalada, localize-a na [lista de soluções instaladas](#list-installed-monitoring-solutions). Clique no nome da solução para abrir a sua página sumária e, em seguida, clique em **Delete**.


## <a name="next-steps"></a>Passos Seguintes
* Obtenha uma [lista de soluções de monitorização da Microsoft](solutions-inventory.md).
* Aprenda a [criar consultas](../log-query/log-query-overview.md) para analisar os dados recolhidos através da monitorização de soluções.

