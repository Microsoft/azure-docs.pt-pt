---
title: Fábrica de dados do Azure - exemplos
description: Fornece detalhes sobre os exemplos fornecidos com o serviço do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: c0538b90-2695-4c4c-a6c8-82f59111f4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 03127dc777588f669ef07af52c8f73d986bfe0ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61260478"
---
# <a name="azure-data-factory---samples"></a>Fábrica de dados do Azure - exemplos
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [exemplos do PowerShell no Data Factory](../samples-powershell.md) e [exemplos na Galeria de exemplos de código do Azure de código](https://azure.microsoft.com/resources/samples/?service=data-factory).


## <a name="samples-on-github"></a>Exemplos no GitHub
O [repositório do GitHub do Azure-DataFactory](https://github.com/azure/azure-datafactory) contém vários exemplos que ajudam a rapidamente aprimore-se com o serviço Azure Data Factory (ou) modificar os scripts e utilizá-lo no próprio aplicativo. A pasta de Samples\JSON contém trechos de código JSON para cenários comuns.

| Exemplo | Descrição |
|:--- |:--- |
| [Instruções ADF](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) |Este exemplo fornece uma passo a passo-a-ponto para o processamento de ficheiros de registo com o Azure Data Factory para transformar os dados de ficheiros de registo às informações. <br/><br/>Nestas instruções, o pipeline da fábrica de dados recolhe registos de exemplo, processos e otimiza os dados de registos com dados de referência e transforma os dados para avaliar a eficácia de uma campanha de marketing que foi lançada recentemente. |
| [Exemplos de JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) |Este exemplo fornece exemplos JSON para cenários comuns. |
| [Exemplo de transferência de dados de HTTP](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) |Este exemplo mostra transferência de dados de um ponto final HTTP para o armazenamento de Blobs do Azure com a atividade .NET personalizada. |
| [Exemplo de atividade de rede de ponto de AppDomain de cruzada](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Este exemplo permite-lhe criar uma atividade .NET personalizada que não está restrito à versões do assembly utilizadas pelo iniciador do ADF (por exemplo, verze 4.3.0 de windowsazure. Storage, newtonsoft v6.0.x, etc.). |
| [Executar R script](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |Este exemplo inclui a atividade personalizada de fábrica de dados que pode ser utilizada para invocar RScript.exe. Este exemplo funciona apenas com seu próprio cluster do HDInsight (não a pedido) que já tenha o R instalado no mesmo. |
| [Invocar tarefas do Spark num cluster do HDInsight Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) |Este exemplo mostra como utilizar a atividade MapReduce para invocar um programa Spark. O programa spark apenas copia dados de um contentor de Blobs do Azure para outra. |
| [Análise do twitter usando a atividade de classificação de lote do Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Este exemplo mostra como utilizar o AzureMLBatchScoringActivity para invocar um modelo do Azure Machine Learning que efetua a análise de sentimentos do twitter, classificação, previsão, etc. |
| [Análise do twitter usando a atividade personalizada](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Este exemplo mostra como utilizar uma atividade .NET personalizada para invocar um modelo do Azure Machine Learning que efetua a análise de sentimentos do twitter, classificação, previsão, etc. |
| [Pipelines parametrizadas para o Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) |O exemplo fornece um ponto-a-ponto C# código para implementar pipelines de N para classificação e de reparametrização cada um com um parâmetro de região diferente em que a lista de regiões é proveniente de um arquivo de parameters.txt, que está incluído com esse exemplo. |
| [Atualização de dados de referência para tarefas do Azure Stream Analytics](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) |Este exemplo mostra como utilizar o Azure Data Factory e o Azure Stream Analytics em conjunto, para executar as consultas com dados de referência e configurar a atualização para dados de referência com base numa agenda. |
| [Pipeline de híbrida com o Hadoop de Hortonworks no local](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) |Este exemplo utiliza um cluster de Hadoop no local como um destino de computação para executar tarefas no Data Factory, tal como adicionaria outros destinos de computação como um HDInsight com base em cluster do Hadoop na cloud. |
| [Ferramenta de conversão de JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) |Esta ferramenta permite-lhe converter JSONs de versão anterior de 2015-07-01-preview mais recente ou 2015-07-01-pré-visualização (predefinição). |
| [Ficheiro de entrada de exemplo do U-SQL](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) |Este ficheiro é um ficheiro de exemplo utilizado por uma atividade de U-SQL. |
| [Eliminar o ficheiro de BLOBs](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity) | Esta amostra apresenta um C# ficheiro que pode ser utilizado como parte da atividade .net personalizada do ADF para eliminar os ficheiros da origem de localização de Blobs do Azure, uma vez que os ficheiros foram copiados.|

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager
Pode encontrar os seguintes modelos do Azure Resource Manager para o Data Factory no GitHub.

| Modelo | Descrição |
| --- | --- |
| [Copiar do armazenamento de Blobs do Azure para a base de dados SQL do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |Implementar este modelo cria uma fábrica de dados do Azure com um pipeline que copia dados de armazenamento de Blobs do Azure especificada para a base de dados SQL do Azure |
| [Copiar do Salesforce para o armazenamento de Blobs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |Implementar este modelo cria uma fábrica de dados do Azure com um pipeline que copia dados da conta do Salesforce especificada para o armazenamento de Blobs do Azure. |
| [Transforme dados executando o script do Hive num cluster HDInsight do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |Implementar este modelo cria uma fábrica de dados do Azure com um pipeline que transforma os dados ao executar o exemplo de script do Hive num cluster do Azure HDInsight Hadoop. |

## <a name="samples-in-azure-portal"></a>Exemplos no portal do Azure
Pode utilizar o **pipelines de exemplo** mosaico na home page de sua fábrica de dados para implementar pipelines de exemplo e respetivas entidades associadas (conjuntos de dados e serviços ligados) na sua fábrica de dados.

1. Crie uma fábrica de dados ou abra uma fábrica de dados existente. Ver [copiar dados de armazenamento de BLOBs para base de dados SQL com o Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter os passos criar uma fábrica de dados.
2. Na **DATA FACTORY** painel do data Factory, clique nas **pipelines de exemplo** mosaico.

    ![Mosaico de pipelines de exemplo](./media/data-factory-samples/SamplePipelinesTile.png)
3. Na **pipelines de exemplo** painel, clique nas **exemplo** que pretende implementar.

    ![Painel de pipelines de exemplo](./media/data-factory-samples/SampleTile.png)
4. Especifica definições de configuração para o exemplo. Por exemplo, a chave de conta e o nome de conta de armazenamento do Azure, nome do servidor SQL do Azure, base de dados, ID de utilizador e palavra-passe, entre outras.

    ![Painel de exemplo](./media/data-factory-samples/SampleBlade.png)
5. Depois de terminar com a especificação das definições de configuração, clique em **criar** para criar/implementar os pipelines de exemplo e serviços/tabelas ligadas utilizadas pelos pipelines.
6. Ver o estado da implementação no mosaico de exemplo que clicou anteriormente a **pipelines de exemplo** painel.

    ![Estado da implementação](./media/data-factory-samples/DeploymentStatus.png)
7. Quando vir o **implementação concluída com êxito** mensagem no mosaico para o exemplo, feche a **pipelines de exemplo** painel.  
8. No **fábrica de dados** painel, verá que os serviços ligados, conjuntos de dados e pipelines são adicionados à sua fábrica de dados.  

    ![Painel Data Factory](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Exemplos do Visual Studio
### <a name="prerequisites"></a>Pré-requisitos
Tem de ter o seguinte instalado no computador:

* Visual Studio 2013 ou Visual Studio 2015
* Transfira o Azure SDK para o Visual Studio 2013 ou Visual Studio 2015. Navegue até à [Página de Transferências do Azure](https://azure.microsoft.com/downloads/) e clique em **VS 2013** ou **VS 2015** na secção **.NET**.
* Transferir o plug-in mais recente do Azure Data Factory para Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Se estiver a utilizar o Visual Studio 2013, também pode atualizar o plug-in, efetuando os seguintes passos: No menu, clique em **ferramentas** -> **extensões e atualizações** -> **Online** -> **galeria do Visual Studio**  ->  **Ferramentas do Microsoft Azure Data Factory para Visual Studio** -> **atualização**.

### <a name="use-data-factory-templates"></a>Utilizar modelos de fábrica de dados
1. Clique em **arquivo** no menu, aponte para **New**e clique em **projeto**.
2. Na caixa de diálogo **Novo Projeto**, siga os seguintes passos:

   1. Selecione **DataFactory** sob **modelos**.
   2. Selecione **modelos de fábrica de dados** no painel da direita.
   3. Introduza um **nome** para o projeto.
   4. Selecione um **localização** para o projeto.
   5. Clique em **OK**.

      ![Caixa de diálogo Novo Projeto](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. Na **modelos de fábrica de dados** caixa de diálogo caixa, selecione o modelo de exemplo do **modelos de casos de utilização** secção e clique em **seguinte**. Os seguintes passos guiá-lo por meio de utilizar o **criação de perfis de cliente** modelo. Passos são semelhantes para os outros exemplos.

    ![Caixa de diálogo de modelos de fábrica de dados](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. Na **configuração do Data Factory** caixa de diálogo, clique em **próxima** no **Noções básicas de fábrica de dados** página.
5. Sobre o **configurar fábrica de dados** página, efetue os seguintes passos:
   1. Selecione **criar nova fábrica de dados**. Também pode selecionar **utilizar a fábrica de dados existente**.
   2. Introduza um **nome** da fábrica de dados.
   3. Selecione o **subscrição do Azure** no qual pretende ser criada a fábrica de dados.
   4. Selecione o **grupo de recursos** da fábrica de dados.
   5. Selecione o **E.U.A. oeste**, **E.U.A. Leste**, ou **Europa do Norte** para o **região**.
   6. Clique em **Seguinte**.
6. Na **configurar arquivos de dados** , especifique um existente **base de dados SQL do Azure** e **conta de armazenamento do Azure** (ou) crie armazenamento/base de dados e clique em seguinte.
7. Na **configurar computação** página, selecione as predefinições e clique em **próxima**.
8. Na **resumo** página, reveja todas as definições e clique em **próxima**.
9. Na **estado de implementação** página, aguarde até a conclusão da implementação e clique em **concluir**.
10. Clique com o botão direito do rato no Explorador de Soluções e, em seguida, clique em **Publicar**.
11. Se vir a caixa de diálogo **Iniciar sessão na sua conta Microsoft**, introduza as credenciais da conta com a subscrição do Azure e clique em **iniciar sessão**.
12. Deverá ver a seguinte caixa de diálogo:

    ![Caixa de diálogo Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. Na página **Configurar fábrica de dados**, execute os seguintes passos:

    1. Confirme que **utilizar a fábrica de dados existente** opção.
    2. Selecione o **fábrica de dados** tinha o selecione ao utilizar o modelo.
    3. Clique em **Seguinte** para mudar para a página **Publicar Itens**. (Prima **Tecla de Tabulação** para sair do campo Nome se o botão **Seguinte** estiver desativado.)
14. Na página **Publicar Itens**, confirme que todas as entidades dos Data Factories estão selecionadas e clique em **Seguinte** para mudar para a página **Resumo**.     
15. Reveja o resumo e clique em **Seguinte** para iniciar o processo de implementação e ver o **Estado da Implementação**.
16. Na página **Estado da Implementação**, deve ver o estado do processo de implementação. Clique em Concluir após a conclusão da implementação.

Ver [criar a primeira fábrica de dados (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) para obter detalhes sobre como utilizar o Visual Studio para criar entidades do Data Factory e publicá-los para o Azure.          
