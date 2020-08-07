---
title: Copiar funcionalidades de otimização do desempenho da atividade
description: Conheça as principais funcionalidades que o ajudam a otimizar o desempenho da atividade da cópia na Azure Data Factory。
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: 45cecccd88b0b84b478bc6fc7346cb9ef9c2f454
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87846348"
---
# <a name="copy-activity-performance-optimization-features"></a>Copiar funcionalidades de otimização do desempenho da atividade

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve as funcionalidades de otimização do desempenho da atividade da cópia que pode alavancar na Azure Data Factory.

## <a name="data-integration-units"></a>Unidades de Integração de Dados

Uma Unidade de Integração de Dados é uma medida que representa a potência (uma combinação de CPU, memória e alocação de recursos de rede) de uma única unidade na Azure Data Factory. A Unidade de Integração de Dados aplica-se apenas ao tempo de funcionamento da [integração do Azure,](concepts-integration-runtime.md#azure-integration-runtime)mas não [ao tempo de integração auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime).

Os DIU autorizados a capacitar uma atividade de cópia é **entre 2 e 256**. Se não for especificado ou escolher "Auto" no UI, a Data Factory aplica dinamicamente a definição de DIU ideal com base no seu par de fontes e padrão de dados. A tabela a seguir lista as gamas de DIU suportadas e o comportamento predefinido em diferentes cenários de cópia:

| Cenário de cópia | Gama de DIU suportada | DIUs padrão determinados pelo serviço |
|:--- |:--- |---- |
| Entre lojas de arquivos |- **Cópia de ou para um único ficheiro**: 2-4 <br>- **Copiar de e para vários ficheiros:** 2-256 dependendo do número e tamanho dos ficheiros <br><br>Por exemplo, se copiar dados de uma pasta com 4 ficheiros grandes e optar por preservar a hierarquia, o DIU máximo eficaz é de 16; quando optar por fundir o ficheiro, o DIU máximo eficaz é 4. |Entre 4 e 32 dependendo do número e tamanho dos ficheiros |
| Da loja de ficheiros à loja de não-arquivos |- **Cópia de um único ficheiro**: 2-4 <br/>- **Cópia de vários ficheiros**: 2-256 dependendo do número e tamanho dos ficheiros <br/><br/>Por exemplo, se copiar dados de uma pasta com 4 ficheiros grandes, o DIU máximo eficaz é de 16. |- **Copiar na Base de Dados Azure SQL ou na Azure Cosmos DB:** entre 4 e 16 dependendo do nível da pia (DTUs/RUs) e do padrão de ficheiro de origem<br>- **Copiar para a Azure Synapse Analytics** utilizando a declaração polyBase ou COPY: 2<br>- Outro cenário: 4 |
| Da loja de não-arquivos à loja de ficheiros |- **Cópia de lojas de dados ativadas por opção de partição** (incluindo [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza,](connector-netezza.md#netezza-as-source) [SQL Server](connector-sql-server.md#sql-server-as-a-source), e [Teradata):](connector-teradata.md#teradata-as-source)2-256 ao escrever para uma pasta, e 2-4 ao escrever para um único ficheiro. Nota por origem a partição de dados pode usar até 4 DIUs.<br>- **Outros cenários**: 2-4 |- **Cópia de REST ou HTTP**: 1<br/>- **Cópia da Amazon Redshift** usando DESCARGA: 2<br>- **Outro cenário**: 4 |
| Entre lojas não-arquivadas |- **Cópia de lojas de dados ativadas por opção de partição** (incluindo [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza,](connector-netezza.md#netezza-as-source) [SQL Server](connector-sql-server.md#sql-server-as-a-source), e [Teradata):](connector-teradata.md#teradata-as-source)2-256 ao escrever para uma pasta, e 2-4 ao escrever para um único ficheiro. Nota por origem a partição de dados pode usar até 4 DIUs.<br/>- **Outros cenários**: 2-4 |- **Cópia de REST ou HTTP**: 1<br>- **Outro cenário**: 4 |

Pode ver os DIU utilizados para cada cópia executada na visualização de monitorização da atividade de cópia ou saída de atividade. Para obter mais informações, consulte [a monitorização da atividade do Copy](copy-activity-monitoring.md). Para anular este padrão, especifique um valor para a `dataIntegrationUnits` propriedade da seguinte forma. O *número real de DIs* que a operação de cópia utiliza no tempo de execução é igual ou inferior ao valor configurado, dependendo do seu padrão de dados.

Você será cobrado **# de dIUs usado \* duração \* unitária preço/DIU-hora**. Veja aqui [os](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)preços atuais. A moeda local e o desconto separado podem ser aplicados por tipo de subscrição.

**Exemplo:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>Escalabilidade de tempo de execução de integração auto-hospedada

Se quiser atingir uma produção mais elevada, pode aumentar ou escalar o IR auto-hospedado:

- Se o CPU e a memória disponível no nó de IR auto-alojado não forem totalmente utilizados, mas a execução de empregos simultâneos está a atingir o limite, deve aumentar o número de postos de trabalho simultâneos que podem funcionar num nó.  Consulte [aqui](create-self-hosted-integration-runtime.md#scale-up) as instruções.
- Se, por outro lado, o CPU estiver no topo do nó IV auto-hospedado ou a memória disponível for baixa, pode adicionar um novo nó para ajudar a escalar a carga através dos múltiplos nós.  Consulte [aqui](create-self-hosted-integration-runtime.md#high-availability-and-scalability) as instruções.

Nota nos seguintes cenários, a execução de atividade de cópia única pode alavancar vários nóns de IR auto-hospedados:

- Copie os dados das lojas baseadas em ficheiros, dependendo do número e tamanho dos ficheiros.
- Copiar dados da loja de dados ativada por opção de partição (incluindo [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA,](connector-sap-hana.md#sap-hana-as-source) [SAP Open Hub,](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) [SAP Table,](connector-sap-table.md#sap-table-as-source) [SQL Server](connector-sql-server.md#sql-server-as-a-source), e [Teradata),](connector-teradata.md#teradata-as-source)dependendo do número de divisórias de dados.

## <a name="parallel-copy"></a>Cópia paralela

Pode definir cópia paralela `parallelCopies` (propriedade) na atividade de cópia para indicar o paralelismo que pretende que a atividade da cópia utilize. Pode pensar nesta propriedade como o número máximo de fios dentro da atividade de cópia que lê a partir da sua fonte ou escrever para as suas lojas de dados de pia em paralelo.

A cópia paralela é ortogonal para [Unidades](#data-integration-units) de Integração de Dados ou [nós de INFRAVERMELHOS auto-hospedados](#self-hosted-integration-runtime-scalability). É contado em todos os DIUs ou nómadas de IR auto-hospedados.

Para cada atividade de cópia executada, por padrão Azure Data Factory aplica dinamicamente a definição de cópia paralela ideal com base no seu par de fontes e padrão de dados. 

> [!TIP]
> O comportamento padrão da cópia paralela geralmente dá-lhe a melhor produção, que é auto-determinada pela ADF com base no seu par de fontes-pia, padrão de dados e número de DIs ou o CPU/memória/contagem de nós do IR auto-hospedado. Consulte o [desempenho da atividade da cópia de resolução de problemas](copy-activity-performance-troubleshooting.md) sobre quando sintonizar a cópia paralela.

A tabela a seguir enumera o comportamento paralelo da cópia:

| Cenário de cópia | Comportamento de cópia paralela |
| --- | --- |
| Entre lojas de arquivos | `parallelCopies`determina o paralelismo **ao nível do ficheiro**. O pedaço dentro de cada ficheiro acontece por baixo de forma automática e transparente. Foi projetado para usar o melhor tamanho adequado para um determinado tipo de loja de dados para carregar dados em paralelo. <br/><br/>O número real de cópias paralelas utiliza a atividade de cópias no tempo de execução não é mais do que o número de ficheiros que tem. Se o comportamento da cópia for **fundirfile** em sumidouro de ficheiros, a atividade da cópia não pode tirar partido do paralelismo de nível de ficheiro. |
| Da loja de ficheiros à loja de não-arquivos | - Ao copiar dados para a Base de Dados Azure SQL ou para a Azure Cosmos DB, a cópia paralela padrão também depende do nível do lavatório (número de DTUs/RUs).<br>- Ao copiar dados para a Tabela Azure, a cópia paralela padrão é 4. |
| Da loja de não-arquivos à loja de ficheiros | - Ao copiar dados de loja de dados ativada por opção de partição (incluindo [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza,](connector-netezza.md#netezza-as-source) [SAP HANA,](connector-sap-hana.md#sap-hana-as-source) [SAP Open Hub,](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) [SAP Table,](connector-sap-table.md#sap-table-as-source) [SQL Server](connector-sql-server.md#sql-server-as-a-source), e [Teradata),](connector-teradata.md#teradata-as-source)a cópia paralela padrão é 4. O número real de cópias paralelas utiliza a atividade de cópias no tempo de execução não é mais do que o número de divisórias de dados que tem. Quando utilizar o tempo de execução de integração auto-hospedado e copiar para Azure Blob/ADLS Gen2, note que a cópia paralela máxima eficaz é de 4 ou 5 por nó DE IR.<br>- Para outros cenários, a cópia paralela não faz efeito. Mesmo que o paralelismo seja especificado, não é aplicado. |
| Entre lojas não-arquivadas | - Ao copiar dados para a Base de Dados Azure SQL ou para a Azure Cosmos DB, a cópia paralela padrão também depende do nível do lavatório (número de DTUs/RUs).<br/>- Ao copiar dados de loja de dados ativada por opção de partição (incluindo [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza,](connector-netezza.md#netezza-as-source) [SAP HANA,](connector-sap-hana.md#sap-hana-as-source) [SAP Open Hub,](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) [SAP Table,](connector-sap-table.md#sap-table-as-source) [SQL Server](connector-sql-server.md#sql-server-as-a-source), e [Teradata),](connector-teradata.md#teradata-as-source)a cópia paralela padrão é 4.<br>- Ao copiar dados para a Tabela Azure, a cópia paralela padrão é 4. |

Para controlar a carga em máquinas que acolhem as suas lojas de dados, ou para sintonizar o desempenho da cópia, pode sobrepor-se ao valor predefinido e especificar um valor para a `parallelCopies` propriedade. O valor deve ser um inteiro maior ou igual a 1. No tempo de execução, para o melhor desempenho, a atividade da cópia utiliza um valor inferior ou igual ao valor que define.

Quando especificar um valor para a `parallelCopies` propriedade, tenha em conta o aumento de carga na sua fonte e afunde as lojas de dados. Considere também o aumento da carga para o tempo de integração auto-hospedado se a atividade da cópia for potenciada por ela. Este aumento de carga acontece especialmente quando você tem múltiplas atividades ou execuções simultâneas das mesmas atividades que funcionam contra a mesma loja de dados. Se notar que a loja de dados ou o tempo de integração auto-hospedado está sobrecarregado com a carga, diminua o `parallelCopies` valor para aliviar a carga.

**Exemplo:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

## <a name="staged-copy"></a>Cópia encenada

Ao copiar dados de uma loja de dados de origem para uma loja de dados de lavatórios, poderá optar por utilizar o armazenamento Blob como uma loja de preparação provisória. A encenação é especialmente útil nos seguintes casos:

- **Pretende ingerir dados de várias lojas de dados no Azure Synapse Analytics (anteriormente SQL Data Warehouse) via PolyBase.** A Azure Synapse Analytics usa a PolyBase como um mecanismo de alta produção para carregar uma grande quantidade de dados em Azure Synapse Analytics. Os dados de origem devem estar no armazenamento da Blob ou na Azure Data Lake Store, e devem cumprir critérios adicionais. Quando carrega dados de uma loja de dados que não seja o armazenamento blob ou a Azure Data Lake Store, pode ativar a cópia de dados através de uma encenação provisória do armazenamento blob. Nesse caso, a Azure Data Factory realiza as transformações de dados necessárias para garantir que satisfaz os requisitos da PolyBase. Em seguida, utiliza o PolyBase para carregar os dados no Azure Synapse Analytics de forma eficiente. Para obter mais informações, consulte [a PolyBase para carregar dados no Armazém de Dados Azure SQL](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Por vezes, demora algum tempo a realizar um movimento de dados híbrido (isto é, a copiar de uma loja de dados no local para uma loja de dados em nuvem) sobre uma ligação de rede lenta.** Para melhorar o desempenho, pode utilizar uma cópia encenada para comprimir os dados no local, de modo a que leve menos tempo a mover dados para a loja de dados de encenação na nuvem. Em seguida, pode descomprimir os dados na loja de preparação antes de carregar na loja de dados de destino.
- **Você não quer abrir portas além do porto 80 e do porto 443 na sua firewall por causa de políticas corporativas de TI.** Por exemplo, quando copia dados de uma loja de dados no local para um lavatório Azure SQL Database ou um lavatório Azure Synapse Analytics, precisa de ativar a comunicação TCP de saída na porta 1433 para a firewall do Windows e para a sua firewall corporativa. Neste cenário, a cópia encenada pode aproveitar o tempo de integração auto-hospedado para copiar primeiro dados para uma instância de armazenamento Blob sobre HTTP ou HTTPS na porta 443. Em seguida, pode carregar os dados na BASE de Dados SQL ou na Azure Synapse Analytics a partir da paragem de armazenamento blob. Neste fluxo, não precisa de ativar a porta 1433.

### <a name="how-staged-copy-works"></a>Como funciona a cópia encenada

Quando ativa a função de encenação, primeiro os dados são copiados da loja de dados de origem para o armazenamento blob de encenação (traga o seu próprio). Em seguida, os dados são copiados da loja de dados de encenação para a loja de dados da pia. A Azure Data Factory gere automaticamente o fluxo de dois estágios para si. A Azure Data Factory também limpa dados temporários do armazenamento de encenação após o movimento de dados estar completo.

![Cópia encenada](media/copy-activity-performance/staged-copy.png)

Quando ativa o movimento de dados utilizando uma loja de encenação, pode especificar se pretende que os dados sejam comprimidos antes de transferir os dados da loja de dados de origem para uma loja de dados provisória ou de paragem e, em seguida, descomprimidos antes de mover os dados de uma loja de dados provisória ou de paragem para a loja de dados do lavatório.

Atualmente, não é possível copiar dados entre duas lojas de dados que estão ligadas através de diferentes IRs auto-hospedados, nem com ou sem cópia encenada. Para tal cenário, pode configurar duas atividades de cópia explicitamente acorrentadas para copiar de fonte para encenação e depois de encenação a pia.

### <a name="configuration"></a>Configuração

Configure a **definição de definição de ativação** na atividade de cópia para especificar se deseja que os dados sejam encenados no armazenamento blob antes de os colocar numa loja de dados de destino. Quando definir **ativar ativar,** `TRUE` especifique as propriedades adicionais listadas na tabela seguinte. Também precisa de criar um serviço de acesso partilhado a azure para a encenação se não tiver um.

| Propriedade | Descrição | Valor predefinido | Obrigatório |
| --- | --- | --- | --- |
| permitir A marcação |Especifique se pretende copiar dados através de uma loja de encenação provisória. |Falso |Não |
| linkedServiceName |Especifique o nome de um serviço ligado a [AzureStorage,](connector-azure-blob-storage.md#linked-service-properties) que se refere à instância de Armazenamento que utiliza como loja de encenação provisória. <br/><br/> Não é possível utilizar o Armazenamento com uma assinatura de acesso partilhado para carregar dados no Azure Synapse Analytics via PolyBase. Podes usá-lo em todos os outros cenários. |N/D |Sim, quando **ativar A definição de marcação** está definida para TRUE |
| caminho |Especifique a trajetória de armazenamento Blob que pretende conter os dados encenados. Se não fornecer um caminho, o serviço cria um recipiente para armazenar dados temporários. <br/><br/> Especifique um caminho apenas se utilizar o Armazenamento com uma assinatura de acesso partilhado, ou se necessitar de dados temporários para estar num local específico. |N/D |Não |
| permitir a compressão |Especifica se os dados devem ser comprimidos antes de serem copiados para o destino. Esta definição reduz o volume de dados que são transferidos. |Falso |Não |

>[!NOTE]
> Se utilizar cópia encenada com compressão ativada, o principal de serviço ou a autenticação MSI para o serviço de ligação blob de paragem não é suportado.

Aqui está uma definição de amostra de uma atividade de cópia com as propriedades descritas na tabela anterior:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Impacto de faturação de cópia encenada

É cobrado com base em dois passos: duração da cópia e tipo de cópia.

* Quando utiliza a encenação durante uma cópia em nuvem, que está a copiar dados de uma loja de dados em nuvem para outra loja de dados em nuvem, ambas as fases empoderadas pelo tempo de funcionamento da integração do Azure, é-lhe cobrada a [soma da duração da cópia para o passo 1 e passo 2] x [preço unitário de cópia em nuvem].
* Quando utiliza a encenação durante uma cópia híbrida, que está a copiar dados de uma loja de dados no local para uma loja de dados em nuvem, uma fase potenciada por um tempo de integração auto-hospedado, é cobrado por [duração da cópia híbrida] x [preço unitário de cópia híbrida] + [duração da cópia da nuvem] x [preço unitário de cópia de nuvem].

## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos de atividade de cópia:

- [Visão geral da atividade da cópia](copy-activity-overview.md)
- [Copiar o desempenho da atividade e o guia de escalabilidade](copy-activity-performance.md)
- [Desempenho da atividade da cópia de resolução de problemas](copy-activity-performance-troubleshooting.md)
- [Utilize a Azure Data Factory para migrar dados do seu lago de dados ou armazém de dados para Azure](data-migration-guidance-overview.md)
- [Migrar dados do Amazon S3 para o Armazenamento do Azure](data-migration-guidance-s3-azure-storage.md)