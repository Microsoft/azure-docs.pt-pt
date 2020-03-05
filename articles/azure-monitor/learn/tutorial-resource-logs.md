---
title: Recolher registos de recursos de um Recurso Azure e analisar com o Monitor Azure
description: Tutorial para configurar configurações de diagnóstico para recolher registos de recursos de um recurso Azure para um espaço de trabalho log Analytics onde podem ser analisados com uma consulta de registo.
ms.subservice: ''
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: d356042d65c419163de4951e64a635a22ea90e6d
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78269199"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Tutorial: Recolher e analisar registos de recursos de um recurso Azure

Os registos de recursos fornecem informações sobre o funcionamento detalhado de um recurso Azure e são úteis para monitorizar a sua saúde e disponibilidade. Os recursos azure geram registos de recursos automaticamente, mas é preciso configurar onde devem ser recolhidos. Este tutorial leva-o através do processo de criação de uma definição de diagnóstico para recolher registos de recursos para um recurso na sua subscrição Azure e analisá-lo com uma consulta de registo.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um espaço de trabalho de Log Analytics no Monitor Azure
> * Criar uma definição de diagnóstico para recolher registos de recursos 
> * Criar uma simples consulta de log para analisar registos


## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial é necessário um recurso Azure para monitorizar. Pode utilizar qualquer recurso na sua subscrição Azure que suporte as definições de diagnóstico. Para determinar se um recurso suporta configurações de diagnóstico, vá ao seu menu no portal Azure e verifique se existe uma opção de **definições** de Diagnóstico na secção **de Monitorização** do menu.


## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-workspace"></a>Criar uma área de trabalho
Um espaço de trabalho de Log Analytics no Azure Monitor recolhe e indexa dados de registo de várias fontes e permite uma análise avançada usando uma linguagem de consulta poderosa. O espaço de trabalho log Analytics precisa de existir antes de criar uma definição de diagnóstico para enviar dados para o mesmo. Pode utilizar um espaço de trabalho existente na sua subscrição Azure ou criar um com o seguinte procedimento. 

> [!NOTE]
> Enquanto pode trabalhar com dados em espaços de trabalho do Log Analytics no menu **Do Monitor Azure,** cria e gere espaços de trabalho no menu de espaços de **trabalho Log Analytics.**

1. De **todos os serviços,** selecione espaços de **trabalho Log Analytics**.
2. Clique em **Adicionar** na parte superior do ecrã e fornecer os seguintes detalhes para o espaço de trabalho:
   - **Log Analytics espaço de trabalho**: Nome para o novo espaço de trabalho. Este nome deve ser globalmente único em todas as subscrições do Azure Monitor.
   - **Subscrição**: Selecione a subscrição para armazenar o espaço de trabalho. Esta não é necessária a mesma subscrição que o recurso que está a ser monitorizado.
   - **Grupo de Recursos**: Selecione um grupo de recursos existente ou clique **em Criar novo** para criar um novo. Este não precisa de ser o mesmo grupo de recursos que o recurso que está a ser monitorizado.
   - **Localização**: Selecione uma região Azure ou crie uma nova. Este não precisa de ser o mesmo local que o recurso que está a ser monitorizado.
   - **Nível**de preços: Selecione *Pay-as-you-go* como o nível de preços. Pode alterar este nível de preços mais tarde. Clique no link de **preços log Analytics** para saber mais sobre diferentes níveis de preços.

    ![Nova área de trabalho](media/tutorial-resource-logs/new-workspace.png)

3. Clique em **OK** para criar o espaço de trabalho.

## <a name="create-a-diagnostic-setting"></a>Criar uma definição de diagnóstico
[As definições de diagnóstico](../platform/diagnostic-settings.md) definem onde devem ser enviados registos de recursos para um determinado recurso. Uma única definição de diagnóstico pode ter [vários destinos,](../platform/diagnostic-settings.md#destinations)mas só usaremos um espaço de trabalho log Analytics neste tutorial.

1. Na secção **de Monitorização** do menu do seu recurso, selecione **definições**de diagnóstico .
2. Deve ter uma mensagem "Sem definições de diagnóstico definidas". Clique em **adicionar definição de diagnóstico**.

    ![Definições de diagnóstico](media/tutorial-resource-logs/diagnostic-settings.png)

3. Cada definição de diagnóstico tem três partes básicas:
 
   - **Nome**: Isto não tem efeito significativo e deve simplesmente ser descritivo para si.
   - **Destinos**: Um ou mais destinos para enviar os registos. Todos os serviços da Azure partilham o mesmo conjunto de três destinos possíveis. Cada definição de diagnóstico pode definir um ou mais destinos, mas não mais do que um destino de um determinado tipo. 
   - **Categorias**: Categorias de registos para enviar para cada um dos destinos. O conjunto de categorias variará para cada serviço Azure.

4. Selecione **Enviar para Log Analytics espaço** de trabalho e, em seguida, selecione o espaço de trabalho que criou.
5. Selecione as categorias que pretende recolher. Consulte a documentação para cada serviço para obter uma definição das suas categorias disponíveis.

    ![Definição de diagnóstico](media/tutorial-resource-logs/diagnostic-setting.png)

6. Clique em **Guardar** para guardar as definições de diagnóstico.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Use uma consulta de registo para recuperar registos
Os dados são recuperados a partir de um espaço de trabalho de Log Analytics usando uma consulta de log escrita em Kusto Consulta Language (KQL). Insights e soluções no Azure Monitor fornecerão consultas de registo para recuperar dados para um determinado serviço, mas pode trabalhar diretamente com consultas de registo e seus resultados no portal Azure com Log Analytics. 

1. Na secção **de Monitorização** do menu do seu recurso, selecione **Registos**.
2. O Log Analytics abre com uma janela de consulta vazia com o âmbito definido para o seu recurso. Quaisquer consultas incluirão apenas registos desse recurso.

    > [!NOTE]
    > Se abrisse os Registos do menu Do Monitor Azure, o âmbito seria definido para o espaço de trabalho do Log Analytics. Neste caso, quaisquer consultas incluirão todos os registos no espaço de trabalho.
   
    ![Registos](media/tutorial-resource-logs/logs.png)

4. O serviço mostrado no exemplo escreve registos de recursos para a tabela **AzureDiagnostics,** mas outros serviços podem escrever para outras tabelas. Consulte [serviços suportados, schemas e categorias para Registos](../platform/diagnostic-logs-schema.md) de Recursos Azure para tabelas utilizadas por diferentes serviços Azure.

    > [!NOTE]
    > Vários serviços escrevem registos de recursos para a tabela AzureDiagnostics. Se iniciar o Log Analytics a partir do menu Do Monitor Azure, terá de adicionar uma declaração `where` com a coluna `ResourceProvider` para especificar o seu serviço específico. Quando iniciar o Log Analytics a partir do menu de um recurso, então o âmbito é definido apenas para registos a partir deste recurso para que esta coluna não seja necessária. Consulte a documentação do serviço para consultas de amostra.


5. Digite uma consulta e clique **em Executar** para inspecionar os resultados. 
6. Consulte Começar com consultas de [log no Monitor Azure](../log-query/get-started-queries.md) para um tutorial sobre escrever consultas de registo.

    ![Consulta de log](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu a recolher registos de recursos num espaço de trabalho do Log Analytics, complete um tutorial sobre escrever consultas de registo para analisar estes dados.

> [!div class="nextstepaction"]
> [Começar com consultas de log no Monitor Azure](../log-query/get-started-queries.md)
