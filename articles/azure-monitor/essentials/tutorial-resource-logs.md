---
title: Recolher registos de recursos de um recurso Azure e analisar com o Azure Monitor
description: Tutorial para configurar definições de diagnóstico para recolher registos de recursos de um recurso Azure num espaço de trabalho log Analytics onde podem ser analisados com uma consulta de log.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: 43a9c88bc64b688bfd1171e331232bb254b0eebe
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032879"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Tutorial: Recolher e analisar registos de recursos a partir de um recurso Azure

Os registos de recursos fornecem informações sobre o funcionamento detalhado de um recurso Azure e são úteis para monitorizar a sua saúde e disponibilidade. Os recursos azure geram registos de recursos automaticamente, mas deve configurar onde devem ser recolhidos. Este tutorial leva-o através do processo de criação de uma definição de diagnóstico para recolher registos de recursos para um recurso na sua subscrição Azure e analisá-lo com uma consulta de log.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um espaço de trabalho log analytics no Azure Monitor
> * Criar uma definição de diagnóstico para recolher registos de recursos 
> * Crie uma consulta de log simples para analisar registos


## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa de um recurso Azure para monitorizar. Pode utilizar qualquer recurso na sua subscrição Azure que suporte definições de diagnóstico. Para determinar se um recurso suporta configurações de diagnóstico, vá ao seu menu no portal Azure e verifique se existe uma opção de **definições** de Diagnóstico na secção de **Monitorização** do menu.


## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com) .


## <a name="create-a-workspace"></a>Criar uma área de trabalho
Um espaço de trabalho Log Analytics no Azure Monitor recolhe e indexa dados de registo de uma variedade de fontes e permite uma análise avançada usando uma linguagem de consulta poderosa. O espaço de trabalho Log Analytics tem de existir antes de criar uma definição de diagnóstico para enviar dados para o mesmo. Pode utilizar um espaço de trabalho existente na sua subscrição Azure ou criar um com o seguinte procedimento. 

> [!NOTE]
> Enquanto pode trabalhar com dados em espaços de trabalho do Log Analytics no menu **Azure Monitor,** cria e gere espaços de trabalho no menu **de espaços de trabalho Do Log Analytics.**

1. De **todos os serviços**, selecione **Log Analytics workspaces**.
2. Clique em **Adicionar** na parte superior do ecrã e fornecer os seguintes detalhes para o espaço de trabalho:
   - **Log Analytics workspace**: Nome para o novo espaço de trabalho. Este nome deve ser globalmente único em todas as subscrições do Azure Monitor.
   - **Assinatura**: Selecione a subscrição para armazenar o espaço de trabalho. Esta não necessita de ser a mesma subscrição que o recurso que está a ser monitorizado.
   - **Grupo de Recursos**: Selecione um grupo de recursos existente ou clique em **Criar novo** para criar um novo. Este não necessita de ser o mesmo grupo de recursos que o recurso que está a ser monitorizado.
   - **Localização**: Selecione uma região Azure ou crie uma nova. Este não necessita de ser o mesmo local que o recurso que está a ser monitorizado.
   - **Nível de preços**: Selecione *Pay-as-you-go* como o nível de preços. Pode alterar este nível de preços mais tarde. Clique no link **de preços do Log Analytics** para saber mais sobre diferentes níveis de preços.

    ![Nova área de trabalho](media/tutorial-resource-logs/new-workspace.png)

3. Clique **em OK** para criar o espaço de trabalho.

## <a name="create-a-diagnostic-setting"></a>Criar uma definição de diagnóstico
[As definições de diagnóstico](../essentials/diagnostic-settings.md) definem onde devem ser enviados registos de recursos para um determinado recurso. Uma única configuração de diagnóstico pode ter [vários destinos,](../essentials/diagnostic-settings.md#destinations)mas só usaremos um espaço de trabalho log analytics neste tutorial.

1. Na secção **de Monitorização** do menu do seu recurso, selecione **Definições de Diagnóstico**.
2. Deve ter uma mensagem "Sem definições de diagnóstico definidas". Clique **na definição de diagnóstico de adicionar**.

    ![Definições de diagnóstico](media/tutorial-resource-logs/diagnostic-settings.png)

3. Cada definição de diagnóstico tem três partes básicas:
 
   - **Nome**: Isto não tem nenhum efeito significativo e deve simplesmente ser descritivo para si.
   - **Destinos**: Um ou mais destinos para enviar os registos. Todos os serviços da Azure partilham o mesmo conjunto de três destinos possíveis. Cada definição de diagnóstico pode definir um ou mais destinos, mas não mais do que um destino de um tipo específico. 
   - **Categorias**: Categorias de registos a enviar para cada um dos destinos. O conjunto de categorias variará para cada serviço Azure.

4. Selecione **Enviar para log analytics espaço de trabalho** e, em seguida, selecionar o espaço de trabalho que criou.
5. Selecione as categorias que pretende recolher. Consulte a documentação de cada serviço para uma definição das categorias disponíveis.

    ![Definição de diagnóstico](media/tutorial-resource-logs/diagnostic-setting.png)

6. Clique em **Guardar** para guardar as definições de diagnóstico.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Use uma consulta de log para recuperar registos
Os dados são obtidos a partir de um espaço de trabalho log Analytics usando uma consulta de log escrita em Língua de Consulta de Kusto (KQL). Insights e soluções no Azure Monitor fornecerão consultas de registo para obter dados para um determinado serviço, mas você pode trabalhar diretamente com consultas de registo e seus resultados no portal Azure com Log Analytics. 

1. Na secção **de Monitorização** do menu do seu recurso, selecione **Registos**.
2. O Log Analytics abre com uma janela de consulta vazia com o âmbito definido para o seu recurso. Quaisquer consultas incluirão apenas registos desse recurso.

    > [!NOTE]
    > Se abrisse Logs a partir do menu Azure Monitor, o âmbito seria definido para o espaço de trabalho Log Analytics. Neste caso, quaisquer consultas incluirão todos os registos no espaço de trabalho.
   
    ![O Screenshot mostra Logs para uma aplicação lógica que exibe uma nova consulta com o nome da aplicação lógica realçada.](media/tutorial-resource-logs/logs.png)

4. O serviço mostrado no exemplo escreve registos de recursos para a tabela **AzureDiagnostics, mas outros serviços** podem escrever para outras tabelas. Consulte [serviços, esquemas e categorias de Registos de Recursos Azure para tabelas utilizadas](../essentials/resource-logs-schema.md) por diferentes serviços Azure.

    > [!NOTE]
    > Vários serviços escrevem registos de recursos para a tabela AzureDiagnostics. Se iniciar o Log Analytics a partir do menu Azure Monitor, então terá de adicionar uma `where` declaração com a coluna para `ResourceProvider` especificar o seu serviço específico. Quando iniciar o Log Analytics a partir do menu de um recurso, o âmbito é definido apenas para registos a partir deste recurso, de modo que esta coluna não é necessária. Consulte a documentação do serviço para consultas de amostragem.


5. Digite uma consulta e clique em **Executar** para inspecionar os resultados. 
6. Consulte [Começar com consultas de log no Azure Monitor](../logs/get-started-queries.md) para um tutorial sobre consultas de registo de escrita.

    ![Consulta de registo](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu a recolher registos de recursos num espaço de trabalho do Log Analytics, preencha um tutorial sobre consultas de registo de escrita para analisar estes dados.

> [!div class="nextstepaction"]
> [Introdução às consultas de registos no Azure Monitor](../logs/get-started-queries.md)