---
title: Monitorizar soluções no Azure Monitor | Documentos da Microsoft
description: Soluções de monitorização no Azure Monitor são um conjunto de dados, visualização e lógica de regras de aquisição que fornecem métricas articuladas em torno de uma área de problema específico.  Este artigo fornece informações sobre como instalar e utilizar soluções de monitorização.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/07/2018
ms.author: bwren
ms.openlocfilehash: b66d9cf15aaeaca975b60f24601b8ad7f555f458
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887561"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Soluções de monitorização no Azure Monitor
Soluções de monitorização tirar partido dos serviços do Azure para fornecer informações adicionais sobre o funcionamento de uma determinada aplicação ou serviço. Este artigo fornece uma breve descrição geral do monitoramento de soluções no Azure e detalhes sobre como utilizar e instalá-los.

> [!NOTE]
> Soluções de monitorização anteriormente eram referidas como soluções de gestão.

Normalmente, soluções de monitorização recolhem dados de registo e fornecem consultas e vistas para analisar os dados recolhidos. Eles também podem tirar partido outros serviços, como a automatização do Azure para efetuar ações relacionadas com a aplicação ou serviço.

Pode adicionar soluções de monitorização para o Azure Monitor para quaisquer aplicações e serviços que utilizar. Eles estão normalmente disponíveis em nenhum dado de custo mas recolher invoca os custos de utilização. Além das soluções fornecidas pela Microsoft, parceiros e clientes podem [criar soluções de gestão](solutions-creating.md) a ser utilizada no seu próprio ambiente ou disponibilizados aos clientes através da Comunidade.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Utilizar soluções de monitorização
Abra o **descrição geral** página no Azure Monitor para apresentar um mosaico para cada solução instalado na área de trabalho. 

1. Inicie sessão no portal do Azure.
1. Open **todos os serviços** e localize **Monitor**.
1. Sob o **Insights** menu, selecione **mais**.
1. Utilize as caixas de lista pendente na parte superior do ecrã para alterar a área de trabalho ou o intervalo de tempo utilizada para os mosaicos.
1. Clique no mosaico de uma solução abrir a vista que inclui uma análise mais detalhada em seus dados recolhidos.

![Descrição geral](media/solutions/overview.png)

Soluções de monitorização podem conter vários tipos de recursos do Azure e pode ver todos os recursos incluídos com uma solução, assim como qualquer outro recurso. Por exemplo, quaisquer consultas de log incluídas na solução estão listadas na **consultas de solução** na [Explorador de consultas](../log-query/get-started-portal.md#load-queries) pode usar essas consultas ao executar a análise ad hoc com [consultas de registo ](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Lista instalado soluções de monitorização 
Utilize o procedimento seguinte para listar as soluções de monitorização instaladas na sua subscrição.

1. Inicie sessão no portal do Azure.
1. Open **todos os serviços** e localize **soluções**.
4. Soluções instaladas em todas as áreas de trabalho são listadas. O nome da solução é seguido do nome da área de trabalho que está instalado na.
1. Utilize as caixas de lista pendente na parte superior do ecrã para filtrar por subscrição ou grupo de recursos.


![Listar todas as soluções](media/solutions/list-solutions-all.png)

Clique no nome de uma solução para abrir a página de resumida. Esta página apresenta todas as vistas incluídas na solução e fornece opções diferentes para a solução em si e a sua área de trabalho. Ver a página de resumo para uma solução utilizando um dos procedimentos acima para soluções de lista e, em seguida, clique no nome da solução.

![Propriedades de solução](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Instalar uma solução de monitorização
Soluções de monitorização da Microsoft e parceiros estão disponíveis a partir da [do Azure Marketplace](https://azuremarketplace.microsoft.com). Pode procurar as soluções disponíveis e instalá-los utilizando o procedimento seguinte. Quando instala uma solução, tem de selecionar uma [área de trabalho do Log Analytics](../platform/manage-access.md) onde a solução será instalada e onde serão recolhidos os seus dados.

1. Partir do [lista de soluções para a sua subscrição](#list-installed-monitoring-solutions), clique em **Add**. 
1. À direita da **soluções de gestão**, clique em **mais**. 
1. Localize a solução de monitorização que pretende e ler sua descrição.
1. Clique em **criar** para iniciar o processo de instalação.
1. Quando o processo de instalação é iniciado, lhe for pedido para fornecer configuração necessárias que varia para cada solução.

![Instalar uma solução](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Instalar uma solução da Comunidade
Membros da Comunidade podem submeter soluções de gestão para modelos de início rápido do Azure. Pode instalar essas soluções diretamente ou transferi-los modelos para posterior instalação.

1. Siga o processo descrito em [área de trabalho do Log Analytics e a conta de automatização](#log-analytics-workspace-and-automation-account) para ligar uma área de trabalho e a conta.
2. Aceda a [modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/). 
3. Procurar por uma solução que está interessado.
4. Selecione a solução dos resultados para ver os detalhes.
5. Clique nas **implementar no Azure** botão.
6. Lhe for pedido para fornecer informações tais como o grupo de recursos e a localização, além de valores de quaisquer parâmetros na solução.
7. Clique em **Compra** para instalar a solução.


## <a name="log-analytics-workspace-and-automation-account"></a>Área de trabalho do log Analytics e a conta de automatização
Todas as soluções de monitorização exigem uma [área de trabalho do Log Analytics](../platform/manage-access.md) para armazenar os dados coletados pela solução e para alojar as suas vistas e pesquisas de registos. Algumas soluções também exigem um [conta de automatização](../../automation/automation-security-overview.md#automation-account-overview) para conter runbooks e recursos relacionados. A área de trabalho e a conta tem de cumprir os seguintes requisitos.

* Cada instalação de uma solução só pode utilizar uma área de trabalho do Log Analytics e uma conta de automatização. Pode instalar a solução em separado em várias áreas de trabalho.
* Se uma solução requer uma conta de automatização, em seguida, a área de trabalho do Log Analytics e a conta de automatização devem ser vinculados a um do outro. Uma área de trabalho do Log Analytics só pode ser ligada a uma conta de automatização e uma conta de automatização só pode ser ligada a uma área de trabalho do Log Analytics.
* Para ser ligada a área de trabalho do Log Analytics e a conta de automatização tem de ser no mesmo grupo de recursos e região. A exceção é uma área de trabalho na região E.U.A. leste e conta de automatização na região E.U.A. Leste 2.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Crie uma ligação entre uma área de trabalho do Log Analytics e uma conta de automatização
Como especificar a área de trabalho do Log Analytics e a conta de automatização depende do método de instalação para a sua solução.

* Quando instala uma solução no Azure Marketplace, lhe for pedido para a área de trabalho e conta de automatização. A ligação entre eles é criada se eles já não estão ligados.
* Para soluções de fora do Azure Marketplace, tem de associar a área de trabalho do Log Analytics e a conta de automatização antes de instalar a solução. Pode fazê-lo ao selecionar qualquer solução no Azure Marketplace e ao selecionar a área de trabalho do Log Analytics e a conta de automatização. Não tem de instalar, na verdade, a solução porque a ligação for criada assim que a área de trabalho do Log Analytics e a conta de automatização estão selecionadas. Depois de criar a ligação, em seguida, pode utilizar essa área de trabalho do Log Analytics e a conta de automatização para qualquer solução.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Certifique-se a ligação entre uma área de trabalho do Log Analytics e uma conta de automatização
Pode verificar a ligação entre uma área de trabalho do Log Analytics e uma conta de automatização utilizando o procedimento seguinte.

1. Selecione a conta de automatização no portal do Azure.
1. Desloque-se para o **recursos relacionados** secção do menu.
1. Se o **área de trabalho** está habilitada, em seguida, esta conta está ligada a uma área de trabalho do Log Analytics. Pode clicar em **área de trabalho** para ver os detalhes da área de trabalho.

## <a name="remove-a-monitoring-solution"></a>Remover uma solução de monitorização
Para remover uma solução de instalados, localizá-la na [lista de soluções instaladas](#list-installed-monitoring-solutions). Clique no nome da solução para abrir a página de resumida e, em seguida, clique em **eliminar**.


## <a name="next-steps"></a>Passos Seguintes
* Obter um [lista de soluções da Microsoft de monitorização](solutions-inventory.md).
* Saiba como [criar consultas](../log-query/log-query-overview.md) para analisar os dados recolhidos por soluções de monitorização.

