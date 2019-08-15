---
title: Soluções de monitoramento no Azure Monitor | Microsoft Docs
description: As soluções de monitoramento no Azure Monitor são uma coleção de lógica, visualização e regras de aquisição de dados que fornecem métricas dinamizadas em uma área de problema específica.  Este artigo fornece informações sobre como instalar e usar soluções de monitoramento.
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
ms.date: 08/13/2019
ms.author: bwren
ms.openlocfilehash: 6932f9f302bf1816842d78a662f01ebaafed8686
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989216"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Soluções de monitoramento no Azure Monitor
As soluções de monitoramento aproveitam os serviços no Azure para fornecer informações adicionais sobre a operação de um determinado aplicativo ou serviço. Este artigo fornece uma breve visão geral das soluções de monitoramento no Azure e detalhes sobre como usá-las e instalá-las.

> [!NOTE]
> As soluções de monitoramento eram anteriormente chamadas de soluções de gerenciamento.

Soluções de monitoramento normalmente coletam dados de log e fornecem consultas e exibições para analisar os dados coletados. Eles também podem tirar partido outros serviços, como a automatização do Azure para efetuar ações relacionadas com a aplicação ou serviço.

Você pode adicionar soluções de monitoramento para Azure Monitor para quaisquer aplicativos e serviços que você usar. Eles estão normalmente disponíveis em nenhum dado de custo mas recolher invoca os custos de utilização. Além das soluções fornecidas pela Microsoft, parceiros e clientes podem [criar soluções de gestão](solutions-creating.md) a ser utilizada no seu próprio ambiente ou disponibilizados aos clientes através da Comunidade.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Utilizar soluções de monitorização
Abra a página **visão geral** no Azure monitor para exibir um bloco para cada solução instalada no espaço de trabalho. 

1. Inicie sessão no portal do Azure.
1. Open **todos os serviços** e localize **Monitor**.
1. Sob o **Insights** menu, selecione **mais**.
1. Utilize as caixas de lista pendente na parte superior do ecrã para alterar a área de trabalho ou o intervalo de tempo utilizada para os mosaicos.
1. Clique no mosaico de uma solução abrir a vista que inclui uma análise mais detalhada em seus dados recolhidos.

![Descrição geral](media/solutions/overview.png)

As soluções de monitoramento podem conter vários tipos de recursos do Azure e você pode exibir todos os recursos incluídos em uma solução, assim como qualquer outro recurso. Por exemplo, todas as consultas de log incluídas na solução são listadas em **consultas de solução** no [Gerenciador de consultas](../log-query/get-started-portal.md#load-queries) . você pode usar essas consultas ao executar análises ad hoc com consultas de [log](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Listar soluções de monitoramento instaladas 
Use o procedimento a seguir para listar as soluções de monitoramento instaladas em sua assinatura.

1. Inicie sessão no portal do Azure.
1. Open **todos os serviços** e localize **soluções**.
4. Soluções instaladas em todas as áreas de trabalho são listadas. O nome da solução é seguido pelo nome do espaço de trabalho em que ele está instalado.
1. Utilize as caixas de lista pendente na parte superior do ecrã para filtrar por subscrição ou grupo de recursos.


![Listar todas as soluções](media/solutions/list-solutions-all.png)

Clique no nome de uma solução para abrir a página de resumida. Esta página apresenta todas as vistas incluídas na solução e fornece opções diferentes para a solução em si e a sua área de trabalho. Ver a página de resumo para uma solução utilizando um dos procedimentos acima para soluções de lista e, em seguida, clique no nome da solução.

![Propriedades de solução](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Instalar uma solução de monitoramento
As soluções de monitoramento da Microsoft e dos parceiros estão disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com). Pode procurar as soluções disponíveis e instalá-los utilizando o procedimento seguinte. Quando instala uma solução, tem de selecionar uma [área de trabalho do Log Analytics](../platform/manage-access.md) onde a solução será instalada e onde serão recolhidos os seus dados.

1. Partir do [lista de soluções para a sua subscrição](#list-installed-monitoring-solutions), clique em **Add**.
1. Procure uma solução ou pesquise por ela. Você também pode procurar soluções [neste link de pesquisa](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Localize a solução de monitoramento que você deseja e leia sua descrição.
1. Clique em **criar** para iniciar o processo de instalação.
1. Quando o processo de instalação for iniciado, você será solicitado a especificar o espaço de trabalho Log Analytics e fornecer qualquer configuração necessária para a solução.

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
Todas as soluções de monitoramento exigem um [espaço de trabalho log Analytics](../platform/manage-access.md) para armazenar dados coletados pela solução e hospedar suas pesquisas e exibições de logs. Algumas soluções também exigem um [conta de automatização](../../automation/automation-security-overview.md#automation-account-overview) para conter runbooks e recursos relacionados. A área de trabalho e a conta tem de cumprir os seguintes requisitos.

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

## <a name="remove-a-monitoring-solution"></a>Remover uma solução de monitoramento
Para remover uma solução de instalados, localizá-la na [lista de soluções instaladas](#list-installed-monitoring-solutions). Clique no nome da solução para abrir a página de resumida e, em seguida, clique em **eliminar**.


## <a name="next-steps"></a>Passos Seguintes
* Obtenha uma [lista de soluções de monitoramento da Microsoft](solutions-inventory.md).
* Saiba como [criar consultas](../log-query/log-query-overview.md) para analisar os dados coletados por soluções de monitoramento.

