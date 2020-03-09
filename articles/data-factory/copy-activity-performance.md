---
title: Copiar guia de desempenho e escalabilidade da atividade
description: Conheça os factores-chave que afetam o desempenho do movimento de dados na Azure Data Factory quando utiliza a atividade da cópia.
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
ms.date: 10/24/2019
ms.openlocfilehash: 28d0da369083d75bc175111d808828e186a366fc
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357163"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Copiar guia de desempenho e escalabilidade da atividade

> [!div class="op_single_selector" title1="Selecione a versão da Azure Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-copy-activity-performance.md)
> * [Versão atual](copy-activity-performance.md)

Quer pretenda realizar uma migração de dados em larga escala do data lake ou do armazém de dados da empresa (EDW) para o Azure, ou se pretende ingerir dados em escala de diferentes fontes para OT para análise de big data, é fundamental alcançar o melhor desempenho e escalabilidade.  A Azure Data Factory fornece um mecanismo performativo, resiliente e rentável para ingerir dados em escala, tornando-o um ótimo ajuste para engenheiros de dados que procuram construir oleodutos de ingestão de dados altamente performantes e escaláveis.

Depois de ler este artigo, será capaz de responder às seguintes perguntas:

- Que nível de desempenho e escalabilidade posso conseguir usando a atividade de cópia ADF para cenários de migração de dados e ingestão de dados?

- Que medidas devo tomar para afinar o desempenho da atividade de cópia seletiva?
- Que botões de otimização de perf aADF posso utilizar para otimizar o desempenho para uma única execução de atividade de cópia?
- Que outros fatores fora da ADF devem considerar na otimização do desempenho da cópia?

> [!NOTE]
> Se não estiver familiarizado com a atividade da cópia em geral, consulte a [visão geral](copy-activity-overview.md) da atividade da cópia antes de ler este artigo.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Desempenho de cópia e escalabilidade alcançável usando ADF

A ADF oferece uma arquitetura sem servidores que permite o paralelismo a diferentes níveis, o que permite que os desenvolvedores construam oleodutos para utilizar totalmente a largura de banda da sua rede, bem como o armazenamento de IOPS e largura de banda para maximizar a produção de movimento de dados para o seu ambiente.  Isto significa que a produção que pode obter pode ser estimada medindo o rendimento mínimo oferecido pela loja de dados de origem, a loja de dados de destino e a largura de banda da rede entre a fonte e o destino.  A tabela abaixo calcula a duração da cópia com base no tamanho dos dados e no limite de largura de banda para o seu ambiente. 

| Tamanho dos dados / <br/> largura de banda | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min    | 1,4 min   | 0,3 min   | 0,1 min  | 0,03 min | 0,01 min | 0,0 min   |
| **10 GB**                   | 27,3 min   | 13,7 min  | 2,7 min   | 1,3 min  | 0,3 min  | 0,1 min  | 0,03 min  |
| **100 GB**                  | 4,6 horas    | 2,3 horas   | 0,5 horas   | 0,2 horas  | 0,05 horas | 0,02 horas | 0,0 horas   |
| **1 TB**                    | 46,6 horas   | 23,3 horas  | 4,7 horas   | 2,3 horas  | 0,5 horas  | 0,2 horas  | 0,05 horas  |
| **10 TB**                   | 19,4 dias  | 9,7 dias  | 1,9 dias  | 0,9 dias | 0,2 dias | 0,1 dias | 0,02 dias |
| **100 TB**                  | 194.2 dias | 97,1 dias | 19,4 dias | 9,7 dias | 1,9 dias | 1 dias   | 0,2 dias  |
| **1 PB**                    | 64,7 mo    | 32,4 mo   | 6,5 mo    | 3.2 mo   | 0,6 mo   | 0,3 mo   | 0,06 mo   |
| **10 PB**                   | 647.3 mo   | 323.6 mo  | 64,7 mo   | 31.6 mo  | 6,5 mo   | 3.2 mo   | 0,6 mo    |

A cópia ADF é escalável a diferentes níveis:

![como as escalas de cópia ADF](media/copy-activity-performance/adf-copy-scalability.png)

- O fluxo de controlo ADF pode iniciar múltiplas atividades de cópia em paralelo, por exemplo, utilizando [Para cada loop](control-flow-for-each-activity.md).
- Uma única atividade de cópia pode tirar partido dos recursos de computação escaláveis: ao utilizar o Tempo de Execução de Integração Azure, pode especificar [até 256 DIUs](#data-integration-units) para cada atividade de cópia de forma sem servidores; ao utilizar o tempo de funcionamento de integração auto-hospedado, pode aumentar manualmente a máquina ou escalar para várias máquinas[(até 4 nós](create-self-hosted-integration-runtime.md#high-availability-and-scalability)), e uma única atividade de cópia irá dividir o seu ficheiro definido em todos os nós.
- Uma única atividade de cópia lê e escreve para a loja de dados usando múltiplos fios [em paralelo](#parallel-copy).

## <a name="performance-tuning-steps"></a>Etapas de ajuste de desempenho

Tome estes passos para afinar o desempenho do seu serviço Azure Data Factory com a atividade de cópia.

1. **Pegue um conjunto de dados de teste e estabeleça uma linha de base.** Durante a fase de desenvolvimento, teste o seu pipeline utilizando a atividade de cópia contra uma amostra representativa de dados. O conjunto de dados que escolher deve representar os seus padrões típicos de dados (estrutura de pastas, padrão de ficheiros, esquema de dados, etc.), e é suficientemente grande para avaliar o desempenho da cópia, por exemplo, demora 10 minutos ou mais para que a atividade da cópia seja concluída. Recolher detalhes de execução e características de desempenho após [a monitorização](copy-activity-overview.md#monitoring)da atividade de cópia .

2. **Como maximizar o desempenho de uma única atividade de cópia:**

   Para começar, recomendamos que maximize primeiro o desempenho usando uma única atividade de cópia.

   **Se a atividade de cópia estiver a ser executada num Tempo de Execução de Integração Azure:**

   Comece com valores predefinidos para Unidades de [Integração de Dados (DIU)](#data-integration-units) e definições [de cópia paralelas.](#parallel-copy)  Execute uma execução de teste de desempenho e tome nota do desempenho alcançado, bem como dos valores reais utilizados para DIUs e cópias paralelas.  Consulte a [monitorização](copy-activity-overview.md#monitoring) da atividade de cópia sobre como recolher os resultados de execução e as definições de desempenho utilizadas.

   Agora realize ensaios de desempenho adicionais, duplicando cada vez o valor para a definição de DIU.  Em alternativa, se achar que o desempenho alcançado utilizando a definição padrão está muito abaixo da sua expectativa, pode aumentar a definição de DIU de forma mais drástica no teste subsequente.

   A atividade de cópia deve escalar quase na perfeição à medida que aumenta a definição de DIU.  Se ao duplicar a definição de DIU não estiver a ver o duplo de entrada, duas coisas podem estar a acontecer:

   - O padrão de cópia específico que está a executar não beneficia de adicionar mais DIUs.  Apesar de ter especificado um valor DIU maior, o DIU usado real permaneceu o mesmo, pelo que está a obter a mesma produção que antes.  Se for esse o caso, maximize a produção agregada executando várias cópias simultaneamente remetendo o passo 3.
   - Adicionando mais DIUs (mais potência) e conduzindo assim uma maior taxa de extração de dados, transferência e carregamento, quer a loja de dados de origem, a rede entre eles, ou a loja de dados de destino, atingiu o seu estrangulamento e possivelmente foi estrangulada.  Se for esse o caso, tente contactar o administrador da sua loja de dados ou o administrador da rede para aumentar o limite máximo, ou, em alternativa, reduzir a definição de DIU até que o estrangulamento pare de ocorrer.

   **Se a atividade de cópia estiver a ser executada num Tempo de Integração auto-hospedado:**

   Recomendamos que utilize uma máquina dedicada separada do servidor que hospeda a loja de dados para hospedar o tempo de execução da integração.

   Comece com valores predefinidos para a definição [de cópia paralela](#parallel-copy) e utilizando um único nó para o IR auto-hospedado.  Execute uma execução de teste de desempenho e tome nota do desempenho alcançado.

   Se quiser obter uma maior entrada, pode aumentar ou aumentar o IR auto-hospedado:

   - Se a CPU e a memória disponível no nó de IR auto-hospedado não estiverem totalmente utilizadas, mas a execução de postos de trabalho simultâneos está a atingir o limite, deverá aumentar o número de postos de trabalho simultâneos que podem funcionar num nó.  Consulte [aqui](create-self-hosted-integration-runtime.md#scale-up) as instruções.
   - Se, por outro lado, o CPU estiver no topo do nó DE IR auto-hospedado ou a memória disponível for baixa, pode adicionar um novo nó para ajudar a escalar a carga através dos vários nós.  Consulte [aqui](create-self-hosted-integration-runtime.md#high-availability-and-scalability) as instruções.

   À medida que aumenta ou aumenta a capacidade do IR auto-hospedado, repita o teste de desempenho para ver se está cada vez melhor.  Se a produção parar de melhorar, muito provavelmente a loja de dados de origem, a rede entre elas ou a loja de dados de destino atingiu o seu estrangulamento e está a começar a ficar estrangulada. Se for esse o caso, tente contactar o administrador da sua loja de dados ou o seu administrador de rede para aumentar o limite máximo, ou, em alternativa, voltar à sua configuração de escala anterior para o IR auto-hospedado. 

3. **Como maximizar a produção agregada executando várias cópias simultaneamente:**

   Agora que maximizou o desempenho de uma única atividade de cópia, se ainda não atingiu os limites superiores de produção do seu ambiente – rede, loja de dados de origem e loja de dados de destino – pode executar múltiplas atividades de cópia em paralelo utilizando construções de fluxo de controlo ADF como [For Each loop](control-flow-for-each-activity.md).

4. **Dicas de afinação de desempenho e funcionalidades de otimização.** Em alguns casos, quando executa uma atividade de cópia na Azure Data Factory, vê-se uma mensagem de "Performance tuning tips" em cima da monitorização da atividade de [cópia,](copy-activity-overview.md#monitor-visually)como mostra o seguinte exemplo. A mensagem diz-lhe o estrangulamento que foi identificado para a execução da cópia. Também o guia sobre o que mudar para aumentar a entrada de cópias. As dicas de afinação de desempenho atualmente fornecem sugestões como:

   - Utilize a PolyBase quando copiar dados para o Armazém de Dados Azure SQL.
   - Aumente as Unidades de Pedido DB azure Cosmos ou a Azure SQL Database DTUs (Unidades de Produção de Base de Dados) quando o recurso do lado da loja de dados for o estrangulamento.
   - Retire a cópia encenada desnecessária.

   As regras de afinação de desempenho também serão enriquecidas gradualmente.

   **Exemplo: Copiar para a Base de Dados SQL Azure com dicas de afinação de desempenho**

   Nesta amostra, durante uma cópia, a Azure Data Factory nota que a base de dados Azure SQL atinge uma utilização elevada de DTU, o que atrasa as operações de escrita. A sugestão é aumentar o nível de Base de Dados Azure SQL com mais DTUs. 

   ![Monitorização de cópias com dicas de afinação de desempenho](media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

   Além disso, são algumas funcionalidades de otimização de desempenho que deve estar ciente:

   - [Cópia paralela](#parallel-copy)
   - [Unidades de Integração de Dados](#data-integration-units)
   - [Cópia encenada](#staged-copy)
   - [Escalabilidade do tempo de execução da integração auto-hospedada](concepts-integration-runtime.md#self-hosted-integration-runtime)

5. **Expanda a configuração para todo o seu conjunto de dados.** Quando estiver satisfeito com os resultados da execução e o desempenho, pode expandir a definição e o pipeline para cobrir todo o conjunto de dados.

## <a name="copy-performance-optimization-features"></a>Copiar funcionalidades de otimização de desempenho

A Azure Data Factory fornece as seguintes funcionalidades de otimização de desempenho:

- [Cópia paralela](#parallel-copy)
- [Unidades de Integração de Dados](#data-integration-units)
- [Cópia encenada](#staged-copy)

### <a name="data-integration-units"></a>Unidades de Integração de Dados

Uma Unidade de Integração de Dados é uma medida que representa o poder (uma combinação de CPU, memória e alocação de recursos de rede) de uma única unidade na Azure Data Factory. A Unidade de Integração de Dados aplica-se apenas ao tempo de execução da [integração do Azure,](concepts-integration-runtime.md#azure-integration-runtime)mas não ao tempo de execução da [integração auto-hospedada.](concepts-integration-runtime.md#self-hosted-integration-runtime)

Será cobrado **# de DIUs usados \* duração da cópia \* preço unitário/DIU-hora**. Veja aqui os [preços](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)correntes. A moeda local e a desvalorização separada podem aplicar-se por tipo de subscrição.

Os DIUs autorizados a capacitar uma execução de atividade de cópia é **entre 2 e 256**. Se não for especificado ou escolher "Auto" no UI, a Data Factory aplica dinamicamente a definição de DIU ideal com base no seu par de sumidouros e no padrão de dados. A tabela que se segue lista as DIUs predefinidas utilizadas em diferentes cenários de cópia:

| Cenário de cópia | DIUs predefinido determinados pelo serviço |
|:--- |:--- |
| Copiar dados entre arquivos baseados em ficheiros | Entre 4 e 32 dependendo do número e tamanho dos ficheiros |
| Copiar dados para Azure SQL Database ou Azure Cosmos DB |Entre 4 e 16 dependendo do sumidouro Azure SQL Database's ou do nível cosmos DB (número de DTUs/RUs) |
| Todos os outros cenários de cópia | 4 |

Para anular este padrão, especifique um valor para a propriedade **dataIntegrationUnits** da seguinte forma. O *número real de DIUs* que a operação de cópia utiliza no tempo de execução é igual ou inferior ao valor configurado, dependendo do seu padrão de dados.

Pode ver os DIUs utilizados para cada cópia executada na saída de atividade da cópia quando monitoriza uma execução de atividade. Para mais informações, consulte a [monitorização da atividade do Copy](copy-activity-overview.md#monitoring).

> [!NOTE]
> A definição de DIUs maior do que quatro aplica-se atualmente apenas quando copia vários ficheiros de Azure Blob/ADLS Gen1/ADLS Gen2/Amazon S3/Google Cloud Storage/cloud FTP/cloud SFTP ou de loja de dados relacionais com oposição de divisória (incluindo [a Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata)](connector-teradata.md#teradata-as-source)a quaisquer outras lojas de dados em nuvem.

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
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="parallel-copy"></a>Cópia paralela

Pode utilizar a propriedade **parallelCopy** para indicar o paralelismo que pretende que a atividade da cópia utilize. Pode pensar nesta propriedade como o número máximo de fios dentro da atividade de cópia que pode ler a partir da sua fonte ou escrever para as suas lojas de dados de pia em paralelo.

Para cada execução de atividade de cópia, a Azure Data Factory determina o número de cópias paralelas a utilizar para copiar dados da loja de dados de origem e para a loja de dados de destino. O número predefinido de cópias paralelas que utiliza depende do tipo de fonte e da pia que utiliza.

| Cenário de cópia | Número de cópias paralela de predefinido determinado pelo serviço |
| --- | --- |
| Copiar dados entre arquivos baseados em ficheiros |Depende do tamanho dos ficheiros e do número de DIUs utilizados para copiar dados entre duas lojas de dados em nuvem, ou da configuração física da máquina de tempo de execução de integração auto-hospedada. |
| Cópia de data store relacional com opção de partição ativada (incluindo [Oráculo,](connector-oracle.md#oracle-as-source) [Netezza,](connector-netezza.md#netezza-as-source) [Teradata,](connector-teradata.md#teradata-as-source) [Tabela SAP](connector-sap-table.md#sap-table-as-source)e [SAP Open Hub)](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)|4 |
| Copiar dados de qualquer loja de origem para armazenamento de tabela seletiva Azure |4 |
| Todos os outros cenários de cópia |1 |

> [!TIP]
> Quando copia dados entre lojas baseadas em ficheiros, o comportamento predefinido geralmente dá-lhe a melhor entrada. O comportamento predefinido é determinado automaticamente com base no seu padrão de ficheiro de origem.

Para controlar a carga em máquinas que hospedam as suas lojas de dados, ou para afinar o desempenho da cópia, pode anular o valor predefinido e especificar um valor para a propriedade **paralelaCopy.** O valor tem de ser um número inteiro maior que ou igual a 1. No tempo de execução, para o melhor desempenho, a atividade de cópia utiliza um valor inferior ou igual ao valor que definiu.

**Pontos a notar:**

- Quando copia dados entre lojas baseadas em ficheiros, a **parallelCopy** determina o paralelismo ao nível dos ficheiros. O pedaço dentro de um único ficheiro acontece por baixo de forma automática e transparente. Foi concebido para utilizar o melhor tamanho adequado para um determinado tipo de loja de dados de origem para carregar dados em paralelo e ortogonal para **cópias paralelas**. O número real de cópias paralelas utiliza o serviço de movimento de dados para a operação de cópia em tempo de execução é não mais do que o número de ficheiros que tiver. Se o comportamento da cópia for **fundidoFile,** a atividade de cópia não pode tirar partido do paralelismo de nível de ficheiro.
- Quando copia dados de lojas que não são baseadas em ficheiros (exceto a [Oracle,](connector-oracle.md#oracle-as-source) [Netezza,](connector-netezza.md#netezza-as-source) [Teradata,](connector-teradata.md#teradata-as-source) [Tabela SAP](connector-sap-table.md#sap-table-as-source)e conector [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) como fonte com partilha de dados habilitados) para lojas baseadas em ficheiros, o serviço de movimento de dados ignora a propriedade **paralelaCopy.** Mesmo que o paralelismo é especificado, não é aplicado neste caso.
- A propriedade **parallelCopy** é ortogonal a **dataIntegrationUnits**. O primeiro é contado em todas as unidades de integração de dados.
- Quando especificar um valor para a propriedade **parallelCopies,** considere o aumento de carga nas suas lojas de dados de origem e afundar. Considere também o aumento da carga para o tempo de funcionação de integração auto-hospedado se a atividade de cópia for empoderada por ela, por exemplo, para cópia híbrida. Este aumento de carga acontece especialmente quando você tem múltiplas atividades ou executagens simultâneas das mesmas atividades que funcionam contra a mesma loja de dados. Se notar que a loja de dados ou o tempo de execução de integração auto-hospedado estão sobrecarregados com a carga, diminua o valor **paraleloCópias** para aliviar a carga.

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

### <a name="staged-copy"></a>Cópia faseada

Quando copia dados de um arquivo de dados de origem para um arquivo de dados de sink, pode optar por utilizar o armazenamento de BLOBs como um armazenamento de teste provisório. Teste é especialmente útil nos seguintes casos:

- **Deseja ingerir dados de várias lojas de dados no SQL Data Warehouse via PolyBase.** O SQL Data Warehouse utiliza o PolyBase como um mecanismo de alto débito para carregar uma grande quantidade de dados para o SQL Data Warehouse. Os dados de origem devem estar no armazenamento blob ou na Azure Data Lake Store, e devem satisfazer critérios adicionais. Quando carrega dados de um arquivo de dados que não seja o armazenamento de BLOBs ou do Azure Data Lake Store, pode ativar copiar por meio de armazenamento de BLOBs de teste provisório de dados. Nesse caso, a Azure Data Factory realiza as transformações de dados necessárias para garantir que satisfaz os requisitos da PolyBase. Em seguida, ele utiliza o PolyBase para carregar dados para o SQL Data Warehouse com eficiência. Para mais informações, consulte Use PolyBase para carregar dados no Armazém de [Dados Azure SQL](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Por vezes demora algum tempo a realizar um movimento de dados híbrido (isto é, copiar de uma loja de dados no local para uma loja de dados em nuvem) sobre uma ligação de rede lenta.** Para melhorar o desempenho, pode utilizar cópias encenadas para comprimir os dados no local, de modo a que leve menos tempo para mover dados para a loja de dados de encenação na nuvem. Em seguida, pode descomprimir os dados na loja de preparação antes de carregar na loja de dados de destino.
- **Não quer abrir portas que não sejam o porto 80 e o porto 443 na sua firewall por causa das políticas de TI corporativas.** Por exemplo, quando copia dados de um arquivo de dados no local para um sink de base de dados do Azure SQL ou um coletor do Azure SQL Data Warehouse, terá de ativar a comunicação de saída de TCP na porta 1433 para a firewall do Windows e sua firewall empresarial. Neste cenário, a cópia encenada pode aproveitar o tempo de execução da integração auto-hospedada para primeiro copiar dados para uma instância de armazenamento Blob sobre HTTP ou HTTPS na porta 443. Em seguida, pode carregar os dados em Base de Dados SQL ou Armazém de Dados SQL a partir da encenação de armazenamento Blob. Neste fluxo, não terá de ativar a porta 1433.

#### <a name="how-staged-copy-works"></a>Funciona como faseada de cópia

Quando ativar a funcionalidade de teste, primeiro os dados são copiados do arquivo de dados de origem para o armazenamento de BLOBs de teste (traga a sua própria). Em seguida, os dados são copiados do arquivo de dados de teste para o arquivo de dados de sink. A Azure Data Factory gere automaticamente o fluxo de dois estágios para si. A Azure Data Factory também limpa dados temporários do armazenamento de encenação após o movimento de dados estar concluído.

![Cópia faseada](media/copy-activity-performance/staged-copy.png)

Quando ativa o movimento de dados utilizando uma loja de encenação, pode especificar se pretende que os dados sejam comprimidos antes de transferir os dados da loja de dados de origem para uma loja provisória ou de encenação de dados e, em seguida, descomprimido antes de mover dados de um dat provisório ou de encenação uma loja para a loja de dados da pia.

Atualmente, não é possível copiar dados entre duas lojas de dados que estão ligadas através de diferentes IRs auto-hospedados, nem com nem com cópia encenada. Para tal cenário, pode configurar duas atividades de cópia explicitamente acorrentadas para copiar de origem para encenação e depois de encenação a afundar.

#### <a name="configuration"></a>Configuração

Configure a definição **de habilitação** Na atividade da cópia para especificar se pretende que os dados sejam encenados no armazenamento blob antes de os carregar numa loja de dados de destino. Quando definir **o habilitação** para `TRUE`, especifique as propriedades adicionais listadas na tabela seguinte. Também precisa de criar um serviço de acesso partilhado azure Storage ou Storage para encenação se não tiver um.

| Propriedade | Descrição | Valor predefinido | Necessário |
| --- | --- | --- | --- |
| enableStaging |Especifique se pretende copiar os dados por meio de um arquivo de teste de interim. |Falso |Não |
| linkedServiceName |Especifique o nome de um serviço ligado ao [AzureStorage,](connector-azure-blob-storage.md#linked-service-properties) que se refere à instância de Armazenamento que utiliza como uma loja de encenação provisória. <br/><br/> Não é possível utilizar o Armazenamento com uma assinatura de acesso partilhado para carregar dados no Armazém de Dados SQL através da PolyBase. Pode usá-lo em todos os outros cenários. |N/D |Sim, quando **o habilitar É** definido para TRUE |
| caminho |Especifique o caminho de armazenamento de BLOBs para conter os dados em etapas. Se não fornecer um caminho, o serviço cria um recipiente para armazenar dados temporários. <br/><br/> Especifique um caminho de apenas se utilizar o armazenamento com uma assinatura de acesso partilhado, ou se necessitar de temporários de dados numa localização específica. |N/D |Não |
| enableCompression |Especifica se os dados devem ser comprimidos antes de serem copiados para o destino. Esta definição reduz o volume de dados a serem transferidos. |Falso |Não |

>[!NOTE]
> Se utilizar cópia faseada com compressão ativada, o diretor de serviço ou a autenticação MSI para a realização do serviço ligado à bolha não é suportado.

Aqui está uma definição de amostra de uma atividade de cópia com as propriedades que são descritas na tabela anterior:

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

#### <a name="staged-copy-billing-impact"></a>Impacto de faturação de cópia faseada

É cobrado com base em dois passos: duração da cópia e tipo de cópia.

* Quando utiliza a encenação durante uma cópia em nuvem, que está a copiar dados de uma loja de dados em nuvem para outra loja de dados em nuvem, ambas as fases empoderadas pelo tempo de execução da integração do Azure, é-lhe cobrada a [soma da duração da cópia para o passo 1 e passo 2] x [preço unitário de cópia em nuvem].
* Quando utiliza a encenação durante uma cópia híbrida, que está a copiar dados de uma loja de dados no local para uma loja de dados em nuvem, uma fase capacitada por um tempo de integração auto-hospedado, é cobrado pela duração [da cópia híbrida] x [preço unitário de cópia híbrida] + [duração da cópia em nuvem] x [preço unitário de cópia de nuvem].

## <a name="references"></a>Referências

Aqui estão as referências de monitorização e afinação de desempenho para algumas das lojas de dados suportadas:

* Armazenamento Azure Blob: Metas de [escalabilidade e desempenho para armazenamento blob](../storage/blobs/scalability-targets.md) e performance e lista de verificação de [escalabilidade para armazenamento Blob](../storage/blobs/storage-performance-checklist.md).
* Armazenamento de mesa azul: Metas de [escalabilidade e desempenho para armazenamento](../storage/tables/scalability-targets.md) de mesa e performance e lista de verificação de [escalabilidade para armazenamento](../storage/tables/storage-performance-checklist.md)de mesa .
* Base de dados Azure SQL: Pode [monitorizar o desempenho](../sql-database/sql-database-single-database-monitor.md) e verificar a percentagem da Unidade de Transações de Bases de Dados (DTU).
* Armazém de Dados Azure SQL: A sua capacidade é medida em Unidades de Armazém de Dados (DWUs). Ver Gerir a potência da computação no Armazém de [Dados Azure SQL (visão geral)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Níveis de desempenho em Azure Cosmos DB](../cosmos-db/performance-levels.md).
* No local, SQL Server: [Monitor e sintonização para desempenho](https://msdn.microsoft.com/library/ms189081.aspx).
* Servidor de ficheiros no local: [Sintonização de desempenho para servidores](https://msdn.microsoft.com/library/dn567661.aspx)de ficheiros .

## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos de atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Utilize a Azure Data Factory para migrar dados do seu lago de dados ou armazém de dados para o Azure](data-migration-guidance-overview.md)
- [Migrar dados da Amazon S3 para o Armazenamento Azure](data-migration-guidance-s3-azure-storage.md)
