---
title: Fábrica de Dados Azure - Amostras
description: Fornece detalhes sobre as amostras que enviam com o serviço Azure Data Factory.
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
ms.openlocfilehash: 2b0238d8026bff435fce9c03b6517efb6a55311d
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84019916"
---
# <a name="azure-data-factory---samples"></a>Fábrica de Dados Azure - Amostras
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [amostras powerShell na Data Factory](../samples-powershell.md) e [colhe amostras na galeria De amostras](https://azure.microsoft.com/resources/samples/?service=data-factory)de código azul .


## <a name="samples-on-github"></a>Exemplos no GitHub
O [repositório GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) contém várias amostras que o ajudam a acelerar rapidamente com o serviço Azure Data Factory (ou) modificar os scripts e usá-lo na própria aplicação. A pasta Samples\JSON contém snippets JSON para cenários comuns.

| Sample | Descrição |
|:--- |:--- |
| [ADF Walkthrough](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFWalkthrough) |Esta amostra fornece um walkthrough de ponta a ponta para o processamento de ficheiros de registo utilizando a Azure Data Factory para transformar os dados dos ficheiros de log em insights. <br/><br/>Neste passeio, o pipeline Data Factory recolhe registos de amostras, processa e enriquece os dados a partir de registos com dados de referência, e transforma os dados para avaliar a eficácia de uma campanha de marketing que foi recentemente lançada. |
| [Amostras JSON](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON) |Esta amostra fornece exemplos JSON para cenários comuns. |
| [Amostra de download de dados de http](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample) |Esta amostra mostra o download de dados de um ponto final http para o Armazenamento De Blob Azure usando a atividade personalizada .NET. |
| [Amostra de atividade do ponto de aplicação cross](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Esta amostra permite-lhe autor de uma atividade personalizada .NET que não está limitada às versões de montagem utilizadas pelo lançador ADF (Por exemplo, WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, etc.). |
| [Executar script R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) |Esta amostra inclui a atividade personalizada da Fábrica de Dados que pode ser usada para invocar RScript.exe. Esta amostra funciona apenas com o seu próprio cluster HDInsight (não a pedido) que já tem R Instalado nele. |
| [Invoque empregos de faísca no cluster Hadoop HDInsight](https://docs.microsoft.com/azure/data-factory/tutorial-transform-data-spark-portal) |Esta amostra mostra como usar a atividade MapReduce para invocar um programa Spark. O programa de faíscas apenas copia dados de um contentor Azure Blob para outro. |
| [Análise do Twitter utilizando atividade de pontuação de lote de aprendizagem automática azure](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Esta amostra mostra como usar a AzureMLBatchScoringActivity para invocar um modelo de Aprendizagem automática Azure que executa análise de sentimentos no Twitter, pontuação, previsão etc. |
| [Análise do Twitter usando atividade personalizada](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Esta amostra mostra como usar uma atividade personalizada .NET para invocar um modelo de Aprendizagem automática Azure que executa análise de sentimentos no Twitter, pontuação, previsão etc. |
| [Gasodutos parametrizados para aprendizagem automática azure](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ParameterizedPipelinesForAzureML) |A amostra fornece um código C# extremo-a-extremo para implantar os gasodutos N para pontuar e retreinar cada um com um parâmetro regional diferente onde a lista de regiões provém de um ficheiro parameters.txt, que está incluído nesta amostra. |
| [Atualização de dados de referência para trabalhos de Análise de Fluxo de Azure](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs) |Esta amostra mostra como utilizar a Azure Data Factory e a Azure Stream Analytics em conjunto para executar as consultas com dados de referência e configurar a atualização para dados de referência numa programação. |
| [Gasoduto Híbrido com Hortonworks Hadoop no local](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HybridPipelineWithOnPremisesHortonworksHadoop) |A amostra usa um cluster Hadoop no local como um alvo de computação para executar trabalhos na Data Factory, tal como adicionaria outros alvos de computação como um cluster Hadoop baseado em HDInsight na nuvem. |
| [Ferramenta de conversão JSON](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSONConversionTool) |Esta ferramenta permite converter JSONs da versão anterior à pré-visualização de 2015-07-01 para a pré-visualização mais recente ou 2015-07-01 (predefinição). |
| [Ficheiro de entrada da amostra U-SQL](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/U-SQL%20Sample%20Input%20File) |Este ficheiro é um ficheiro de amostra utilizado por uma atividade U-SQL. |
| [Eliminar ficheiro blob](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity) | Esta amostra apresenta um ficheiro C# que pode ser utilizado como parte da atividade personalizada aDf .net para eliminar ficheiros da localização de origem Azure Blob uma vez copiados os ficheiros.|

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager
Pode encontrar os seguintes modelos do Gestor de Recursos Azure para data factory no GitHub.

| Modelo | Descrição |
| --- | --- |
| [Cópia do armazenamento de Blob Azure para base de dados Azure SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |A implementação deste modelo cria uma fábrica de dados Azure com um pipeline que copia dados do armazenamento de blob Azure especificado para a base de dados Azure SQL |
| [Cópia da Salesforce para o Armazenamento De Blob Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |A implementação deste modelo cria uma fábrica de dados Azure com um pipeline que copia dados da conta de Salesforce especificada para o armazenamento de blob Azure. |
| [Transforme os dados executando o script da Hive num cluster Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |A implementação deste modelo cria uma fábrica de dados Azure com um pipeline que transforma dados executando o script da amostra da Hive num cluster Hadoop Azure HDInsight. |

## <a name="samples-in-azure-portal"></a>Amostras no portal Azure
Pode utilizar o azulejo **da Amostra** na página inicial da sua fábrica de dados para implantar pipelines de amostras e suas entidades associadas (conjuntos de dados e serviços ligados) na sua fábrica de dados.

1. Criar uma fábrica de dados ou abrir uma fábrica de dados existente. Consulte [os dados de Cópia do Armazenamento Blob para a Base de Dados SQL utilizando](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) a Data Factory para obter medidas para criar uma fábrica de dados.
2. Na lâmina **DATA FACTORY** para a fábrica de dados, clique no azulejo da **amostra.**

    ![Amostra de azulejos de gasodutos](./media/data-factory-samples/SamplePipelinesTile.png)
3. Na lâmina de **gasodutos Sample,** clique na **amostra** que pretende implementar.

    ![Lâmina de gasodutos de amostra](./media/data-factory-samples/SampleTile.png)
4. Especifique as definições de configuração para a amostra. Por exemplo, o nome da sua conta de armazenamento Azure e chave de conta, nome lógico do servidor SQL, base de dados, ID do utilizador e palavra-passe, etc.

    ![Lâmina de amostra](./media/data-factory-samples/SampleBlade.png)
5. Depois de terminar com a especificação das definições de configuração, clique em **Criar/implementar** os gasodutos de amostra e serviços/tabelas ligados utilizados pelos gasodutos.
6. Vê o estado de implantação no azulejo da amostra clicou anteriormente na lâmina do **gasoduto Sample.**

    ![Estado da implementação](./media/data-factory-samples/DeploymentStatus.png)
7. Quando vir a mensagem **de implantação bem sucedida** no azulejo para a amostra, feche a lâmina de **gasodutos sample.**  
8. Na lâmina **DATA FACTORY,** vê-se que os serviços ligados, conjuntos de dados e oleodutos são adicionados à sua fábrica de dados.  

    ![Painel Data Factory](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Amostras em Estúdio Visual
### <a name="prerequisites"></a>Pré-requisitos
Tem de ter o seguinte instalado no computador:

* Visual Studio 2013 ou Visual Studio 2015
* Transfira o Azure SDK para o Visual Studio 2013 ou Visual Studio 2015. Navegue até à [Página de Transferências do Azure](https://azure.microsoft.com/downloads/) e clique em **VS 2013** ou **VS 2015** na secção **.NET**.
* Transfira o plug-in mais recente do Azure Data Factory para o Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Se estiver a utilizar o Visual Studio 2013, também pode atualizar o plugin fazendo os seguintes passos: No menu, clique em **Tools**  ->  **Tools Extensions and Updates**  ->  **Online**  ->  **Visual Studio Gallery**Microsoft  ->  **Azure Data Factory Tools for Visual Studio**  ->  **Update**.

### <a name="use-data-factory-templates"></a>Utilizar modelos de fábrica de dados
1. Clique em **File** no menu, aponte para **Novo**, e clique em **Projeto**.
2. Na caixa de diálogo **Novo Projeto**, siga os seguintes passos:

   1. Selecione **DataFactory** em **modelos**.
   2. Selecione **modelos** de fábrica de dados no painel direito.
   3. Introduza um **nome** para o projeto.
   4. Selecione um **local** para o projeto.
   5. Clique em **OK**.

      ![Caixa de diálogo Novo Projeto](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. Na caixa de diálogo de **modelos** de fábrica de dados, selecione o modelo de amostra da secção **de modelos de caso de utilização** e clique **em Seguinte**. Os seguintes passos passam por você usando o modelo de **Perfil do Cliente.** Os passos são semelhantes para as outras amostras.

    ![Data Factory Templates log box](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. No diálogo de configuração da fábrica de **dados,** clique em **Seguinte** na página Debase da Fábrica de **Dados.**
5. Na página de fábrica de **dados Configure,** faça os seguintes passos:
   1. Selecione **Criar nova fábrica**de dados . Também pode selecionar Utilize a fábrica de **dados existente.**
   2. Insira um **nome** para a fábrica de dados.
   3. Selecione a **subscrição Azure** na qual pretende criar a fábrica de dados.
   4. Selecione o **grupo de recursos** para a fábrica de dados.
   5. Selecione os **EUA Ocidentais,** **Os EUA Orientais**ou norte da **Europa** para a **região.**
   6. Clique em **Seguinte**.
6. Na página de lojas de **dados Configure,** especifique uma base de **dados Azure SQL** existente e a conta de **armazenamento Azure** (ou) crie base de dados/armazenamento e clique em Seguinte.
7. Na página **de cálculo Configure,** selecione predefinições e clique **em Next**.
8. Na página **Resumo,** reveja todas as definições e clique em **Next**.
9. Na página 'Estado de **Implementação',** aguarde até que a implementação esteja concluída e clique em **Terminar**.
10. Clique com o botão direito do rato no Explorador de Soluções e, em seguida, clique em **Publicar**.
11. Se vir a caixa de diálogo **Iniciar sessão na sua conta Microsoft**, introduza as credenciais da conta com a subscrição do Azure e clique em **iniciar sessão**.
12. Deverá ver a seguinte caixa de diálogo:

    ![Caixa de diálogo Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. Na página **Configurar fábrica de dados**, execute os seguintes passos:

    1. Confirme que utilize a opção de fábrica de **dados existente.**
    2. Selecione a fábrica de **dados** que selecionou ao utilizar o modelo.
    3. Clique em **Seguinte** para mudar para a página **Publicar Itens**. (Prima **Tecla de Tabulação** para sair do campo Nome se o botão **Seguinte** estiver desativado.)
14. Na página **Publicar Itens**, confirme que todas as entidades dos Data Factories estão selecionadas e clique em **Seguinte** para mudar para a página **Resumo**.     
15. Reveja o resumo e clique em **Seguinte** para iniciar o processo de implementação e ver o **Estado da Implementação**.
16. Na página **Estado da Implementação**, deve ver o estado do processo de implementação. Clique em Concluir após a conclusão da implementação.

Consulte a Build a sua primeira fábrica de [dados (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) para obter detalhes sobre a utilização do Visual Studio para autores de entidades data Factory e publicá-las no Azure.          
