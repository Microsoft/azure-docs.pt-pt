---
title: Azure Data Factory - Perguntas frequentes
description: Perguntas frequentes sobre a Azure Data Factory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645145"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory - Perguntas frequentes
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [frequentemente a pergunta - Data Factory](../frequently-asked-questions.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-data-factory"></a>O que é o Azure Data Factory?
Data Factory é um serviço de integração de dados baseado na nuvem que **automatiza o movimento e transformação de dados.** Tal como uma fábrica que gere equipamentos para pegar em matérias-primas e transformá-las em bens acabados, a Data Factory orquestra serviços existentes que recolhem dados brutos e os transformam em informações prontas a usar.

Data Factory permite criar fluxos de trabalho orientados para dados para mover dados entre as lojas de dados no local e na nuvem, bem como processar/transformar dados usando serviços de computação como O Azure HDInsight e Azure Data Lake Analytics. Depois de criar um pipeline que executa a ação de que necessita, pode programar para funcionar periodicamente (hora, diariamente, semanalmente, etc.).   

Para mais informações, consulte [a visão geral & Principais Conceitos.](data-factory-introduction.md)

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Onde posso encontrar detalhes sobre preços para a Azure Data Factory?
Consulte a página de Detalhes de [Preços da Fábrica][adf-pricing-details] de Dados para obter os detalhes de preços para a Fábrica de Dados Azure.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Como posso começar com a Azure Data Factory?
* Para uma visão geral da Azure Data Factory, consulte introdução à Fábrica de [Dados Azure](data-factory-introduction.md).
* Para um tutorial sobre como **copiar/mover dados** utilizando a Copy Activity, consulte [os dados de Cópia do Armazenamento De Azure Blob para a Base de Dados SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Para um tutorial sobre como **transformar dados** usando a Atividade da Colmeia HDInsight. Consulte [os dados do Processo executando o script da Hive no cluster Hadoop](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Qual é a disponibilidade da região da Fábrica de Dados?
A Fábrica de Dados está disponível nos **EUA Oeste** e Norte **da Europa.** Os serviços de computação e armazenamento utilizados pelas fábricas de dados podem ser noutras regiões. Ver [regiões apoiadas.](data-factory-introduction.md#supported-regions)

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Quais são os limites do número de fábricas/oleodutos/atividades/conjuntos de dados?
Consulte a secção limites de fábrica de **dados azure** do artigo Limites de [Subscrição e Serviço Seleções Azure, Quotas e Restrições.](../../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Qual é a experiência de autoria/desenvolvedor com o serviço Azure Data Factory?
Pode ser autor/criar fábricas de dados utilizando uma das seguintes ferramentas/SDKs:

* **Estúdio Visual** Pode utilizar o Visual Studio para criar uma fábrica de dados Azure. Consulte [a Construção do seu primeiro pipeline](data-factory-build-your-first-pipeline-using-vs.md) de dados utilizando o Visual Studio para obter mais detalhes.
* **Azure PowerShell** Consulte criar e monitorizar a Azure Data Factory utilizando o [Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) para um tutorial/walkthrough para criar uma fábrica de dados utilizando o PowerShell. Consulte o conteúdo de referência da [Data Factory Cmdlet][adf-powershell-reference] na Biblioteca MSDN para obter uma documentação completa de cmdlets da Data Factory.
* **Biblioteca de Classes .NET** Pode criar programáticamente fábricas de dados utilizando data factory .NET SDK. Consulte [Criar, monitorizar e gerir fábricas de dados utilizando o .NET SDK](data-factory-create-data-factories-programmatically.md) para uma passagem pela criação de uma fábrica de dados utilizando o .NET SDK. Consulte a Referência da [Biblioteca da Classe fábrica][msdn-class-library-reference] de dados para obter uma documentação completa da Data Factory .NET SDK.
* **API REST** Também pode utilizar a API REST exposta pelo serviço Azure Data Factory para criar e implantar fábricas de dados. Consulte data [Factory REST API Referência][msdn-rest-api-reference] para obter uma documentação completa da Data Factory REST API.
* **Modelo de gestor de recursos azure** Consulte tutorial: Construa a sua primeira fábrica de dados Azure utilizando detalhes do [modelo do Gestor de Recursos Azure.](data-factory-build-your-first-pipeline-using-arm.md)

### <a name="can-i-rename-a-data-factory"></a>Posso mudar o nome de uma fábrica de dados?
Não. Tal como outros recursos do Azure, o nome de uma fábrica de dados Azure não pode ser alterado.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Posso mover uma fábrica de dados de uma assinatura Azure para outra?
Sim. Utilize o botão **Mover** na lâmina da sua fábrica de dados, como mostrado no seguinte diagrama:

![Mover fábrica de dados](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Quais são os ambientes computacionais suportados pela Data Factory?
A tabela seguinte fornece uma lista de ambientes computacionais suportados pela Data Factory e as atividades que podem ser executadas sobre eles.

| Ambiente de computação | atividades |
| --- | --- |
| [Cluster HDInsight a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) ou [o seu próprio cluster HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet,](data-factory-use-custom-activities.md) [Colmeia,](data-factory-hive-activity.md) [Porco,](data-factory-pig-activity.md) [MapReduce,](data-factory-map-reduce.md) [Streaming de Hadoop](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Atividades de Machine Learning: Execução de Lotes e Atualizar Recurso](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL,](data-factory-compute-linked-services.md#azure-sql-linked-service)Armazém de [Dados Azure SQL,](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service) [Servidor SQL](data-factory-compute-linked-services.md#sql-server-linked-service) |[Procedimento Armazenado](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Como é que a Azure Data Factory se compara com os Serviços de Integração de Servidores SQL (SSIS)? 
Consulte a apresentação da [Azure Data Factory vs. SSIS](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) de um dos nossos MVPs (Profissionais Mais Valorizados): Reza Rad. Algumas das recentes alterações na Data Factory podem não estar listadas no slide deck. Estamos continuamente adicionando mais capacidades à Azure Data Factory. Estamos continuamente adicionando mais capacidades à Azure Data Factory. Incorporaremos estas atualizações na comparação das tecnologias de integração de dados da Microsoft ainda este ano.   

## <a name="activities---faq"></a>Atividades - FAQ
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Quais são os diferentes tipos de atividades que pode utilizar num pipeline data factory?
* [Atividades](data-factory-data-movement-activities.md) de Movimento de Dados para mover dados.
* [Atividades](data-factory-data-transformation-activities.md) de transformação de dados para processar/transformar dados.

### <a name="when-does-an-activity-run"></a>Quando funciona uma atividade?
A configuração de configuração de **disponibilidade** na tabela de dados de saída determina quando a atividade é executada. Se forem especificados os conjuntos de dados de entrada, a atividade verifica se todas as dependências de dados de entrada estão satisfeitas (isto é, estado **pronto)** antes de começar a funcionar.

## <a name="copy-activity---faq"></a>Atividade de Cópia - FAQ
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>É melhor ter um oleoduto com múltiplas atividades ou um oleoduto separado para cada atividade?
Os oleodutos devem agregar atividades relacionadas. Se os conjuntos de dados que os ligam não forem consumidos por qualquer outra atividade fora do oleoduto, pode manter as atividades num único oleoduto. Desta forma, não precisaria de correntes de períodos ativos em gasoduto para que se alinhassem uns com os outros. Além disso, a integridade dos dados nas tabelas internas do gasoduto é melhor preservada na atualização do gasoduto. A atualização do gasoduto essencialmente para todas as atividades dentro do oleoduto, remove-as e cria-as novamente. Do ponto de vista da autoria, também pode ser mais fácil ver o fluxo de dados dentro das atividades relacionadas num ficheiro JSON para o pipeline.

### <a name="what-are-the-supported-data-stores"></a>Quais são as lojas de dados suportadas?
A Atividade de Cópia no Data Factory copia os dados de um arquivo de dados de origem para um arquivo de dados sink. O Data Factory suporta os arquivos de dados seguintes. Os dados de qualquer origem podem ser escritos em qualquer sink. Clique num arquivo de dados para saber como copiar dados de e para esse arquivo.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Os arquivos de dados com * podem estar no local ou no Azure IaaS e requerem a instalação do [Data Management Gateway](data-factory-data-management-gateway.md) num computador no local/Azure IaaS.

### <a name="what-are-the-supported-file-formats"></a>Quais são os formatos de ficheiro suportados?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Onde é realizada a operação de cópia?
Consulte a secção de [movimento de dados disponível globalmente](data-factory-data-movement-activities.md#global) para obter mais detalhes. Em suma, quando uma loja de dados no local está envolvida, a operação de cópia é realizada pelo Portal de Gestão de Dados no seu ambiente no local. E, quando o movimento de dados é entre duas lojas em nuvem, a operação de cópia é realizada na região mais próxima do local do lavatório na mesma geografia.

## <a name="hdinsight-activity---faq"></a>Atividade HDInsight - FAQ
### <a name="what-regions-are-supported-by-hdinsight"></a>Que regiões são apoiadas pelo HDInsight?
Consulte a secção de Disponibilidade Geográfica no seguinte artigo: ou Detalhes de [Preços HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Que região é utilizada por um cluster HDInsight a pedido?
O cluster HDInsight a pedido é criado na mesma região onde existe o armazenamento que especificou para ser utilizado com o cluster.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Como associar contas de armazenamento adicionais ao seu cluster HDInsight?
Se estiver a utilizar o seu próprio Cluster HDInsight (BYOC - Bring Your Own Cluster), consulte os seguintes tópicos:

* [Utilizando um Cluster HDInsight com Contas de Armazenamento Alternativos e Metastores][hdinsight-alternate-storage]
* [Utilize contas de armazenamento adicionais com hiv HDInsight][hdinsight-alternate-storage-2]

Se estiver a utilizar um cluster a pedido criado pelo serviço Data Factory, especifique contas de armazenamento adicionais para o serviço ligado ao HDInsight para que o serviço Data Factory os registe em seu nome. Na definição JSON para o serviço ligado a pedido, utilize **propriedades adicionais LinkedServiceNames** para especificar contas de armazenamento alternativas, como mostrado no seguinte snippet JSON:

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
No exemplo acima, outrosLinkedServiceName1 e outrosLinkedServiceName2 representam serviços ligados cujas definições contêm credenciais de que o cluster HDInsight precisa para aceder a contas de armazenamento alternativas.

## <a name="slices---faq"></a>Fatias - FAQ
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Porque é que as minhas fatias de entrada não estão em estado de pronto?
Um erro comum não é estabelecer propriedade **externa** **verdadeiramente** no conjunto de dados de entrada quando os dados de entrada são externos à fábrica de dados (não produzidos pela fábrica de dados).

No exemplo seguinte, basta definir **o exterior** para o verdadeiro no **dataset1**.  

**DataFactory1** Pipeline 1: dataset1 -> atividade1 -> dataset2 -> atividade2 -> dataset3 Pipeline 2: dataset3-> atividade3 -> dataset4

Se tiver outra fábrica de dados com um pipeline que tome dataset4 (produzido pelo pipeline 2 na fábrica de dados 1), marque o conjunto de dados4 como um conjunto de dados externo porque o conjunto de dados é produzido por uma fábrica de dados diferente (DataFactory1, e não DataFactory2).  

**DataFactory2**    
Pipeline 1: dataset4->atividade4->dataset5

Se a propriedade externa estiver corretamente definida, verifique se os dados de entrada existem no local especificado na definição de conjunto de dados de entrada.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Como executar uma fatia em outra hora que não a meia-noite quando a fatia é produzida diariamente?
Utilize a propriedade **offset** para especificar o tempo em que pretende que a fatia seja produzida. Consulte a secção [de disponibilidade do Dataset](data-factory-create-datasets.md#dataset-availability) para obter detalhes sobre esta propriedade. Aqui está um exemplo rápido:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
As fatias diárias começam às **6 da manhã** em vez da meia-noite padrão.     

### <a name="how-can-i-rerun-a-slice"></a>Como posso refazer uma fatia?
Pode reproduzir uma fatia de uma das seguintes formas:

* Utilize o Monitor e a Manage App para reproduzir uma janela ou fatia de atividade. Consulte as janelas de [atividade selecionadas para](data-factory-monitor-manage-app.md#perform-batch-actions) obter instruções.   
* Clique **Em executar** na barra de comando na lâmina DATA **SLICE** para a fatia no portal Azure.
* Executar **Set-AzDataFactorySliceStatus** cmdlet com estado definido para **esperar** pela fatia.   

    ```powershell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  Consulte o [Set-AzDataFactorySliceStatus][set-azure-datafactory-slice-status] para obter mais detalhes sobre o cmdlet.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Quanto tempo demorou a processar uma fatia?
Use o Explorador de Janelas de Atividade no Monitor & Gerir app para saber quanto tempo demorou a processar uma fatia de dados. Consulte o [Activity Window Explorer](data-factory-monitor-manage-app.md#activity-window-explorer) para obter mais detalhes.

Pode também fazer o seguinte no portal Azure:  

1. Clique em **azulejo sinuoso** na lâmina DATA **FACTORY** para a sua fábrica de dados.
2. Clique no conjunto de dados específico na lâmina **datasets.**
3. Selecione a fatia que lhe interessa na lista de **fatias recentes** na lâmina **TABLE.**
4. Clique na execução da atividade da lista **'Executar'** na lâmina **DATA SLICE.**
5. Clique em **propriedades** de azulejos na lâmina DE **DETALHES DE EXECUÇÃO DE ATIVIDADE.**
6. Deve ver o campo **DURATION** com um valor. Este valor é o tempo que se demorou a processar a fatia.   

### <a name="how-to-stop-a-running-slice"></a>Como parar uma fatia corrente?
Se precisar de impedir a execução do gasoduto, pode utilizar o [cmdlet Suspend-AzDataFactoryPipeline.](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) Atualmente, a suspensão do gasoduto não impede as execuções de fatias que estão em curso. Uma vez que as execuções em curso terminam, nenhuma fatia extra é apanhada.

Se realmente quiser parar todas as execuções imediatamente, a única maneira seria apagar o oleoduto e criá-lo novamente. Se optar por apagar o gasoduto, NÃO precisa de eliminar tabelas e serviços ligados utilizados pelo gasoduto.

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
