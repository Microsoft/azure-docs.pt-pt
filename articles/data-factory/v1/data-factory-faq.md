---
title: Azure Data Factory-perguntas frequentes
description: Perguntas frequentes sobre Azure Data Factory.
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
ms.openlocfilehash: 1b042c0279e458cf83bd5c81147f4b1f25d7687d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645145"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory-perguntas frequentes
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte perguntas [frequentes-data Factory](../frequently-asked-questions.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-data-factory"></a>O que é o Azure Data Factory?
Data Factory é um serviço de integração de dados baseado em nuvem que **automatiza a movimentação e a transformação de dados**. Assim como uma fábrica que executa equipamentos para pegar matérias-primas e transformá-las em bens acabados, Data Factory orquestra os serviços existentes que coletam dados brutos e os transformam em informações prontas para uso.

Data Factory permite que você crie fluxos de trabalho controlados por dados para mover dados entre armazenamentos de dados locais e na nuvem, bem como processar/transformar dados usando serviços de computação, como o Azure HDInsight e o Azure Data Lake Analytics. Depois de criar um pipeline que executa a ação de que você precisa, você pode agendá-lo para execução periódica (por hora, diariamente, semanalmente, etc.).   

Para obter mais informações, consulte [visão geral & conceitos principais](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Onde posso encontrar detalhes de preços para Azure Data Factory?
Consulte [Data Factory página de detalhes de preços][adf-pricing-details] para obter os detalhes de preços do Azure data Factory.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Como fazer introdução ao Azure Data Factory?
* Para obter uma visão geral do Azure Data Factory, consulte [introdução ao Azure data Factory](data-factory-introduction.md).
* Para obter um tutorial sobre como **copiar/mover dados** usando a atividade de cópia, consulte [copiar dados do armazenamento de BLOBs do Azure para o banco de dados SQL do Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Para obter um tutorial sobre como **transformar dados** usando a atividade hive do HDInsight. Consulte [processar dados executando o script do hive no cluster Hadoop](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Qual é a disponibilidade da região do Data Factory?
Data Factory está disponível no **oeste dos EUA** e **Europa setentrional**. Os serviços de computação e armazenamento usados pelas fábricas de dados podem estar em outras regiões. Consulte [regiões com suporte](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Quais são os limites de número de fábricas/pipelines/atividades/conjuntos de dados?
Consulte a seção **Azure data Factory limites** do artigo [assinatura e limites de serviço, cotas e restrições do Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) .

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>O que é a experiência de criação/desenvolvedor com o serviço de Azure Data Factory?
Você pode criar fábricas de dados usando uma das seguintes ferramentas/SDKs:

* **Visual Studio** Você pode usar o Visual Studio para criar uma data factory do Azure. Consulte [criar seu primeiro pipeline de dados usando o Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) para obter detalhes.
* **Azure PowerShell** Confira [criar e monitorar Azure data Factory usando o Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) para obter um tutorial/instruções para criar um data Factory usando o PowerShell. Consulte [Data Factory][adf-powershell-reference] o conteúdo de referência do cmdlet na biblioteca MSDN para obter uma documentação abrangente dos cmdlets data Factory.
* **Biblioteca de classes .net** Você pode criar fábricas de dados programaticamente usando Data Factory SDK do .NET. Consulte [criar, monitorar e gerenciar fábricas de dados usando o SDK do .net](data-factory-create-data-factories-programmatically.md) para obter uma explicação sobre como criar um data Factory usando o SDK do .net. Consulte [Data Factory referência da biblioteca de classes][msdn-class-library-reference] para obter uma documentação abrangente de data Factory SDK do .net.
* **API REST** Você também pode usar a API REST exposta pelo serviço de Azure Data Factory para criar e implantar fábricas de dados. Consulte [Data Factory referência da API REST][msdn-rest-api-reference] para obter uma documentação abrangente de data Factory API REST.
* **Modelo de Azure Resource Manager** Consulte [o tutorial: criar seu primeiro data Factory do Azure usando Azure Resource Manager modelo](data-factory-build-your-first-pipeline-using-arm.md) fo detalhes.

### <a name="can-i-rename-a-data-factory"></a>Posso renomear uma data factory?
Não. Como outros recursos do Azure, o nome de uma data factory do Azure não pode ser alterado.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Posso mover uma data factory de uma assinatura do Azure para outra?
Sim. Use o botão **mover** na folha data Factory, conforme mostrado no diagrama a seguir:

![Mover data factory](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Quais são os ambientes de computação com suporte pelo Data Factory?
A tabela a seguir fornece uma lista de ambientes de computação com suporte pelo Data Factory e as atividades que podem ser executadas neles.

| Ambiente de computação | atividades |
| --- | --- |
| [Cluster Hdinsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) ou [seu próprio cluster hdinsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[Dotnet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [streaming do Hadoop](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Atividades de Machine Learning: Execução de Lotes e Atualizar Recurso](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure SQL Data Warehouse](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), [SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Procedimento Armazenado](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Como Azure Data Factory comparar com SQL Server Integration Services (SSIS)? 
Consulte a apresentação [Azure data Factory versus SSIS](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) de um de nossos MVPs (profissionais mais valorizados): reza Rad. Algumas das alterações recentes no Data Factory podem não estar listadas no deck de slides. Estamos continuamente adicionando mais recursos para Azure Data Factory. Estamos continuamente adicionando mais recursos para Azure Data Factory. Vamos incorporar essas atualizações à comparação de tecnologias de integração de dados da Microsoft em um momento posterior neste ano.   

## <a name="activities---faq"></a>Atividades-perguntas frequentes
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Quais são os diferentes tipos de atividades que você pode usar em um pipeline Data Factory?
* [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para mover dados.
* [Atividades de transformação de dados](data-factory-data-transformation-activities.md) para processar/transformar dados.

### <a name="when-does-an-activity-run"></a>Quando uma atividade é executada?
A definição de configuração de **disponibilidade** na tabela de dados de saída determina quando a atividade é executada. Se os conjuntos de dados de entrada forem especificados, a atividade verificará se todas as dependências de dado de entrada estão satisfeitas (ou seja, estado **pronto** ) antes de iniciar a execução.

## <a name="copy-activity---faq"></a>Atividade de cópia-perguntas frequentes
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>É melhor ter um pipeline com várias atividades ou um pipeline separado para cada atividade?
Os pipelines devem agrupar as atividades relacionadas. Se os conjuntos de valores que os conectam não forem consumidos por nenhuma outra atividade fora do pipeline, você poderá manter as atividades em um pipeline. Dessa forma, você não precisaria encadear os períodos ativos de pipeline para que eles fiquem alinhados uns com os outros. Além disso, a integridade dos dados nas tabelas internas para o pipeline é melhor preservada ao atualizar o pipeline. A atualização de pipeline essencialmente interrompe todas as atividades no pipeline, remove-as e as cria novamente. Da perspectiva de criação, também pode ser mais fácil ver o fluxo de dados dentro das atividades relacionadas em um arquivo JSON para o pipeline.

### <a name="what-are-the-supported-data-stores"></a>Quais são os armazenamentos de dados com suporte?
A Atividade de Cópia no Data Factory copia os dados de um arquivo de dados de origem para um arquivo de dados sink. O Data Factory suporta os arquivos de dados seguintes. Os dados de qualquer origem podem ser escritos em qualquer sink. Clique num arquivo de dados para saber como copiar dados de e para esse arquivo.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Os arquivos de dados com * podem estar no local ou no Azure IaaS e requerem a instalação do [Data Management Gateway](data-factory-data-management-gateway.md) num computador no local/Azure IaaS.

### <a name="what-are-the-supported-file-formats"></a>Quais são os formatos de arquivo com suporte?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Onde a operação de cópia é executada?
Consulte a seção de [movimentação de dados disponível globalmente](data-factory-data-movement-activities.md#global) para obter detalhes. Em suma, quando um armazenamento de dados local está envolvido, a operação de cópia é executada pelo gateway de Gerenciamento de Dados em seu ambiente local. E, quando a movimentação de dados está entre duas lojas de nuvem, a operação de cópia é executada na região mais próxima do local do coletor na mesma geografia.

## <a name="hdinsight-activity---faq"></a>Atividade do HDInsight-perguntas frequentes
### <a name="what-regions-are-supported-by-hdinsight"></a>Quais regiões têm suporte no HDInsight?
Consulte a seção de disponibilidade geográfica no seguinte artigo: ou [detalhes de preços do HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Qual região é usada por um cluster HDInsight sob demanda?
O cluster HDInsight sob demanda é criado na mesma região em que o armazenamento que você especificou para ser usado com o cluster existe.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Como associar contas de armazenamento adicionais ao cluster HDInsight?
Se você estiver usando seu próprio cluster HDInsight (BYOC-traga seu próprio cluster), consulte os seguintes tópicos:

* [Usando um cluster HDInsight com metastores e contas de armazenamento alternativas][hdinsight-alternate-storage]
* [Usar contas de armazenamento adicionais com o hive do HDInsight][hdinsight-alternate-storage-2]

Se você estiver usando um cluster sob demanda que é criado pelo serviço de Data Factory, especifique contas de armazenamento adicionais para o serviço vinculado do HDInsight para que o serviço de Data Factory possa registrá-los em seu nome. Na definição de JSON para o serviço vinculado sob demanda, use a propriedade **additionalLinkedServiceNames** para especificar contas de armazenamento alternativas, conforme mostrado no trecho de JSON a seguir:

```JSON
{
    "name": "MyHDInsightOnDemandLinkedService",
    "properties":
    {
        "type": "HDInsightOnDemandLinkedService",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "LinkedService-SampleData",
            "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ]
        }
    }
}
```
No exemplo acima, otherLinkedServiceName1 e otherLinkedServiceName2 representam serviços vinculados cujas definições contêm credenciais que o cluster HDInsight precisa para acessar contas de armazenamento alternativas.

## <a name="slices---faq"></a>Fatias-perguntas frequentes
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Por que minhas fatias de entrada não estão no estado pronto?
Um erro comum não é definir a propriedade **external** como **true** no conjunto de dados de entrada quando os dado de entrada são externos ao data Factory (não produzido pelo data Factory).

No exemplo a seguir, você só precisa definir **external** como true no **dataSet1**.  

**DataFactory1** Pipeline 1: dataSet1-> Atividade1-> dataset2-> da atividade2-> dataset3 pipeline 2: dataset3-> a atividade3 começará-> dataset4

Se você tiver outro data factory com um pipeline que usa dataset4 (produzido pelo pipeline 2 no data factory 1), marque dataset4 como um conjunto de um conjuntos de uma, pois o conjunto de os é produzido por um data factory diferente (DataFactory1, não DataFactory2).  

**DataFactory2**    
Pipeline 1: dataset4-> a atividade4-> dataset5

Se a propriedade external estiver definida corretamente, verifique se os dados de entrada existem no local especificado na definição do conjunto de dados de entrada.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Como executar uma fatia em outro horário da meia-noite quando a fatia está sendo produzida diariamente?
Use a propriedade **offset** para especificar a hora em que você deseja que a fatia seja produzida. Consulte a seção [disponibilidade do conjunto](data-factory-create-datasets.md#dataset-availability) de informações para obter detalhes sobre essa propriedade. Este é um exemplo rápido:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
As fatias diárias começam às **6h** , em vez da meia-noite padrão.     

### <a name="how-can-i-rerun-a-slice"></a>Como posso executar novamente uma fatia?
Você pode executar novamente uma fatia de uma das seguintes maneiras:

* Use o aplicativo monitorar e gerenciar para executar novamente uma janela de atividade ou uma fatia. Consulte [executar novamente as janelas de atividades selecionadas](data-factory-monitor-manage-app.md#perform-batch-actions) para obter instruções.   
* Clique em **executar** na barra de comandos na folha **fatia de dados** para a fatia no portal do Azure.
* Execute o cmdlet **set-AzDataFactorySliceStatus** com o status definido como **aguardando** a fatia.   

    ```powershell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  Consulte [set-AzDataFactorySliceStatus][set-azure-datafactory-slice-status] para obter detalhes sobre o cmdlet.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Quanto tempo foi levado para processar uma fatia?
Use o Gerenciador de janelas de atividades no monitor & gerenciar o aplicativo para saber quanto tempo demorou para processar uma fatia de dados. Consulte [Gerenciador de janelas de atividades](data-factory-monitor-manage-app.md#activity-window-explorer) para obter detalhes.

Você também pode fazer o seguinte no portal do Azure:  

1. Clique no bloco **conjuntos** de dados na folha **Data FACTORY** para sua data Factory.
2. Clique no conjunto de informações específico na folha **conjuntos de valores** .
3. Selecione a fatia na qual você está interessado na lista de **fatias recentes** na folha **tabela** .
4. Clique **na lista execução da atividade na** folha **fatia de dados** .
5. Clique no bloco **Propriedades** na folha **detalhes da execução da atividade** .
6. Você deve ver o campo **duração** com um valor. Esse valor é o tempo necessário para processar a fatia.   

### <a name="how-to-stop-a-running-slice"></a>Como interromper uma fatia em execução?
Se precisar interromper a execução do pipeline, você poderá usar o cmdlet [Suspend-AzDataFactoryPipeline](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) . Atualmente, suspender o pipeline não interrompe as execuções de fatia que estão em andamento. Depois que as execuções em andamento forem concluídas, nenhuma fatia extra será coletada.

Se você realmente quiser interromper todas as execuções imediatamente, a única maneira seria excluir o pipeline e criá-lo novamente. Se você optar por excluir o pipeline, não será necessário excluir tabelas e serviços vinculados usados pelo pipeline.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/module/az.datafactory/
[azure-portal]: https://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/module/az.datafactory/set-Azdatafactoryslicestatus

[adf-pricing-details]: https://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: https://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: https://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
