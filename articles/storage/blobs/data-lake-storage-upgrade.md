---
title: Atualize suas soluções de análise de Big Data de Azure Data Lake Storage Gen1 para Azure Data Lake Storage Gen2
description: Atualize sua solução para usar o Azure Data Lake Storage Gen2
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 11/01/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: rugopala
ms.openlocfilehash: d86e2cca31487a73e089124e9552b574bcc9166f
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584399"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2"></a>Atualize suas soluções de análise de Big Data de Azure Data Lake Storage Gen1 para Azure Data Lake Storage Gen2

Se você estiver usando Azure Data Lake Storage Gen1 em suas soluções do Big Data Analytics, este guia o ajudará a atualizar essas soluções para usar o Azure Data Lake Storage Gen2. Você pode usar este documento para avaliar as dependências que sua solução tem em Data Lake Storage Gen1. Este guia também mostra como planejar e executar a atualização.

Vamos ajudá-lo nas seguintes tarefas:

: heavy_check_mark: avalie o upgrade Readiness

: heavy_check_mark: planejar uma atualização

: heavy_check_mark: executar a atualização

## <a name="assess-your-upgrade-readiness"></a>Avaliar a preparação do upgrade

Nossa meta é que todos os recursos presentes no Data Lake Storage Gen1 sejam disponibilizados no Data Lake Storage Gen2. Como esses recursos são expostos, por exemplo, no SDK, na CLI etc., podem ser diferentes entre Data Lake Storage Gen1 e Data Lake Storage Gen2. Aplicativos e serviços que funcionam com Data Lake Storage Gen1 precisam ser capazes de trabalhar de forma semelhante com Data Lake Storage Gen2. Por fim, alguns dos recursos não estarão disponíveis em Data Lake Storage Gen2 imediatamente. À medida que se tornarem disponíveis, nós os anunciaremos neste documento.

Estas seções a seguir ajudarão você a decidir a melhor maneira de atualizar para o Data Lake Storage Gen2 e quando isso pode fazer mais sentido fazer isso.

### <a name="data-lake-storage-gen1-solution-components"></a>Componentes da solução Data Lake Storage Gen1

Provavelmente, quando você usa Data Lake Storage Gen1 em suas soluções de análise ou pipelines, há muitas tecnologias adicionais que você emprega para obter a funcionalidade geral de ponta a ponta. Este [artigo](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) descreve vários componentes do fluxo de dados que incluem a ingestão, o processamento e o consumo de dados.

Além disso, há componentes de corte cruzado para provisionar, gerenciar e monitorar esses componentes. Cada um dos componentes opera com Data Lake Storage Gen1 usando uma interface mais adequada para eles. Quando estiver planejando atualizar sua solução para Data Lake Storage Gen2, você precisará estar ciente das interfaces que são usadas. Você precisará atualizar as interfaces de gerenciamento, bem como as interfaces de dados, já que cada interface tem requisitos distintos.

![Componentes da solução Data Lake Storage](./media/data-lake-storage-upgrade/solution-components.png)

A **Figura 1** acima mostra os componentes de funcionalidade que você veria na maioria das soluções de análise.

A **Figura 2** mostra um exemplo de como esses componentes serão implementados usando tecnologias específicas.

A funcionalidade de armazenamento no **Figura 1** é fornecida por data Lake Storage Gen1 (**Figura 2**). Observe como os vários componentes no fluxo de dados interagem com Data Lake Storage Gen1 usando APIs REST ou o SDK do Java. Observe também como os componentes de funcionalidade de corte cruzado interagem com Data Lake Storage Gen1. O componente de provisionamento usa modelos de recursos do Azure, enquanto o componente de monitoramento que usa Azure Monitor logs utiliza dados operacionais provenientes de Data Lake Storage Gen1.

Para atualizar uma solução usando Data Lake Storage Gen1 para Data Lake Storage Gen2, você precisará copiar os dados e metadados, conectar os fluxos de dados novamente e, em seguida, todos os componentes precisarão ser capazes de trabalhar com Data Lake Storage Gen2.

As seções a seguir fornecem informações para ajudá-lo a tomar decisões melhores:

: heavy_check_mark: recursos da plataforma

: heavy_check_mark: interfaces de programação

: heavy_check_mark: ecossistema do Azure

: heavy_check_mark: ecossistema de parceiros

: heavy_check_mark: informações operacionais

Em cada seção, você poderá determinar o "necessário" para a atualização. Depois de ter certeza de que os recursos estão disponíveis, ou se tiver certeza de que há soluções alternativas razoáveis em vigor, vá para a seção [planejando uma atualização](#planning-for-an-upgrade) deste guia.

### <a name="platform-capabilities"></a>Recursos da plataforma

Esta seção descreve quais Data Lake Storage Gen1 recursos de plataforma que estão disponíveis atualmente no Data Lake Storage Gen2.

| | Armazenamento do Data Lake Ger1 | Meta de Data Lake Storage Gen2 | Status de Data Lake Storage Gen2 disponibilidade  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| Organização de dados| Dá suporte a dados armazenados como pastas e arquivos | Dá suporte a dados armazenados como objetos/BLOBs, bem como pastas e arquivos- [link](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Dá suporte a dados armazenados como pastas e arquivos – *disponíveis agora* <br><br> Dá suporte a dados armazenados como objetos/BLOBs- *ainda não disponível* |
| Namespace| Hierárquica | Hierárquica |  *Disponível agora*  |
| API  | API REST sobre HTTPS | API REST sobre HTTP/HTTPS| *Disponível agora* |
| API do lado do servidor| [API REST compatível com WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) | API REST do serviço blob do Azure [Data Lake Storage Gen2 API REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | API REST do Data Lake Storage Gen2 – *disponível agora* <br><br> API REST do serviço blob do Azure – *disponível agora*       |
| Cliente do sistema de arquivos Hadoop | Sim ([Azure data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Sim ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Disponível agora*  |  
| Operações de dados – autorização  | Listas de controle de acesso POSIX (ACLs) de nível de arquivo e de pasta com base em identidades de Azure Active Directory  | As listas de controle de acesso POSIX (ACLs) de nível de arquivo e de pasta com base em identidades de Azure Active Directory [compartilham chave](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) para[RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)(controle de acesso baseado em função) de autorização de nível de conta para acessar contêineres | *Disponível agora* |
| Operações de dados – logs  | Sim | Sim | *Disponível agora* (visualização). Consulte [problemas conhecidos](data-lake-storage-known-issues.md).<br><br> Integração do monitoramento do Azure – *ainda não disponível* |
| Dados de criptografia em repouso | Transparente, lado do servidor com chaves gerenciadas pelo serviço e com chaves gerenciadas pelo cliente no Azure keyvault | Transparente, lado do servidor com chaves gerenciadas pelo serviço e com chaves gerenciadas pelas chaves do cliente no Azure keyvault | Chaves gerenciadas pelo serviço – *disponíveis agora*<br><br> Chaves gerenciadas pelo cliente – *disponíveis agora*  |
| Operações de gerenciamento (por exemplo, criação de conta) | RBAC ( [controle de acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview) ) fornecido pelo Azure para gerenciamento de conta | RBAC ( [controle de acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview) ) fornecido pelo Azure para gerenciamento de conta | *Disponível agora*|
| SDKs de desenvolvedor | .NET, Java, Python, Node. js  | .NET, Java, Python, Node. js, C++, Ruby, PHP, go, Android, Ios| SDK do blob – *disponível agora*. SDK do Azure Data Lake Storage Gen2- *ainda não disponível*  |
| |Desempenho otimizado para cargas de trabalho de análise paralela. Alta taxa de transferência e IOPS. | Desempenho otimizado para cargas de trabalho de análise paralela. Alta taxa de transferência e IOPS. | *Disponível agora* |
| Suporte à VNet (rede virtual)  | [Usando a integração de rede virtual](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Usando o ponto de extremidade de serviço para o armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Disponível agora* |
| Limites de tamanho | Não há limites de tamanhos de conta, tamanhos de arquivo ou número de arquivos | Não há limites de tamanhos de conta ou número de arquivos. Tamanho do arquivo limitado a 5 TB. | *Disponível agora*|
| Redundância geográfica| Com redundância local (LRS) | Localmente redundante (LRS) com redundância de zona (ZRS) com redundância geográfica de acesso de leitura (GRS) com redundância geográfica (RA-GRS), consulte [aqui](https://docs.microsoft.com/azure/storage/common/storage-redundancy) para obter mais informações| *Disponível agora* |
| Disponibilidade regional | Veja [aqui](https://azure.microsoft.com/regions/) | Todas as [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Disponível agora*                                                                                                                           |
| Preço                                       | Consulte os [preços](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | Consulte os [preços](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| SLA de disponibilidade                            | [Consulte SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Consulte SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Disponível agora*                                                                                                                           |
| Gestão de Dados                             | Expiração do arquivo                                                                                                                                        | Políticas de ciclo de vida                                                                                                                                                                                                                                                                                                                                                                                                          | Políticas de ciclo de vida *disponíveis agora* (visualização). Consulte [problemas conhecidos](data-lake-storage-known-issues.md).                                                                                                                     |

### <a name="programming-interfaces"></a>Interfaces de programação

Esta tabela descreve quais conjuntos de API estão disponíveis para seus aplicativos personalizados. O suporte do SDK para operações de ACL e de nível de diretório ainda não tem suporte.

|  Conjunto de API                           |  Armazenamento do Data Lake Ger1                                                                                                                                                                                                                                                                                                   | Disponibilidade para Data Lake Storage Gen2-com autenticação de chave compartilhada | Disponibilidade para Data Lake Storage Gen2-com Autenticação OAuth                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SDK .NET                  | [Ligação](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet) | *Disponível agora* | *Disponível agora* |
| Java SDK                | [Ligação](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Disponível agora*                                                      | *Disponível agora*                                     |
| Node.js                |   de [link](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                               | *Disponível agora*                                                     | *Disponível agora*                                                                                           |
| Python SDK                   |   de [link](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                | *Disponível agora*                                                      | *Disponível agora*                                        |
| API REST                 | [Ligação](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Disponível agora*                                                      | *Disponível agora*                                                                                                                                               |
| PowerShell | [Ligação](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | *Disponível agora* |
| CLI                 | [Ligação](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Disponível agora*                                                      | *Disponível agora*                                                               |
| Modelos de Azure Resource Manager – gerenciamento             | [Template1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Disponível agora*                                                      | *Disponível agora*                                          |

### <a name="azure-ecosystem"></a>Ecossistema do Azure

Ao usar o Data Lake Storage Gen1, você pode usar uma variedade de produtos e serviços da Microsoft em seus pipelines de ponta a ponta. Esses serviços e produtos funcionam com o Data Lake Storage Gen1 direta ou indiretamente. Esta tabela mostra uma lista dos serviços que modificamos para trabalhar com Data Lake Storage Gen1 e mostra quais deles são atualmente compatíveis com Data Lake Storage Gen2.

| **Área**             | **Disponibilidade para Data Lake Storage Gen1**                                                                                                                                    | **Disponibilidade para Data Lake Storage Gen2 – com autenticação de chave compartilhada**                                                                                                           | **Disponibilidade para Data Lake Storage Gen2 – com o OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Estrutura de análise  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Disponível agora*                                                                                                                                                              | *Disponível agora*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [Hdinsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3,6- *disponível agora* Hdinsight 4,0- *ainda não disponível*      | HDInsight 3,6 ESP – *disponível agora* <br><br>  HDInsight 4,0 ESP- *ainda não disponível*                                                                 |
|                      | Databricks Runtime 3,1 e superior                                                                                                                                               | [Databricks Runtime 5,1 e acima](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *– disponível agora* | [Databricks Runtime 5,1 e acima](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) – *disponível agora*                                                                                              |
|                      | [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Não suportado*                                                                                                                                                              | [Link](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) *disponível agora* |
| Integração de dados     | [Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [Versão 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *disponível agora* versão 1 – *sem suporte*                               | [Versão 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *disponível agora* <br><br> Versão 1 – *sem suporte*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Não suportado*                                                                                                                                                              | *Não suportado*                                                                                                                                 |
|                      | [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Disponível agora*                                                                                                                                                          | *Disponível agora*                                                                                                                             |
|                      | [Catálogo de Dados](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Ainda não disponível*                                                                                                                                                          | *Ainda não disponível*                                                                                                                             |
|                      | [Aplicações Lógicas](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Disponível agora*                                                                                                                                                          | *Disponível agora*                                                                                                                             |
| IoT                  | [Hubs de eventos – capturar](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Disponível agora*                                                                                                                                                          | *Disponível agora*                                                                                                                             |
|                      | [Stream Analytics](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Disponível agora*                                                                                                                                                          | *Disponível agora*                                                                                                                             |
| Consumo          | [Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Disponível agora*                                                                                                                                                          | *Disponível agora*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Ainda não disponível*                                                                                                                                                          | *Ainda não disponível*                                                                                                                             |
|                      | [Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Ainda não disponível*                                                                                                                                                          | *Ainda não disponível*                                                                                                                             |
| Produtividade         | [Portal do Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Não suportado*                                                                                                                                                              | Gerenciamento de conta *– disponível agora* <br><br>Operações de dados *–*  *ainda não disponíveis*                                                                   |
|                      | [Ferramentas de Data Lake para o Visual Studio](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Ainda não disponível*                                                                                                                                                          | *Ainda não disponível*                                                                                                                             |
|                      | [Explorador do Armazenamento do Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Disponível agora*                                                                                                                                                              | *Disponível agora*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Ainda não disponível*                                                                                                                                                          | *Ainda não disponível*                                                                                                                             |

### <a name="partner-ecosystem"></a>Ecossistema de parceiros

Esta tabela mostra uma lista dos serviços e produtos de terceiros que foram modificados para funcionar com Data Lake Storage Gen1. Ele também mostra quais são atualmente compatíveis com Data Lake Storage Gen2.

| Área            | Partner  | Produto/serviço  | Disponibilidade para Data Lake Storage Gen1                                                                                                                                               | Disponibilidade para Data Lake Storage Gen2 – com autenticação de chave compartilhada                                                   | Disponibilidade para Data Lake Storage Gen2 – com o OAuth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Estrutura de análise | Cloudera     | CDH                  | [Ligação](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Ainda não disponível*                                                                                                  | [Ligação](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [Ligação](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Não suportado*                                                                                                                  | *Ainda não disponível*                                                                                                  |
|                     | HortonWorks  | HDP 3,0              |   de [link](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                      | *Ainda não disponível*                                                                                                  | *Ainda não disponível*                                                                                                  |
|                     | Qubole       |                      | [Ligação](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Ainda não disponível*                                                                                                  | *Ainda não disponível*                                                                                                  |
| Extração, Transformação e Carregamento                 | StreamSets   |                      | [Ligação](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Ainda não disponível*                                                                                                  | *Ainda não disponível*                                                                                                  |
|                     | Informatica  |                      | [Ligação](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Ainda não disponível*                                                                                                  | *Ainda não disponível*                                                                                                  |
|                     | Attunity     |                      | [Ligação](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Ainda não disponível*                                                                                                  | *Ainda não disponível*                                                                                                  |
|                     | Alteryx      |                      | [Ligação](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Ainda não disponível*                                                                                                  | *Ainda não disponível*                                                                                                  |
|                     | ImanisData   |                      | [Ligação](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Ainda não disponível*                                                                                                  | *Ainda não disponível*                                                                                                  |
|                     | WANdisco     |                      | [Ligação](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Ligação](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Ligação](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Informações operacionais

Data Lake Storage Gen1 envia informações e dados específicos para outros serviços, o que ajuda você a colocar seus pipelines em operação. Esta tabela mostra a disponibilidade do suporte correspondente no Data Lake Storage Gen2.

| Tipo de dados                                                                                       | Disponibilidade para Data Lake Storage Gen1                                                                 | Disponibilidade para Data Lake Storage Gen2                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Dados de cobrança – medidores enviados à equipe de comércio para cobrança e disponibilizados para os clientes  | *Disponível agora*                                                                                             | *Disponível agora*                                                                                                                           |
| Registos de atividade                                                                                          | [Ligação](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Solicitações one-off para logs de duração específica usando o tíquete de suporte – *disponível agora* integração de monitoramento do Azure- *ainda não disponível* |
| Registos de diagnósticos                                                                                        | [Ligação](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | *Disponível agora* (visualização). Consulte [problemas conhecidos](data-lake-storage-known-issues.md). <br>Integração do monitoramento do Azure- *ainda não disponível* |
| Métricas                                                                                                | *Não suportado*                                                                                               | *Disponível agora-* [link](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Planejando uma atualização

Esta seção pressupõe que você analisou a seção [avaliar a preparação de atualização](#assess-your-upgrade-readiness) deste guia e que todas as suas dependências foram atendidas. Se houver recursos que ainda não estão disponíveis no Data Lake Storage Gen2, continue somente se você souber as soluções alternativas correspondentes. As seções a seguir fornecem orientação sobre como você pode planejar a atualização de seus pipelines. A execução da atualização real será descrita na seção [executando a atualização](#performing-the-upgrade) deste guia.

### <a name="upgrade-strategy"></a>Estratégia de atualização

A parte mais crítica da atualização é decidir a estratégia. Essa decisão determinará as opções disponíveis para você.

Esta tabela lista algumas estratégias conhecidas que foram usadas para migrar bancos de dados, clusters Hadoop, etc. Adotaremos estratégias semelhantes em nossas diretrizes e as adaptaremos ao nosso contexto.

| Estratégia                   | Prós                                                                                  | Contras                                                           | Quando utilizar?                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Comparação de precisão e deslocamento                 | Mais simples.                                                                                 | Requer tempo de inatividade para copiar dados, mover trabalhos e mover a entrada e saída                                             | Para soluções mais simples, em que não há várias soluções acessando a mesma conta do Gen1 e, portanto, podem ser movidas juntas de maneira rápida controlada.                                                  |
| Copiar uma vez e copiar incremental | Reduza o tempo de inatividade ao executar a cópia em segundo plano enquanto o sistema de origem ainda estiver ativo. | Requer tempo de inatividade para mover a entrada e saída.                   | A quantidade de dados a ser copiada é grande e o tempo de inatividade associado à vida e mudança não é aceitável. O teste pode ser necessário com dados de produção significativos no sistema de destino antes da transição. |
| Adoção paralela              | O tempo de inatividade mínimo permite que os aplicativos migrem a seu próprio critério.           | A mais elaborada, pois a sincronização de duas vias é necessária entre os dois sistemas. | Para cenários complexos em que os aplicativos criados em Data Lake Storage Gen1 não podem ser transferidos de uma só vez e devem ser movidos de maneira incremental.                                                      |

Abaixo estão mais detalhes sobre as etapas envolvidas para cada uma das estratégias. As etapas listam o que você faria com os componentes envolvidos na atualização. Isso inclui o sistema de origem geral, o sistema de destino geral, as fontes de entrada para o sistema de origem, os destinos de saída para o sistema de origem e os trabalhos em execução no sistema de origem.

Essas etapas não devem ser prescritivas. Eles destinam-se a definir a estrutura sobre como estamos pensando em cada estratégia. Forneceremos estudos de caso para cada estratégia, pois vemos que elas estão sendo implementadas.

#### <a name="lift-and-shift"></a>Comparação de precisão e deslocamento

1. Pausar o sistema de origem – fontes de entrada, trabalhos, destinos de saída.
2. Copie todos os dados do sistema de origem para o sistema de destino.
3. Aponte todas as fontes de entrada para o sistema de destino. Aponte para o destino de saída do sistema de destino.
4. Mover, modificar, executar todos os trabalhos para o sistema de destino.
5. Desligue o sistema de origem.

#### <a name="copy-once-and-copy-incremental"></a>Cópia única e cópia incremental

1. Copie os dados do sistema de origem para o sistema de destino.
2. Copie os dados incrementais do sistema de origem para o sistema de destino em intervalos regulares.
3. Aponte para o destino de saída do sistema de destino.
4. Mover, modificar, executar todos os trabalhos no sistema de destino.
5. Aponte fontes de entrada incrementalmente para o sistema de destino de acordo com sua conveniência.
6. Quando todas as fontes de entrada estiverem apontando para o sistema de destino.
    1. Desative a cópia incremental.
    2. Desligue o sistema de origem.

#### <a name="parallel-adoption"></a>Adoção paralela

1. Configure o sistema de destino.
2. Configure uma replicação bidirecional entre o sistema de origem e o sistema de destino.
3. Apontar fontes de entrada incrementalmente para o sistema de destino.
4. Mover, modificar, executar trabalhos incrementalmente para o sistema de destino.
5. Aponte para destinos de saída incrementalmente do sistema de destino.
6. Depois que todas as fontes de entrada originais, trabalhos e destino de saída estiverem trabalhando com o sistema de destino, desligue o sistema de origem.

### <a name="data-upgrade"></a>Atualização de dados

A estratégia geral que você usa para executar a atualização (descrita na seção [estratégia de atualização](#upgrade-strategy) deste guia) determinará as ferramentas que você pode usar para a atualização de dados. As ferramentas listadas abaixo se baseiam em informações atuais e são sugestões. 

#### <a name="tools-guidance"></a>Diretrizes de ferramentas

| Estratégia                       | Ferramentas                                                                                                             | Prós                                                                                                                             | Considerações                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Comparação de precisão e deslocamento**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Serviço de nuvem gerenciado                                                                                                                | Os dados e as ACLs podem ser copiados no momento.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Permissões de ferramenta conhecidas, fornecidas pelo Hadoop, ou seja, as ACLs podem ser copiadas com essa ferramenta                                                   | Requer um cluster que possa se conectar a Data Lake Storage Gen1 e Gen2 ao mesmo tempo.                                                                                                                                                                                   |
| **Copiar uma vez e copiar incremental** | Azure Data Factory                                                                                                    | Serviço de nuvem gerenciado                                                                                                                | Os dados e as ACLs podem ser copiados no momento. Para dar suporte à cópia incremental no ADF, os dados precisam ser organizados de uma maneira de série temporal. O intervalo mais curto entre as cópias incrementais é [15 minutos](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). |
| **Adoção paralela**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Suporte à replicação consistente se estiver usando um ambiente do Hadoop puro conectado ao Azure Data Lake Storage, dá suporte à replicação bidirecional | Se não estiver usando um ambiente de Hadoop puro, pode haver um atraso na replicação.                                                                                                                                                                                                                                                  |

Observe que há terceiros que podem lidar com a Data Lake Storage Gen1 Data Lake Storage Gen2 atualização sem envolver as ferramentas de cópia de dados/metadados acima (por exemplo: [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). Eles fornecem uma experiência de "um ponto de interrupção" que executa a migração de dados, bem como a migração de carga de trabalho. Talvez você precise executar uma atualização fora de banda para qualquer ferramenta que esteja fora de seu ecossistema.

#### <a name="considerations"></a>Considerações

* Você precisará criar manualmente a conta de Data Lake Storage Gen2 primeiro, antes de iniciar qualquer parte da atualização, já que a diretriz atual não inclui nenhum processo de conta Gen2 automático com base nas informações de sua conta do Gen1. Certifique-se de comparar os processos de criação de contas para [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) e [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account).

* Data Lake Storage Gen2, o só dá suporte a arquivos de até 5 TB. Para atualizar para o Data Lake Storage Gen2, talvez seja necessário redimensionar os arquivos no Data Lake Storage Gen1 para que eles tenham menos de 5 TB de tamanho.

* Se você usar uma ferramenta que não copia ACLs ou não deseja copiar as ACLs, você precisará definir as ACLs no destino manualmente no nível superior apropriado. Você pode fazer isso usando Gerenciador de Armazenamento. Certifique-se de que essas ACLs sejam as ACLs padrão para que os arquivos e pastas copiados por você as herdem.

* No Data Lake Storage Gen1, o nível mais alto que você pode definir ACLs está na raiz da conta. No Data Lake Storage Gen2, no entanto, o nível mais alto que você pode definir ACLs está na pasta raiz em um contêiner, não na conta inteira. Portanto, se você quiser definir ACLs padrão no nível da conta, será necessário duplicá-las em todos os sistemas de arquivos em sua conta do Data Lake Storage Gen2.

* As restrições de nomenclatura de arquivo são diferentes entre os dois sistemas de armazenamento. Essas diferenças são especialmente relacionadas ao copiar de Data Lake Storage Gen2 para Data Lake Storage Gen1, já que o último tem restrições mais restritas.

### <a name="application-upgrade"></a>Atualização da aplicação

Quando precisar criar aplicativos em Data Lake Storage Gen1 ou Data Lake Storage Gen2, você precisará primeiro escolher uma interface de programação apropriada. Ao chamar uma API nessa interface, você terá que fornecer o URI apropriado e as credenciais apropriadas. A representação desses três elementos, a API, o URI e a forma como as credenciais são fornecidas, é diferente entre Data Lake Storage Gen1 e Data Lake Storage Gen2.So, como parte da atualização do aplicativo, você precisará mapear essas três construções adequadamente.

#### <a name="uri-changes"></a>Alterações de URI

A principal tarefa aqui é converter os URIS que têm um prefixo de `adl://` em URIS que têm um prefixo `abfss://`.

O esquema de URI para Data Lake Storage Gen1 é mencionado [aqui](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) em detalhes, mas amplamente falando, é *adl://mydatalakestore.azuredatalakestore.net/\<file_path\>.*

O esquema de URI para acessar os arquivos de Data Lake Storage Gen2 é explicado [aqui](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) em detalhes, mas em geral, é `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`.

Você precisará percorrer os aplicativos existentes e garantir que você tenha alterado os URIs adequadamente para apontar para Data Lake Storage Gen2. Além disso, você precisará adicionar as credenciais apropriadas. Por fim, como você desativa os aplicativos originais e substitua pelo novo aplicativo precisará estar alinhado com sua estratégia geral de atualização.

#### <a name="custom-applications"></a>Aplicativos personalizados

Dependendo da interface que seu aplicativo usa com Data Lake Storage Gen1, você precisará modificá-lo para adaptá-lo ao Data Lake Storage Gen2.

##### <a name="rest-apis"></a>APIs REST

Se seu aplicativo usar Data Lake Storage APIs REST, você precisará modificar seu aplicativo para usar as APIs REST do Data Lake Storage Gen2. Os links são fornecidos na seção *interfaces de programação* .

##### <a name="sdks"></a>SDKs

Como foi chamado na seção *avaliar a preparação de atualização, os* SDKs não estão disponíveis no momento. Se você quiser que a porta sobre seus aplicativos Data Lake Storage Gen2, recomendamos que você aguarde a disponibilidade dos SDKs com suporte.

##### <a name="powershell"></a>PowerShell

Conforme chamado na seção avaliar a preparação de atualização, o suporte do PowerShell não está disponível no momento para o plano de dados.

Você pode substituir o plano de gerenciamento commandlets com os apropriados no Data Lake Storage Gen2. Os links são fornecidos na seção *interfaces de programação* .

##### <a name="cli"></a>CLI

Como chamado na seção *avaliar sua preparação para atualização* , o suporte à CLI não está disponível no momento para o plano de dados.

Você pode substituir os comandos do plano de gerenciamento com os apropriados no Data Lake Storage Gen2. Os links são fornecidos na seção *interfaces de programação* .

### <a name="analytics-frameworks-upgrade"></a>Atualização de estruturas de análise

Se seu aplicativo criar metadados sobre as informações na loja, como caminhos de arquivos e pastas explícitos, você precisará executar ações adicionais após a atualização de dados/metadados de armazenamento. Isso é especialmente verdadeiro para estruturas de análise, como Azure HDInsight, databricks, etc., que geralmente criam dados de catálogo nos dados da loja.

As estruturas de análise funcionam com dados e metadados armazenados em armazenamentos remotos, como Data Lake Storage Gen1 e Gen2. Portanto, teoricamente, os mecanismos podem ser efêmeros e são ativados somente quando os trabalhos precisam ser executados em relação aos dados armazenados.

No entanto, para otimizar o desempenho, as estruturas de análise podem criar referências explícitas para os arquivos e pastas armazenados no armazenamento remoto e, em seguida, criar um cache para contê-los. Se o URI dos dados remotos for alterado, por exemplo, um cluster que estava armazenando dados em Data Lake Storage Gen1 anteriormente e, agora, deseja armazenar em Data Lake Storage Gen2, o URI para o mesmo conteúdo copiado será diferente. Assim, depois que os dados e metadados são atualizados, os caches para esses mecanismos também precisam ser atualizadas ou inicializadas novamente

Como parte do processo de planejamento, você precisará identificar seu aplicativo e descobrir como as informações de metadados podem ser reinicializadas para apontar para os dados que agora estão armazenados em Data Lake Storage Gen2. Veja abaixo as diretrizes para estruturas de análise comumente adotadas para ajudá-lo com suas etapas de atualização.

#### <a name="azure-databricks"></a>Azure Databricks

Dependendo da estratégia de atualização escolhida, as etapas serão diferentes. A seção atual pressupõe que você tenha escolhido a estratégia de "vida útil e mudança". Além disso, o espaço de trabalho do databricks existente que usou para acessar dados em uma conta de Data Lake Storage Gen1 deve funcionar com os dados que são copiados para Data Lake Storage Gen2 conta.

Primeiro, certifique-se de que você criou a conta Gen2 e copiou os dados e meta de Gen1 para Gen2 usando uma ferramenta apropriada. Essas ferramentas são chamadas na seção de [atualização de dados](#data-upgrade) deste guia.

Em seguida, [atualize](https://docs.azuredatabricks.net/user-guide/clusters/index.html) seu cluster do databricks existente para começar a usar o databricks Runtime 5,1 ou superior, que deve dar suporte a data Lake Storage Gen2.

As etapas posteriores são baseadas em como o espaço de trabalho existente do databricks acessa os dados na conta de Data Lake Storage Gen1. Isso pode ser feito chamando URIs adl:// [diretamente](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) de notebooks ou por meio de [montagem](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs).

Se você estiver acessando diretamente de notebooks fornecendo os URIs adl://completos, precisará passar por cada bloco de anotações e alterar a configuração para acessar o URI de Data Lake Storage Gen2 correspondente.

No futuro, você precisará reconfigurá-lo para apontar para Data Lake Storage Gen2 conta. Nenhuma alteração é necessária e os notebooks devem ser capazes de trabalhar como antes.

Se você estiver usando qualquer uma das outras estratégias de atualização, poderá criar uma variação das etapas acima para atender às suas necessidades.

### <a name="azure-ecosystem-upgrade"></a>Atualização de ecossistema do Azure

Cada uma das ferramentas e serviços chamados na seção [ecossistema do Azure](#azure-ecosystem) deste guia precisará ser configurado para trabalhar com data Lake Storage Gen2.

Primeiro, verifique se há integração disponível com Data Lake Storage Gen2.

Em seguida, os elementos chamados acima (por exemplo: URI e credenciais) precisarão ser alterados. Você pode modificar a instância existente que funciona com Data Lake Storage Gen1 ou pode criar uma nova instância que funcionaria com Data Lake Storage Gen2.

### <a name="partner-ecosystem-upgrade"></a>Atualização do ecossistema do parceiro

Trabalhe com o parceiro, fornecendo o componente e as ferramentas para garantir que eles possam trabalhar com Data Lake Storage Gen2. 

## <a name="performing-the-upgrade"></a>Executando a atualização

### <a name="pre-upgrade"></a>Pré-atualização

Como parte disso, você terá feito a seção *avaliar sua preparação de atualização* e a seção [planejamento para uma atualização](#planning-for-an-upgrade) deste guia, você recebeu todas as informações necessárias e criou um plano que atenderia às suas necessidades de. Você provavelmente terá uma tarefa de teste durante essa fase.

### <a name="in-upgrade"></a>Em atualização

Dependendo da estratégia escolhida e das complexidades de sua solução, essa fase pode ser uma pequena ou uma estendida em que há várias cargas de trabalho aguardando para serem incrementadas de forma incremental para Data Lake Storage Gen2. Essa será a parte mais crítica da sua atualização.

### <a name="post-upgrade"></a>Pós-atualização

Depois de concluir a operação de transição, as etapas finais envolverão uma verificação completa. Isso inclui, mas não está limitado a verificar se os dados foram copiados de forma confiável, verificando se as ACLs foram definidas corretamente, verificando se os pipelines de ponta a ponta estão funcionando corretamente, etc. Depois que as verificações forem concluídas, você poderá agora desativar seus pipelines antigos, excluir suas contas de Data Lake Storage Gen1 de origem e obter velocidade completa em suas soluções baseadas em Data Lake Storage Gen2.

## <a name="conclusion"></a>Conclusão

As diretrizes fornecidas neste documento devem ter ajudado você a atualizar sua solução para usar o Data Lake Storage Gen2. 

Se você tiver mais perguntas ou tiver comentários, forneça comentários abaixo ou forneça comentários no fórum de [comentários do Azure](https://feedback.azure.com/forums/327234-data-lake).
