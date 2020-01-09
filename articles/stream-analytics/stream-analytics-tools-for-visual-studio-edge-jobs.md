---
title: Azure Stream Analytics trabalhos do Edge no Visual Studio
description: Este artigo descreve como criar e depurar o Stream Analytics em trabalhos IoT Edge usando as ferramentas do Stream Analytics para Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 07f109b3d5539f7cd87a12fb42a36803573c2bdf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354555"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Desenvolver Stream Analytics trabalhos do Edge usando as ferramentas do Visual Studio

Neste tutorial, você aprenderá a usar as ferramentas de Stream Analytics para o Visual Studio. Você aprende a criar, a depurar e cria seus trabalhos do Stream Analytics Edge. Depois de criar e testar o trabalho, você pode ir para o portal do Azure para implantá-lo em seus dispositivos. 

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes pré-requisitos para concluir este tutorial:

* Instale o [visual studio 2019](https://visualstudio.microsoft.com/downloads/), o [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/)ou o [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). As edições Enterprise (Ultimate/Premium), Professional e Community são suportadas. Não há suporte para a Express Edition.  

* Siga as [instruções de instalação](stream-analytics-tools-for-visual-studio-edge-jobs.md) para instalar o Stream Analytics Tools para Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Criar um projeto de borda Stream Analytics 

No Visual Studio, selecione **arquivo** > **novo** **projeto**de > . Navegue até a lista de **modelos** à esquerda > expanda **Azure Stream Analytics** > **Stream Analytics borda** > **aplicativo de borda Azure Stream Analytics**. Forneça um nome, um local e um nome de solução para seu projeto e selecione **OK**.

![Novo projeto do Edge Stream Analytics no Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

Depois que o projeto é criado, navegue até o **Gerenciador de soluções** para exibir a hierarquia de pastas.

![Exibição do Gerenciador de soluções do trabalho do Stream Analytics Edge](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Escolha a assinatura correta

1. No menu **Exibir** do Visual Studio, selecione **Gerenciador de servidores**.  

2. Clique com o botão direito do mouse em **Azure** > selecione **conectar à assinatura do Microsoft Azure** > e, em seguida, entre com sua conta do Azure.

## <a name="define-inputs"></a>Definir entradas

1. No **Gerenciador de soluções**, expanda o nó **entradas** , você deverá ver uma entrada chamada **EdgeInput. JSON**. Clique duas vezes para exibir suas configurações.  

2. Defina o tipo de origem como **fluxo de dados**. Em seguida, defina a origem para o **Hub do Edge**, o formato de serialização de evento como **JSON**e a codificação como **UTF8**. Opcionalmente, você pode renomear o **alias de entrada**, vamos deixá-lo como está para este exemplo. Caso você renomeie o alias de entrada, use o nome especificado ao definir a consulta. Selecione **Guardar** para guardar as definições.  
   ![Stream Analytics configuração de entrada do trabalho](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definir saídas

1. No **Gerenciador de soluções**, expanda o nó **saídas** , você deverá ver uma saída chamada **EdgeOutput. JSON**. Clique duas vezes para exibir suas configurações.  

2. Certifique-se de definir o coletor para selecionar o **Hub do Edge**, definir o formato de serialização de evento como **JSON**, definir codificação como **UTF8**e definir a **matriz**de formato. Opcionalmente, você pode renomear o **alias de saída**, vamos deixá-lo como está para este exemplo. Caso você renomeie o alias de saída, use o nome especificado ao definir a consulta. Selecione **Guardar** para guardar as definições. 
   ![Stream Analytics configuração de saída do trabalho](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

Stream Analytics trabalhos implantados no Stream Analytics IoT Edge ambientes dão suporte à maioria das [Stream Analytics referência de linguagem de consulta](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396). No entanto, as operações a seguir ainda não têm suporte para trabalhos do Stream Analytics Edge: 


|**Categoria**  | **Comando**  |
|---------|---------|
|Outros operadores | <ul><li>PARTIÇÃO POR</li><li>CARIMBO DE DATA/HORA EM</li><li>UDF do JavaScript</li><li>Agregações definidas pelo usuário (UDA)</li><li>GetMetadataPropertyValue</li><li>Usando mais de 14 agregações em uma única etapa</li></ul>   |

Quando você cria um trabalho de borda Stream Analytics no portal, o compilador avisará automaticamente se você não estiver usando um operador com suporte.

No Visual Studio, defina a seguinte consulta de transformação no editor de consultas (**arquivo script. asaql**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Testar o trabalho localmente

Para testar a consulta localmente, você deve carregar os dados de exemplo. Você pode obter dados de exemplo baixando dados de registro do [repositório do GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) e salvando-os em seu computador local. 

1. Para carregar dados de exemplo, clique com o botão direito do mouse no arquivo **EdgeInput. JSON** e escolha **Adicionar entrada local**  

2. Na janela pop-up > **procure** os dados de exemplo em seu caminho local > selecione **salvar**.
   ![a configuração de entrada local no Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Um arquivo chamado **local_EdgeInput. JSON** é adicionado automaticamente à sua pasta entradas.  
4. Você pode executá-lo localmente ou enviar para o Azure. Para testar a consulta, selecione **executar localmente**.  
   ![Stream Analytics opções de execução de trabalho no Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. A janela de prompt de comando mostra o status do trabalho. Quando o trabalho é executado com êxito, ele cria uma pasta parecida com "2018-02-23-11-31-42" no caminho da pasta do projeto "Visual Studio 2015 \ Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42". Navegue até o caminho da pasta para exibir os resultados na pasta local:

   Você também pode entrar no portal do Azure e verificar se o trabalho foi criado. 

   ![Stream Analytics pasta de resultados do trabalho](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Enviar o trabalho para o Azure

1. Antes de enviar o trabalho para o Azure, você deve se conectar à sua assinatura do Azure. Abra **Gerenciador de Servidores** > clique com o botão direito do mouse em **Azure** > **conectar para Microsoft Azure assinatura** > entrar na sua assinatura do Azure.  

2. Para enviar o trabalho para o Azure, navegue até o editor de consultas > selecione **Enviar para o Azure**.  

3. Uma janela pop-up é aberta. Escolha atualizar um trabalho de borda Stream Analytics existente ou crie um novo. Quando você atualizar um trabalho existente, ele substituirá toda a configuração do trabalho, neste cenário, você publicará um novo trabalho. Selecione **criar um novo trabalho de Azure Stream Analytics** > Insira um nome para o seu trabalho, algo como **MyASAEdgeJob** > escolha a **assinatura**necessária, o **grupo de recursos**e o **local** > selecione **Enviar**.

   ![Enviar Stream Analytics trabalho para o Azure por meio do Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Agora, o trabalho do Stream Analytics Edge foi criado. Você pode consultar o [tutorial executar trabalhos no IOT Edge](stream-analytics-edge.md) para saber como implantá-lo em seus dispositivos. 

## <a name="manage-the-job"></a>Gerenciar o trabalho 

Você pode exibir o status do trabalho e o diagrama de trabalho do Gerenciador de Servidores. Em **Stream Analytics** em **Gerenciador de servidores**, expanda a assinatura e o grupo de recursos em que você implantou o trabalho do Stream Analytics Edge. Você pode exibir o MyASAEdgejob com o status **criado**. Expanda o nó do trabalho e clique duas vezes nele para abrir a exibição do trabalho.

![Opções de gerenciamento de trabalho do Gerenciador de servidores](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
A janela de exibição de trabalho fornece operações como atualizar o trabalho, excluir o trabalho e abrir o trabalho de portal do Azure.

![Diagrama de trabalho e outras opções no Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Passos seguintes

* [Mais informações sobre o Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [ASA do tutorial do IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Enviar comentários para o agrupamento com este inquérito](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
