---
title: Atualizar Azure Data Lake Storage de Gen1 para Gen2
description: Atualize Azure Data Lake Storage de Gen1 para Gen2.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 11/19/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: rugopala
ms.openlocfilehash: 41074561b4805fef1889bd889b625e1a59d57d91
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327871"
---
# <a name="upgrade-azure-data-lake-storage-from-gen1-to-gen2"></a>Atualizar Azure Data Lake Storage de Gen1 para Gen2

Se você estiver usando Azure Data Lake Storage Gen1 em suas soluções do Big Data Analytics, este guia o ajudará a atualizar essas soluções para usar o Azure Data Lake Storage Gen2. Pode utilizar este documento para avaliar as dependências que sua solução tem na geração 1 de armazenamento do Data Lake. Este guia também mostra como planear e efetuar a atualização.

Podemos irá ajudá-lo através das seguintes tarefas:

: heavy_check_mark: avaliar sua prontidão para atualização

: heavy_check_mark: planear uma atualização

: heavy_check_mark: efetuar a atualização

## <a name="assess-your-upgrade-readiness"></a>Avaliar a preparação de sua atualização

Nosso objetivo é que todas as capacidades que estão presentes no Data Lake Storage Gen1 serão disponibilizadas na geração 2 de armazenamento do Data Lake. Como esses recursos são expostos por exemplo, no SDK, etc. CLI, poderão diferir entre o Data Lake Storage Gen1 e Gen2 de armazenamento do Data Lake. Aplicações e serviços que funcionam com o Data Lake Storage Gen1 precisam ser capazes de trabalhar da mesma forma com geração 2 de armazenamento do Data Lake. Por fim, algumas das funcionalidades não estarão disponíveis na geração 2 de armazenamento do Data Lake imediatamente. À medida que ficam disponíveis, serão anunciados-los neste documento.

Estas secções seguintes irão ajudá-lo a decidir a melhor maneira atualizar para ger2 de armazenamento do Data Lake e o pode fazer mais sentido para o fazer.

### <a name="data-lake-storage-gen1-solution-components"></a>Componentes da solução do Data Lake Storage Gen1

É muito provável que, quando usa o Gen1 de armazenamento do Data Lake em suas soluções de análise ou pipelines, há muitas tecnologias adicionais que empregam para atingir a funcionalidade geral ponto-a-ponto. Este [artigo](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) descreve vários componentes do fluxo de dados que incluem a ingestão, o processamento e o consumo de dados.

Além disso, existem componentes abrangentes para aprovisionar, gerir e monitorizar esses componentes. Cada um dos componentes operam com a geração 1 de armazenamento do Data Lake, utilizando uma interface mais adequada aos mesmos. Quando estiver a planear atualizar a sua solução de geração 2 de armazenamento do Data Lake, precisará estar ciente das interfaces que são utilizadas. Precisará atualizar tanto a interfaces de gerenciamento, bem como interfaces de dados, uma vez que cada interface tem requisitos distintos.

![Componentes da solução do Data Lake Storage](./media/data-lake-storage-upgrade/solution-components.png)

A **Figura 1** acima mostra os componentes de funcionalidade que você veria na maioria das soluções de análise.

A **Figura 2** mostra um exemplo de como esses componentes serão implementados usando tecnologias específicas.

A funcionalidade de armazenamento no **Figura 1** é fornecida por data Lake Storage Gen1 (**Figura 2**). Observe como os vários componentes do fluxo de dados interagem com a geração 1 de armazenamento do Data Lake ao utilizar REST APIs ou SDK de Java. Observe também como os componentes de funcionalidades transversais interagem com a geração 1 de armazenamento do Data Lake. O componente de provisionamento usa modelos de recursos do Azure, enquanto o componente de monitoramento que usa Azure Monitor logs utiliza dados operacionais provenientes de Data Lake Storage Gen1.

Para atualizar uma solução da utilização de geração 1 de armazenamento do Data Lake para geração 2 de armazenamento do Data Lake, precisará copiar os dados e metadados, vincular novamente os fluxos de dados e, em seguida, todos os componentes terão de ser capazes de trabalhar com a geração 2 de armazenamento do Data Lake.

As seções abaixo fornecem informações para ajudar a tomar melhores decisões:

: heavy_check_mark: capacidades da plataforma

: heavy_check_mark: as interfaces de programação

: heavy_check_mark: ecossistema do Azure

: heavy_check_mark: ecossistema de parceiros

: heavy_check_mark: informações operacionais

Cada secção, irá ser capaz de determinar os "indispensáveis" para a atualização. Depois de ter certeza de que os recursos estão disponíveis, ou se tiver certeza de que há soluções alternativas razoáveis em vigor, vá para a seção [planejando uma atualização](#planning-for-an-upgrade) deste guia.

### <a name="platform-capabilities"></a>Capacidades da plataforma

Esta secção descreve as capacidades da plataforma de geração 1 de armazenamento do Data Lake que estão atualmente disponíveis na geração 2 de armazenamento do Data Lake.

| | Armazenamento do Data Lake Ger1 | Data Lake Storage Gen2 - objetivo | Data Lake Storage Gen2 - estado de disponibilidade  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| Organização de dados| Suporta dados armazenados como arquivos e pastas | Dá suporte a dados armazenados como objetos/BLOBs, bem como pastas e arquivos- [link](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Dá suporte a dados armazenados como pastas e arquivos – *disponíveis agora* <br><br> Dá suporte a dados armazenados como objetos/BLOBs- *ainda não disponível* |
| Espaço de Nomes| Hierárquica | Hierárquica |  *Disponível agora*  |
| API  | API de REST através de HTTPS | API de REST através de HTTP/HTTPS| *Disponível agora* |
| API do lado do servidor| [API REST compatível com WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) | API REST do serviço blob do Azure [Data Lake Storage Gen2 API REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | API REST do Data Lake Storage Gen2 – *disponível agora* <br><br> API REST do serviço blob do Azure – *disponível agora*       |
| Cliente de sistema de ficheiro do Hadoop | Sim ([Azure data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Sim ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Disponível agora*  |  
| Operações de dados – autorização  | Arquivo e pasta de nível POSIX acesso listas de controle (ACLs) com base em identidades do Active Directory do Azure  | As listas de controle de acesso POSIX (ACLs) de nível de arquivo e de pasta com base em identidades de Azure Active Directory [compartilham chave](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) para[RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)(controle de acesso baseado em função) de autorização de nível de conta para acessar contêineres | *Disponível agora* |
| Operações de dados – registos  | Sim | Sim | *Disponível agora* (visualização). Consulte [problemas conhecidos](data-lake-storage-known-issues.md).<br><br> Integração do monitoramento do Azure – *ainda não disponível* |
| Dados de encriptação em repouso | Transparente, do lado do servidor com chaves geridas pelo serviço e com chaves geridas pelo cliente no Azure KeyVault | Transparente, do lado do servidor com chaves geridas pelo serviço e com chaves cliente chaves geridas no Azure KeyVault | Chaves gerenciadas pelo serviço – *disponíveis agora*<br><br> Chaves gerenciadas pelo cliente – *disponíveis agora*  |
| Operações de gestão (por exemplo, criar conta) | RBAC ( [controle de acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview) ) fornecido pelo Azure para gerenciamento de conta | RBAC ( [controle de acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview) ) fornecido pelo Azure para gerenciamento de conta | *Disponível agora*|
| SDKs de programador | .NET, node. js de Java, Python,  | .NET, Java, Python, node. js, C++, Ruby, PHP, Go, Android, iOS| SDK do blob – *disponível agora*. SDK do Azure Data Lake Storage Gen2- *ainda não disponível*  |
| |Desempenho otimizado para cargas de trabalho de análise paralela. Alto débito e IOPS. | Desempenho otimizado para cargas de trabalho de análise paralela. Alto débito e IOPS. | *Disponível agora* |
| Suporte à VNet (rede virtual)  | [Usando a integração de rede virtual](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Usando o ponto de extremidade de serviço para o armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Disponível agora* |
| Limites de tamanho | Sem limites de tamanhos de conta, tamanhos de ficheiro ou número de ficheiros | Sem limites de tamanhos de conta ou o número de ficheiros. Limitado a 5TB de tamanho de ficheiro. | *Disponível agora*|
| Redundância geográfica| Localmente redundante (LRS) | Localmente redundante (LRS) com redundância de zona (ZRS) com redundância geográfica de acesso de leitura (GRS) com redundância geográfica (RA-GRS), consulte [aqui](https://docs.microsoft.com/azure/storage/common/storage-redundancy) para obter mais informações| *Disponível agora* |
| Disponibilidade regional | Veja [aqui](https://azure.microsoft.com/regions/) | Todas as [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Disponível agora*                                                                                                                           |
| Preço                                       | Consulte os [preços](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | Consulte os [preços](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| SLA de disponibilidade                            | [Consulte SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Consulte SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Disponível agora*                                                                                                                           |
| Gestão de Dados                             | Expiração de ficheiros                                                                                                                                        | Políticas de ciclo de vida                                                                                                                                                                                                                                                                                                                                                                                                          | Políticas de ciclo de vida *disponíveis agora* (visualização). Consulte [problemas conhecidos](data-lake-storage-known-issues.md).                                                                                                                     |

### <a name="programming-interfaces"></a>Interfaces de programação

A tabela seguinte descreve que API define que estão disponíveis para as suas aplicações personalizadas. O suporte do SDK para operações de ACL e de nível de diretório ainda não tem suporte.

|  Conjunto de APIS                           |  Armazenamento do Data Lake Ger1                                                                                                                                                                                                                                                                                                   | Disponibilidade de geração 2 de armazenamento do Data Lake - com autenticação de chave partilhada | Disponibilidade de geração 2 de armazenamento do Data Lake - com autenticação do OAuth                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SDK .NET                  | [Ligação](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet) | *Disponível agora* | *Disponível agora* |
| SDK Java                | [Ligação](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Disponível agora*                                                      | *Disponível agora*                                     |
| Node.js                |   de [link](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                               | *Disponível agora*                                                     | *Disponível agora*                                                                                           |
| SDK Python                   |   de [link](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                | *Disponível agora*                                                      | *Disponível agora*                                        |
| API REST                 | [Ligação](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Disponível agora*                                                      | *Disponível agora*                                                                                                                                               |
| PowerShell | [Ligação](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | *Disponível agora* |
| CLI                 | [Ligação](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Disponível agora*                                                      | *Disponível agora*                                                               |
| Modelos do Azure Resource Manager - gerenciamento             | [Template1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Disponível agora*                                                      | *Disponível agora*                                          |

### <a name="azure-ecosystem"></a>Ecossistema do Azure

Ao utilizar o Data Lake Storage Gen1, pode utilizar uma variedade de serviços da Microsoft e produtos em seus pipelines de ponto-a-ponto. Estes serviços e produtos funcionam com geração 1 de armazenamento do Data Lake direta ou indiretamente. Esta tabela mostra uma lista dos serviços que podemos ter modificado para funcionar com a geração 1 de armazenamento do Data Lake e mostra quais os que são atualmente compatíveis com geração 2 de armazenamento do Data Lake.

| **Área**             | **Disponibilidade para Data Lake Storage Gen1**                                                                                                                                    | **Disponibilidade para Data Lake Storage Gen2 – com autenticação de chave compartilhada**                                                                                                           | **Disponibilidade para Data Lake Storage Gen2 – com o OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Estrutura de análise  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Disponível agora*                                                                                                                                                              | *Disponível agora*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [Hdinsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3,6- *disponível agora* Hdinsight 4,0- *ainda não disponível*      | HDInsight 3,6 ESP – *disponível agora* <br><br>  HDInsight 4,0 ESP- *ainda não disponível*                                                                 |
|                      | Runtime do Databricks 3.1 e acima                                                                                                                                               | [Databricks Runtime 5,1 e acima](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *– disponível agora* | [Databricks Runtime 5,1 e acima](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) – *disponível agora*                                                                                              |
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

Esta tabela mostra uma lista dos serviços de terceiros e produtos que foram modificados para trabalhar com a geração 1 de armazenamento do Data Lake. Ela também mostra quais os que são atualmente compatíveis com geração 2 de armazenamento do Data Lake.

| Área            | Parceiro  | Produtos/serviços  | Disponibilidade para a geração 1 do Data Lake Storage                                                                                                                                               | Disponibilidade de geração 2 de armazenamento do Data Lake – com autenticação de chave partilhada                                                   | Disponibilidade para Gen2 Lake armazenamento de dados – com o Oauth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Estrutura de análise | Cloudera     | CDH                  | [Ligação](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Ainda não disponível*                                                                                                  | [Ligação](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [Ligação](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Não suportado*                                                                                                                  | *Ainda não disponível*                                                                                                  |
|                     | HortonWorks  | HDP 3.0              |   de [link](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                      | *Ainda não disponível*                                                                                                  | *Ainda não disponível*                                                                                                  |
|                     | Qubole       |                      | [Ligação](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Ainda não disponível*                                                                                                  | *Ainda não disponível*                                                                                                  |
| Extração, Transformação e Carregamento                 | StreamSets   |                      | [Ligação](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Ainda não disponível*                                                                                                  | *Ainda não disponível*                                                                                                  |
|                     | Informatica  |                      | [Ligação](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Ainda não disponível*                                                                                                  | *Ainda não disponível*                                                                                                  |
|                     | Attunity     |                      | [Ligação](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Ainda não disponível*                                                                                                  | *Ainda não disponível*                                                                                                  |
|                     | Alteryx      |                      | [Ligação](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Ainda não disponível*                                                                                                  | *Ainda não disponível*                                                                                                  |
|                     | ImanisData   |                      | [Ligação](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Ainda não disponível*                                                                                                  | *Ainda não disponível*                                                                                                  |
|                     | WANdisco     |                      | [Ligação](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Ligação](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Ligação](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Informações operacionais

Geração 1 de armazenamento do Data Lake envia por push dados e informações específicas para outros serviços que ajuda-o para operacionalizar os seus pipelines. Esta tabela mostra a disponibilidade de suporte correspondente na geração 2 de armazenamento do Data Lake.

| Tipo de dados                                                                                       | Disponibilidade para a geração 1 do Data Lake Storage                                                                 | Disponibilidade de geração 2 do Data Lake Storage                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Os dados de faturação - medidores que são enviados para o comércio da Equipe para faturação e, em seguida, disponibilizada para clientes  | *Disponível agora*                                                                                             | *Disponível agora*                                                                                                                           |
| Registos de atividade                                                                                          | [Ligação](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Solicitações one-off para logs de duração específica usando o tíquete de suporte – *disponível agora* integração de monitoramento do Azure- *ainda não disponível* |
| Registos de diagnósticos                                                                                        | [Ligação](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | *Disponível agora* (visualização). Consulte [problemas conhecidos](data-lake-storage-known-issues.md). <br>Integração do monitoramento do Azure- *ainda não disponível* |
| Métricas                                                                                                | *Não suportado*                                                                                               | *Disponível agora-* [link](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Planear uma atualização

Esta seção pressupõe que você analisou a seção [avaliar a preparação de atualização](#assess-your-upgrade-readiness) deste guia e que todas as suas dependências foram atendidas. Se existirem recursos que ainda não estiverem disponíveis na geração 2 de armazenamento do Data Lake, continue apenas se souber as soluções alternativas correspondentes. As secções seguintes fornecem orientações sobre como pode planejar para a atualização dos seus pipelines. A execução da atualização real será descrita na seção [executando a atualização](#performing-the-upgrade) deste guia.

### <a name="upgrade-strategy"></a>Estratégia de atualização

A parte mais importante da atualização é decidir a estratégia. Esta decisão irá determinar as opções disponíveis para si.

Esta tabela lista algumas estratégias conhecidas que foram usadas para migrar bancos de dados, clusters Hadoop, etc. Adotaremos estratégias semelhantes em nossas diretrizes e as adaptaremos ao nosso contexto.

| Estratégia                   | Profissionais de TI                                                                                  | Contras                                                           | Quando utilizar?                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Migração lift-and-shift                 | Mais simples.                                                                                 | Requer o período de indisponibilidade de copiar dados, tarefas de movimentação e móvel de entrada e saída                                             | Para soluções mais simples, em que não há várias soluções Acessando a mesma Gen1 conta e, por conseguinte, podem ser movidos em conjunto maneira controlada rápida.                                                  |
| Copiar uma vez-e-cópia incremental | Reduza o tempo de inatividade por efetuar a cópia em segundo plano enquanto o sistema de origem ainda estiver ativa. | Requer o tempo de inatividade para mover a entrada e saída.                   | Quantidade de dados ser copiadas é grande e o tempo de inatividade associado a vida-and-shift não é aceitável. Teste poderá ser necessário com dados de produção significativas no sistema de destino antes de transição. |
| Adoção paralela              | Permite que hora de tempo de inatividade, pelo menos, para aplicações migrar a sua própria critério.           | É necessária mais elaborado, desde a sincronização de 2 vias entre os dois sistemas. | Para cenários complexos, onde os aplicativos baseados em Gen1 de armazenamento do Data Lake não podem ser transferência ao mesmo tempo e tem de ser movidos através de uma forma incremental.                                                      |

Seguem-se mais detalhes sobre os passos envolvidos para cada uma das estratégias. Os passos listam o que faria com os componentes envolvidos na atualização. Isto inclui todo o sistema de origem, todo o sistema de destino, origens de entrada do sistema de origem, destinos de saída para o sistema de origem e tarefas em execução num sistema de origem.

Estes passos não devem ser prescritivas. Elas se destinam a definir a estrutura sobre como podemos está pensando em cada estratégia. Forneceremos estudos de caso para cada estratégia como verá-los a ser implementado.

#### <a name="lift-and-shift"></a>Migração lift-and-shift

1. Colocar em pausa o sistema de origem – origens de entrada, tarefas, destinos de saída.
2. Copie todos os dados de sistema de origem para o sistema de destino.
3. Ponto de todas as origens de entrada, ao sistema de destino. Aponte para o destino de saída do sistema de destino.
4. Mover, modificar, executar todas as tarefas para o sistema de destino.
5. Desative o sistema de origem.

#### <a name="copy-once-and-copy-incremental"></a>Copiar-uma vez e cópia incremental

1. Copie os dados do sistema de origem para o sistema de destino.
2. Copie os dados incrementais do sistema de origem para o sistema de destino em intervalos regulares.
3. Aponte para o destino de saída do sistema de destino.
4. Mover, modificar, executar todas as tarefas no sistema de destino.
5. Ponto origens de entrada de forma incremental para o sistema de destino de acordo com a sua comodidade.
6. Depois de todas as origens de entrada estão apontando para o sistema de destino.
    1. Desative cópia incremental.
    2. Desative o sistema de origem.

#### <a name="parallel-adoption"></a>Adoção paralela

1. Configure o sistema de destino.
2. Configure uma replicação bidirecional entre o sistema de origem e o sistema de destino.
3. Ponto origens de entrada de forma incremental para o sistema de destino.
4. Mover, modificar, executar tarefas de forma incremental para o sistema de destino.
5. Aponte para destinos de saída incrementalmente do sistema de destino.
6. Depois de todas as origens de entrada original, tarefas e o destino de saída estão trabalhando com o sistema de destino, desative o sistema de origem.

### <a name="data-upgrade"></a>Atualização de dados

A estratégia geral que você usa para executar a atualização (descrita na seção [estratégia de atualização](#upgrade-strategy) deste guia) determinará as ferramentas que você pode usar para a atualização de dados. As ferramentas listadas abaixo se baseiam em informações atuais e sugestões. 

#### <a name="tools-guidance"></a>Diretrizes de ferramentas

| Estratégia                       | Ferramentas                                                                                                             | Profissionais de TI                                                                                                                             | Considerações                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Comparação de precisão e deslocamento**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Serviço gerido na cloud                                                                                                                | Os dados e as ACLs podem ser copiados no momento.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Conhecidas ferramenta fornecidos pelo Hadoop, permissões, ou seja, as ACLs pode ser copiada com essa ferramenta                                                   | Necessita de um cluster que pode ligar ao Data Lake Storage Gen1 e Gen2 ao mesmo tempo.                                                                                                                                                                                   |
| **Copiar uma vez e copiar incremental** | Azure Data Factory                                                                                                    | Serviço gerido na cloud                                                                                                                | Os dados e as ACLs podem ser copiados no momento. Para suportar a cópia incremental no ADF, os dados têm de ser organizado de maneira de séries de tempo. O intervalo mais curto entre as cópias incrementais é [15 minutos](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). |
| **Adoção paralela**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Suporte de replicação consistente se usando um ambiente de Hadoop puro ligado ao armazenamento do Azure Data Lake, suporta a replicação bidirecional | Se não utilizar um ambiente de pura Hadoop, poderá haver um atraso na replicação.                                                                                                                                                                                                                                                  |

Observe que há terceiros que podem lidar com a Data Lake Storage Gen1 Data Lake Storage Gen2 atualização sem envolver as ferramentas de cópia de dados/metadados acima (por exemplo: [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). Eles fornecem uma experiência de "conveniências" que efetua a migração de dados, bem como a migração da carga de trabalho. Poderá ter de efetuar uma atualização de fora de banda de quaisquer ferramentas que estão fora do seu ecossistema.

#### <a name="considerations"></a>Considerações

* Terá de criar manualmente a conta de geração 2 de armazenamento do Data Lake em primeiro lugar, antes de iniciar qualquer parte da atualização, uma vez que a orientação atual não inclui qualquer processo de conta de geração 2 automático com base nas suas informações de conta de geração 1. Certifique-se de comparar os processos de criação de contas para [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) e [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account).

* Geração 2 de armazenamento do Data Lake, apenas suporta os arquivos de até 5 TB de tamanho. Para atualizar para o Data Lake Storage Gen2, talvez seja necessário redimensionar os arquivos no Data Lake Storage Gen1 para que eles tenham menos de 5 TB de tamanho.

* Se usar uma ferramenta que não copia as ACLs ou não pretende copiar sobre as ACLs, terá de definir as ACLs no destino manualmente no nível superior apropriado. Pode fazê-lo através do Explorador de armazenamento. Certifique-se de que as ACLs são as ACLs padrão para que os ficheiros e pastas que copie herdarem-los.

* Gen1 de armazenamento do Data Lake, é o nível mais elevado, pode definir ACLs na raiz da conta. No Data Lake Storage Gen2, no entanto, o nível mais alto que você pode definir ACLs está na pasta raiz em um contêiner, não na conta inteira. Então, se pretender definir ACLs padrão ao nível da conta, precisará duplicar através de todos os sistemas de ficheiros na sua conta de geração 2 de armazenamento do Data Lake.

* Restrições de nomenclatura de ficheiro são diferentes entre os dois sistemas de armazenamento. Essas diferenças são especialmente em relação a quando copiar de geração 2 de armazenamento do Data Lake para geração 1 de armazenamento do Data Lake, uma vez que o segundo tem mais restrita restrições.

### <a name="application-upgrade"></a>Atualização da aplicação

Quando precisar da criação de aplicativos Gen1 de armazenamento do Data Lake ou de geração 2 de armazenamento do Data Lake, terá de optar pela primeira vez uma interface de programação apropriada. Ao chamar uma API na interface terá de fornecer o URI apropriado e as credenciais apropriadas. A representação destes três elementos, a API, URI e como as credenciais são fornecidas, são diferente entre a geração 1 de armazenamento do Data Lake e Gen2.So de armazenamento do Data Lake, como parte da atualização do aplicativo, precisará mapear essas três construções adequadamente.

#### <a name="uri-changes"></a>Alterações URI

A principal tarefa aqui é converter os URIS que têm um prefixo de `adl://` em URIS que têm um prefixo de `abfss://`.

O esquema de URI para Data Lake Storage Gen1 é mencionado [aqui](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) em detalhes, mas amplamente falando, é *adl://mydatalakestore.azuredatalakestore.net/\<file_path\>.*

O esquema de URI para acessar os arquivos de Data Lake Storage Gen2 é explicado [aqui](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) em detalhes, mas em geral, é `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`.

Precisará passar por seus aplicativos existentes e certifique-se de que alterou os URIs adequadamente para apontar para a geração 2 de armazenamento do Data Lake aqueles. Além disso, terá de adicionar as credenciais apropriadas. Por fim, como extinguir os aplicativos originais e substitua o novo aplicativo terão de estar alinhado atentamente para a sua estratégia de atualização global.

#### <a name="custom-applications"></a>Aplicações personalizadas

Dependendo da interface a que sua aplicação utilizar com a geração 1 de armazenamento do Data Lake, terá de modificá-lo para adaptá-lo à geração 2 de armazenamento do Data Lake.

##### <a name="rest-apis"></a>APIs REST

Se o aplicativo utilizar APIs de REST de armazenamento do Data Lake, terá de modificar a sua aplicação para utilizar as APIs de REST do Data Lake armazenamento geração 2. Os links são fornecidos na seção *interfaces de programação* .

##### <a name="sdks"></a>SDKs

Como foi chamado na seção *avaliar a preparação de atualização, os* SDKs não estão disponíveis no momento. Se você quiser que a porta sobre seus aplicativos Data Lake Storage Gen2, recomendamos que você aguarde a disponibilidade dos SDKs com suporte.

##### <a name="powershell"></a>PowerShell

Como indicadas na avaliar a seção de preparação da atualização, suporte do PowerShell não está atualmente disponível para o plano de dados.

Pode substituir os commandlets do plano de gestão com aqueles apropriado na geração 2 de armazenamento do Data Lake. Os links são fornecidos na seção *interfaces de programação* .

##### <a name="cli"></a>CLI

Como chamado na seção *avaliar sua preparação para atualização* , o suporte à CLI não está disponível no momento para o plano de dados.

Poderia substituir comandos do plano de gestão por aqueles que o adequado na geração 2 de armazenamento do Data Lake. Os links são fornecidos na seção *interfaces de programação* .

### <a name="analytics-frameworks-upgrade"></a>Atualização de estruturas de análise

Se a sua aplicação cria metadados sobre as informações no arquivo, como o arquivo explícito e caminhos de pastas, terá de executar ações adicionais após atualizar os arquivo de dados/metadados. Isso é especialmente verdadeiro de estruturas de análise, como o Azure HDInsight, etc. do Databricks, que, normalmente, criar dados de catálogo nos dados do arquivo.

Estruturas de análise trabalham com dados e metadados armazenados nos arquivos de remotos, como o Data Lake Storage Gen1 e Gen2. Assim, em teoria, os motores podem ser efémeros e ser recuperados apenas quando precisam de tarefas executar em relação os dados armazenados.

No entanto, para otimizar o desempenho, as estruturas de análise podem criar referências explícitas para os ficheiros e pastas armazenadas no arquivo remoto e, em seguida, criar uma cache para armazená-los. Deve o URI de dados remotos alterar, por exemplo, um cluster que foi a armazenar dados no Data Lake Storage Gen1 anteriormente e, agora que desejam armazenar na geração 2 de armazenamento do Data Lake, o URI para o mesmo conteúdo copiado será diferente. Então, depois dos dados e metadados caches para estes mecanismos de atualização também tem de ser atualizada ou inicializada novamente

Como parte do processo de planejamento, terá de identificar seu aplicativo e descobrir como as informações de metadados podem ser reinicializadas para apontar para dados que estão agora armazenados numa geração 2 de armazenamento do Data Lake. Segue-se orientações para estruturas de análise normalmente adotados para ajudá-lo com suas etapas de atualização.

#### <a name="azure-databricks"></a>Azure Databricks

Dependendo da estratégia de atualização que escolher, os passos variam. A seção atual assume que escolheu a estratégia de "Vida-and-shift". Além disso, a área de trabalho existente do Databricks utilizado para aceder a dados de uma conta de geração 1 de armazenamento do Data Lake é esperada para trabalhar com os dados que são copiados para a conta de geração 2 de armazenamento do Data Lake.

Em primeiro lugar, certifique-se de que criou a conta de geração 2 e, em seguida, copiados sobre dados e a meta de geração 1 para geração 2 utilizando uma ferramenta adequada. Essas ferramentas são chamadas na seção de [atualização de dados](#data-upgrade) deste guia.

Em seguida, [atualize](https://docs.azuredatabricks.net/user-guide/clusters/index.html) seu cluster do databricks existente para começar a usar o databricks Runtime 5,1 ou superior, que deve dar suporte a data Lake Storage Gen2.

Os passos subsequentes são baseados em como a área de trabalho do Databricks existente acessa dados na conta do Data Lake Storage Gen1. Isso pode ser feito chamando URIs adl:// [diretamente](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) de notebooks ou por meio de [montagem](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs).

Se estiver a aceder diretamente a partir de blocos de notas, fornecendo o completa adl: / / URIs, precisará passar por cada bloco de notas e alterar a configuração para acessar o URI de geração 2 do armazenamento de Lake de dados correspondente.

Daqui em diante, precisará para reconfigurá-lo para apontar para a conta de geração 2 de armazenamento do Data Lake. Não existem mais alterações são necessárias e os blocos de notas devem ser capazes de trabalhar como antes.

Se estiver a utilizar qualquer uma das outras estratégias de atualização, pode criar uma variação dos passos acima para satisfazer os seus requisitos.

### <a name="azure-ecosystem-upgrade"></a>Atualização do ecossistema do Azure

Cada uma das ferramentas e serviços chamados na seção [ecossistema do Azure](#azure-ecosystem) deste guia precisará ser configurado para trabalhar com data Lake Storage Gen2.

Em primeiro lugar, certifique-se de que existe integração com a geração 2 de armazenamento do Data Lake.

Em seguida, os elementos descritas acima (por exemplo: URI e as credenciais), terá de ser alterado. Poderia modificar a instância existente que funciona com a geração 1 de armazenamento do Data Lake ou poderia criar uma nova instância que funcionaria com geração 2 de armazenamento do Data Lake.

### <a name="partner-ecosystem-upgrade"></a>Atualização do ecossistema de parceiros

Trabalhe em conjunto com o parceiro fornecendo o componente e ferramentas para garantir que podem trabalhar com a geração 2 de armazenamento do Data Lake. 

## <a name="performing-the-upgrade"></a>Efetuar a atualização

### <a name="pre-upgrade"></a>Pré-atualização

Como parte disso, você terá feito a seção *avaliar sua preparação de atualização* e a seção [planejamento para uma atualização](#planning-for-an-upgrade) deste guia, você recebeu todas as informações necessárias e criou um plano que atenderia às suas necessidades de. Provavelmente terá uma tarefa de teste durante esta fase.

### <a name="in-upgrade"></a>Na atualização

Consoante a estratégia que escolher e as complexidades da sua solução, esta fase podem ser um pequeno ou um expandida em que há várias cargas de trabalho a aguardar de forma incremental ser movido para a geração 2 de armazenamento do Data Lake. Esta será a parte mais importante da atualização.

### <a name="post-upgrade"></a>Pós-atualização

Depois de terminar com a operação de transição, os últimos passos envolverá verificação completa. Isso inclui, mas não está limitado a verificar se os dados foram copiados de forma confiável, verificando se as ACLs foram definidas corretamente, verificando se os pipelines de ponta a ponta estão funcionando corretamente, etc. Depois que as verificações forem concluídas, você poderá agora desativar seus pipelines antigos, excluir suas contas de Data Lake Storage Gen1 de origem e obter velocidade completa em suas soluções baseadas em Data Lake Storage Gen2.

## <a name="conclusion"></a>Conclusão

As orientações fornecidas neste documento devem tenham ajudado a atualizar a sua solução para utilizar a geração 2 de armazenamento do Data Lake. 

Se você tiver mais perguntas ou tiver comentários, forneça comentários abaixo ou forneça comentários no fórum de [comentários do Azure](https://feedback.azure.com/forums/327234-data-lake).
