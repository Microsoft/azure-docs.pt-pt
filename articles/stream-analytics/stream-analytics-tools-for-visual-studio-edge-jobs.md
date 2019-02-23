---
title: Tarefas do Edge nas ferramentas do Azure Stream Analytics para Visual Studio
description: Este artigo descreve como criar, depurar e criar as tarefas do Edge do Stream Analytics com as ferramentas do Stream Analytics para Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 684690baa63f5ccd65c69e3a1b7e310c2f809e59
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735563"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Desenvolver trabalhos do Edge do Stream Analytics com ferramentas do Visual Studio

Neste tutorial, saiba como utilizar ferramentas do Stream Analytics para Visual Studio para criar, depurar e criar as tarefas do Edge do Stream Analytics. Depois de criar e testar a tarefa, pode aceder ao portal do Azure, implantá-la em seus dispositivos. 

## <a name="prerequisites"></a>Pré-requisitos

É necessário para concluir este tutorial, os seguintes pré-requisitos:

* Instale [Visual Studio 2017](https://www.visualstudio.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/), ou [Visual Studio 2013 atualização 4](https://www.microsoft.com/download/details.aspx?id=45326). As edições Enterprise (Ultimate/Premium), Professional e Community são suportadas. Edição Express não é suportada.  

* Siga os [instruções de instalação](stream-analytics-tools-for-visual-studio-edge-jobs.md) para instalar as ferramentas do Stream Analytics para Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Criar um projeto do Edge do Stream Analytics 

A partir do Visual Studio, selecione **arquivo** > **New** > **projeto**. Navegue para o **modelos** lista à esquerda > expanda **Azure Stream Analytics** > **Edge do Stream Analytics**  >   **Aplicação de borda do Azure Stream Analytics**. Forneça um nome de nome, a localização e a solução para seu projeto e selecione **OK**.

![Novo projeto de borda no Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

Depois do projeto é criado, navegue para o **Explorador de soluções** para ver a hierarquia de pastas.

![Vista de Explorador de soluções de tarefa do Edge do Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Escolha a subscrição correta

1. Do Visual Studio **View** menu, selecione **Explorador de servidores**.  

2. Clique com o botão direito do rato em **Azure** > selecione **ligar a subscrição do Microsoft Azure** > e, em seguida, inicie sessão com a sua conta do Azure.

## <a name="define-inputs"></a>Definir entradas

1. Do **Explorador de soluções**, expanda o **entradas** nó deverá ver uma entrada com o nome **EdgeInput.json**. Faça duplo clique para ver as respetivas definições.  

2. Definir o tipo de origem **Stream dados**. Em seguida, definir a origem **Hub do Edge**, formato de serialização de eventos para **Json**e a codificação a ser **UTF8**. Opcionalmente, pode renomear o **Alias de entrada**, vamos deixá-la como está para este exemplo. No caso de mudar o nome de alias de entrada, utilize o nome que especificou quando definir a consulta. Selecione **Guardar** para guardar as definições.  
   ![Configuração de entrada de tarefa do Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definir saídas

1. Partir do **Explorador de soluções**, expanda o **saídas** nó deverá ver uma saída com o nome **EdgeOutput.json**. Faça duplo clique para ver as respetivas definições.  

2. Certifique-se de que o Sink está definida para selecionar **Hub do Edge** > formato de serialização do evento definido como **Json** > e Encoding é definido como **UTF8** > e formato está definido como  **Matriz**. Opcionalmente, pode renomear o **Alias de saída**, vamos deixá-la como está para este exemplo. No caso de mudar o nome de alias de saída, utilize o nome que especificou quando definir a consulta. Selecione **Guardar** para guardar as definições. 
   ![Configuração de saída da tarefa do Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

Tarefas do Stream Analytics implementadas nos ambientes de Edge suportam a maioria dos [referência de linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396), no entanto, as seguintes operações ainda não são suportadas para as tarefas do Edge: 


|**Categoria**  | **Comando**  |
|---------|---------|
|Operadores geoespaciais |<ul><li>CreatePoint</li><li>CreatePolygon</li><li>CreateLineString</li><li>ST_DISTANCE</li><li>ST_WITHIN</li><li>ST_OVERLAPS</li><li>ST_INTERSECTS</li></ul> |
|Outros operadores | <ul><li>PARTIÇÃO POR</li><li>TIMESTAMP POR ANO</li><li>DISTINTOS</li><li>Parâmetro de expressão no operador CONTAGEM</li><li>Microssegundo nas funções de data e hora</li><li>UDA de JavaScript (esta funcionalidade ainda está em pré-visualização para as tarefas implementadas na cloud)</li></ul>   |

Quando cria uma tarefa do Edge no portal, o compilador automaticamente avisará se não estiver a utilizar um operador de suporte.

A partir do Visual Studio, definir a seguinte consulta de transformação no editor de consultas (**script.asaql ficheiro**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Testar a tarefa localmente

Para testar a consulta localmente, deverá carregar os dados de exemplo. Pode obter dados de exemplo ao transferir dados de registo do [repositório do GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) e guarde-o para o computador local. 

1. Para carregar dados de exemplo, clique em **EdgeInput.json** do ficheiro e escolha **adicionar a entrada Local**  

2. Na janela de pop-up > **navegue** os dados de exemplo do seu caminho local > selecione **guardar**.
   ![Configuração de entrada local no Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Um ficheiro denominado **local_EdgeInput.json** é adicionado automaticamente à sua pasta de entradas.  
4. Pode executá-lo localmente ou envie para o Azure. Para testar a consulta > selecione **executar localmente**.  
   ![Tarefa do Stream Analytics executar opções no Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. A janela de prompt de comando mostra o estado da tarefa. Quando a tarefa é executada com êxito, ele cria uma pasta que é semelhante a "2018-02-23-11-31-42" no caminho de pasta do projeto "2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42 do Visual Studio". Navegue para o caminho da pasta para ver os resultados na pasta local:

   Também pode iniciar sessão no portal do Azure e certifique-se de que a tarefa é criada. 

   ![Pasta de resultado de tarefa do Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Submeter a tarefa para o Azure

1. Antes de submeter a tarefa para o Azure, tem de ligar à sua subscrição do Azure. Open **Explorador de servidores** > Clique com o botão direito do rato em **Azure** > **ligar a subscrição do Microsoft Azure** > Iniciar sessão na sua subscrição do Azure.  

2. Para submeter a tarefa para o Azure, navegue para o editor de consultas > selecione **submeter para o Azure**.  

3. É aberta uma janela de pop-up, onde pode optar por atualizar uma tarefa do Edge existente ou criar um novo. Quando atualizar uma tarefa existente, irá substituir toda a configuração de trabalho como, neste cenário, o que irá publicar uma nova tarefa. Selecione **criar uma nova tarefa de análise do Azure Stream** > introduza um nome para a sua tarefa algo como **MyASAEdgeJob** > Escolha necessários **subscrição**, **Grupo de recursos**, e **localização** > selecione **submeter**.

   ![Submeter a tarefa de Stream Analytics para o Azure a partir do Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Agora que criou a tarefa do Edge do Stream Analytics, pode consultar o [executar tarefas num tutorial de IoT Edge](stream-analytics-edge.md) para aprender a implementá-la para os seus dispositivos. 

## <a name="manage-the-job"></a>Gerir a tarefa 

Pode ver o estado da tarefa e o diagrama de tarefas a partir do Explorador de servidores. Partir **Stream Analytics** na **Explorador de servidores**, expanda a subscrição e o grupo de recursos onde implementou a tarefa do Edge. Pode ver o MyASAEdgejob com estado **criado**. Expanda o nó de trabalho e clique duas vezes nele para abrir a vista de tarefas.

![Opções de gestão de tarefas do Server explorer](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
A janela de exibição de tarefa fornece operações como atualizar a tarefa, a eliminação da tarefa e abrir a tarefa a partir do portal do Azure.

![Diagrama de tarefas e outras opções no Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Passos Seguintes

* [Obter mais informações sobre o Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [ASA do tutorial do IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Enviar comentários para o agrupamento com este inquérito](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
