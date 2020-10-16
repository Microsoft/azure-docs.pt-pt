---
title: Azure Stream Analytics Edge empregos em Estúdio Visual
description: Este artigo descreve como autor, depurar e criar o seu Stream Analytics em trabalhos IoT Edge usando as ferramentas Stream Analytics para Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 44b84c03dd9c070fd7ca3764a0dc50e8caa9e1fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045166"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Desenvolver trabalhos stream Analytics Edge usando ferramentas de Estúdio Visual

Neste tutorial, aprende-se a usar ferramentas Stream Analytics para o Visual Studio. Aprende-se a autor, depurar e a criar os seus trabalhos stream Analytics Edge. Depois de criar e testar o trabalho, pode ir ao portal Azure implantá-lo nos seus dispositivos. 

## <a name="prerequisites"></a>Pré-requisitos

Precisa dos seguintes pré-requisitos para completar este tutorial:

* Instalar [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/)ou [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). As edições Enterprise (Ultimate/Premium), Professional e Community são suportadas. A edição expressa não é apoiada.  

* Siga as [instruções de instalação](stream-analytics-tools-for-visual-studio-edge-jobs.md) para instalar as ferramentas Stream Analytics para o Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Criar um projeto Stream Analytics Edge 

A partir do Estúdio Visual, selecione **File**  >  **New**  >  **Project**. Navegue para a lista **de modelos** no lado esquerdo > expandir a aplicação de borda **azure stream analytics**  >  **edge**  >  **azure stream azure stream**. Forneça um nome, localização e solução para o seu projeto e selecione **OK**.

![Novo projeto Stream Analytics Edge em Estúdio Visual](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

Após a criação do projeto, navegue até ao **Solution Explorer** para ver a hierarquia da pasta.

![Vista exploradora de solução do trabalho stream Analytics Edge](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Escolha a subscrição correta

1. A partir do menu Visual Studio **View,** selecione **Server Explorer**.  

2. Clique no **Azure** > Select **Connect to Microsoft Azure Subscription** > e, em seguida, iniciar sôs-nos na sua conta Azure.

## <a name="define-inputs"></a>Definir entradas

1. A partir do **Solution Explorer,** expanda o nó **de entradas** em que deve ver uma entrada com o nome **EdgeInput.js.** Clique duas vezes para ver as suas definições.  

2. Definir o tipo de origem para o **fluxo de dados**. Em seguida, desacorda a Fonte para **Edge Hub,** Formato de Serialização de Eventos para **Json**, e Codificação para **UTF8**. Opcionalmente, pode mudar o nome do **Alias de Entrada,** vamos deixá-lo como é para este exemplo. Caso rebatize o pseudónimo de entrada, utilize o nome especificado ao definir a consulta. Selecione **Guardar** para guardar as definições.  
   ![Configuração de entrada de trabalho do Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definir saídas

1. A partir do **Solution Explorer,** expanda o nó **outputs** em que deve ver uma saída com o nome **EdgeOutput.js.** Clique duas vezes para ver as suas definições.  

2. Certifique-se de que define o Sink para selecionar **o Edge Hub,** desemocione o Formato de Serialização de Eventos para **Json,** desemocione a codificação para **UTF8**e desfaça o **Conjunto de**Formatos . Opcionalmente, pode mudar o nome do **Alias de Saída,** vamos deixá-lo como é para este exemplo. Caso rebatize o pseudónimo de saída, utilize o nome especificado ao definir a consulta. Selecione **Guardar** para guardar as definições. 
   ![Configuração de saída de trabalho do Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

Os trabalhos stream Analytics implantados nos ambientes Stream Analytics IoT Edge suportam a maior parte da [referência de Linguagem De Consulta de Stream Analytics](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396). No entanto, as seguintes operações ainda não são suportadas para trabalhos stream Analytics Edge: 


|**Categoria**  | **Comando**  |
|---------|---------|
|Outros operadores | <ul><li>PARTIÇÃO POR</li><li>TIMETAMP POR OVER</li><li>UDF em JavaScript</li><li>Agregados definidos pelo utilizador (UDA)</li><li>GetMetadataPropertyValue</li><li>Usando mais de 14 agregados num único passo</li></ul>   |

Quando criar um trabalho stream Analytics Edge no portal, o compilador avisa-o automaticamente se não estiver a utilizar um operador suportado.

A partir do seu Estúdio Visual, defina a seguinte consulta de transformação no editor de consulta **(script.asaql file)**

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Teste o trabalho localmente

Para testar a consulta localmente, deve fazer o upload dos dados da amostra. Pode obter dados de amostras descarregando os dados de registo do [repositório GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) e guardá-os para o seu computador local. 

1. Para fazer upload de dados da amostra, clique no **EdgeInput.jsno** ficheiro e escolha Adicionar **Entrada Local**  

2. Na janela pop-up > **Navegue** os dados da amostra do seu caminho local > Select **Save**.
   ![Configuração de entrada local no Estúdio Visual](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Um ficheiro com o nome **local_EdgeInput.jsligado** é adicionado automaticamente à pasta das entradas.  
4. Pode executá-lo localmente ou submeter-se ao Azure. Para testar a consulta, selecione **Executar Localmente**.  
   ![Stream Analytics opções de execução de emprego em Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. A janela de ordem mostra o estado do trabalho. Quando o trabalho funciona com sucesso, cria uma pasta que se parece com "2018-02-23-11-31-42" no seu percurso de pasta de projeto "Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-4". Navegue até ao caminho da pasta para ver os resultados na pasta local:

   Também pode iniciar seduca no portal Azure e verificar se o trabalho foi criado. 

   ![Pasta de resultados de trabalho stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Submeta o trabalho ao Azure

1. Antes de submeter o trabalho ao Azure, tem de se ligar à sua Assinatura Azure. O Open **Server Explorer** > clicar > clicar no **Azure**  >  **Connect para a subscrição do Microsoft Azure** > iniciar súpido na subscrição do Azure.  

2. Para submeter o trabalho ao Azure, navegue para o editor de consulta > **selecione Submeter ao Azure**.  

3. Uma janela pop-up abre-se. Opte por atualizar um trabalho stream Analytics Edge ou criar um novo. Quando atualizar um trabalho existente, substituirá toda a configuração de trabalho, neste cenário, publicará um novo emprego. Selecione **Criar um novo Azure Stream Analytics Job** > introduzir um nome para o seu trabalho algo como **MyASAEdgeJob** > escolher a **subscrição**necessária, **Grupo de Recursos**e **Localização** > Select **Submit**.

   ![Submeta o trabalho de Stream Analytics para Azure do Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Agora o seu trabalho stream Analytics Edge foi criado. Pode consultar os [trabalhos run no tutorial IoT Edge](stream-analytics-edge.md) para aprender a implantá-lo nos seus dispositivos. 

## <a name="manage-the-job"></a>Gerir o trabalho 

Pode ver o estado do trabalho e o diagrama de trabalho do Explorador de Servidor. A partir do **Stream Analytics** no **Server Explorer,** expanda a subscrição e o grupo de recursos onde implementou o trabalho Stream Analytics Edge. Pode ver o MyASAEdgejob com o status **Criado.** Expanda o seu nó de trabalho e clique duas vezes nele para abrir a vista de emprego.

![Opções de gestão de emprego de explorador de servidor](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
A janela de vista de emprego proporciona-lhe operações como refrescar o trabalho, apagar o trabalho e abrir o trabalho a partir do portal Azure.

![Diagrama de trabalho e outras opções no Estúdio Visual](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Passos seguintes

* [Mais informações sobre Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [ASA no tutorial IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Envie feedback para a equipa usando este inquérito](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
