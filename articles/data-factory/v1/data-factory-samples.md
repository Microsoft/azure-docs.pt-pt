---
title: Azure Data Factory-amostras
description: Fornece detalhes sobre os exemplos fornecidos com o serviço Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 63db1810cbdd2133bc0577530325351997b31f30
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70139253"
---
# <a name="azure-data-factory---samples"></a>Azure Data Factory-amostras
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [exemplos do PowerShell em data Factory](../samples-powershell.md) e [exemplos de código na Galeria de exemplos de código do Azure](https://azure.microsoft.com/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Exemplos no GitHub
O [repositório Azure-](https://github.com/azure/azure-datafactory) datafactory do GitHub contém vários exemplos que ajudam você a se acelerar rapidamente com o serviço de Azure data Factory (ou) modificar os scripts e usá-los no próprio aplicativo. A pasta Samples\JSON contém trechos de código JSON para cenários comuns.

| Exemplo | Descrição |
|:--- |:--- |
| [Instruções do ADF](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFWalkthrough) |Este exemplo fornece uma explicação de ponta a ponta para processar arquivos de log usando Azure Data Factory para transformar dados de arquivos de log no insights. <br/><br/>Neste tutorial, o pipeline de Data Factory coleta logs de exemplo, processa e enriquece os dados de logs com dados de referência e transforma os dados para avaliar a eficácia de uma campanha de marketing que foi iniciada recentemente. |
| [Exemplos de JSON](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON) |Este exemplo fornece exemplos de JSON para cenários comuns. |
| [Exemplo de download de dados http](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample) |Este exemplo demonstra o download de dados de um ponto de extremidade HTTP para o armazenamento de BLOBs do Azure usando uma atividade personalizada do .NET. |
| [Exemplo de atividade net de ponto de AppDomain cruzado](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Este exemplo permite que você crie uma atividade personalizada do .NET que não esteja restrita às versões de assembly usadas pelo iniciador do ADF (por exemplo, WindowsAzure. Storage v 4.3.0, Newtonsoft. JSON v 6.0. x, etc.). |
| [Executar script R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) |Este exemplo inclui a Data Factory atividade personalizada que pode ser usada para invocar RScript. exe. Este exemplo só funciona com seu próprio cluster HDInsight (não sob demanda) que já tem o R instalado nele. |
| [Invocar trabalhos do Spark no cluster HDInsight Hadoop](https://docs.microsoft.com/azure/data-factory/tutorial-transform-data-spark-portal) |Este exemplo mostra como usar a atividade MapReduce para invocar um programa Spark. O programa Spark apenas copia dados de um contêiner de blob do Azure para outro. |
| [Análise do Twitter usando Azure Machine Learning atividade de Pontuação de lote](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Este exemplo mostra como usar o AzureMLBatchScoringActivity para invocar um modelo de Azure Machine Learning que executa análise de sentimentos do Twitter, pontuação, previsão, etc. |
| [Análise do Twitter usando atividade personalizada](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Este exemplo mostra como usar uma atividade personalizada do .NET para invocar um modelo de Azure Machine Learning que executa análise de sentimentos do Twitter, pontuação, previsão, etc. |
| [Pipelines com parâmetros para Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ParameterizedPipelinesForAzureML) |O exemplo fornece um código de ponta a ponta C# para implantar N pipelines para pontuação e treinar novamente cada um com um parâmetro de região diferente em que a lista de regiões é proveniente de um arquivo Parameters. txt, que está incluído neste exemplo. |
| [Atualização de dados de referência para trabalhos de Azure Stream Analytics](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs) |Este exemplo mostra como usar Azure Data Factory e Azure Stream Analytics em conjunto para executar as consultas com dados de referência e configurar a atualização para dados de referência em um agendamento. |
| [Pipeline híbrido com Hadoop Hortonworks local](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HybridPipelineWithOnPremisesHortonworksHadoop) |O exemplo usa um cluster Hadoop local como um destino de computação para executar trabalhos em Data Factory assim como você adicionaria outros destinos de computação, como um cluster Hadoop baseado em HDInsight na nuvem. |
| [Ferramenta de conversão JSON](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSONConversionTool) |Essa ferramenta permite converter JSONs de versão anterior a 2015-07-01-Preview para o mais recente ou 2015-07-01-Preview (padrão). |
| [Arquivo de entrada de exemplo do U-SQL](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/U-SQL%20Sample%20Input%20File) |Esse arquivo é um arquivo de exemplo usado por uma atividade do U-SQL. |
| [Excluir arquivo de BLOB](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity) | Este exemplo demonstra um C# arquivo que pode ser usado como parte da atividade personalizada do .net do ADF para excluir arquivos do local do blob do Azure de origem depois que os arquivos forem copiados.|

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager
Você pode encontrar os modelos de Azure Resource Manager a seguir para Data Factory no GitHub.

| Modelo | Descrição |
| --- | --- |
| [Copiar do armazenamento de BLOBs do Azure para o banco de dados SQL do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |A implantação desse modelo cria uma data factory do Azure com um pipeline que copia dados do armazenamento de BLOBs do Azure especificado para o banco de dados SQL do Azure |
| [Copiar do Salesforce para o armazenamento de BLOBs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |A implantação desse modelo cria uma data factory do Azure com um pipeline que copia dados da conta especificada do Salesforce para o armazenamento de BLOBs do Azure. |
| [Transformar dados executando o script do hive em um cluster do Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |A implantação desse modelo cria uma data factory do Azure com um pipeline que transforma dados executando o script hive de exemplo em um cluster Azure HDInsight Hadoop. |

## <a name="samples-in-azure-portal"></a>Exemplos no portal do Azure
Você pode usar o bloco pipelines de **exemplo** no Home Page do seu data Factory para implantar pipelines de exemplo e suas entidades associadas (conjuntos de valores e serviços vinculados) no seu data Factory.

1. Crie um data factory ou abra um data factory existente. Confira [copiar dados do armazenamento de BLOBs para o SQL Database usando data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter as etapas para criar um data Factory.
2. Na folha **Data Factory** do data Factory, clique no bloco pipelines de **exemplo** .

    ![Bloco pipelines de exemplo](./media/data-factory-samples/SamplePipelinesTile.png)
3. Na folha **pipelines de exemplo** , clique no **exemplo** que você deseja implantar.

    ![Folha pipelines de exemplo](./media/data-factory-samples/SampleTile.png)
4. Especifique as definições de configuração para o exemplo. Por exemplo, o nome da conta de armazenamento do Azure e a chave de conta, o nome do SQL Server do Azure, o banco de dados, a ID de usuário e a senha, etc.

    ![Folha de exemplo](./media/data-factory-samples/SampleBlade.png)
5. Depois de concluir a especificação das definições de configuração, clique em **criar** para criar/implantar os pipelines de exemplo e os serviços/tabelas vinculados usados pelos pipelines.
6. Você verá o status da implantação no bloco de exemplo em que você clicou anteriormente na folha pipelines de **exemplo** .

    ![Estado da implementação](./media/data-factory-samples/DeploymentStatus.png)
7. Quando você vir a mensagem **implantação bem-sucedida** no bloco do exemplo, feche a folha pipelines de **exemplo** .  
8. Na folha **Data Factory** , você vê que os serviços vinculados, os conjuntos de dados e os pipelines são adicionados à sua data Factory.  

    ![Painel Data Factory](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Exemplos no Visual Studio
### <a name="prerequisites"></a>Pré-requisitos
Tem de ter o seguinte instalado no computador:

* Visual Studio 2013 ou Visual Studio 2015
* Transfira o Azure SDK para o Visual Studio 2013 ou Visual Studio 2015. Navegue até à [Página de Transferências do Azure](https://azure.microsoft.com/downloads/) e clique em **VS 2013** ou **VS 2015** na secção **.NET**.
* Baixe o plug-in de Azure Data Factory mais recente para o Visual Studio: [Vs 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [vs 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Se você estiver usando Visual Studio 2013, também poderá atualizar o plug-in executando as seguintes etapas: No menu, clique em **ferramentas** -> **extensões e atualizações** -> **online** -> **Galeria** -> do Visual Studio**Microsoft Azure data Factory Tools for Visual Studio**  ->  **Atualizar**.

### <a name="use-data-factory-templates"></a>Usar modelos de Data Factory
1. Clique em **arquivo** no menu, aponte para **novo**e clique em **projeto**.
2. Na caixa de diálogo **Novo Projeto**, siga os seguintes passos:

   1. Selecione datafactory em **modelos**.
   2. Selecione **modelos de data Factory** no painel direito.
   3. Introduza um **nome** para o projeto.
   4. Selecione um **local** para o projeto.
   5. Clique em **OK**.

      ![Caixa de diálogo Novo Projeto](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. Na caixa de diálogo **modelos de data Factory** , selecione o modelo de exemplo na seção modelos de **caso de uso** e clique em **Avançar**. As etapas a seguir mostrarão como usar o modelo de **criação de perfil do cliente** . As etapas são semelhantes para os outros exemplos.

    ![Caixa de diálogo modelos de Data Factory](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. Na caixa de diálogo **configuração do data Factory** , clique em **Avançar** na página **noções básicas do data Factory** .
5. Na página **configurar data Factory** , execute as seguintes etapas:
   1. Selecione **criar novo data Factory**. Você também pode selecionar **usar data Factory existentes**.
   2. Insira um **nome** para o data Factory.
   3. Selecione a **assinatura do Azure** na qual você deseja que a data Factory seja criada.
   4. Selecione o **grupo de recursos** para o data Factory.
   5. Selecione o **oeste dos EUA**, **leste dos EUA**ou **Europa setentrional** para a **região**.
   6. Clique em **Seguinte**.
6. Na página **Configurar armazenamentos de dados** , especifique uma **conta de armazenamento** do Azure e um banco de dado **SQL do** Azure existente (ou) criar banco/dados/armazenamento e clique em Avançar.
7. Na página **Configurar computação** , selecione padrões e clique em **Avançar**.
8. Na página **Resumo** , examine todas as configurações e clique em **Avançar**.
9. Na página **status da implantação** , aguarde até que a implantação seja concluída e clique em **concluir**.
10. Clique com o botão direito do rato no Explorador de Soluções e, em seguida, clique em **Publicar**.
11. Se vir a caixa de diálogo **Iniciar sessão na sua conta Microsoft**, introduza as credenciais da conta com a subscrição do Azure e clique em **iniciar sessão**.
12. Deverá ver a seguinte caixa de diálogo:

    ![Caixa de diálogo Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. Na página **Configurar fábrica de dados**, execute os seguintes passos:

    1. Confirme que use a opção de **Data Factory existente** .
    2. Selecione o **Data Factory** que você selecionou ao usar o modelo.
    3. Clique em **Seguinte** para mudar para a página **Publicar Itens**. (Prima **Tecla de Tabulação** para sair do campo Nome se o botão **Seguinte** estiver desativado.)
14. Na página **Publicar Itens**, confirme que todas as entidades dos Data Factories estão selecionadas e clique em **Seguinte** para mudar para a página **Resumo**.     
15. Reveja o resumo e clique em **Seguinte** para iniciar o processo de implementação e ver o **Estado da Implementação**.
16. Na página **Estado da Implementação**, deve ver o estado do processo de implementação. Clique em Concluir após a conclusão da implementação.

Consulte [criar seu primeiro data Factory (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) para obter detalhes sobre como usar o Visual Studio para criar data Factory entidades e publicá-las no Azure.          
