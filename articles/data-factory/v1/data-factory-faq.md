---
title: Azure Data Factory - Perguntas frequentes
description: Perguntas frequentes sobre a Azure Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 450a12ae5ae1e13c9cc9b5599a41b6c29d740a9f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785330"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory - Perguntas frequentes
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [a pergunta mais frequente - Data Factory](../frequently-asked-questions.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-data-factory"></a>O que é o Azure Data Factory?
Data Factory é um serviço de integração de dados baseado na nuvem que **automatiza o movimento e transformação de dados.** Tal como uma fábrica que gere equipamentos para pegar em matérias-primas e transformá-las em bens acabados, a Data Factory orquestra os serviços existentes que recolhem dados brutos e os transformam em informação pronta a usar.

A Data Factory permite criar fluxos de trabalho baseados em dados para mover dados entre lojas de dados no local e em nuvem, bem como processar/transformar dados usando serviços computativos como a Azure HDInsight e a Azure Data Lake Analytics. Depois de criar um oleoduto que execute a ação de que necessita, pode programar periodicamente (hora, dia, semanais, etc.).   

Para mais informações, consulte [a visão geral & Conceitos-Chave.](data-factory-introduction.md)

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Onde posso encontrar detalhes sobre preços para a Azure Data Factory?
Consulte [a página de detalhes de preços da fábrica de dados][adf-pricing-details] para ver os detalhes dos preços da Fábrica de Dados Azure.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Como é que começo com a Azure Data Factory?
* Para uma visão geral da Azure Data Factory, consulte [a Introdução à Fábrica de Dados Azure.](data-factory-introduction.md)
* Para obter um tutorial sobre como **copiar/mover dados** utilizando a Copy Activity, consulte [os dados da Cópia do Armazenamento de Blob Azure para a Base de Dados Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Para um tutorial sobre como **transformar dados** usando a Atividade de Colmeia HDInsight. Consulte [os dados do processo executando o script da Hive no cluster Hadoop](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Qual é a disponibilidade da região da Data Factory?
A Data Factory está disponível nos **EUA Oeste** e Norte **da Europa.** Os serviços de computação e armazenamento utilizados pelas fábricas de dados podem ser noutras regiões. Ver [regiões apoiadas.](data-factory-introduction.md#supported-regions)

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Quais são os limites para o número de fábricas de dados/oleodutos/atividades/conjuntos de dados?
Consulte a secção **Azure Data Factory Limits** do artigo Limites de [Subscrição e Serviço Azure, Quotas e Restrições.](../../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Qual é a experiência de autoria/desenvolvedor com o serviço Azure Data Factory?
Pode autorizar/criar fábricas de dados utilizando uma das seguintes ferramentas/SDKs:

* **Estúdio Visual** Pode utilizar o Visual Studio para criar uma fábrica de dados Azure. Consulte [construir o seu primeiro pipeline de dados utilizando o Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) para obter detalhes.
* **Concha de Azure PowerShell** Consulte [Criar e monitorizar a Azure Data Factory utilizando a Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) para um tutorial/walkthrough para criar uma fábrica de dados utilizando o PowerShell. Consulte o teor [de referência da Data Factory cmdlet][adf-powershell-reference] na Biblioteca MSDN para obter uma documentação completa dos cmdlets da Data Factory.
* **Biblioteca classe .NET** Pode criar programáticamente fábricas de dados utilizando data factory .NET SDK. Ver [Criar, monitorizar e gerir fábricas de dados utilizando .NET SDK](data-factory-create-data-factories-programmatically.md) para uma produção de uma fábrica de dados utilizando .NET SDK. Consulte [a referência da Biblioteca da Classe de Data Factory][msdn-class-library-reference] para obter uma documentação completa da Data Factory .NET SDK.
* **REPOUSO API** Também pode utilizar a API REST exposta pelo serviço Azure Data Factory para criar e implantar fábricas de dados. Consulte [a Referência API DO REST da Data Factory][msdn-rest-api-reference] para uma documentação completa da API REST da Data Factory.
* **Modelo de gestor de recursos Azure** Consulte [Tutorial: Construa a sua primeira fábrica de dados Azure utilizando detalhes do modelo fo do Azure Resource Manager.](data-factory-build-your-first-pipeline-using-arm.md)

### <a name="can-i-rename-a-data-factory"></a>Posso mudar o nome de uma fábrica de dados?
N.º Tal como outros recursos da Azure, o nome de uma fábrica de dados Azure não pode ser alterado.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Posso mover uma fábrica de dados de uma assinatura Azure para outra?
Sim. Utilize o botão **Move** na lâmina da sua fábrica de dados, tal como mostrado no seguinte diagrama:

![Mover fábrica de dados](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Quais são os ambientes computativos suportados pela Data Factory?
A tabela seguinte fornece uma lista de ambientes computativos apoiados pela Data Factory e as atividades que podem ser executadas neles.

| Ambiente de computação | atividades |
| --- | --- |
| [Cluster HDInsight a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) ou [o seu próprio cluster HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet,](data-factory-use-custom-activities.md) [Colmeia,](data-factory-hive-activity.md) [Porco,](data-factory-pig-activity.md) [MapReduce,](data-factory-map-reduce.md) [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning Studio (clássico)](data-factory-compute-linked-services.md#azure-machine-learning-studio-classic-linked-service) |[Atividades de estúdio (clássicos): Execução de Lote e Recurso de Atualização](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure Synapse Analytics,](data-factory-compute-linked-services.md#azure-synapse-analytics-linked-service) [SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Procedimento Armazenado](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Como é que a Azure Data Factory se compara com os Serviços de Integração de Servidores SQL (SSIS)? 
Consulte a [Azure Data Factory vs. SSIS](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) apresentação de um dos nossos MVPs (Profissionais Mais Valorizados): Reza Rad. Algumas das recentes alterações na Data Factory podem não estar listadas no slide deck. Estamos continuamente a adicionar mais capacidades à Azure Data Factory. Estamos continuamente a adicionar mais capacidades à Azure Data Factory. Iremos incorporar estas atualizações na comparação das tecnologias de integração de dados da Microsoft ainda este ano.   

## <a name="activities---faq"></a>Atividades - FAQ
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Quais são os diferentes tipos de atividades que pode utilizar num oleoduto da Data Factory?
* [Atividades de Movimento de Dados](data-factory-data-movement-activities.md) para mover dados.
* [Atividades de Transformação de Dados](data-factory-data-transformation-activities.md) para processar/transformar dados.

### <a name="when-does-an-activity-run"></a>Quando é que uma atividade funciona?
A configuração da configuração **de disponibilidade** na tabela de dados de saída determina quando a atividade é executada. Se os conjuntos de dados de entrada forem especificados, a atividade verifica se todas as dependências de dados de entrada estão satisfeitas (isto é, estado **de pronto)** antes de começar a funcionar.

## <a name="copy-activity---faq"></a>Atividade de cópia - FAQ
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>É melhor ter um oleoduto com múltiplas atividades ou um oleoduto separado para cada atividade?
Os oleodutos devem agregar atividades relacionadas. Se os conjuntos de dados que os ligam não forem consumidos por qualquer outra atividade fora do oleoduto, pode manter as atividades num único oleoduto. Desta forma, não precisaria de acorrentar períodos ativos de gasoduto para que se alinhassem entre si. Além disso, a integridade dos dados nas tabelas internas do gasoduto é melhor preservada na atualização do gasoduto. A atualização do gasoduto essencialmente para todas as atividades dentro do oleoduto, remove-as e cria-as novamente. Do ponto de vista da autoria, poderia também ser mais fácil ver o fluxo de dados dentro das atividades relacionadas num ficheiro JSON para o pipeline.

### <a name="what-are-the-supported-data-stores"></a>Quais são as lojas de dados suportadas?
A Atividade de Cópia no Data Factory copia os dados de um arquivo de dados de origem para um arquivo de dados sink. O Data Factory suporta os arquivos de dados seguintes. Os dados de qualquer origem podem ser escritos em qualquer sink. Clique num arquivo de dados para saber como copiar dados de e para esse arquivo.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Os arquivos de dados com * podem estar no local ou no Azure IaaS e requerem a instalação do [Data Management Gateway](data-factory-data-management-gateway.md) num computador no local/Azure IaaS.

### <a name="what-are-the-supported-file-formats"></a>Quais são os formatos de ficheiro suportados?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Onde é realizada a operação de cópia?
Consulte a secção de [movimento de dados disponível globalmente](data-factory-data-movement-activities.md#global) para obter mais detalhes. Em suma, quando uma loja de dados no local está envolvida, a operação de cópia é realizada pelo Data Management Gateway no seu ambiente no local. E, quando o movimento de dados é entre duas lojas em nuvem, a operação de cópia é realizada na região mais próxima do local da pia na mesma geografia.

## <a name="hdinsight-activity---faq"></a>Atividade HDInsight - FAQ
### <a name="what-regions-are-supported-by-hdinsight"></a>Que regiões são apoiadas pela HDInsight?
Consulte a secção disponibilidade geográfica no seguinte artigo: ou [Detalhes de preços hdinsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Que região é utilizada por um cluster HDInsight a pedido?
O cluster HDInsight a pedido é criado na mesma região onde existe o armazenamento especificado para ser utilizado com o cluster.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Como associar contas de armazenamento adicionais ao seu cluster HDInsight?
Se estiver a utilizar o seu próprio Cluster HDInsight (BYOC - Bring Your Own Cluster), consulte os seguintes tópicos:

* [Utilizando um cluster HDInsight com contas de armazenamento alternativos e metastores][hdinsight-alternate-storage]
* [Utilize contas de armazenamento adicionais com hive HDInsight][hdinsight-alternate-storage-2]

Se estiver a utilizar um cluster a pedido criado pelo serviço Data Factory, especifique contas de armazenamento adicionais para o serviço ligado ao HDInsight para que o serviço Data Factory possa registá-los em seu nome. Na definição JSON para o serviço ligado a pedido, utilize uma propriedade **adicional doLinkedServiceNames** para especificar contas de armazenamento alternativas, tal como mostrado no seguinte corte JSON:

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
No exemplo acima, outros LinkedServiceName1 e outros LinkedServiceName2 representam serviços ligados cujas definições contêm credenciais de que o cluster HDInsight precisa de aceder a contas de armazenamento alternativas.

## <a name="slices---faq"></a>Fatias - FAQ
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Porque é que as minhas fatias de entrada não estão em Estado pronto?
Um erro comum não é estabelecer a propriedade **externa** **verdadeiramente** no conjunto de dados de entrada quando os dados de entrada são externos à fábrica de dados (não produzidos pela fábrica de dados).

No exemplo seguinte, basta definir **externo** para verdadeiro no **conjunto de dados1**.  

**DataFactory1** Pipeline 1: dataset1 -> atividade1 -> dataset2 -> atividade2 -> dataset3 Pipeline 2: dataset3-> activity3 -> dataset44

Se tiver outra fábrica de dados com um pipeline que leva o conjunto de dados4 (produzido pelo pipeline 2 na fábrica de dados 1), marque o dataset4 como um conjunto de dados externo porque o conjunto de dados é produzido por uma fábrica de dados diferente (DataFactory1, e não DataFactory2).  

**DataFactory2**    
Pipeline 1: dataset4->atividade4->conjunto de dados5

Se a propriedade externa estiver devidamente definida, verifique se os dados de entrada existem na localização especificada na definição de conjunto de dados de entrada.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Como correr uma fatia noutra hora que a meia-noite quando a fatia é produzida diariamente?
Utilize a propriedade **offset** para especificar o momento em que pretende que a fatia seja produzida. Consulte a secção [de disponibilidade do Dataset](data-factory-create-datasets.md#dataset-availability) para obter mais detalhes sobre esta propriedade. Aqui está um exemplo rápido:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
As fatias diárias começam às **6 da manhã** em vez da meia-noite padrão.     

### <a name="how-can-i-rerun-a-slice"></a>Como posso repetir uma fatia?
Pode repetir uma fatia de uma das seguintes formas:

* Utilize o Monitor e gerencie a App para refazer uma janela de atividade ou uma fatia. Consulte [as janelas de atividade selecionadas](data-factory-monitor-manage-app.md#perform-batch-actions) para obter instruções.   
* Clique em **Executar** na barra de comando na lâmina **DATA SLICE** para a fatia no portal Azure.
* Executar **Set-AzDataFactorySliceStatus** cmdlet com status definido para **esperar** pela fatia.   

    ```powershell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  Consulte [o Set-AzDataFactorySliceStatus][set-azure-datafactory-slice-status] para obter mais informações sobre o cmdlet.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Quanto tempo demorou para processar uma fatia?
Utilize o Explorador de Janelas de Atividade no Monitor & Gerir a App para saber quanto tempo demorou a processar uma fatia de dados. Consulte [o Explorador da Janela de Atividades](data-factory-monitor-manage-app.md#activity-window-explorer) para obter mais detalhes.

Também pode fazer o seguinte no portal Azure:  

1. Clique em **azulejo datasets** na lâmina **DATA FACTORY** para a sua fábrica de dados.
2. Clique no conjunto de dados específico na lâmina **datasets.**
3. Selecione a fatia que lhe interessa na lista de **fatias recentes** na lâmina **TABLE.**
4. Clique na atividade executada a partir da lista **'Executa a atividade'** na lâmina **DATA SLICE.**
5. Clique em **Azulejos de Propriedades** na lâmina **ACTIVITY RUN DETAILS.**
6. Deve ver o campo **DURA** com um valor. Este valor é o tempo necessário para processar a fatia.   

### <a name="how-to-stop-a-running-slice"></a>Como parar uma fatia de corrida?
Se precisar de parar a execução do gasoduto, pode utilizar o cmdlet [Suspend-AzDataFactoryPipeline.](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) Atualmente, a suspensão do gasoduto não impede as execuções em fatias que estão em curso. Uma vez terminadas as execuções em curso, não é colhida nenhuma fatia extra.

Se realmente quer parar todas as execuções imediatamente, a única maneira seria apagar o oleoduto e criá-lo novamente. Se optar por eliminar o gasoduto, NÃO necessita de apagar tabelas e serviços ligados utilizados pelo pipeline.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/module/az.datafactory/
[azure-portal]: https://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/module/az.datafactory/set-Azdatafactoryslicestatus

[adf-pricing-details]: https://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: https://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: /archive/blogs/cindygross/use-additional-storage-accounts-with-hdinsight-hive