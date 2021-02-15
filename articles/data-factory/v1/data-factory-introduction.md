---
title: Introdução à Data Factory, um serviço de integração de dados
description: 'Saiba o que o Azure Data Factory é: um serviço de integração de dados em nuvem que orquestra e automatiza o movimento e a transformação de dados.'
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: overview
ms.date: 01/22/2018
ms.openlocfilehash: 1cee5f92af4e23a6bc0d195bc13bc3f11f53443d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382978"
---
# <a name="introduction-to-azure-data-factory"></a>Introdução ao Azure Data Factory 
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-introduction.md)
> * [Versão 2 (versão atual)](../introduction.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [Introdução ao Data Factory V2](../introduction.md).


## <a name="what-is-azure-data-factory"></a>O que é o Azure Data Factory?
No mundo dos macrodados, de que modo é que as empresas tiram partido dos dados existentes? É possível utilizar dados de referência de origens de dados no local ou de outras origens de dados diferentes para enriquecer os dados gerados na cloud? 

Por exemplo, uma empresa de jogos recolhe registos produzidos pelos jogos na cloud. A empresa quer analisar estes registos para obter informações sobre as preferências dos clientes, dados demográficos, comportamentos de utilização, entre outros. Quer também identificar as oportunidades de venda superior e venda cruzada, desenvolver novas funcionalidades apelativas para impulsionar o crescimento do negócio e proporcionar uma melhor experiência aos clientes. 

Para analisar estes registos, a empresa tem de utilizar dados de referência, como informações do cliente, informações do jogo, informações de campanhas de marketing, que se encontram num arquivo de dados no local. Por isso, a empresa pretende ingerir dados de registo provenientes do arquivo de dados na cloud e dados de referência provenientes do arquivo de dados no local. 

Em seguida, quer processar os dados através do Hadoop na cloud (Azure HDInsight). Pretendem publicar os dados dos resultados num armazém de dados em nuvem, como o Azure Synapse Analytics ou uma loja de dados no local, como o SQL Server. A empresa quer que este fluxo de trabalho seja executado uma vez por semana. 

Precisa de uma plataforma onde seja possível criar um fluxo de trabalho que faça a ingestão de dados em arquivos de dados no local e na cloud. A empresa também tem de transformar ou processar os dados através dos serviços de computação existentes, como o Hadoop, e publicar os resultados num arquivo de dados no local ou na cloud para consumo das aplicações de BI. 

![Descrição geral do Data Factory](media/data-factory-introduction/what-is-azure-data-factory.png) 

O Azure Data Factory é a plataforma para estes tipos de cenários. É um *serviço de integração de dados baseado na cloud que permite criar fluxos de trabalho condicionados por dados na cloud para orquestrar e automatizar o movimento e a transformação de dados*. Ao utilizar o Azure Data Factory, pode efetuar as seguintes tarefas: 

- Criar e agendar fluxos de trabalho condicionados por dados (denominados pipelines) que podem ingerir dados a partir de arquivos de dados diferentes.

- Processar ou transformar os dados através de serviços de computação como o Azure HDInsight, Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning.

-  Publique dados de saída em lojas de dados como a Azure Synapse Analytics para aplicações de inteligência empresarial (BI) para consumir.  

É mais uma plataforma de Extração e Carregamento (EL) e de Transformação e Carregamento (TL) do que uma plataforma de Extração, Transformação e Carregamento (ETL) tradicional. As transformações processam os dados através de serviços de computação em vez de adicionar colunas derivadas, incluindo o número de linhas, a ordenação de dados, etc. 

Atualmente, no Azure Data Factory, os dados que são consumidos e produzidos pelos fluxos de trabalho são *dados repartidos pelo tempo* (de hora a hora, diariamente, semanalmente, etc.). Por exemplo, um pipeline pode ler dados de entrada, processar dados e produzir dados de saída uma vez por dia. Também pode executar um fluxo de trabalho apenas uma vez.  
  

## <a name="how-does-it-work"></a>Como funciona? 
Os pipelines (fluxos de trabalho condicionados por dados) no Azure Data Factory executam, normalmente, os três passos seguintes:

![Três fases do Azure Data Factory](media/data-factory-introduction/three-information-production-stages.png)

### <a name="connect-and-collect"></a>Ligar e recolher
As empresas têm dados de diversos tipos localizados em diferentes origens. O primeiro passo na criação de um sistema de produção de informações é ligar a todas as origens de dados e processamento necessárias. Estas origens incluem serviços SaaS, partilhas de ficheiros, FTP e serviços Web. Em seguida, os dados devem ser movidos conforme necessário para uma localização centralizada para processamento subsequente.

Sem o Data Factory, as empresas têm de criar componentes de movimento de dados personalizados ou escrever serviços personalizados para integrar essas origens de dados e esse processamento. É dispendioso e difícil integrar e manter esses sistemas. Muitas vezes, estes sistemas não têm monitorização, alertas e controlos de nível empresarial que um serviço totalmente gerido pode oferecer.

Com o Data Factory, pode utilizar a Atividade de Cópia num pipeline de dados para mover dados de arquivos de dados no local e na cloud para um arquivo centralizado na cloud, para análises adicionais. 

Por exemplo, pode recolher dados no Azure Data Lake Store e transformá-los posteriormente com um serviço de computação do Azure Data Lake Analytics. Em alternativa, pode recolher dados no armazenamento de blobs do Azure e transformá-los mais tarde com um cluster do Azure HDInsight Hadoop.

### <a name="transform-and-enrich"></a>Transformar e enriquecer
Depois de os dados estarem presentes num arquivo de dados centralizado na cloud, processe ou transforme-os através de serviços de computação, tais como o HDInsight Hadoop, Spark, Data Lake Analytics ou Machine Learning. Pretende produzir de forma fiável os dados transformados numa agenda controlada e sustentável, para alimentar ambientes de produção com dados fidedignos. 

### <a name="publish"></a>Publicar 
Forneça os dados transformados a partir da cloud para origens no local, como o SQL Server. Em alternativa, mantenha-os nas origens de armazenamento da cloud para consumo das ferramentas de BI e análise, e outras aplicações.

## <a name="key-components"></a>Componentes principais
Uma subscrição do Azure pode ter uma ou mais instâncias do Azure Data Factory (ou fábricas de dados). O Azure Data Factory é constituído por quatro componentes principais. Estes componentes funcionam em conjunto para fornecer a plataforma na qual pode compor fluxos de trabalho orientados por dados com passos para mover e transformar dados. 

### <a name="pipeline"></a>Pipeline
Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline é um grupo de atividades. Em conjunto, as atividades num pipeline executam uma tarefa. 

Por exemplo, um pipeline pode conter um grupo de atividades que ingere dados de um blob do Azure e, em seguida, executa uma consulta de Hive num cluster do HDInsight para particionar os dados. A vantagem neste caso é que o pipeline lhe permite gerir as atividades como um conjunto, em vez de individualmente. Por exemplo, pode implementar e agendar o pipeline em vez das atividades de agendamento independentes. 

### <a name="activity"></a>Atividade
Um pipeline pode conter uma ou mais atividades. As Atividades definem as ações a efetuar nos seus dados. Por exemplo, pode utilizar uma atividade de cópia para copiar dados de um arquivo de dados para outro. Da mesma forma, pode utilizar uma atividade do Hive. Uma atividade do Hive executa uma consulta do Hive num cluster do Azure HDInsight para transformar ou analisar os seus dados. O Data Factory suporta dois tipos de atividades -- atividades de movimento de dados e atividades de transformação de dados.

### <a name="data-movement-activities"></a>Atividades de movimento de dados
A Atividade de Cópia no Data Factory copia os dados de um arquivo de dados de origem para um arquivo de dados sink. Os dados de qualquer origem podem ser escritos em qualquer sink. Selecione um arquivo de dados para saber como copiar dados de e para esse arquivo. O Data Factory suporta os seguintes arquivos de dados:

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Para obter mais informações, veja [Mover dados através da Atividade de Cópia](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Atividades de transformação de dados
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Para obter mais informações, veja [Mover dados através da Atividade de Cópia](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Atividades .NET personalizadas
Crie uma atividade .NET personalizada se precisar de mover dados de e para um arquivo de dados que a Atividade de Cópia não suporte ou para transformar dados com a sua própria lógica. Para obter detalhes sobre como criar e utilizar uma atividade personalizada, veja [Utilizar atividades personalizadas num pipeline do Azure Data Factory](data-factory-use-custom-activities.md).

### <a name="datasets"></a>Conjuntos de dados
Uma atividade aceita zero ou mais conjuntos de dados como entradas e um ou mais conjuntos de dados como saídas. Os conjuntos de dados representam estruturas de dados dentro dos arquivos de dados. Estas estruturas apontam ou referenciam os dados que quer utilizar nas suas atividades (como entradas ou saídas). 

Por exemplo, um conjunto de dados de blobs do Azure especifica o contentor de blobs e a pasta no armazenamento de blobs do Azure a partir dos quais o pipeline deve ler os dados. Em alternativa, um conjunto de dados de tabela SQL do Azure especifica a tabela na qual os dados de saída são escritos pela atividade. 

### <a name="linked-services"></a>Serviços ligados
Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Encare da seguinte forma: um serviço ligado define a ligação à origem de dados e um conjunto de dados representa a estrutura dos dados. 

Por exemplo, um serviço ligado do Armazenamento do Azure especifica a cadeia de ligação para ligar à conta de Armazenamento do Azure. Um conjunto de dados de blobs do Azure especifica o contentor de blobs e a pasta que contém os dados.   

Os serviços ligados são utilizados por dois motivos no Data Factory:

* Para representar uma *loja de dados* que inclui, mas não se limita a, uma base de dados sql Server, base de dados Oracle, partilha de ficheiros ou conta de armazenamento de blob Azure. Veja a secção [Data movement activities (Atividades de movimento de dados)](#data-movement-activities) para obter uma lista dos arquivos de dados suportados.

* Para representar um *recurso de computação* que pode alojar a execução de uma atividade. Por exemplo, a Atividade HDInsightHive é executada num cluster do HDInsight Hadoop. Veja a secção [Atividades de transformação de dados](#data-transformation-activities) para obter uma lista dos ambientes de computação suportados.

### <a name="relationship-between-data-factory-entities"></a>Relação entre as entidades do Data Factory

![Diagrama: Data Factory, um serviço de integração de dados na cloud – principais conceitos](./media/data-factory-introduction/data-integration-service-key-concepts.png)

## <a name="supported-regions"></a>Regiões suportadas
Atualmente, pode criar fábricas de dados nas regiões E.U.A. Oeste, E.U.A. Leste e Europa do Norte. No entanto, uma fábrica de dados pode aceder aos arquivos de dados e serviços de computação noutras regiões do Azure para mover dados entre os arquivos de dados ou processar dados com serviços de computação.

O Azure Data Factory propriamente dito não armazena quaisquer dados. Permite criar fluxos de trabalho condicionados por dados para orquestrar o movimento de dados entre os [arquivos de dados suportados](#data-movement-activities). Também permite processar os dados através de [serviços de computação](#data-transformation-activities) noutras regiões ou num ambiente no local. Também permite [monitorizar e gerir fluxos de trabalho](data-factory-monitor-manage-pipelines.md) com mecanismos programáticos e de IU.

O Data Factory está disponível apenas nas regiões E.U.A. Oeste, E.U.A. Leste e Europa do Norte. No entanto, o serviço que permite o movimento de dados no Data Factory está disponível [globalmente](data-factory-data-movement-activities.md#global) em várias regiões. Se uma loja de dados estiver por trás de uma firewall, então um Gateway de Gestão de [Dados](data-factory-move-data-between-onprem-and-cloud.md) instalado no seu ambiente no local move os dados em vez disso.

Por exemplo, vamos assumir que os seus ambientes de computação, tais como o cluster do Azure HDInsight e o Azure Machine Learning, estão localizados na região Europa Ocidental. Pode criar e utilizar uma instância do Azure Data Factory na Europa do Norte. Em seguida, pode utilizá-la para agendar tarefas nos seus ambientes de computação na Europa Ocidental. O Data Factory demora alguns milissegundos a acionar a tarefa no seu ambiente de computação, mas o tempo de execução da tarefa nesse ambiente não sofre alterações.

## <a name="get-started-with-creating-a-pipeline"></a>Introdução à criação de um pipeline
Pode utilizar uma destas ferramentas ou APIs para criar pipelines de dados no Azure Data Factory: 

- Visual Studio
- PowerShell
- API .NET
- API REST
- Modelo Azure Resource Manager

Para saber como criar fábricas de dados com pipelines de dados, siga as instruções passo a passo nos tutoriais seguintes:

| Tutorial | Description |
| --- | --- |
| [Move data between two cloud data stores (Mover dados entre dois arquivos de dados na cloud)](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Crie uma fábrica de dados com um pipeline que transa de dados do armazenamento de bolhas para a Base de Dados SQL. |
| [Utilizar o cluster do Hadoop para transformar dados](data-factory-build-your-first-pipeline.md) |Crie a primeira fábrica de dados do Azure com um pipeline de dados que processa dados através da execução do script Hive num cluster do Azure HDInsight (Hadoop). |
| [Utilizar o Data Management Gateway para mover dados entre um arquivo de dados no local e um arquivo de dados na cloud](data-factory-move-data-between-onprem-and-cloud.md) |Construa uma fábrica de dados com um oleoduto que move dados de uma base de dados do SQL Server para uma bolha Azure. Como parte das instruções, vai instalar e configurar o Data Management Gateway no seu computador. |
