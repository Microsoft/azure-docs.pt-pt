---
title: Soluções de monitorização no Monitor Azure / Microsoft Docs
description: As soluções de monitorização no Monitor Azure são uma coleção de regras de lógica, visualização e aquisição de dados que fornecem métricas focadas numa determinada área problemática.  Este artigo fornece informações sobre a instalação e utilização de soluções de monitorização.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/23/2020
ms.openlocfilehash: 44094497c98c306b0995f3a74da68809ef66a0ac
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682832"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Soluções de monitorização no Monitor Azure

A monitorização de soluções de alavancagem de serviços em Azure para fornecer informações adicionais sobre o funcionamento de uma determinada aplicação ou serviço. Este artigo fornece uma breve visão geral das soluções de monitorização no Azure e detalhes sobre a sua utilização e instalação.

> [!NOTE]
> As soluções de monitorização eram anteriormente referidas como soluções de gestão.

As soluções de monitorização normalmente recolhem dados de registo e fornecem consultas e pontos de vista para analisar os dados recolhidos. Podem ainda aproveitar outros serviços, como a Azure Automation, para realizar ações relacionadas com a aplicação ou serviço.

Pode adicionar soluções de monitorização ao Azure Monitor para quaisquer aplicações e serviços que utilize. Normalmente estão disponíveis sem custos, mas recolhem dados que podem invocar taxas de utilização. Além das soluções fornecidas pela Microsoft, parceiros e clientes podem [criar soluções](solutions-creating.md) de gestão para serem usadas no seu próprio ambiente ou disponibilizadas aos clientes através da comunidade.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Utilizar soluções de monitorização

Abra a página **de visão geral** no Monitor Azure para exibir um azulejo para cada solução instalada no espaço de trabalho. 

1. Vá ao [portal Azure.](https://ms.portal.azure.com) Procure e selecione **Monitor**.
1. No menu **Insights,** selecione **Mais**.
1. Utilize as caixas de dropdown na parte superior do ecrã para alterar o espaço de trabalho ou a faixa de tempo utilizada para os azulejos.
1. Clique no azulejo para obter uma solução para abrir a sua visão que inclua uma análise mais detalhada dos seus dados recolhidos.

![Descrição geral](media/solutions/overview.png)

As soluções de monitorização podem conter vários tipos de recursos Azure, e você pode ver quaisquer recursos incluídos com uma solução como qualquer outro recurso. Por exemplo, quaisquer consultas de registo incluídas na solução estão listadas no **Solution Queries** in [Query explorer](../log-query/get-started-portal.md#load-queries) Pode utilizar essas consultas ao efetuar análises ad hoc com consultas de [registo](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Lista de soluções de monitorização instaladas

Utilize o seguinte procedimento para listar as soluções de monitorização instaladas na sua subscrição.

1. Vá ao [portal Azure.](https://ms.portal.azure.com) Procure e selecione **Soluções**.
1. Estão listadas soluções instaladas em todos os espaços de trabalho. O nome da solução é seguido pelo nome do espaço de trabalho em que está instalado.
1. Utilize as caixas de dropdown na parte superior do ecrã para filtrar por subscrição ou grupo de recursos.


![Enumerar todas as soluções](media/solutions/list-solutions-all.png)

Clique no nome de uma solução para abrir a sua página sumária. Esta página exibe quaisquer pontos de vista incluídos na solução e fornece diferentes opções para a própria solução e o seu espaço de trabalho. Veja a página de resumo para obter uma solução utilizando um dos procedimentos acima para listar soluções e, em seguida, clique no nome da solução.

![Propriedades de solução](media/solutions/solution-properties.png)

## <a name="install-a-monitoring-solution"></a>Instalar uma solução de monitorização

As soluções de monitorização da Microsoft e dos parceiros estão disponíveis no [Azure Marketplace.](https://azuremarketplace.microsoft.com) Pode pesquisar as soluções disponíveis e instalá-las utilizando o seguinte procedimento. Quando instalar uma solução, deve selecionar um espaço de [trabalho de Log Analytics](../platform/manage-access.md) onde a solução será instalada e onde os seus dados serão recolhidos.

1. A partir da [lista de soluções para a sua subscrição,](#list-installed-monitoring-solutions)clique em **Adicionar**.
1. Navegue ou procure uma solução. Também pode navegar em soluções a partir [deste link](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)de pesquisa .
1. Localize a solução de monitorização que deseja e leia através da sua descrição.
1. Clique em **Criar** para iniciar o processo de instalação.
1. Quando o processo de instalação começar, é solicitado que especifique o espaço de trabalho do Log Analytics e forneça qualquer configuração necessária para a solução.

![Instalar uma solução](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Instale uma solução da comunidade

Os membros da comunidade podem submeter soluções de gestão aos Modelos De Arranque Rápido do Azure. Pode instalar estas soluções diretamente ou descarregar os modelos para posterior instalação.

1. Siga o processo descrito no espaço de [trabalho log Analytics e na conta de Automação](#log-analytics-workspace-and-automation-account) para ligar um espaço de trabalho e conta.
2. Vá aos [modelos Azure Quickstart](https://azure.microsoft.com/documentation/templates/). 
3. Procure uma solução que lhe interesse.
4. Selecione a solução a partir dos resultados para ver os seus detalhes.
5. Clique no botão **'Enviar' para Azure.**
6. É-lhe pedido que forneça informações como o grupo de recursos e a localização, além de valores para quaisquer parâmetros na solução.
7. Clique em **Comprar** para instalar a solução.

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics workspace e conta de Automação

Todas as soluções de monitorização requerem um espaço de [trabalho do Log Analytics](../platform/manage-access.md) para armazenar dados recolhidos pela solução e para alojar as suas pesquisas e vistas de registo. Algumas soluções também requerem uma [conta de Automação](../../automation/automation-security-overview.md) para conter livros de execução e recursos conexos. O espaço de trabalho e a conta devem satisfazer os seguintes requisitos.

* Cada instalação de uma solução só pode utilizar um espaço de trabalho Log Analytics e uma conta Automation. Pode instalar a solução separadamente em vários espaços de trabalho.
* Se uma solução requer uma conta de Automação, então o espaço de trabalho log Analytics e a conta de Automação devem estar ligados uns aos outros. Um espaço de trabalho de Log Analytics só pode estar ligado a uma conta de Automação, e uma conta de Automação só pode estar ligada a um espaço de trabalho de Log Analytics.
* Para ser ligado, o espaço de trabalho log Analytics e a conta de Automação devem estar na mesma subscrição, mas podem estar em diferentes grupos de recursos implantados na mesma região. A exceção é um espaço de trabalho na região leste dos EUA e conta de Automação no Leste dos EUA 2.

Quando instala uma solução através do Mercado Azure, é solicitado para uma conta de espaço de trabalho e Automação. A ligação entre eles é criada se ainda não estiverem ligadas.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Verifique a ligação entre um espaço de trabalho log Analytics e a conta de Automação

Pode verificar a ligação entre um espaço de trabalho de Log Analytics e uma conta de Automação utilizando o seguinte procedimento.

1. Selecione a conta Automation no portal Azure.
1. Percorra a secção **recursos relacionados** do menu e selecione **Linked workspace**.
1. Se o **Workspace** estiver ligado a uma conta De automatização, então esta página lista o espaço de trabalho a que está ligado. Se selecionar o nome do espaço de trabalho listado, é redirecionado para a página de visão geral para esse espaço de trabalho.

## <a name="remove-a-monitoring-solution"></a>Remover uma solução de monitorização

Para remover uma solução instalada, localize-a na [lista de soluções instaladas](#list-installed-monitoring-solutions). Clique no nome da solução para abrir a sua página sumária e, em seguida, clique em **Delete**.

## <a name="next-steps"></a>Passos seguintes

* Obtenha uma [lista de soluções de monitorização da Microsoft](solutions-inventory.md).
* Aprenda a [criar consultas](../log-query/log-query-overview.md) para analisar os dados recolhidos através da monitorização de soluções.