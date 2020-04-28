---
title: Azure Stream Analytics Edge empregos em Estúdio Visual
description: Este artigo descreve como autor, depuração e cria o seu Stream Analytics em trabalhos de IoT Edge usando as ferramentas Stream Analytics para O Estúdio Visual.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 07f109b3d5539f7cd87a12fb42a36803573c2bdf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75354555"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Desenvolver trabalhos stream analytics edge usando ferramentas do Estúdio Visual

Neste tutorial, aprende-se a usar ferramentas Stream Analytics para O Estúdio Visual. Aprende-se a autor, depurar e criar os seus trabalhos de Streaming Analytics Edge. Depois de criar e testar o trabalho, pode ir ao portal Azure para o implementar nos seus dispositivos. 

## <a name="prerequisites"></a>Pré-requisitos

Precisa dos seguintes pré-requisitos para completar este tutorial:

* Instalar [o Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), Visual Studio [2015](https://www.visualstudio.com/vs/older-downloads/), ou [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). As edições Enterprise (Ultimate/Premium), Professional e Community são suportadas. A edição expressa não é apoiada.  

* Siga as [instruções de instalação](stream-analytics-tools-for-visual-studio-edge-jobs.md) para instalar ferramentas Stream Analytics para o Estúdio Visual.
 
## <a name="create-a-stream-analytics-edge-project"></a>Criar um projeto Stream Analytics Edge 

A partir do Estúdio Visual, selecione **File** > **New** > **Project**. Navegue para a lista de **modelos** à esquerda > expandir a aplicação de ponta de análise**Stream Analytics Edge** > de fluxo de fluxo > de fluxo**azure azure do** **Azure**Stream Azure . Forneça um nome, localização e nome de solução para o seu projeto e selecione **OK**.

![Novo projeto Stream Analytics Edge no Estúdio Visual](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

Depois de o projeto ser criado, navegue até ao **Solution Explorer** para ver a hierarquia da pasta.

![Vista exploradora de soluções do trabalho de Stream Analytics Edge](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Escolha a subscrição correta

1. A partir do menu Visual Studio **View,** selecione **Server Explorer**.  

2. Clique no **Azure** > Selecione Connect para o **Microsoft Azure >** de subscrição e, em seguida, inscreva-se na sua conta Azure.

## <a name="define-inputs"></a>Definir entradas

1. Do **Solution Explorer,** expanda o nó de **entrada,** deve ver uma entrada chamada **EdgeInput.json**. Clique duas vezes para ver as suas definições.  

2. Definir tipo de origem para fluxo de **dados**. Em seguida, definir Source to **Edge Hub**, Formato de Serialização de Eventos para **Json,** e Codificação para **UTF8**. Opcionalmente, pode renomear o **Input Alias,** vamos deixá-lo como é para este exemplo. No caso de renomear o pseudónimo de entrada, utilize o nome especificado ao definir a consulta. Selecione **Guardar** para guardar as definições.  
   ![Configuração de entrada de trabalho stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definir saídas

1. Do **Solution Explorer,** expanda o nó **de saídas** deve ver uma saída chamada **EdgeOutput.json**. Clique duas vezes para ver as suas definições.  

2. Certifique-se de definir o Sink para selecionar **edge hub,** definir formato de serialização de eventos para **Json,** definir a codificação para **UTF8**e definir o **Formato Array**. Opcionalmente, pode renomear o **'Output Alias',** vamos deixá-lo como é para este exemplo. No caso de mudar o nome do pseudónimo de saída, utilize o nome especificado ao definir a consulta. Selecione **Guardar** para guardar as definições. 
   ![Configuração de saída de trabalho stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

Os trabalhos stream analytics implantados nos ambientes Stream Analytics IoT Edge suportam a maior parte da referência à linguagem de consulta de [Análise stream analytics](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396). No entanto, as seguintes operações ainda não são apoiadas para trabalhos stream analytics edge: 


|**Categoria**  | **Comando**  |
|---------|---------|
|Outros operadores | <ul><li>PARTIÇÃO POR</li><li>TIMESTAMP BY OVER</li><li>UDF em JavaScript</li><li>Agregados definidos pelo utilizador (UDA)</li><li>GetMetadataPropertyValue</li><li>Usando mais de 14 agregados num único passo</li></ul>   |

Quando criar um trabalho de Stream Analytics Edge no portal, o compilador irá avisá-lo automaticamente se não estiver a utilizar um operador suportado.

A partir do seu Estúdio Visual, defina a seguinte consulta de transformação no editor de consulta **(script.asaql file**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Teste o trabalho localmente

Para testar a consulta local, deve fazer o upload dos dados da amostra. Pode obter dados de amostra saqueando dados de registo do [repositório GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) e guardá-los para o seu computador local. 

1. Para fazer upload dos dados da amostra, clique no ficheiro **EdgeInput.json** e escolha **Adicionar Entrada Local**  

2. Na janela pop-up > **Navegue** os dados da amostra do seu caminho local > Selecione **Save**.
   ![Configuração de entrada local no Estúdio Visual](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Um ficheiro chamado **local_EdgeInput.json** é adicionado automaticamente à pasta de entrada.  
4. Pode executá-lo localmente ou submeter-se ao Azure. Para testar a consulta, selecione **Executar Localmente**.  
   ![Stream Analytics opções de execução de trabalho no Estúdio Visual](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. A janela de instrução mostra o estado do trabalho. Quando o trabalho corre com sucesso, cria uma pasta que se parece com "2018-02-23-11-31-42" no seu percurso de pasta de projeto "Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42". Navegue para o caminho da pasta para ver os resultados na pasta local:

   Pode também iniciar sessão no portal Azure e verificar se o trabalho está criado. 

   ![Pasta de resultados de trabalho de Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Submeta o trabalho a Azure

1. Antes de submeter o trabalho ao Azure, deve ligar-se à sua Assinatura Azure. O Open **Server Explorer** > clique direito no **Azure** > Connect para a subscrição do**Microsoft Azure** > iniciar sessão na subscrição do Azure.  

2. Para submeter o trabalho ao Azure, navegue para o editor de consulta > selecione **Submeter ao Azure**.  

3. Abre-se uma janela pop-up. Opte por atualizar um trabalho existente no Stream Analytics Edge ou criar um novo. Quando atualizar um trabalho existente, substituirá toda a configuração de trabalho, neste cenário, publicará um novo emprego. Selecione Criar um novo trabalho de **análise de fluxo de azure** > insira um nome para o seu trabalho algo como **MyASAEdgeJob** > escolha a **subscrição**necessária, **Grupo de Recursos**e > De **localização** Selecione **Submeter**.

   ![Submeta o trabalho de Stream Analytics ao Azure do Estúdio Visual](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Agora o seu trabalho stream analytics edge foi criado. Pode consultar os [trabalhos run no tutorial IoT Edge](stream-analytics-edge.md) para aprender a implantá-lo para os seus dispositivos. 

## <a name="manage-the-job"></a>Gerir o trabalho 

Pode ver o estado do trabalho e o diagrama de trabalho do Server Explorer. A partir do **Stream Analytics** no **Server Explorer,** expanda a subscrição e o grupo de recursos onde implementou o trabalho stream analytics edge. Pode ver o MyASAEdgejob com estatuto **Criado**. Expanda o seu nó de trabalho e clique duas vezes nele para abrir a vista de trabalho.

![Opções de gestão de trabalho do explorador do servidor](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
A janela de vista de emprego proporciona-lhe operações como refrescar o trabalho, apagar o trabalho e abrir o trabalho a partir do portal Azure.

![Diagrama de trabalho e outras opções no Estúdio Visual](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Passos seguintes

* [Mais informações sobre o Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [ASA no tutorial IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Envie feedback à equipa que usa esta pesquisa](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
