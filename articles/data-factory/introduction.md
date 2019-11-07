---
title: Introdução ao Azure Data Factory
description: Saiba mais sobre o Azure Data Factory, um serviço de integração de dados na cloud que orquestra e automatiza o movimento e a transformação de dados.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: overview
ms.date: 09/30/2019
ms.openlocfilehash: e23268d21b01435aa71d4f471afc6d37ee9e9c1d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672709"
---
# <a name="what-is-azure-data-factory"></a>O que é o Azure Data Factory?

No mundo dos macrodados, muitas vezes, são armazenados dados desorganizados e não processados em sistemas relacionais, não relacionais, entre outros. No entanto, por si só, os dados não processados não têm o contexto ou o significado adequado para disponibilizar informações significativas para analistas, cientistas de dados ou decisores empresariais. 

Os macrodados precisam de um serviço que orquestre e operacionalize processos para refinar estes enormes arquivos de dados não processados em informações comerciais acionáveis. O Azure Data Factory é um serviço na cloud gerido criado para estes projetos complexos e híbridos de extract-transform-load (ETL), extract-load-transform (ELT) e integração de dados.

Por exemplo, imagine uma empresa de jogos que recolhe petabytes de registos de jogos que são produzidos por jogos na cloud. A empresa quer analisar estes registos para obter informações sobre as preferências dos clientes, dados demográficos e comportamentos de utilização. Quer também identificar as oportunidades de vendas superiores e vendas cruzadas, desenvolver funcionalidades novas e apelativas, impulsionar o crescimento do negócio e proporcionar uma melhor experiência aos clientes.

Para analisar estes registos, a empresa tem de utilizar dados de referência, como informações do cliente, informações do jogo, informações de campanhas de marketing, que se encontram num arquivo de dados no local. A empresa quer utilizar estes dados a partir do arquivo de dados no local e combiná-los com os dados de registos adicionais que tem num arquivo de dados na cloud. 

Para extrair informações, espera processar os dados combinados mediante a utilização de um cluster do Spark na cloud (Azure HDInsight) e, por fim, publicar os dados transformados num armazém de dados na cloud, como o Azure SQL Data Warehouse, para criar facilmente um relatório com base no mesmo. Pretende automatizar este fluxo de trabalho e monitorizá-lo e geri-lo com base numa agenda diária. Também quer executá-lo quando são adicionados ficheiros a um contentor de arquivo de blobs.

O Azure Data Factory é a plataforma que resolve estes cenários de dados. É o *ETL baseado em nuvem e o serviço de integração de dados que permite criar fluxos de trabalho controlados por dados para orquestrar a movimentação de dados e transformar dados em escala*. Ao utilizar o Azure Data Factory, pode criar e agendar fluxos de trabalho orientados por dados (denominados pipelines) que podem ingerir dados a partir de arquivos de dados diferentes. Você pode criar processos de ETL complexos que transformam os dados visualmente com fluxos de dados ou usando serviços de computação, como Azure HDInsight Hadoop, Azure Databricks e o Azure SQL Database. 

Além disso, você pode publicar seus dados transformados em armazenamentos de dados, como o Azure SQL Data Warehouse para que os aplicativos de business intelligence (BI) consumam. Em última análise, através do Azure Data Factory, os dados não processados podem ser organizados em arquivos de dados com significado e em data lakes para uma melhor tomada de decisões empresariais.

![Vista de nível superior do Data Factory](media/data-flow/overview.png)

## <a name="how-does-it-work"></a>Como funciona?

O Data Factory contém uma série de sistemas interconectados que fornecem uma plataforma completa para engenheiros de dados.

### <a name="connect-and-collect"></a>Ligar e recolher

As empresas têm dados dos mais diversos tipos localizados em diferentes origens, seja no local, na cloud, estruturadas, não estruturadas e semi-estruturadas, que são recebidos em intervalos e velocidades distintos. 

O primeiro passo na criação de um sistema de produção de informação é ligar a todas as origens de dados e processamento necessários, como serviços de software como serviços de software como serviço (SaaS), bases de dados, partilhas de ficheiros e serviços Web de FTP. O passo seguinte é mover os dados conforme necessário para uma localização centralizada, para processamento subsequente.

Sem o Data Factory, as empresas têm de criar componentes de movimento de dados personalizados ou escrever serviços personalizados para integrar essas origens de dados e esse processamento. Integrar e manter estes sistemas é dispendioso e difícil. Além disso, muitas vezes, não têm monitorização, alertas e controlos de nível empresarial que um serviço totalmente gerido pode oferecer.

Com o Data Factory, pode utilizar a [Atividade de Cópia](copy-activity-overview.md) num pipeline de dados para mover dados de arquivos de dados no local e na cloud para um arquivo centralizado na cloud, para análises adicionais. Por exemplo, você pode coletar dados em Azure Data Lake Storage e transformar os dados mais tarde usando um serviço de computação Azure Data Lake Analytics. Também pode recolher dados no armazenamento de blobs do Azure e transformá-los mais tarde com um cluster do Azure HDInsight Hadoop.

### <a name="transform-and-enrich"></a>Transformar e enriquecer
Depois que os dados estiverem presentes em um armazenamento de dados centralizado na nuvem, processe ou transforme os dados coletados usando fluxos de dados de mapeamento do ADF. Os fluxos de dados permitem que os engenheiros de dados criem e mantenham gráficos de transformação de dados que são executados no Spark sem a necessidade de entender clusters Spark ou a programação Spark.

Se você preferir transformações de código manualmente, o ADF dá suporte a atividades externas para executar suas transformações em serviços de computação, como HDInsight Hadoop, Spark, Data Lake Analytics e Machine Learning.

### <a name="cicd-and-publish"></a>CI/CD e publicar
O Data Factory oferece suporte completo para CI/CD de seus pipelines de dados usando o Azure DevOps e o GitHub. Isso permite que você desenvolva e entregue incrementalmente seus processos de ETL antes de publicar o produto concluído. Quando os dados não processados estiverem refinados e prontos para consumo por parte das empresas, carregue os dados para o Azure Data Warehouse, para a Base de Dados SQL do Azure, para o Azure CosmosDB ou para qualquer que seja o motor de análise para o qual os seus utilizadores empresariais podem apontar a partir as ferramentas de business intelligence que utilizam.

### <a name="monitor"></a>Monitorizar
Depois de criar e implementar com êxito o seu pipeline de integração de dados, proporcionando valor comercial a partir dos dados refinados, monitorize as atividades e os pipelines agendados relativamente às taxas de êxito e falha. O Azure Data Factory tem suporte interno para monitoramento de pipeline por meio de Azure Monitor, API, PowerShell, logs de Azure Monitor e painéis de integridade no portal do Azure.

## <a name="top-level-concepts"></a>Principais conceitos
As subscrições do Azure podem ter uma ou várias instâncias do Azure Data Factory (ou fábricas de dados). O Azure Data Factory é constituído por quatro componentes principais. Estes componentes funcionam em conjunto para fornecer a plataforma na qual pode compor fluxos de trabalho orientados por dados com passos para mover e transformar dados.

### <a name="pipeline"></a>Pipeline
Uma fábrica de dados pode ter um ou mais pipelines. Os pipelines são agrupamentos lógicos de atividades que realizam uma unidade de trabalho. Em conjunto, as atividades num pipeline executam tarefas. Por exemplo, um pipeline pode conter um grupo de atividades que ingere dados de um blob do Azure e, em seguida, executa uma consulta de Hive num cluster do HDInsight para particionar os dados. 

A vantagem neste caso é que o pipeline lhe permite gerir as atividades como um conjunto, em vez de individualmente. As atividades num pipeline podem ser encadeadas para funcionar sequencialmente ou podem funcionar de forma independente em paralelo

### <a name="mapping-data-flows"></a>Fluxos de dados de mapeamento
Crie e gerencie grafos de lógica de transformação de dados que você pode usar para transformar dados de qualquer tamanho. Você pode criar uma biblioteca reutilizável de rotinas de transformação de dados e executar esses processos de maneira expandida de seus pipelines do ADF. Data Factory executará sua lógica em um cluster Spark que gira e gira quando você precisar dela. Você nunca precisará gerenciar ou manter clusters.

### <a name="activity"></a>Atividade
As atividades representam uma fase de processamento num pipeline. Por exemplo, pode utilizar uma atividade de cópia para copiar dados de um arquivo de dados para outro. Da mesma forma, pode utilizar uma atividade do Hive, que executa uma consulta do Hive num cluster do Azure HDInsight, para transformar ou analisar os seus dados. O Data Factory suporta três tipos de atividades: atividades de movimento de dados, atividades de transformação de dados e atividades de controlo.

### <a name="datasets"></a>Conjuntos de dados
Os conjuntos de dados representam estruturas de dados nos arquivos de dados, que simplesmente apontam ou referenciam os dados que pretende utilizar nas suas atividades como entrada ou saída. 

### <a name="linked-services"></a>Serviços ligados
Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Encare da seguinte forma: um serviço ligado define a ligação à origem de dados e um conjunto de dados representa a estrutura dos dados. Por exemplo, um serviço ligado do Armazenamento do Azure especifica a cadeia de ligação para ligar à conta do Armazenamento do Azure. Além disso, um conjunto de dados de blobs do Azure especifica o contentor de blobs e a pasta que contém os dados.

Os serviços ligados são utilizados para duas finalidades no Data Factory:

- Para representar um **arquivo de dados**, que inclua, entre outros, uma base de dados do SQL Server no local, uma base de dados Oracle, uma partilha de ficheiros ou uma conta de armazenamento de blobs do Azure. Para obter uma lista dos arquivos de dados suportados, veja o artigo [copy activity](copy-activity-overview.md) (Atividade de Cópia).

- Para representar um **recurso de computação** que pode alojar a execução de uma atividade. Por exemplo, a Atividade HDInsightHive é executada num cluster do HDInsight Hadoop. Para obter uma lista das atividades de transformação e os ambientes de computação suportados, veja o artigo [Transform data](transform-data.md) (Transformar dados).

### <a name="triggers"></a>Acionadores
Os acionadores representam a unidade de processamento que determina quando é que uma execução de pipeline tem de arrancar. Existem diferentes tipos de acionadores para diferentes tipos de eventos.

### <a name="pipeline-runs"></a>Execuções de pipeline
As instâncias de pipeline são instâncias da execução dos pipelines. Normalmente, as execuções de pipeline são instanciadas pela transmissão de argumentos aos parâmetros definidos nos pipelines. Os argumentos podem ser transmitidos manualmente ou na definição do acionador.

### <a name="parameters"></a>Parâmetros
Os parâmetros são pares chave-valor de configuração só de leitura.  Os parâmetros são definidos no pipeline. Os argumentos para os parâmetros definidos são transmitidos durante a execução a partir do contexto da instância criado por um acionador ou por um pipeline executado manualmente. As atividades dentro do pipeline consomem os valores dos parâmetros.

Os conjuntos de dados são parâmetros inflexíveis e uma entidade reutilizável/referenciável. As atividades podem referenciar conjuntos de dados e podem consumir as propriedades definidas na definição do conjunto de dados

Os serviços ligados também são um parâmetro inflexível que contém as informações de ligação a um arquivo de dados ou a um ambiente de computação. Também é uma entidade reutilizável/referenciável.

### <a name="control-flow"></a>Fluxo de controlo
O fluxo de controlo é uma orquestração das atividades dos pipelines que incluem o encadeamento de atividades numa sequência, a ramificação, a definição de parâmetros ao nível do pipeline e a transmissão de argumentos quando é invocado o pipeline a pedido ou a partir de um acionador. Também inclui a transmissão de estado personalizado e os contentores de ciclo, ou seja, iteradores For-each.

### <a name="variables"></a>Variáveis
As variáveis podem ser usadas dentro de pipelines para armazenar valores temporários e também podem ser usadas em conjunto com parâmetros para habilitar a passagem de valores entre pipelines, fluxos de dados e outras atividades.

## <a name="next-steps"></a>Passos seguintes
Aqui estão os documentos importantes da próxima etapa a serem explorados:

- [Dataset and linked services](concepts-datasets-linked-services.md) (Conjuntos de dados e serviços ligados)
- [Pipelines e atividades](concepts-pipelines-activities.md)
- [Integration runtime](concepts-integration-runtime.md) (Runtime de integração)
- [Mapeando fluxos de dados](concepts-data-flow-overview.md)
- [IU do Data Factory no portal do Azure](quickstart-create-data-factory-portal.md)
- [Ferramenta de Copiar Dados no portal do Azure](quickstart-create-data-factory-copy-data-tool.md)
- [PowerShell](quickstart-create-data-factory-powershell.md)
- [.NET](quickstart-create-data-factory-dot-net.md)
- [Python](quickstart-create-data-factory-python.md)
- [REST](quickstart-create-data-factory-rest-api.md)
- [Modelo do Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md)
 
