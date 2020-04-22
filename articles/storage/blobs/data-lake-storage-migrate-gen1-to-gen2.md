---
title: Migrae Azure Data Lake Storage da Gen1 para gen2
description: Migrao Azure Data Lake Storage da Gen1 para gen2.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 80c0afafca3b0bf497689cbd4a0870eedd066cfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677134"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Migrae Azure Data Lake Storage da Gen1 para gen2

Pode migrar os seus dados, cargas de trabalho e aplicações desde data Lake Storage Gen1 para Data Lake Storage Gen2.

O Azure Data Lake Storage Gen2 é construído no [armazenamento do Azure Blob](storage-blobs-introduction.md) e fornece um conjunto de capacidades dedicadas à análise de big data. [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) combina funcionalidades do [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), tais como semântica do sistema de ficheiros, diretório e segurança e escala de nível de ficheiro com armazenamento de baixo custo, hierárquico, capacidades de alta disponibilidade/recuperação de desastres do [armazenamento De Blob Azure.](storage-blobs-introduction.md)

> [!NOTE]
> Para uma leitura mais fácil, este artigo usa o termo *Gen1* para se referir ao Azure Data Lake Storage Gen1, e o termo *Gen2* para se referir ao Azure Data Lake Storage Gen2.

## <a name="recommended-approach"></a>Abordagem recomendada

Para migrar para a Gen2, recomendamos a seguinte abordagem.

:heavy_check_mark: Passo 1: Avaliar a prontidão

:heavy_check_mark: Passo 2: Preparar-se para migrar

:heavy_check_mark: Passo 3: Migrar dados e cargas horárias de aplicações

:heavy_check_mark: Passo 4: Corte da Gen1 para a Gen2

> [!NOTE]
> Gen1 e Gen2 são serviços diferentes, não há experiência de upgrade no local, esforço de migração intencional necessário. 

### <a name="step-1-assess-readiness"></a>Passo 1: Avaliar a prontidão

1. Saiba mais sobre a oferta de [Data Lake Storage Gen2;](https://azure.microsoft.com/services/storage/data-lake-storage/) é benefícios, custos e arquitetura geral. 

2. [Compare as capacidades](#gen1-gen2-feature-comparison) da Gen1 com as da Gen2. 

3. Reveja uma lista de [questões conhecidas](data-lake-storage-known-issues.md) para avaliar eventuais lacunas na funcionalidade.

4. A Gen2 suporta funcionalidades de armazenamento Blob, tais como [registo de diagnóstico,](../common/storage-analytics-logging.md) [níveis de acesso](storage-blob-storage-tiers.md)e políticas de gestão de ciclo de vida de armazenamento [blob.](storage-lifecycle-management-concepts.md) Se você é interessante em usar qualquer uma destas funcionalidades, reveja o [nível atual de suporte](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-supported-blob-storage-features).

5. Reveja o estado atual do suporte ao [ecossistema Azure](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access) para garantir que a Gen2 suporta quaisquer serviços de que as suas soluções dependam.

### <a name="step-2-prepare-to-migrate"></a>Passo 2: Preparar-se para migrar

1. Identifique os conjuntos de dados que vai migrar.

   Aproveite esta oportunidade para limpar conjuntos de dados que já não utiliza. A menos que planeie migrar todos os seus dados de uma só vez, deva este tempo identificar grupos lógicos de dados que pode migrar por fases.
   
2. Determine o impacto que uma migração terá no seu negócio.

   Por exemplo, considere se pode pagar algum tempo de inatividade enquanto a migração ocorre. Estas considerações podem ajudá-lo a identificar um padrão de migração adequado, e a escolher as ferramentas mais adequadas.

3. Criar um plano de migração. 

   Recomendamos estes padrões de [migração.](#migration-patterns) Você pode escolher um destes padrões, combiná-los juntos, ou desenhar um padrão personalizado próprio.

### <a name="step-3-migrate-data-workloads-and-applications"></a>Passo 3: Migrar dados, cargas de trabalho e aplicações

Migrar dados, cargas de trabalho e aplicações utilizando o padrão que prefere. Recomendamos que valide os cenários de forma incremental.

1. [Crie uma conta de armazenamento](data-lake-storage-quickstart-create-account.md) e ative a funcionalidade hierárquica de espaço de nome. 

2. Migra os teus dados. 

3. Configure [os serviços nas suas cargas](data-lake-storage-integrate-with-azure-services.md) de trabalho para apontar para o seu ponto final gen2. 
   
4. Atualizar aplicações para utilizar APIs Gen2. Consulte guias para [.NET,](data-lake-storage-directory-file-acl-dotnet.md) [Java,](data-lake-storage-directory-file-acl-java.md) [Python,](data-lake-storage-directory-file-acl-python.md) [JavaScript](data-lake-storage-directory-file-acl-javascript.md) e [REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2). 
   
5. Atualize scripts para utilizar data Lake Storage Gen2 [PowerShell cmdlets](data-lake-storage-directory-file-acl-powershell.md), e [comandos Azure CLI](data-lake-storage-directory-file-acl-cli.md).
   
6. Procure referências URI que `adl://` contenham a cadeia em ficheiros de código, ou em cadernos databricks, ficheiros Apache Hive HQL ou qualquer outro ficheiro utilizado como parte das suas cargas de trabalho. Substitua estas referências pelo [URI formado gen2](data-lake-storage-introduction-abfs-uri.md) da sua nova conta de armazenamento. Por exemplo: o Gen1 `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`URI: pode tornar-se . 

7. Configure a segurança na sua conta para incluir [funções de controlo de acesso baseado em Funções (RBAC),](../common/storage-auth-aad-rbac-portal.md)segurança de nível de [ficheiros e pastas,](data-lake-storage-access-control.md)e firewalls de [armazenamento azure e redes virtuais](../common/storage-network-security.md).

### <a name="step-4-cutover-from-gen1-to-gen2"></a>Passo 4: Corte da Gen1 para a Gen2

Depois de ter confiança de que as suas aplicações e cargas de trabalho são estáveis na Gen2, pode começar a usar a Gen2 para satisfazer os seus cenários de negócio. Desligue os oleodutos restantes que estejam em funcionamento na Gen1 e desmantele a sua conta Gen1. 

<a id="gen1-gen2-feature-comparison" />

## <a name="gen1-vs-gen2-capabilities"></a>Capacidades Gen1 vs Gen2

Esta tabela compara as capacidades da Gen1 com a da Gen2.

|Área |Gen1   |Gen2 |
|---|---|---|
|Organização de dados|[Espaço de nome hierárquico](data-lake-storage-namespace.md)<br>Suporte de ficheiros e pastas|[Espaço de nome hierárquico](data-lake-storage-namespace.md)<br>Suporte de contentores, ficheiros e pastas |
|Geo-redundância| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS,](../common/storage-redundancy.md#locally-redundant-storage) [ZRS,](../common/storage-redundancy.md#zone-redundant-storage) [GRS,](../common/storage-redundancy.md#geo-redundant-storage) [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Autenticação|[Identidade gerida pela AAD](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Principais de serviço](../../active-directory/develop/app-objects-and-service-principals.md)|[Identidade gerida pela AAD](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Principais de serviço](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Chave de acesso partilhado](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|Autorização|Gestão - [RBAC](../../role-based-access-control/overview.md)<br>Dados – [ACLs](data-lake-storage-access-control.md)|Gestão – [RBAC](../../role-based-access-control/overview.md)<br>Dados - [ACLs,](data-lake-storage-access-control.md) [RBAC](../../role-based-access-control/overview.md) |
|Encriptação – Dados em repouso|Lado do servidor – com chaves [geridas pela Microsoft](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ou [geridas pelo cliente](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|Lado do servidor – com chaves [geridas pela Microsoft](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ou [geridas pelo cliente](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Suporte VNET|[Integração VNET](../../data-lake-store/data-lake-store-network-security.md)|[Pontos Finais de Serviço](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Pontos Finais Privados](../common/storage-private-endpoints.md)|
|Experiência de desenvolvimento|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.NET,](../../data-lake-store/data-lake-store-data-operations-net-sdk.md) [Java,](../../data-lake-store/data-lake-store-get-started-java-sdk.md) [Python,](../../data-lake-store/data-lake-store-data-operations-python.md) [PowerShell,](../../data-lake-store/data-lake-store-get-started-powershell.md) [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|Geralmente disponível - [REST](/rest/api/storageservices/data-lake-storage-gen2), [.NET,](data-lake-storage-directory-file-acl-dotnet.md) [Java,](data-lake-storage-directory-file-acl-java.md) [Python](data-lake-storage-directory-file-acl-python.md)<br>Pré-visualização pública - [JavaScript,](data-lake-storage-directory-file-acl-javascript.md) [PowerShell,](data-lake-storage-directory-file-acl-powershell.md) [Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|Registos de diagnósticos|Troncos clássicos<br>[Monitor Azure integrado](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Registos clássicos](../common/storage-analytics-logging.md) - Geralmente disponíveis<br>Integração do monitor Azure – tbd de linha temporal|
|Ecossistema|[HDInsight (3.6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks (3.1 ou superior)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [SQL DW,](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store) [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3.6, 4.0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (5.1 ou superior)](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [SQL DW,](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md) [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns" />

## <a name="gen1-to-gen2-patterns"></a>Gen1 para padrões Gen2

Escolha um padrão de migração e, em seguida, modifique esse padrão conforme necessário.

|||
|---|---|
|**Elevador e Turno**|O padrão mais simples. Ideal se os seus gasodutos de dados puderem suportar o tempo de inatividade.|
|**Cópia incremental**|Semelhante ao *elevador e ao turno,* mas com menos tempo de inatividade. Ideal para grandes quantidades de dados que demoram mais tempo a copiar.|
|**Gasoduto duplo**|Ideal para oleodutos que não podem pagar tempo de inatividade.|
|**Sincronização bidirecional**|Semelhante ao *gasoduto duplo,* mas com uma abordagem mais faseada que é adequada para oleodutos mais complicados.|

Vamos ver mais de perto cada padrão.
 
### <a name="lift-and-shift-pattern"></a>Padrão de elevação e mudança

Este é o padrão mais simples.

1. Pare todos os escritos para a Gen1.

2. Mova os dados da Gen1 para a Gen2. Recomendamos a [Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) Os ACLs copiam com os dados.

3. Aponte as operações e as cargas de trabalho para a Gen2.

4. Desativação Gen1.

> [!div class="mx-imgBorder"]
> ![padrão de elevação e mudança](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>Considerações para usar o padrão de elevação e mudança

:heavy_check_mark: Corte da Gen1 para a Gen2 para todas as cargas de trabalho ao mesmo tempo.

:heavy_check_mark: Espere tempo de inatividade durante a migração e o período de redução.

:heavy_check_mark: Ideal para gasodutos que podem pagar tempo de inatividade e todas as aplicações podem ser atualizadas de uma só vez.

### <a name="incremental-copy-pattern"></a>Padrão de cópia incremental

1. Comece a mover dados da Gen1 para a Gen2. Recomendamos a [Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) Os ACLs copiam com os dados.

2. Copie gradualmente novos dados da Gen1.

3. Depois de todos os dados ser copiado, pare todos os escritos para gen1, e aponte cargas de trabalho para gen2.

4. Desativação Gen1.

> [!div class="mx-imgBorder"]
> ![Padrão de cópia incremental](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>Considerações para a utilização do padrão de cópia incremental:

:heavy_check_mark: Corte da Gen1 para a Gen2 para todas as cargas de trabalho ao mesmo tempo.

:heavy_check_mark: Espere tempo de paragem apenas durante o período de redução.

:heavy_check_mark: Ideal para oleodutos onde todas as aplicações foram atualizadas de uma só vez, mas a cópia de dados requer mais tempo.

### <a name="dual-pipeline-pattern"></a>Padrão de gasoduto duplo

1. Mova os dados da Gen1 para a Gen2. Recomendamos a [Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) Os ACLs copiam com os dados.

2. Ingerir novos dados tanto para a Gen1 como para a Gen2.

3. Aponte cargas de trabalho para a Gen2.

4. Pare todos os escritos para gen1 e depois desativa gen1.

> [!div class="mx-imgBorder"]
> ![Padrão de gasoduto duplo](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>Considerações para a utilização do padrão de gasoduto duplo:

:heavy_check_mark: Os gasodutos Gen1 e Gen2 correm lado a lado.

:heavy_check_mark: Suporta zero tempo de inatividade.

:heavy_check_mark: Ideal em situações em que as suas cargas de trabalho e aplicações não podem suportar qualquer tempo de inatividade, podendo ingerir em ambas as contas de armazenamento.

### <a name="bi-directional-sync-pattern"></a>Padrão de sincronização bidirecional

1. Instale uma replicação bidirecional entre gen1 e Gen2. Recomendamos [o WanDisco.](https://docs.wandisco.com/bigdata/wdfusion/adls/) Oferece uma funcionalidade de reparação para os dados existentes.

3. Quando todos os movimentos estiverem completos, pare todos os escritos para a Gen1 e desligue a replicação bidirecional.

4. Desativação Gen1.

> [!div class="mx-imgBorder"]
> ![Padrão bidirecional](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>Considerações para a utilização do padrão de sincronização bidirecional:

:heavy_check_mark: Ideal para cenários complexos que envolvam um grande número de oleodutos e dependências onde uma abordagem faseada pode fazer mais sentido.  

:heavy_check_mark: O esforço de migração é elevado, mas fornece apoio lado a lado para a Gen1 e gen2.

## <a name="next-steps"></a>Passos seguintes

- Conheça as várias partes da criação de segurança para uma conta de armazenamento. Consulte o guia de [segurança azure storage](../common/storage-security-guide.md).
- Otimize o desempenho da sua Data Lake Store. Consulte [o Otimize Azure Data Lake Storage Gen2 para o desempenho](data-lake-storage-performance-tuning-guidance.md)
- Reveja as melhores práticas para gerir a sua Data Lake Store. Ver [as melhores práticas para utilizar o Azure Data Lake Storage Gen2](data-lake-storage-best-practices.md)

