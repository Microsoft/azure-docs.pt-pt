---
title: Fábrica de dados do Azure - perguntas mais frequentes
description: Perguntas mais frequentes sobre o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: d9d26ced30f718f06d6d0ba9eb7c2a78682305ad
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58102371"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Fábrica de dados do Azure - perguntas mais frequentes
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [pergunta mais frequente - Data Factory](../frequently-asked-questions.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-data-factory"></a>O que é o Azure Data Factory?
Data Factory é uma cloud com base no serviço de integração de dados que **automatiza o movimento e transformação de dados**. Assim como uma fábrica que opera equipamento para matéria e transformá-la em bens acabados, o Data Factory orquestra serviços existentes que recolhem dados não processados e transformação-los em informações prontas a utilizar.

Fábrica de dados permite-lhe criar fluxos de trabalho condicionados por dados para mover dados entre tanto no local e arquivos de dados na cloud, bem como processar/transformar os dados com serviços de computação, como o Azure HDInsight e Azure Data Lake Analytics. Depois de criar um pipeline que executa a ação que precisa, pode agendar para ser executado periodicamente (hora a hora, diariamente, semanalmente, etc.).   

Para obter mais informações, consulte [descrição geral e conceitos chave](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Onde posso encontrar os detalhes dos preços do Azure Data Factory?
Ver [página de detalhes de preços de fábrica de dados] [ adf-pricing-details] para os detalhes de preços do Azure Data Factory.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Como posso começar a utilizar com o Azure Data Factory?
* Para obter uma descrição geral do Azure Data Factory, veja [introdução ao Azure Data Factory](data-factory-introduction.md).
* Para obter um tutorial sobre como **dados de copiar/mover** a utilizar a atividade de cópia, veja [copiar dados de armazenamento de Blobs do Azure para a base de dados do Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Para obter um tutorial sobre como **transformar dados** com a atividade do Hive do HDInsight. Consulte [processar dados executando o script do Hive num cluster de Hadoop](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>O que é a disponibilidade das regiões do Data Factory?
Data Factory estar disponível no **E.u.a. oeste** e **Europa do Norte**. Os serviços de computação e armazenamento utilizados pelo fábricas de dados podem estar noutras regiões. Ver [regiões suportadas](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Quais são os limites no número de dados fábricas/pipelines/atividades/conjuntos de dados?
Ver **limites do Azure Data Factory** secção a [subscrição do Azure e limites do serviço, Quotas e restrições](../../azure-subscription-service-limits.md#data-factory-limits) artigo.

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>O que é a experiência de criação/programador com o serviço Azure Data Factory?
Pode autor/criar fábricas de dados através de uma das ferramentas/SDKs seguintes:

* **Portal do Azure** painéis do Data Factory no portal do Azure fornecem a interface do usuário avançada para a criação de serviços de ad ligado de fábricas de dados. O **Editor do Data Factory**, que também faz parte do portal, pode facilmente criar serviços ligados, tabelas, conjuntos de dados e pipelines com a especificação de definições de JSON para esses artefactos. Ver [criar seu primeiro pipeline de dados com o portal do Azure](data-factory-build-your-first-pipeline-using-editor.md) para um exemplo de como utilizar o portal/editor para criar e implementar uma fábrica de dados.
* **Visual Studio** pode utilizar o Visual Studio para criar uma fábrica de dados do Azure. Ver [criar seu primeiro pipeline de dados com o Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) para obter detalhes.
* **O Azure PowerShell** veja [criar e monitor do Azure Data Factory com o Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) para um tutorial/passo a passo para criar uma fábrica de dados com o PowerShell. Ver [referência de Cmdlet do Data Factory] [ adf-powershell-reference] conteúdo na biblioteca MSDN para obter uma documentação completa de cmdlets do Data Factory.
* **Biblioteca de classes .NET** pode criar fábricas de dados programaticamente ao utilizar o SDK .NET do Data Factory. Ver [criar, monitorizar e gerir fábricas de dados com o .NET SDK](data-factory-create-data-factories-programmatically.md) para obter instruções sobre a criação de uma fábrica de dados com o .NET SDK. Ver [dados de referência da biblioteca de classe de fábrica] [ msdn-class-library-reference] para obter uma documentação completa do SDK .NET do Data Factory.
* **REST API** também pode utilizar a API REST exposta pelo serviço do Azure Data Factory para criar e implementar as fábricas de dados. Ver [referência de API de REST do Data Factory] [ msdn-rest-api-reference] para uma documentação abrangente de API de REST do Data Factory.
* **Modelo Azure Resource Manager** consulte [Tutorial: Criar a primeira fábrica de dados do Azure com o modelo Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md) detalhes fo.

### <a name="can-i-rename-a-data-factory"></a>Posso renomear uma fábrica de dados?
Não. Como os outros recursos do Azure, não é possível alterar o nome de uma fábrica de dados do Azure.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Posso mover uma fábrica de dados de uma subscrição do Azure para outra?
Sim. Utilize o **mover** botão no seu painel de fábrica de dados, conforme mostrado no diagrama seguinte:

![Mover a fábrica de dados](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Quais são os ambientes de computação suportados pelo Data Factory?
A tabela seguinte fornece uma lista dos ambientes de computação suportados pela fábrica de dados e as atividades que podem ser executadas nos mesmos.

| Ambiente de computação | atividades |
| --- | --- |
| [Cluster de HDInsight a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) ou [seu próprio cluster do HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Atividades do Machine Learning: Execução de lotes e atualizar recurso](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure SQL Data Warehouse](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), [SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Procedimento Armazenado](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Como é que o Azure Data Factory compara com o SQL Server Integration Services (SSIS)? 
Consulte o [fábrica de dados do Azure vs. SSIS](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) apresentação de um dos nossos MVPs (Most Valued Professionals): Reza Rad. Algumas das alterações recentes na fábrica de dados não podem estar relacionadas no conjunto de slides. Estamos continuamente a adicionar mais recursos ao Azure Data Factory. Estamos continuamente a adicionar mais recursos ao Azure Data Factory. Podemos irá incorporar essas atualizações a comparação de tecnologias de integração de dados da Microsoft algum tempo ainda este ano.   

## <a name="activities---faq"></a>Atividades - FAQ
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Quais são os diferentes tipos de atividades, que pode usar num pipeline do Data Factory?
* [Atividades de movimento de dados](data-factory-data-movement-activities.md) para mover dados.
* [Atividades de transformação de dados](data-factory-data-transformation-activities.md) para processar/transformar os dados.

### <a name="when-does-an-activity-run"></a>Quando é executada uma atividade?
O **disponibilidade** determina de definição de configuração na tabela de dados de saída quando a atividade é executada. Se os conjuntos de dados de entrada forem especificados, a atividade verifica se a todas as dependências de dados de entrada são cumpridas (ou seja, **pronto** Estado) antes de começar a ser executado.

## <a name="copy-activity---faq"></a>Atividade Copy - FAQ
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>É melhor ter um pipeline com várias atividades ou por um pipeline separado para cada atividade?
Pipelines devem para agrupar atividades relacionadas. Se os conjuntos de dados que os ligam não são consumidos por qualquer outra atividade fora do pipeline, pode manter as atividades num pipeline. Dessa forma, não terá períodos de ativos de pipeline de cadeia, de modo que eles se alinham uns com os outros. Além disso, a integridade dos dados nas tabelas internas para o pipeline é preservada melhor ao atualizar o pipeline. Atualização do pipeline, essencialmente, interrompe todas as atividades no pipeline, remove-los e cria-los novamente. Da perspectiva de criação, também pode ser mais fácil ver o fluxo de dados dentro de atividades relacionadas num arquivo JSON do pipeline.

### <a name="what-are-the-supported-data-stores"></a>Quais são os arquivos de dados suportados?
A Atividade de Cópia no Data Factory copia os dados de um arquivo de dados de origem para um arquivo de dados sink. O Data Factory suporta os arquivos de dados seguintes. Os dados de qualquer origem podem ser escritos em qualquer sink. Clique num arquivo de dados para saber como copiar dados de e para esse arquivo.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Os arquivos de dados com * podem estar no local ou no Azure IaaS e requerem a instalação do [Data Management Gateway](data-factory-data-management-gateway.md) num computador no local/Azure IaaS.

### <a name="what-are-the-supported-file-formats"></a>Quais são os formatos de ficheiro suportados?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Onde é executada a operação de cópia?
Ver [movimento de dados globalmente disponível](data-factory-data-movement-activities.md#global) secção para obter detalhes. Em resumo, quando está envolvido num arquivo de dados no local, a operação de cópia é realizada pelo Gateway de gestão de dados no seu ambiente no local. Além disso, quando o movimento de dados entre dois arquivos em nuvem, a operação de cópia é executada na região mais próxima para a localização de sink na mesma geografia.

## <a name="hdinsight-activity---faq"></a>Atividade do HDInsight - FAQ
### <a name="what-regions-are-supported-by-hdinsight"></a>Que regiões são suportadas pelo HDInsight?
Consulte a secção de disponibilidade geográfica no seguinte artigo: ou [detalhes dos preços do HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Em que região é usado por um cluster do HDInsight a pedido?
O cluster de HDInsight a pedido é criado na mesma região onde existe o armazenamento que especificou para ser utilizado com o cluster.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Como associar as contas de armazenamento adicionais ao seu cluster do HDInsight?
Se estiver a utilizar o seu próprio Cluster do HDInsight (BYOC - traga seu próprio Cluster), consulte os seguintes tópicos:

* [Utilizar um Cluster do HDInsight com contas de armazenamento alternativo e Metastores][hdinsight-alternate-storage]
* [Utilizar contas de armazenamento adicionais com o Hive do HDInsight][hdinsight-alternate-storage-2]

Se estiver a utilizar um cluster a pedido que é criado pelo serviço Data Factory, especifique as contas de armazenamento adicional para o HDInsight serviço ligado para que o serviço Data Factory pode registá-los em seu nome. Na definição de JSON para o serviço ligado a pedido, utilize **additionalLinkedServiceNames** propriedade para especificar as contas de armazenamento alternativo, conforme mostrado no fragmento JSON seguinte:

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
No exemplo acima, otherLinkedServiceName1 e otherLinkedServiceName2 representam serviços ligados, cujas definições contêm as credenciais que o cluster do HDInsight tem de aceder a contas de armazenamento alternativo.

## <a name="slices---faq"></a>Setores - FAQ
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Por que são meus setores de entrada não está no estado pronto?
Um erro comum é a configuração não **externo** propriedade **verdadeiro** no conjunto de dados entrado quando os dados de entrada são externos à fábrica de dados (não produzida pela fábrica de dados).

No exemplo a seguir, só precisa definir **externo** como verdadeiro no **dataset1**.  

**DataFactory1** Pipeline 1: dataset1 -> activity1 -> dataset2-activity2 > -> dataset3 Pipeline 2: dataset3 -> activity3 -> dataset4

Se tiver outro fábrica de dados com um pipeline que usa dataset4 (produzidos pelo pipeline 2 no data factory 1), marque dataset4 como um conjunto de dados externo porque o conjunto de dados é produzido por uma fábrica de dados diferentes (DataFactory1, não DataFactory2).  

**DataFactory2**    
Pipeline 1: dataset4 activity4 -> -> dataset5

Se a propriedade externa está definida corretamente, verifique se os dados de entrada existem na localização especificada na definição do conjunto de dados de entrada.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Como executar um setor em outro momento do que meia-noite, quando o setor está a ser produzido diariamente?
Utilize o **deslocamento** propriedade para especificar o tempo em que pretende que o setor para ser produzido. Ver [conjunto de dados disponibilidade](data-factory-create-datasets.md#dataset-availability) secção para obter detalhes sobre esta propriedade. Eis um exemplo rápido:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Iniciar o setores diárias **6 da Manhã** em vez da meia-noite de predefinição.     

### <a name="how-can-i-rerun-a-slice"></a>Como posso executar um setor?
Pode voltar a executar um setor de uma das seguintes formas:

* Utilize Gerir aplicação de monitorização e voltar a executar uma janela de atividade ou do setor. Ver [volte a executar selecionado janelas de atividade](data-factory-monitor-manage-app.md#perform-batch-actions) para obter instruções.   
* Clique em **execute** na barra de comandos no **SETOR de dados** painel para o setor no portal do Azure.
* Execute **Set-AzDataFactorySliceStatus** cmdlet com o estado definido como **aguardando** para o setor.   

    ```PowerShell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  Ver [Set-AzDataFactorySliceStatus] [ set-azure-datafactory-slice-status] para obter detalhes sobre o cmdlet.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Quanto tempo demorou a processar um setor?
Utilize o Explorador de janelas de atividade em monitorizar e gerir aplicações para saber quanto tempo demorou a processar um setor de dados. Ver [Explorador de janelas de atividade](data-factory-monitor-manage-app.md#activity-window-explorer) para obter detalhes.

Também pode fazer o seguinte no portal do Azure:  

1. Clique em **conjuntos de dados** mosaico a **DATA FACTORY** painel fábrica de dados.
2. Clique com o conjunto de dados específico no **conjuntos de dados** painel.
3. Selecione o setor que está interessado em do **setores recentes** lista o **tabela** painel.
4. Clique na atividade execute a partir da **execuções de atividades** lista o **SETOR de dados** painel.
5. Clique em **propriedades** mosaico a **detalhes da execução da ATIVIDADE** painel.
6. Deverá ver o **duração** campo com um valor. Este valor é o tempo necessário para processar o setor.   

### <a name="how-to-stop-a-running-slice"></a>Como parar um setor em execução?
Se precisar de interromper o pipeline de execução, pode usar [Suspend-AzDataFactoryPipeline](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) cmdlet. Atualmente, suspender o pipeline não a interromperá se o setor de execuções em curso. Depois de concluir as execuções em curso, nenhum setor extra é escolhido.

Se realmente desejar parar todas as execuções imediatamente, a única forma seria excluir o pipeline e criá-la novamente. Se optar por eliminar o pipeline, não é necessário eliminar tabelas e serviços ligados, utilizados pelo pipeline.

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
