---
title: Soluções de monitoramento no Azure Monitor | Microsoft Docs
description: As soluções de monitoramento no Azure Monitor são uma coleção de lógica, visualização e regras de aquisição de dados que fornecem métricas dinamizadas em uma área de problema específica.  Este artigo fornece informações sobre como instalar e usar soluções de monitoramento.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: b5304f2f9af3f3dac5a1259669cdd78548c2e832
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933730"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Soluções de monitoramento no Azure Monitor
As soluções de monitoramento aproveitam os serviços no Azure para fornecer informações adicionais sobre a operação de um determinado aplicativo ou serviço. Este artigo fornece uma breve visão geral das soluções de monitoramento no Azure e detalhes sobre como usá-las e instalá-las.

> [!NOTE]
> As soluções de monitoramento eram anteriormente chamadas de soluções de gerenciamento.

Soluções de monitoramento normalmente coletam dados de log e fornecem consultas e exibições para analisar os dados coletados. Eles também podem aproveitar outros serviços, como a automação do Azure para executar ações relacionadas ao aplicativo ou serviço.

Você pode adicionar soluções de monitoramento para Azure Monitor para quaisquer aplicativos e serviços que você usar. Normalmente, eles estão disponíveis sem custos, mas coletam dados que podem invocar encargos de uso. Além das soluções fornecidas pela Microsoft, os parceiros e clientes podem [criar soluções de gerenciamento](solutions-creating.md) para serem usadas em seu próprio ambiente ou disponibilizadas para os clientes por meio da Comunidade.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Utilizar soluções de monitorização
Abra a página **visão geral** no Azure monitor para exibir um bloco para cada solução instalada no espaço de trabalho. 

1. Inicie sessão no portal do Azure.
1. Abra **todos os serviços** e localize o **Monitor**.
1. No menu **insights** , selecione **mais**.
1. Use as caixas suspensas na parte superior da tela para alterar o espaço de trabalho ou o intervalo de tempo usado para os blocos.
1. Clique no bloco de uma solução para abrir sua exibição, que inclui uma análise mais detalhada dos dados coletados.

![Visão geral](media/solutions/overview.png)

As soluções de monitoramento podem conter vários tipos de recursos do Azure e você pode exibir todos os recursos incluídos em uma solução, assim como qualquer outro recurso. Por exemplo, todas as consultas de log incluídas na solução são listadas em **consultas de solução** no [Gerenciador de consultas](../log-query/get-started-portal.md#load-queries) . você pode usar essas consultas ao executar análises ad hoc com consultas de [log](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Listar soluções de monitoramento instaladas 
Use o procedimento a seguir para listar as soluções de monitoramento instaladas em sua assinatura.

1. Inicie sessão no portal do Azure.
1. Abra **todos os serviços** e localize **soluções**.
4. As soluções instaladas em todos os seus espaços de trabalho são listadas. O nome da solução é seguido pelo nome do espaço de trabalho em que ele está instalado.
1. Use as caixas suspensas na parte superior da tela para filtrar por assinatura ou grupo de recursos.


![Listar todas as soluções](media/solutions/list-solutions-all.png)

Clique no nome de uma solução para abrir sua página de resumo. Esta página exibe todas as exibições incluídas na solução e fornece opções diferentes para a própria solução e seu espaço de trabalho. Exiba a página Resumo de uma solução usando um dos procedimentos acima para listar soluções e, em seguida, clique no nome da solução.

![Propriedades da solução](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Instalar uma solução de monitoramento
As soluções de monitoramento da Microsoft e dos parceiros estão disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com). Você pode pesquisar soluções disponíveis e instalá-las usando o procedimento a seguir. Ao instalar uma solução, você deve selecionar um [espaço de trabalho log Analytics](../platform/manage-access.md) onde a solução será instalada e onde seus dados serão coletados.

1. Na [lista de soluções para sua assinatura](#list-installed-monitoring-solutions), clique em **Adicionar**.
1. Procure uma solução ou pesquise por ela. Você também pode procurar soluções [neste link de pesquisa](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Localize a solução de monitoramento que você deseja e leia sua descrição.
1. Clique em **criar** para iniciar o processo de instalação.
1. Quando o processo de instalação for iniciado, você será solicitado a especificar o espaço de trabalho Log Analytics e fornecer qualquer configuração necessária para a solução.

![Instalar uma solução](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Instalar uma solução da Comunidade
Os membros da Comunidade podem enviar soluções de gerenciamento para modelos de início rápido do Azure. Você pode instalar essas soluções diretamente ou baixá-las modelos para instalação posterior.

1. Siga o processo descrito em [log Analytics espaço de trabalho e conta de automação](#log-analytics-workspace-and-automation-account) para vincular um espaço de trabalho e uma conta.
2. Vá para [modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/). 
3. Procure uma solução na qual esteja interessado.
4. Selecione a solução nos resultados para exibir seus detalhes.
5. Clique no botão **implantar no Azure** .
6. Você será solicitado a fornecer informações como o grupo de recursos e o local, além de valores para quaisquer parâmetros na solução.
7. Clique em **comprar** para instalar a solução.


## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics o espaço de trabalho e a conta de automação
Todas as soluções de monitoramento exigem um [espaço de trabalho log Analytics](../platform/manage-access.md) para armazenar dados coletados pela solução e hospedar suas pesquisas e exibições de logs. Algumas soluções também exigem uma [conta de automação](../../automation/automation-security-overview.md#automation-account-overview) para conter runbooks e recursos relacionados. O espaço de trabalho e a conta devem atender aos seguintes requisitos.

* Cada instalação de uma solução pode usar apenas um espaço de trabalho Log Analytics e uma conta de automação. Você pode instalar a solução separadamente em vários espaços de trabalho.
* Se uma solução exigir uma conta de automação, o espaço de trabalho Log Analytics e a conta de automação deverão ser vinculadas umas às outras. Um espaço de trabalho Log Analytics só pode ser vinculado a uma conta de automação e uma conta de automação só pode ser vinculada a um espaço de trabalho de Log Analytics.
* Para ser vinculado, o espaço de trabalho Log Analytics e a conta de automação devem estar no mesmo grupo de recursos e região. A exceção é um espaço de trabalho na região leste dos EUA e conta de automação no leste dos EUA 2.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Criar um link entre um espaço de trabalho Log Analytics e uma conta de automação
Como você especifica o espaço de trabalho Log Analytics e a conta de automação depende do método de instalação para sua solução.

* Ao instalar uma solução por meio do Azure Marketplace, você será solicitado a fornecer um espaço de trabalho e uma conta de automação. O vínculo entre eles será criado se eles ainda não estiverem vinculados.
* Para soluções fora do Azure Marketplace, você deve vincular o espaço de trabalho Log Analytics e a conta de automação antes de instalar a solução. Você pode fazer isso selecionando qualquer solução no Azure Marketplace e selecionando o espaço de trabalho Log Analytics e a conta de automação. Você não precisa realmente instalar a solução porque o link é criado assim que o espaço de trabalho Log Analytics e a conta de automação são selecionados. Depois que o link for criado, você poderá usar esse Log Analytics espaço de trabalho e a conta de automação para qualquer solução.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Verificar o link entre um espaço de trabalho Log Analytics e uma conta de automação
Você pode verificar o vínculo entre um Log Analytics espaço de trabalho e uma conta de automação usando o procedimento a seguir.

1. Selecione a conta de automação no portal do Azure.
1. Role até a seção **recursos relacionados** do menu.
1. Se a configuração do **espaço de trabalho** estiver habilitada, essa conta será vinculada a um espaço de trabalho log Analytics. Você pode clicar em **espaço de trabalho** para exibir os detalhes do espaço de trabalho.

## <a name="remove-a-monitoring-solution"></a>Remover uma solução de monitoramento
Para remover uma solução instalada, localize-a na [lista de soluções instaladas](#list-installed-monitoring-solutions). Clique no nome da solução para abrir sua página de resumo e, em seguida, clique em **excluir**.


## <a name="next-steps"></a>Passos seguintes
* Obtenha uma [lista de soluções de monitoramento da Microsoft](solutions-inventory.md).
* Saiba como [criar consultas](../log-query/log-query-overview.md) para analisar os dados coletados por soluções de monitoramento.

