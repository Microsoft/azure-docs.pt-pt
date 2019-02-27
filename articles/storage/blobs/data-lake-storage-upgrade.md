---
title: Atualizar as soluções de análise de macrodados de geração 1 de armazenamento do Azure Data Lake para geração 2 de armazenamento do Azure Data Lake
description: Atualizar a sua solução para utilizar a geração 2 de armazenamento do Azure Data Lake
services: storage
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 02/07/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: d07e11d005ac05c9bf048a48c5865885813707cb
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56874550"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2"></a>Atualizar as soluções de análise de macrodados de geração 1 de armazenamento do Azure Data Lake para geração 2 de armazenamento do Azure Data Lake

Se estiver a utilizar o Azure Data Lake Storage Gen1 nas suas soluções de análise de macrodados, este guia ajuda-o a atualizar essas soluções para utilizar a geração 2 de armazenamento do Azure Data Lake. Pode utilizar este documento para avaliar as dependências que sua solução tem na geração 1 de armazenamento do Data Lake. Este guia também mostra como planear e efetuar a atualização.

Podemos irá ajudá-lo através das seguintes tarefas:

:heavy_check_mark: Avaliar a preparação de sua atualização

:heavy_check_mark: Plano para uma atualização

:heavy_check_mark: Efetuar a atualização

## <a name="assess-your-upgrade-readiness"></a>Avaliar a preparação de sua atualização

Nosso objetivo é que todas as capacidades que estão presentes no Data Lake Storage Gen1 serão disponibilizadas na geração 2 de armazenamento do Data Lake. Como esses recursos são expostos por exemplo, no SDK, etc. CLI, poderão diferir entre o Data Lake Storage Gen1 e Gen2 de armazenamento do Data Lake. Aplicações e serviços que funcionam com o Data Lake Storage Gen1 precisam ser capazes de trabalhar da mesma forma com geração 2 de armazenamento do Data Lake. Por fim, algumas das funcionalidades não estarão disponíveis na geração 2 de armazenamento do Data Lake imediatamente. À medida que ficam disponíveis, serão anunciados-los neste documento.

Estas secções seguintes irão ajudá-lo a decidir a melhor maneira atualizar para ger2 de armazenamento do Data Lake e o pode fazer mais sentido para o fazer.

### <a name="data-lake-storage-gen1-solution-components"></a>Componentes da solução do Data Lake Storage Gen1

É muito provável que, quando usa o Gen1 de armazenamento do Data Lake em suas soluções de análise ou pipelines, há muitas tecnologias adicionais que empregam para atingir a funcionalidade geral ponto-a-ponto. Isso [artigo](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) descreve vários componentes de fluxo de dados que incluem a ingerir, processar e consumir dados.

Além disso, existem componentes abrangentes para aprovisionar, gerir e monitorizar esses componentes. Cada um dos componentes operam com a geração 1 de armazenamento do Data Lake, utilizando uma interface mais adequada aos mesmos. Quando estiver a planear atualizar a sua solução de geração 2 de armazenamento do Data Lake, precisará estar ciente das interfaces que são utilizadas. Precisará atualizar tanto a interfaces de gerenciamento, bem como interfaces de dados, uma vez que cada interface tem requisitos distintos.

![Componentes da solução do Data Lake Storage](./media/data-lake-storage-upgrade/solution-components.png)

**Figura 1** acima mostra os componentes de funcionalidade que veria na maioria das soluções de análise.

**Figura 2** mostra um exemplo de como esses componentes irão ser implementados através da utilização de tecnologias específicas.

A funcionalidade de armazenar no **figura 1** fornecido pelo Data Lake Storage Gen1 (**figura 2**). Observe como os vários componentes do fluxo de dados interagem com a geração 1 de armazenamento do Data Lake ao utilizar REST APIs ou SDK de Java. Observe também como os componentes de funcionalidades transversais interagem com a geração 1 de armazenamento do Data Lake. O componente de aprovisionamento utiliza modelos de recursos do Azure, ao passo que o componente de monitorização que utiliza o Log Analytics utiliza dados operacionais que provém de geração 1 de armazenamento do Data Lake.

Para atualizar uma solução da utilização de geração 1 de armazenamento do Data Lake para geração 2 de armazenamento do Data Lake, precisará copiar os dados e metadados, vincular novamente os fluxos de dados e, em seguida, todos os componentes terão de ser capazes de trabalhar com a geração 2 de armazenamento do Data Lake.

As seções abaixo fornecem informações para ajudar a tomar melhores decisões:

:heavy_check_mark: Capacidades da plataforma

:heavy_check_mark: Interfaces de programação

:heavy_check_mark: Ecossistema do Azure

:heavy_check_mark: Ecossistema de parceiros

:heavy_check_mark: Informações operacionais

Cada secção, irá ser capaz de determinar os "indispensáveis" para a atualização. Depois que tem a certeza de que as capacidades estão disponíveis, ou tem a certeza de que existem soluções alternativas razoáveis in-loco, avance para o [planear uma atualização](#planning-for-an-upgrade) secção deste guia.

### <a name="platform-capabilities"></a>Capacidades da plataforma

Esta secção descreve as capacidades da plataforma de geração 1 de armazenamento do Data Lake que estão atualmente disponíveis na geração 2 de armazenamento do Data Lake.

| | Armazenamento do Data Lake Ger1 | Data Lake Storage Gen2 - objetivo | Data Lake Storage Gen2 - estado de disponibilidade  |
|-----------------------|-----------------|----------------------|----------------------------------|
| Organização de dados| Suporta dados armazenados como arquivos e pastas | Suporta dados armazenados como objetos/blobs, bem como pastas e ficheiros - [ligação](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Suporta dados armazenados como pastas e ficheiros – *já disponível* <br><br> Suporta dados armazenados como objetos/blobs - *ainda não está disponível* |
| Espaço de nomes| Hierárquica | Hierárquica |  *Já disponível*  |
| API  | API de REST através de HTTPS | API de REST através de HTTP/HTTPS| *Já disponível* |
| API do lado do servidor| [API REST compatíveis com WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) | API de REST do serviço de Blobs do Azure [API de REST do Data Lake Storage geração 2](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | API de REST do Data Lake Storage Gen2 – *já estão disponíveis* <br><br> BLOBs do Azure, API de REST do serviço – *ainda não está disponível*       |
| Cliente de sistema de ficheiro do Hadoop | Sim ([armazenamento do Azure Data Lake](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Sim ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Já disponível*  |  | [Chave partilhada](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key), [identidades do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-authentication-scenarios), [assinaturas de acesso (SAS) partilhado](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) | *Já disponível*  |
| Operações de dados – autorização  | Arquivo e pasta de nível POSIX acesso listas de controle (ACLs) com base em identidades do Active Directory do Azure  | Arquivo e pasta de nível POSIX acesso listas de controle (ACLs) com base em identidades do Active Directory do Azure [chave de partilha](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) de autorização de nível de conta controlo de acesso baseado em funções ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) para contentores de acesso | *Já disponível* |
| Operações de dados – registos  | Sim | Pedidos pontuais para os registos para uma duração específica, com o pedido de suporte de integração de monitorização do Azure | Pontuais pedido – de suporte de pedidos de registos para a utilização de duração específica *já disponível*<br><br> Integração de monitorização do Azure – *ainda não está disponível* |
| Dados de encriptação em repouso | Transparente, do lado do servidor com chaves geridas pelo serviço e com chaves geridas pelo cliente no Azure KeyVault | Transparente, do lado do servidor com chaves geridas pelo serviço e com chaves cliente chaves geridas no Azure KeyVault | As chaves geridas pelo serviço – *já disponível*<br><br> As chaves geridas pelo cliente – *já disponível*  |
| Suporte da virtual Network (VNet)  | [Utilizar a integração de rede Virtual](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Utilizar o ponto final de serviço para o armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Já disponível* | Operações de gestão (por exemplo, criar conta) | [Controlo de acesso baseado em funções](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) fornecida pelo Azure para a gestão de conta | [Controlo de acesso baseado em funções](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) fornecida pelo Azure para a gestão de conta | *Já disponível*| SDKs de programador | .NET, node. js de Java, Python,  | .NET, Java, Python, node. js, C++, Ruby, PHP, Go, Android, iOS| *Ainda não está disponível* | Desempenho otimizado para cargas de trabalho de análise paralela. Alto débito e IOPS. | Desempenho otimizado para cargas de trabalho de análise paralela. Alto débito e IOPS. | *Já disponível* |
| Limites de tamanho | Sem limites de tamanhos de conta, tamanhos de ficheiro ou número de ficheiros | Sem limites de tamanhos de conta ou o número de ficheiros. Limitado a 5TB de tamanho de ficheiro. | *Já disponível*|
| Redundância geográfica| Localmente redundante (LRS) | Localmente redundante (LRS) zona redundante (ZRS) globalmente redundante consulte (RA-GRS) globalmente georredundante com acesso de leitura (GRS) [aqui](https://docs.microsoft.com/azure/storage/common/storage-redundancy) para obter mais informações| *Já disponível* |
| Disponibilidade regional | Consulte [aqui](https://azure.microsoft.com/regions/) | Todos os [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Já disponível*                                                                                                                           |
| Preço                                       | Consulte [preços](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | Consulte [preços](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| SLA de disponibilidade                            | [Veja o SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Veja o SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Já disponível*                                                                                                                           |
| Gestão de Dados                             | Expiração de ficheiros                                                                                                                                        | Políticas de ciclo de vida                                                                                                                                                                                                                                                                                                                                                                                                          | *Ainda não está disponível*                                                                                                                       |

### <a name="programming-interfaces"></a>Interfaces de programação

A tabela seguinte descreve que API define que estão disponíveis para as suas aplicações personalizadas. Para tornar as coisas um pouco mais claras, tenha separado estes conjuntos de API em 2 tipos: APIs de gestão e as APIs de sistema de ficheiros.

APIs de gestão de ajudá-lo a gerir contas, enquanto o sistema de ficheiros APIs ajudam-na trabalhar os ficheiros e pastas.

|  Conjunto de APIS                           |  Armazenamento do Data Lake Ger1                                                                                                                                                                                                                                                                                                   | Disponibilidade de geração 2 de armazenamento do Data Lake - com autenticação de chave partilhada | Disponibilidade de geração 2 de armazenamento do Data Lake - com autenticação do OAuth                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SDK de .NET - gerenciamento                  | [Link](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet)                                                                                                                                                                                                                 | *Não suportado*                                                      | *Já estão disponíveis -* [ligação](https://docs.microsoft.com/rest/api/storageservices/operations-on-the-account--blob-service-)                                    |
| SDK do .NET – sistema de ficheiros                  | [Link](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/client?view=azure-dotnet)                                                                                                                                                                                                                     | *Ainda não está disponível*                                                | *Ainda não está disponível*                                                                                                                                             |
| Java SDK - gerenciamento                  | [Link](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Não suportado*                                                      | *Já estão disponíveis –* [ligação](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-stable)                                     |
| Java SDK – sistema de ficheiros                  | [Ligação](https://docs.microsoft.com/java/api/overview/azure/datalake)                                                                                                                                                                                                                                         | *Ainda não está disponível*                                                | *Ainda não está disponível*                                                                                                                                             |
| NODE. js - gestão                   | [Ligação](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | Não suportado                                                      | *Já estão disponíveis -* [ligação](http://azure.github.io/azure-storage-node/)                                                                                            |
| NODE. js - sistema de ficheiros                   | [Ligação](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | *Ainda não está disponível*                                                | *Ainda não está disponível*                                                                                                                                             |
| Python – gerenciamento                    | [Ligação](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *Não suportado*                                                      | *Já estão disponíveis -* [ligação](https://docs.microsoft.com/python/api/overview/azure/storage/management?view=azure-python)                                       |
| Python – sistema de ficheiros                    | [Link](http://azure-datalake-store.readthedocs.io/en/latest/)                                                                                                                                                                                                                                                                 | *Ainda não está disponível*                                                | *Ainda não está disponível*                                                                                                                                             |
| API REST - gerenciamento                  | [Link](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Não suportado*                                                      | *Agora, disponíveis*                                                                                                                                               |
| API REST - sistema de ficheiros                  | [Link](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)                                                                                                                                                                                                                                       | *Já disponível*                                                    | *Já estão disponíveis -* [ligação](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)                                                      |
| PowerShell - gestão e o sistema de ficheiros | [Link](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | Gestão – não suportado de sistema de ficheiros - *ainda não está disponível*        | Gestão – *já estão disponíveis -* [ligação](https://docs.microsoft.com/powershell/module/az.storage) <br><br>Sistema de ficheiros - *ainda não está disponível* |
| CLI – gerenciamento                       | [Link](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Não suportado*                                                      | *Já estão disponíveis -* [ligação](https://docs.microsoft.com/cli/azure/storage?view=azure-cli-latest)                                                              |
| CLI - sistema de ficheiros                       | [Link](https://docs.microsoft.com/cli/azure/dls/fs?view=azure-cli-latest)                                                                                                                                                                                                                                               | *Ainda não está disponível*                                                | *Ainda não está disponível*                                                                                                                                             |
| Modelos do Azure Resource Manager - gerenciamento             | [Template1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Não suportado*                                                      | *Já estão disponíveis -* [ligação](https://docs.microsoft.com/azure/templates/microsoft.storage/2018-07-01/storageaccounts)                                         |

### <a name="azure-ecosystem"></a>Ecossistema do Azure

Ao utilizar o Data Lake Storage Gen1, pode utilizar uma variedade de serviços da Microsoft e produtos em seus pipelines de ponto-a-ponto. Estes serviços e produtos funcionam com geração 1 de armazenamento do Data Lake direta ou indiretamente. Esta tabela mostra uma lista dos serviços que podemos ter modificado para funcionar com a geração 1 de armazenamento do Data Lake e mostra quais os que são atualmente compatíveis com geração 2 de armazenamento do Data Lake.

| **Área**             | **Disponibilidade para a geração 1 do Data Lake Storage**                                                                                                                                    | **Disponibilidade de geração 2 de armazenamento do Data Lake – com autenticação de chave partilhada**                                                                                                           | **Disponibilidade para Gen2 Lake armazenamento de dados – com o OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Estrutura de análise  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Já disponível*                                                                                                                                                              | *Já disponível*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [HDInsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3.6 - *agora disponível* HDInsight 4.0 - *ainda não está disponível*      | O HDInsight 3.6 ESP – *já estão disponíveis* <br><br>  HDInsight 4.0 ESP - *ainda não está disponível*                                                                 |
|                      | Runtime do Databricks 3.1 e acima                                                                                                                                               | [Runtime do Databricks 5.1 e posterior](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *- agora disponível* | [Runtime do Databricks 5.1 e posterior](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) – *já estão disponíveis*                                                                                              |
|                      | [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Não suportado*                                                                                                                                                              | *Já estão disponíveis* [ligação](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) |
| Integração de dados     | [Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [Versão 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *agora disponível* versão 1 – *não suportado*                               | [Versão 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *já estão disponíveis* <br><br> Versão 1 – *não suportado*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Não suportado*                                                                                                                                                              | *Não suportado*                                                                                                                                 |
|                      | [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |
|                      | [Catálogo de Dados](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |
|                      | [Aplicações Lógicas](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |
| IoT                  | [Captura de Hubs de eventos – de](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |
|                      | [Stream Analytics](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |
| Consumo          | [Ambiente de trabalho do Power BI](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |
|                      | [Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |
| Produtividade         | [Portal do Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Não suportado*                                                                                                                                                              | Gerenciamento de contas *– agora disponível* <br><br>Operações de dados *–**ainda não está disponível*                                                                   |
|                      | [Ferramentas do Data Lake para Visual Studio](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |
|                      | [Explorador do Armazenamento do Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Já disponível*                                                                                                                                                              | *Já disponível*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |

### <a name="partner-ecosystem"></a>Ecossistema de parceiros

Esta tabela mostra uma lista dos serviços de terceiros e produtos que foram modificados para trabalhar com a geração 1 de armazenamento do Data Lake. Ela também mostra quais os que são atualmente compatíveis com geração 2 de armazenamento do Data Lake.

| Área            | Partner  | Produtos/serviços  | Disponibilidade para a geração 1 do Data Lake Storage                                                                                                                                               | Disponibilidade de geração 2 de armazenamento do Data Lake – com autenticação de chave partilhada                                                   | Disponibilidade para Gen2 Lake armazenamento de dados – com o Oauth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Estrutura de análise | Cloudera     | CDH                  | [Link](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Ainda não está disponível*                                                                                                  | [Link](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [Link](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Não suportado*                                                                                                                  | *Ainda não está disponível*                                                                                                  |
|                     | HortonWorks  | HDP 3.0              | [Ligação](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                       | *Ainda não está disponível*                                                                                                  | *Ainda não está disponível*                                                                                                  |
|                     | Qubole       |                      | [Link](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Ainda não está disponível*                                                                                                  | *Ainda não está disponível*                                                                                                  |
| ETL                 | StreamSets   |                      | [Link](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Ainda não está disponível*                                                                                                  | *Ainda não está disponível*                                                                                                  |
|                     | Informatica  |                      | [Link](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Ainda não está disponível*                                                                                                  | *Ainda não está disponível*                                                                                                  |
|                     | Attunity     |                      | [Link](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Ainda não está disponível*                                                                                                  | *Ainda não está disponível*                                                                                                  |
|                     | Alteryx      |                      | [Link](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Ainda não está disponível*                                                                                                  | *Ainda não está disponível*                                                                                                  |
|                     | ImanisData   |                      | [Link](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Ainda não está disponível*                                                                                                  | *Ainda não está disponível*                                                                                                  |
|                     | WANdisco     |                      | [Link](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Link](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Link](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Informações operacionais

Geração 1 de armazenamento do Data Lake envia por push dados e informações específicas para outros serviços que ajuda-o para operacionalizar os seus pipelines. Esta tabela mostra a disponibilidade de suporte correspondente na geração 2 de armazenamento do Data Lake.

| Tipo de dados                                                                                       | Disponibilidade para a geração 1 do Data Lake Storage                                                                 | Disponibilidade de geração 2 do Data Lake Storage                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Os dados de faturação - medidores que são enviados para o comércio da Equipe para faturação e, em seguida, disponibilizada para clientes  | *Já disponível*                                                                                             | *Já disponível*                                                                                                                           |
| Registos de atividade                                                                                          | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Pontuais pedido – de suporte de pedidos de registos para a utilização de duração específica *agora disponível* integração de monitorização do Azure - *ainda não está disponível* |
| Registos de diagnósticos                                                                                        | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | Pontuais pedido – de suporte de pedidos de registos para a utilização de duração específica *agora disponível* integração de monitorização do Azure - *ainda não está disponível* |
| Métricas                                                                                                | *Não suportado*                                                                                               | *Já estão disponíveis -* [ligação](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Planear uma atualização

Esta secção assume que reviu os [avaliar a preparação de sua atualização](#assess-your-upgrade-readiness) secção deste guia e que todas as suas dependências são cumpridas. Se existirem recursos que ainda não estiverem disponíveis na geração 2 de armazenamento do Data Lake, continue apenas se souber as soluções alternativas correspondentes. As secções seguintes fornecem orientações sobre como pode planejar para a atualização dos seus pipelines. Efetuar a atualização real será descrita a [efetuar a atualização](#performing-the-upgrade) secção deste guia.

### <a name="upgrade-strategy"></a>Estratégia de atualização

A parte mais importante da atualização é decidir a estratégia. Esta decisão irá determinar as opções disponíveis para si.

Esta tabela lista algumas estratégias bem conhecidas que foram utilizadas para migrar bases de dados, os clusters do Hadoop, etc. Vamos adotar estratégias semelhantes a nossa documentação de orientação e adaptá-los ao nosso contexto.

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

A estratégia geral que utilizar para efetuar a atualização (descrito a [atualizar estratégia](#upgrade-strategy) secção deste guia), irá determinar as ferramentas que pode utilizar para a atualização de dados. As ferramentas listadas abaixo se baseiam em informações atuais e sugestões. 

#### <a name="tools-guidance"></a>Diretrizes de ferramentas

| Estratégia                       | Ferramentas                                                                                                             | Profissionais de TI                                                                                                                             | Considerações                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Migração lift-and-shift**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Serviço gerido na cloud                                                                                                                | Copia apenas os dados. As ACLs não podem ser copiadas atualmente.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Conhecidas ferramenta fornecidos pelo Hadoop, permissões, ou seja, as ACLs pode ser copiada com essa ferramenta                                                   | Necessita de um cluster que pode ligar ao Data Lake Storage Gen1 e Gen2 ao mesmo tempo.                                                                                                                                                                                   |
| **Copiar uma vez-e-cópia incremental** | Azure Data Factory                                                                                                    | Serviço gerido na cloud                                                                                                                | Para suportar a cópia incremental no ADF, os dados têm de ser organizado de maneira de séries de tempo. É o intervalo mais curto entre cópias incrementais [15 minutos](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). Para intervalos mais curtos, ADF não funcionará. As ACLs não podem ser copiadas atualmente. |
| **Adoção paralela**              | [WANdisco](http://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Suporte de replicação consistente se usando um ambiente de Hadoop puro ligado ao armazenamento do Azure Data Lake, suporta a replicação bidirecional | Se não utilizar um ambiente de pura Hadoop, poderá haver um atraso na replicação.                                                                                                                                                                                                                                                  |

Tenha em atenção que existem, terceiros que podem lidar com o Data Lake Storage geração 1 com a atualização de geração 2 de armazenamento do Data Lake sem envolver os acima dados/metadados copiar ferramentas (por exemplo: [Cloudera](http://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). Eles fornecem uma experiência de "conveniências" que efetua a migração de dados, bem como a migração da carga de trabalho. Poderá ter de efetuar uma atualização de fora de banda de quaisquer ferramentas que estão fora do seu ecossistema.

#### <a name="considerations"></a>Considerações

* Terá de criar manualmente a conta de geração 2 de armazenamento do Data Lake em primeiro lugar, antes de iniciar qualquer parte da atualização, uma vez que a orientação atual não inclui qualquer processo de conta de geração 2 automático com base nas suas informações de conta de geração 1. Certifique-se de que compare os processos de criação de contas para [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) e [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account).

* Geração 2 de armazenamento do Data Lake, apenas suporta os arquivos de até 5 TB de tamanho. Para atualizar para ger2 de armazenamento do Data Lake, poderá ter de redimensionar os ficheiros no Data Lake Storage Gen1, para que fiquem mais pequenos do que 5 TB de tamanho.

* Se usar uma ferramenta que não copia as ACLs ou não pretende copiar sobre as ACLs, terá de definir as ACLs no destino manualmente no nível superior apropriado. Pode fazê-lo através do Explorador de armazenamento. Certifique-se de que as ACLs são as ACLs padrão para que os ficheiros e pastas que copie herdarem-los.

* Gen1 de armazenamento do Data Lake, é o nível mais elevado, pode definir ACLs na raiz da conta. No ger2 de armazenamento do Data Lake, no entanto, o nível mais elevado, pode definir ACLs é na pasta raiz no sistema de ficheiros, não a conta de todo. Então, se pretender definir ACLs padrão ao nível da conta, precisará duplicar através de todos os sistemas de ficheiros na sua conta de geração 2 de armazenamento do Data Lake.

* Restrições de nomenclatura de ficheiro são diferentes entre os dois sistemas de armazenamento. Essas diferenças são especialmente em relação a quando copiar de geração 2 de armazenamento do Data Lake para geração 1 de armazenamento do Data Lake, uma vez que o segundo tem mais restrita restrições.

### <a name="application-upgrade"></a>Atualização da aplicação

Quando precisar da criação de aplicativos Gen1 de armazenamento do Data Lake ou de geração 2 de armazenamento do Data Lake, terá de optar pela primeira vez uma interface de programação apropriada. Ao chamar uma API na interface terá de fornecer o URI apropriado e as credenciais apropriadas. A representação destes três elementos, a API, URI e como as credenciais são fornecidas, são diferente entre a geração 1 de armazenamento do Data Lake e Gen2.So de armazenamento do Data Lake, como parte da atualização do aplicativo, precisará mapear essas três construções adequadamente.

#### <a name="uri-changes"></a>Alterações URI

A principal tarefa aqui é a conversão do adl: / / URI que estava a ser utilizada nas cargas de trabalho existentes num abfss: / / URI.

O esquema URI para a geração 1 de armazenamento do Data Lake é mencionado [aqui](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) em detalhes, mas falando genericamente, é *adl://mydatalakestore.azuredatalakestore.net/\<file_path\>.*

O esquema URI para aceder a ficheiros de geração 2 de armazenamento do Data Lake é explicado [aqui](https://docs.microsoft.com/azure/storage/data-lake-storage/use-hdi-cluster) em detalhes, mas falando genericamente, é *abfss: / /\<FILE_SYSTEM_NAME\> \@ \< Nome_conta\>.dfs.core.widows.net/\<caminho\>.*

Precisará passar por seus aplicativos existentes e certifique-se de que alterou os URIs adequadamente para apontar para a geração 2 de armazenamento do Data Lake aqueles. Além disso, terá de adicionar as credenciais apropriadas. Por fim, como extinguir os aplicativos originais e substitua o novo aplicativo terão de estar alinhado atentamente para a sua estratégia de atualização global.

#### <a name="custom-applications"></a>Aplicações personalizadas

Dependendo da interface a que sua aplicação utilizar com a geração 1 de armazenamento do Data Lake, terá de modificá-lo para adaptá-lo à geração 2 de armazenamento do Data Lake.

##### <a name="rest-apis"></a>APIs REST

Se o aplicativo utilizar APIs de REST de armazenamento do Data Lake, terá de modificar a sua aplicação para utilizar as APIs de REST do Data Lake armazenamento geração 2. São fornecidas hiperligações em *interfaces de programação* secção.

##### <a name="sdks"></a>SDKs

Como é chamado no *avaliar a preparação de sua atualização* secção, SDKs não estão atualmente disponíveis. Se quiser porta ao longo de seus aplicativos de geração 2 de armazenamento do Data Lake, é recomendável que aguardar SDKs suportados estar disponível.

##### <a name="powershell"></a>PowerShell

Como indicadas na avaliar a seção de preparação da atualização, suporte do PowerShell não está atualmente disponível para o plano de dados.

Pode substituir os commandlets do plano de gestão com aqueles apropriado na geração 2 de armazenamento do Data Lake. São fornecidas hiperligações em *interfaces de programação* secção.

##### <a name="cli"></a>CLI

Como é chamado no *avaliar a preparação de sua atualização* secção, o suporte da CLI não está atualmente disponível para o plano de dados.

Poderia substituir comandos do plano de gestão por aqueles que o adequado na geração 2 de armazenamento do Data Lake. São fornecidas hiperligações em *interfaces de programação* secção.

### <a name="analytics-frameworks-upgrade"></a>Atualização de estruturas de análise

Se a sua aplicação cria metadados sobre as informações no arquivo, como o arquivo explícito e caminhos de pastas, terá de executar ações adicionais após atualizar os arquivo de dados/metadados. Isso é especialmente verdadeiro de estruturas de análise, como o Azure HDInsight, etc. do Databricks, que, normalmente, criar dados de catálogo nos dados do arquivo.

Estruturas de análise trabalham com dados e metadados armazenados nos arquivos de remotos, como o Data Lake Storage Gen1 e Gen2. Assim, em teoria, os motores podem ser efémeros e ser recuperados apenas quando precisam de tarefas executar em relação os dados armazenados.

No entanto, para otimizar o desempenho, as estruturas de análise podem criar referências explícitas para os ficheiros e pastas armazenadas no arquivo remoto e, em seguida, criar uma cache para armazená-los. Deve o URI de dados remotos alterar, por exemplo, um cluster que foi a armazenar dados no Data Lake Storage Gen1 anteriormente e, agora que desejam armazenar na geração 2 de armazenamento do Data Lake, o URI para o mesmo conteúdo copiado será diferente. Então, depois dos dados e metadados caches para estes mecanismos de atualização também tem de ser atualizada ou inicializada novamente

Como parte do processo de planejamento, terá de identificar seu aplicativo e descobrir como as informações de metadados podem ser reinicializadas para apontar para dados que estão agora armazenados numa geração 2 de armazenamento do Data Lake. Segue-se orientações para estruturas de análise normalmente adotados para ajudá-lo com suas etapas de atualização.

#### <a name="azure-databricks"></a>Azure Databricks

Dependendo da estratégia de atualização que escolher, os passos variam. A seção atual assume que escolheu a estratégia de "Vida-and-shift". Além disso, a área de trabalho existente do Databricks utilizado para aceder a dados de uma conta de geração 1 de armazenamento do Data Lake é esperada para trabalhar com os dados que são copiados para a conta de geração 2 de armazenamento do Data Lake.

Em primeiro lugar, certifique-se de que criou a conta de geração 2 e, em seguida, copiados sobre dados e a meta de geração 1 para geração 2 utilizando uma ferramenta adequada. Essas ferramentas são descritas [atualizar dados](#data-upgrade) secção deste guia.

Em seguida, [atualizar](https://docs.azuredatabricks.net/user-guide/clusters/index.html) seu cluster do Databricks existente para começar a utilizar o runtime do Databricks 5.1 ou superior, que deve dar suporte a geração 2 de armazenamento do Data Lake.

Os passos subsequentes são baseados em como a área de trabalho do Databricks existente acessa dados na conta do Data Lake Storage Gen1. Isso pode ser feito por chamada adl: / / URIs [diretamente](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) a partir de blocos de notas ou através de [mountpoints](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs).

Se estiver a aceder diretamente a partir de blocos de notas, fornecendo o completa adl: / / URIs, precisará passar por cada bloco de notas e alterar a configuração para acessar o URI de geração 2 do armazenamento de Lake de dados correspondente.

Daqui em diante, precisará para reconfigurá-lo para apontar para a conta de geração 2 de armazenamento do Data Lake. Não existem mais alterações são necessárias e os blocos de notas devem ser capazes de trabalhar como antes.

Se estiver a utilizar qualquer uma das outras estratégias de atualização, pode criar uma variação dos passos acima para satisfazer os seus requisitos.

### <a name="azure-ecosystem-upgrade"></a>Atualização do ecossistema do Azure

Cada uma das ferramentas e serviços nas [ecossistema do Azure](#azure-ecosystem) secção deste guia, terá de ser configurado para trabalhar com a geração 2 de armazenamento do Data Lake.

Em primeiro lugar, certifique-se de que existe integração com a geração 2 de armazenamento do Data Lake.

Em seguida, os elementos descritas acima (por exemplo: URI e as credenciais), terá de ser alteradas. Poderia modificar a instância existente que funciona com a geração 1 de armazenamento do Data Lake ou poderia criar uma nova instância que funcionaria com geração 2 de armazenamento do Data Lake.

### <a name="partner-ecosystem-upgrade"></a>Atualização do ecossistema de parceiros

Trabalhe em conjunto com o parceiro fornecendo o componente e ferramentas para garantir que podem trabalhar com a geração 2 de armazenamento do Data Lake. 

## <a name="performing-the-upgrade"></a>Efetuar a atualização

### <a name="pre-upgrade"></a>Pré-atualização

Como parte desse processo, seria já leu a *avaliar sua prontidão do upgrade* secção e a [planear uma atualização](#planning-for-an-upgrade) secção deste guia, que recebeu a todas as informações necessárias e que criar um plano que faria com satisfazer as suas necessidades. Provavelmente terá uma tarefa de teste durante esta fase.

### <a name="in-upgrade"></a>Na atualização

Consoante a estratégia que escolher e as complexidades da sua solução, esta fase podem ser um pequeno ou um expandida em que há várias cargas de trabalho a aguardar de forma incremental ser movido para a geração 2 de armazenamento do Data Lake. Esta será a parte mais importante da atualização.

### <a name="post-upgrade"></a>Pós-atualização

Depois de terminar com a operação de transição, os últimos passos envolverá verificação completa. Isso seria incluem, mas não estar limitado a verificar dados foi copiada através de forma fiável, verificação de ACLs foram definidas corretamente, verificar e2e pipelines estão a funcionar corretamente etc. Depois das verificações forem concluídas, pode agora desativar os pipelines antigos, eliminar as contas de origem Gen1 de armazenamento do Data Lake e aceda a velocidade máxima em suas soluções com base na geração 2 de armazenamento do Data Lake.

## <a name="conclusion"></a>Conclusão

As orientações fornecidas neste documento devem tenham ajudado a atualizar a sua solução para utilizar a geração 2 de armazenamento do Data Lake. 

Se tiver mais perguntas, ou se tiver comentários, os comentários abaixo ou forneça comentários no [fórum de comentários do Azure](https://feedback.azure.com/forums/327234-data-lake).
