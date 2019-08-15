---
title: Guia de desempenho e ajuste da atividade de cópia no Azure Data Factory | Microsoft Docs
description: Saiba mais sobre os principais fatores que afetam o desempenho da movimentação de dados em Azure Data Factory quando você usa a atividade de cópia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: d8ce0a4f6bacdd1c8c858d474e6f3957a23c6357
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967364"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Guia de desempenho e ajuste da atividade de cópia
> [!div class="op_single_selector" title1="Selecione a versão do Azure Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-copy-activity-performance.md)
> * [Versão atual](copy-activity-performance.md)


O Azure Data Factory atividade de cópia fornece uma solução de carregamento de dados segura, confiável e de alto desempenho de primeira classe. Você pode usá-lo para copiar dezenas de terabytes de dados todos os dias em uma grande variedade de armazenamentos de dados locais e na nuvem. O desempenho rápido de carregamento de dados é a chave para garantir que você possa se concentrar no principal problema de Big Data: criar soluções de análise avançada e obter informações aprofundadas de todos esses dados.

O Azure fornece um conjunto de soluções de armazenamento e data warehouse de dados de nível empresarial. A atividade de cópia oferece uma experiência de carregamento de dados altamente otimizada que é fácil de configurar e configurar. Com uma única atividade de cópia, você pode carregar dados em:

* Azure SQL Data Warehouse a 1,2 GBps.
* Armazenamento de BLOBs do Azure a 1,0 GBps.
* Azure Data Lake Store a 1,0 GBps.

Este artigo descreve:

* Os [números de referência de desempenho](#performance-reference) para armazenamentos de dados de origem e coletor com suporte para ajudá-lo a planejar seu projeto.
* Recursos que podem impulsionar a taxa de transferência de cópia em diferentes cenários, o que inclui DIUs ( [unidades de integração de dados](#data-integration-units) ), [cópia paralela](#parallel-copy)e [cópia em etapas](#staged-copy).
* [Diretrizes de ajuste de desempenho](#performance-tuning-steps) sobre como ajustar o desempenho e os fatores principais que podem afetar o desempenho da cópia.

> [!NOTE]
> Se você não estiver familiarizado com a atividade de cópia em geral, consulte a [visão geral da atividade de cópia](copy-activity-overview.md) antes de ler este artigo.
>

## <a name="performance-reference"></a>Referência de desempenho

Como referência, a tabela a seguir mostra o número da taxa de transferência de cópia em MBps para os pares de origem e coletor determinados em uma única execução de atividade de cópia com base em testes internos. Para comparação, ele também demonstra como configurações diferentes de [unidades de integração de dados](#data-integration-units) ou escalabilidade de [tempo de execução de integração auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime) (vários nós) pode ajudar no desempenho da cópia.

![Matriz de desempenho](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> Quando a atividade de cópia é executada em um tempo de execução de integração do Azure, as unidades de integração de dados mínimas permitidas (anteriormente conhecidas como unidades de movimentação de dados) são duas. Se não for especificado, consulte as unidades de integração de dados padrão que estão sendo usadas em [unidades de integração de dados](#data-integration-units).

**Pontos a serem observados:**

* A taxa de transferência é calculada usando a seguinte fórmula: [tamanho dos dados lidos da origem]/[duração da execução da atividade de cópia].
* Os números de referência de desempenho na tabela foram medidos usando um conjunto de um DataSet do [TPC-H](http://www.tpc.org/tpch/) em uma única execução da atividade de cópia. Arquivos de teste para repositórios baseados em arquivo são vários arquivos com 10 GB de tamanho.
* Nos arquivos de dados do Azure, a origem e sink são na mesma região do Azure.
* Para cópia híbrida entre armazenamentos de dados locais e na nuvem, cada nó de tempo de execução de integração auto-hospedado estava em execução em um computador separado do armazenamento de dados com a especificação a seguir. Quando uma única atividade estiver em execução, a operação de cópia consumido apenas uma pequena parte de CPU, memória ou largura de banda de rede da máquina de teste.
    <table>
    <tr>
        <td>CPU</td>
        <td>32 núcleos 2.20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Memória</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Rede</td>
        <td>Interface de Internet: 10 Gbps; interface da intranet: 40 Gbps</td>
    </tr>
    </table>


> [!TIP]
> Você pode obter uma taxa de transferência mais alta usando mais DIUs. Por exemplo, com 100 DIUs, você pode copiar dados do armazenamento de BLOBs do Azure para Azure Data Lake Store a 1,0 GBps. Para obter mais informações sobre esse recurso e o cenário com suporte, consulte a seção [unidades de integração de dados](#data-integration-units) . 

## <a name="data-integration-units"></a>Unidades de integração de dados

Uma unidade de integração de dados é uma medida que representa a potência (uma combinação de CPU, memória e alocação de recursos de rede) de uma única unidade no Azure Data Factory. A unidade de integração de dados só se aplica ao [Integration Runtime do Azure](concepts-integration-runtime.md#azure-integration-runtime), mas não ao [tempo de execução de integração auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime).

O mínimo de DIUs para capacitar uma execução de atividade de cópia é dois. Se não for especificado, a tabela seguinte lista os DIUs padrão usados em cenários de cópia diferentes:

| Cenário de cópia | DIUs predefinido determinados pelo serviço |
|:--- |:--- |
| Copiar dados entre arquivos baseados em ficheiros | Entre 4 e 32, dependendo do número e do tamanho dos arquivos |
| Copiar dados para o banco de dados SQL do Azure ou Azure Cosmos DB |Entre 4 e 16 dependendo da camada do banco de dados SQL do Azure ou do Cosmos DB (número de DTUs/RUs) |
| Todos os outros cenários de cópia | 4 |

Para substituir esta predefinição, especifique um valor para o **dataIntegrationUnits** propriedade da seguinte forma. Os *valores permitidos* para a propriedade **dataIntegrationUnits** é de até 256. O *número real de DIUs* que a operação de cópia utiliza no tempo de execução é igual ou inferior ao valor configurado, dependendo do padrão de dados. Para obter informações sobre o nível de ganho de desempenho poderá obter ao configurar mais unidades para uma origem de cópia específico e de sink, consulte a [referência de desempenho](#performance-reference).

Você pode ver o DIUs usado para cada execução de cópia na saída da atividade de cópia ao monitorar uma execução de atividade. Para obter mais informações, consulte [monitoramento de atividade de cópia](copy-activity-overview.md#monitoring).

> [!NOTE]
> A configuração de DIUs maior que quatro atualmente se aplica somente quando você copia vários arquivos do armazenamento do Azure, Azure Data Lake Storage, Amazon S3, Google Cloud Storage, FTP de nuvem ou SFTP em nuvem para qualquer outro armazenamento de dados de nuvem.
>

**Exemplo**

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

### <a name="data-integration-units-billing-impact"></a>Impacto de faturação de unidades de integração de dados

Lembre-se de que você será cobrado com base no tempo total da operação de cópia. A duração total que você é cobrada pela movimentação de dados é a soma da duração em DIUs. Se uma tarefa de cópia costumava levar uma hora com duas unidades de cloud e agora demora 15 minutos com oito unidades de cloud, a fatura global permanece praticamente o mesmo.

## <a name="parallel-copy"></a>Cópia paralela

Você pode usar a propriedade **parallelCopies** para indicar o paralelismo que você deseja que a atividade de cópia use. Você pode considerar essa propriedade como o número máximo de threads na atividade de cópia que pode ler de sua origem ou gravar em armazenamentos de dados de coletor em paralelo.

Para cada execução de atividade de cópia, Azure Data Factory determina o número de cópias paralelas a serem usadas para copiar dados do armazenamento de dados de origem e para o armazenamento de dados de destino. O número padrão de cópias paralelas que ele usa depende do tipo de fonte e do coletor que você usa.

| Cenário de cópia | Número de cópias paralela de predefinido determinado pelo serviço |
| --- | --- |
| Copiar dados entre arquivos baseados em ficheiros |Depende do tamanho dos arquivos e do número de DIUs usados para copiar dados entre dois armazenamentos de dados de nuvem ou a configuração física do computador do Integration Runtime de hospedagem interna. |
| Copiar dados de qualquer repositório de origem para o armazenamento de tabelas do Azure |4 |
| Todos os outros cenários de cópia |1 |

> [!TIP]
> Quando você copia dados entre repositórios baseados em arquivo, o comportamento padrão geralmente oferece a melhor taxa de transferência. O comportamento padrão é determinado automaticamente com base no padrão do arquivo de origem.

Para controlar a carga em computadores que hospedam seus armazenamentos de dados ou para ajustar o desempenho da cópia, você pode substituir o valor padrão e especificar um valor para a propriedade **parallelCopies** . O valor tem de ser um número inteiro maior que ou igual a 1. Em tempo de execução, para obter o melhor desempenho, a atividade de cópia usa um valor menor ou igual ao valor que você definiu.

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

**Pontos a serem observados:**

* Quando você copia dados entre repositórios baseados em arquivo, o **parallelCopies** determina o paralelismo no nível de arquivo. O agrupamento em um único arquivo ocorre abaixo de forma automática e transparente. Ele foi projetado para usar o melhor tamanho de bloco adequado para um determinado tipo de armazenamento de dados de origem para carregar dados em paralelo e ortogonal em **parallelCopies**. O número real de cópias paralelas utiliza o serviço de movimento de dados para a operação de cópia em tempo de execução é não mais do que o número de ficheiros que tiver. Se o comportamento de cópiafor mergefile, a atividade de cópia não poderá tirar proveito do paralelismo de nível de arquivo.
* Quando você copia dados de armazenamentos que não são baseados em arquivo (exceto [Oracle](connector-oracle.md#oracle-as-source), [Teradata](connector-teradata.md#teradata-as-source), [tabela SAP](connector-sap-table.md#sap-table-as-source)e conector de [Hub aberto SAP](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) como fonte com particionamento de dados habilitado) para armazenamentos que são baseados em arquivo, o serviço de movimentação de dados ignora a propriedade **parallelCopies** . Mesmo que o paralelismo é especificado, não é aplicado neste caso.
* A propriedade **parallelCopies** é ortogonal a **dataIntegrationUnits**. O primeiro é contado em todas as unidades de integração de dados.
* Ao especificar um valor para a propriedade **parallelCopies** , considere o aumento de carga nos armazenamentos de dados de origem e do coletor. Considere também o aumento de carga para o tempo de execução de integração auto-hospedado se a atividade de cópia for habilitada por ti, por exemplo, para cópia híbrida. Esse aumento de carga ocorre especialmente quando você tem várias atividades ou execuções simultâneas das mesmas atividades executadas no mesmo armazenamento de dados. Se você observar que o armazenamento de dados ou o tempo de execução de integração auto-hospedado está sobrecarregado com a carga, diminua o valor de **parallelCopies** para aliviar a carga.

## <a name="staged-copy"></a>Cópia faseada

Quando copia dados de um arquivo de dados de origem para um arquivo de dados de sink, pode optar por utilizar o armazenamento de BLOBs como um armazenamento de teste provisório. Teste é especialmente útil nos seguintes casos:

- **Você deseja ingerir dados de vários armazenamentos de dados para SQL Data Warehouse por meio do polybase.** O SQL Data Warehouse utiliza o PolyBase como um mecanismo de alto débito para carregar uma grande quantidade de dados para o SQL Data Warehouse. Os dados de origem devem estar no armazenamento de BLOBs ou Azure Data Lake Store e devem atender a critérios adicionais. Quando carrega dados de um arquivo de dados que não seja o armazenamento de BLOBs ou do Azure Data Lake Store, pode ativar copiar por meio de armazenamento de BLOBs de teste provisório de dados. Nesse caso, Azure Data Factory executa as transformações de dados necessárias para garantir que ele atenda aos requisitos do polybase. Em seguida, ele utiliza o PolyBase para carregar dados para o SQL Data Warehouse com eficiência. Para obter mais informações, consulte [utilizar o PolyBase para carregar dados para o Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Às vezes, demora um pouco para executar uma movimentação de dados híbrido (ou seja, copiar de um armazenamento de dados local para um armazenamento de dados de nuvem) em uma conexão de rede lenta.** Para melhorar o desempenho, você pode usar cópia em etapas para compactar os dados locais, de modo que leve menos tempo para mover dados para o armazenamento de dados de preparo na nuvem. Em seguida, você pode descompactar os dados no armazenamento de preparo antes de carregar no armazenamento de dados de destino.
- **Você não quer abrir portas que não sejam a porta 80 e a porta 443 em seu firewall devido a políticas corporativas de ti.** Por exemplo, quando copia dados de um arquivo de dados no local para um sink de base de dados do Azure SQL ou um coletor do Azure SQL Data Warehouse, terá de ativar a comunicação de saída de TCP na porta 1433 para a firewall do Windows e sua firewall empresarial. Nesse cenário, a cópia preparada pode aproveitar o tempo de execução de integração auto-hospedado para primeiro copiar dados para uma instância de preparo de armazenamento de BLOBs por HTTP ou HTTPS na porta 443. Em seguida, ele pode carregar os dados no banco de dados SQL ou SQL Data Warehouse do preparo do armazenamento de BLOBs. Neste fluxo, não terá de ativar a porta 1433.

### <a name="how-staged-copy-works"></a>Funciona como faseada de cópia

Quando ativar a funcionalidade de teste, primeiro os dados são copiados do arquivo de dados de origem para o armazenamento de BLOBs de teste (traga a sua própria). Em seguida, os dados são copiados do arquivo de dados de teste para o arquivo de dados de sink. Azure Data Factory gerencia automaticamente o fluxo de duas etapas para você. Azure Data Factory também limpa dados temporários do armazenamento de preparo após a conclusão da movimentação de dados.

![Cópia faseada](media/copy-activity-performance/staged-copy.png)

Ao ativar a movimentação de dados usando um armazenamento de preparo, você pode especificar se deseja que os dados sejam compactados antes de mover os dados do armazenamento de dados de origem para um armazenamento de dados provisório ou de preparo e, em seguida, descompactados antes de mover dados de um dat provisório ou de preparo uma loja para o armazenamento de dados do coletor.

No momento, não é possível copiar dados entre dois armazenamentos de dados que estão conectados por meio do IRs hospedado internamente diferente, nem com nem sem cópia preparada. Para esse cenário, você pode configurar duas atividades de cópia encadeadas explicitamente para copiar da origem para o preparo e, em seguida, do preparo para o coletor.

### <a name="configuration"></a>Configuração

Defina a configuração **enableStaging** na atividade de cópia para especificar se deseja que os dados sejam preparados no armazenamento de BLOBs antes de carregá-los em um armazenamento de dados de destino. Ao definir **enableStaging** como `TRUE`, especifique as propriedades adicionais listadas na tabela a seguir. Você também precisa criar um armazenamento do Azure ou um serviço vinculado à assinatura de acesso compartilhado de armazenamento para preparação, se você não tiver um.

| Propriedade | Descrição | Valor predefinido | Requerido |
| --- | --- | --- | --- |
| enableStaging |Especifique se pretende copiar os dados por meio de um arquivo de teste de interim. |Falso |Não |
| linkedServiceName |Especifique o nome de um [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) serviço, que se refere à instância do armazenamento que utilizar como um armazenamento de teste provisório ligado. <br/><br/> Você não pode usar o armazenamento com uma assinatura de acesso compartilhado para carregar dados em SQL Data Warehouse por meio do polybase. Pode usá-lo em todos os outros cenários. |N/A |Sim, quando **enableStaging** está definido como TRUE |
| path |Especifique o caminho de armazenamento de BLOBs para conter os dados em etapas. Se você não fornecer um caminho, o serviço criará um contêiner para armazenar dados temporários. <br/><br/> Especifique um caminho de apenas se utilizar o armazenamento com uma assinatura de acesso partilhado, ou se necessitar de temporários de dados numa localização específica. |N/A |Não |
| enableCompression |Especifica se os dados devem ser compactados antes de serem copiados para o destino. Esta definição reduz o volume de dados a serem transferidos. |Falso |Não |

>[!NOTE]
> Se você usar cópia em etapas com compactação habilitada, a entidade de serviço ou a autenticação MSI para o serviço vinculado de blob de preparo não terá suporte.

Aqui está uma definição de exemplo de uma atividade de cópia com as propriedades descritas na tabela anterior:

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

### <a name="staged-copy-billing-impact"></a>Impacto de faturação de cópia faseada

Você é cobrado com base em duas etapas: copiar duração e copiar tipo.

* Quando você usa o preparo durante uma cópia de nuvem, que está copiando dados de um armazenamento de dados de nuvem para outro armazenamento de dados de nuvem, ambos os estágios capacitados pelo tempo de execução de integração do Azure, você é cobrado pela [soma da duração da cópia para a etapa 1 e etapa 2] x [preço unitário da cópia de nuvem].
* Quando você usa o preparo durante uma cópia híbrida, que está copiando dados de um armazenamento de dados local para um armazenamento de dados de nuvem, um estágio capacitado por um tempo de execução de integração auto-hospedado, você é cobrado pelo [duração da cópia híbrida] x [preço unitário da cópia híbrida] + [duração da cópia da nuvem] x [preço unitário da cópia de nuvem].

## <a name="performance-tuning-steps"></a>Etapas de ajuste de desempenho

Siga estas etapas para ajustar o desempenho do seu serviço de Azure Data Factory com a atividade de cópia.

1. **Estabelecer uma linha de base.** Durante a fase de desenvolvimento, teste seu pipeline usando a atividade de cópia em um exemplo de dados representativos. Coletar detalhes de execução e características de desempenho após o [monitoramento da atividade de cópia](copy-activity-overview.md#monitoring).

2. **Diagnostique e otimize o desempenho.** Se o desempenho que você observar não atender às suas expectativas, identifique gargalos de desempenho. Em seguida, a otimizar o desempenho para remover ou reduzir o efeito de afunilamentos.

    Em alguns casos, ao executar uma atividade de cópia no Azure Data Factory, você verá uma mensagem "dicas de ajuste de desempenho" na parte superior da [página de monitoramento da atividade de cópia](copy-activity-overview.md#monitor-visually), conforme mostrado no exemplo a seguir. A mensagem informa o afunilamento que foi identificado para a execução de cópia fornecida. Ele também orienta você sobre o que mudar para impulsionar a produtividade da cópia. As dicas de ajuste de desempenho atualmente fornecem sugestões como:

    - Use o polybase ao copiar dados para o Azure SQL Data Warehouse.
    - Aumente Azure Cosmos DB unidades de solicitação ou DTUs do banco de dados SQL do Azure (unidades de produtividade do banco de dado) quando o recurso no lado do repositório de armazenamento for o afunilamento.
    - Remova a cópia preparada desnecessária.

    As regras de ajuste de desempenho também serão aprimoradas gradualmente.

    **Exemplo: Copiar no banco de dados SQL do Azure com dicas de ajuste de desempenho**

    Neste exemplo, durante uma execução de cópia, Azure Data Factory observa que o banco de dados SQL do Azure do coletor atinge alta utilização de DTU, o que reduz as operações de gravação. A sugestão é aumentar a camada do banco de dados SQL do Azure com mais DTUs. 

    ![Copiar monitoramento com dicas de ajuste de desempenho](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    Além disso, estas são algumas considerações comuns. Uma descrição completa do diagnóstico de desempenho está além do escopo deste artigo.

   * Recursos de desempenho:
     * [Cópia paralela](#parallel-copy)
     * [Unidades de Integração de Dados](#data-integration-units)
     * [Cópia faseada](#staged-copy)
     * [Escalabilidade do tempo de execução de integração auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Runtime de integração autoalojado](#considerations-for-self-hosted-integration-runtime)
   * [Origem](#considerations-for-the-source)
   * [sink](#considerations-for-the-sink)
   * [Serialização e desserialização](#considerations-for-serialization-and-deserialization)
   * [Compressão](#considerations-for-compression)
   * [Mapeamento de colunas](#considerations-for-column-mapping)
   * [Outras considerações](#other-considerations)

3. **Expanda a configuração para todo o conjunto de seus conjuntos de seus.** Quando estiver satisfeito com os resultados e o desempenho da execução, você poderá expandir a definição e o pipeline para abranger todo o seu conjunto de todos.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Considerações sobre o tempo de execução de integração auto-hospedado

Se a atividade de cópia for executada em um tempo de execução de integração auto-hospedado, observe o seguinte:

**Instalação**: Recomendamos que você use um computador dedicado para hospedar o Integration Runtime. Consulte [considerações para usar o tempo de execução de integração auto-hospedado](concepts-integration-runtime.md).

**Escalar horizontalmente**: Um único tempo de execução de integração lógico auto-hospedado com um ou mais nós pode atender a várias execuções de atividade de cópia ao mesmo tempo simultaneamente. Se você tiver muita necessidade de movimentação de dados híbrido com um grande número de execuções simultâneas da atividade de cópia ou com um grande volume de dados a serem copiados, considere [expandir o tempo de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md#high-availability-and-scalability) para provisionar mais recursos para capacitar a cópia.

## <a name="considerations-for-the-source"></a>Considerações para a origem

### <a name="general"></a>Geral

Certifique-se de que o armazenamento de dados subjacente não esteja sobrecarregado por outras cargas de trabalho em execução ou contra ele.

Para armazenamentos de dados da Microsoft, consulte [Tópicos de monitoramento e ajuste](#performance-reference) específicos para armazenamentos de dados. Estes tópicos podem ajudá-lo a compreender as características de desempenho do armazenamento de dados e como minimizar os tempos de resposta e maximizar o débito.

* Se você copiar dados do armazenamento de BLOBs para SQL Data Warehouse, considere o uso do polybase para impulsionar o desempenho. Para obter mais informações, consulte [utilizar o PolyBase para carregar dados para o Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Se você copiar dados do HDFS para o armazenamento de BLOBs do Azure ou Azure Data Lake Store, considere o uso de DistCp para aumentar o desempenho. Para obter mais informações, consulte [usar DistCp para copiar dados do HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Se você copiar dados do redshift para o Azure SQL Data Warehouse, armazenamento de BLobs do Azure ou Azure Data Lake Store, considere o uso de UNLOAD para impulsionar o desempenho. Para obter mais informações, consulte [usar Unload para copiar dados do Amazon redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Arquivos de dados baseados em ficheiros

* **Tamanho médio do arquivo e contagem de arquivos**: A atividade de cópia transfere dados de um arquivo por vez. Com a mesma quantidade de dados para ser movida, o débito global é inferior se os dados consistam em muitos arquivos pequenos em vez de poucos arquivos grandes devido a fase de arranque de configuração para cada ficheiro. Se possível, combine arquivos pequenos em arquivos maiores para obter uma taxa de transferência mais alta.
* **Formato e compactação de arquivo**: Para obter mais maneiras de melhorar o desempenho, consulte as seções [Considerações sobre serialização e](#considerations-for-serialization-and-deserialization) desserialização e [considerações para compactação](#considerations-for-compression) .

### <a name="relational-data-stores"></a>Arquivos de dados relacionais

* **Padrão de dados**: A esquema da tabela afeta o débito de cópia. Um tamanho de linha grande oferece um desempenho melhor do que um pequeno tamanho de linha para copiar a mesma quantidade de dados. O motivo é que a base de dados com mais eficiência pode obter menos lotes de dados que contêm menos linhas.
* **Consulta ou procedimento armazenado**: Otimize a lógica da consulta ou procedimento armazenado que você especificar na origem da atividade de cópia para buscar dados com mais eficiência.

## <a name="considerations-for-the-sink"></a>Considerações para o sink

### <a name="general"></a>Geral

Certifique-se de que o armazenamento de dados subjacente não esteja sobrecarregado por outras cargas de trabalho em execução ou contra ele.

Para armazenamentos de dados da Microsoft, consulte [Tópicos de monitoramento e ajuste](#performance-reference) específicos para armazenamentos de dados. Estes tópicos podem ajudá-lo a compreender as características de desempenho do armazenamento de dados e como minimizar os tempos de resposta e maximizar o débito.

* Se você copiar dados de qualquer armazenamento de dados para SQL Data Warehouse do Azure, considere usar o polybase para impulsionar o desempenho. Para obter mais informações, consulte [utilizar o PolyBase para carregar dados para o Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Se você copiar dados do HDFS para o armazenamento de BLOBs do Azure ou Azure Data Lake Store, considere o uso de DistCp para aumentar o desempenho. Para obter mais informações, consulte [usar DistCp para copiar dados do HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Se você copiar dados do redshift para o Azure SQL Data Warehouse, armazenamento de BLOBs do Azure ou Azure Data Lake Store, considere o uso de UNLOAD para impulsionar o desempenho. Para obter mais informações, consulte [usar Unload para copiar dados do Amazon redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Arquivos de dados baseados em ficheiros

* **Copie o comportamento**: Se você copiar dados de um armazenamento de dados baseado em arquivo diferente, a atividade de cópia terá três opções por meio da propriedade **copyBehavior** . Preserva a hierarquia, nivela hierarquia ou intercala ficheiros. A preservação ou mesclar a hierarquia tem pouca ou nenhuma sobrecarga de desempenho, mas mesclar arquivos faz com que a sobrecarga de desempenho aumentar.
* **Formato e compactação de arquivo**: Para obter mais maneiras de melhorar o desempenho, consulte as seções [Considerações sobre serialização e](#considerations-for-serialization-and-deserialization) desserialização e [considerações para compactação](#considerations-for-compression) .

### <a name="relational-data-stores"></a>Arquivos de dados relacionais

* **Comportamento de cópia e implicação de desempenho**: Há diferentes maneiras de gravar dados em um coletor SQL. Saiba mais na [prática recomendada para carregar dados no banco de dado SQL do Azure](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database).

* **Tamanho de dados padrão e batch**:
  * A esquema da tabela afeta o débito de cópia. Para copiar a mesma quantidade de dados, um tamanho de linha grandes fornece desempenho melhor do que um tamanho de linha de pequenos porque a base de dados com mais eficiência pode comprometer-se menos lotes de dados.
  * A atividade de cópia insere dados em uma série de lotes. Pode definir o número de linhas num batch ao utilizar o **writeBatchSize** propriedade. Se os dados tiverem linhas pequenas, pode definir o **writeBatchSize** propriedade com um valor mais alto para beneficiar de sobrecarga de lote menor e um débito mais elevado. Se o tamanho de linha dos seus dados é grande, tenha cuidado quando aumenta **writeBatchSize**. Um valor elevado pode levar a uma falha de cópia causada pela sobrecarga da base de dados.

### <a name="nosql-stores"></a>Arquivos NoSQL

* Para **armazenamento de tabelas**:
  * **Partição**: Gravar dados em partições intercaladas degrada drasticamente o desempenho. Classifique os dados de origem por chave de partição para que os dados sejam inseridos com eficiência em uma partição após a outra. Ou, você pode ajustar a lógica para gravar os dados em uma única partição.

## <a name="considerations-for-serialization-and-deserialization"></a>Considerações sobre a serialização e desserialização

A serialização e a desserialização podem ocorrer quando seu conjunto de dados de entrada ou de saída é um arquivo. Para obter mais informações sobre formatos de arquivo com suporte por atividade de cópia, consulte [formatos de arquivo e compactação com suporte](supported-file-formats-and-compression-codecs.md).

**Copie o comportamento**:

* Copiar ficheiros entre arquivos de dados de ficheiros:
  * Quando os conjuntos de dados de entrada e saída têm as mesmas ou nenhuma configuração de formato de arquivo, o serviço de movimentação de dados executa uma *cópia binária* sem nenhuma serialização ou desserialização. Ver um débito mais elevado, em comparação com o cenário, em que as definições de formato de ficheiro de origem e sink são diferentes entre si.
  * Quando os conjuntos de dados de entrada e saída estiverem no formato de texto e apenas o tipo de codificação for diferente, o serviço de movimentação de dados apenas fará a conversão de codificação. Ele não faz qualquer serialização e desserialização, o que faz com que alguma sobrecarga em comparação comparado uma cópia binária de desempenho.
  * Quando os conjuntos de dados de entrada e saída têm diferentes formatos de arquivo ou configurações diferentes, como delimitadores, o serviço de movimentação de dados desserializa os dados de origem para transmitir, transformar e, em seguida, serializa-os no formato de saída que você indicou. Esta operação resulta num desempenho muito mais significativo sobrecarga em comparação comparado outros cenários.
* Quando você copia arquivos de ou para um repositório de dados que não é baseado em arquivo, por exemplo, de um armazenamento baseado em arquivo para um relational store, a etapa de serialização ou desserialização é necessária. Este passo resulta em sobrecarga de desempenho significativo.

**Formato de arquivo**: O formato de arquivo escolhido pode afetar o desempenho da cópia. Por exemplo, o Avro é um formato binário compacto que armazena os metadados com dados. Ele tem um amplo suporte no ecossistema do Hadoop para consulta e processamento. O Avro é mais caro para serialização e desserialização, o que resulta em uma taxa de transferência de cópia mais baixa em comparação com o formato de texto. 

Faça sua escolha de formato de ficheiro em todo o fluxo de processamento holística. Comece com:

- Em que formato os dados são armazenados, armazenamentos de dados de origem ou serem extraídos de sistemas externos.
- O melhor formato para armazenamento, processamento analítico e consulta.
- Em que formato os dados devem ser exportados para marts de dados para ferramentas de relatório e visualização.

Por vezes, um formato de ficheiro que é inferior ao ideal para ler e desempenho de escrita pode ser uma boa opção quando considera o processo geral analítico.

## <a name="considerations-for-compression"></a>Considerações para compressão

Quando o conjunto de dados de entrada ou saída é um arquivo, você pode definir a atividade de cópia para executar a compactação ou descompactação enquanto grava dados no destino. Ao escolher compressão, faça uma compensação entre (e/s) de entrada/saída e CPU. Comprimir os custos de dados Extras em recursos de computação. Mas, em troca, reduz e/s de rede e armazenamento. Dependendo de seus dados, você poderá ver um aumento na produtividade geral da cópia.

**Codec**: Cada codec de compactação tem vantagens. Por exemplo, bzip2 tem a menor taxa de transferência de cópia, mas obtém o melhor desempenho das consultas do Hive com bzip2 como pode dividir a para processamento. Gzip é a opção mais equilibrada e é usado com mais frequência. Escolha o codec que melhor se adequa aos seu cenário de ponto-a-ponto.

**Nível**: Você pode escolher entre duas opções para cada codec de compactação: mais rápido compactado e compactado de maneira ideal. A opção compactada mais rápida compacta os dados o mais rápido possível, mesmo que o arquivo resultante não seja compactado de forma ideal. A opção ideal comprimida gasta mais tempo a compactação e gera uma quantidade mínima de dados. Pode testar as duas opções para ver o que fornece um melhor desempenho geral no seu caso.

**Uma consideração**: Para copiar uma grande quantidade de dados entre um repositório local e a nuvem, considere usar a [cópia preparada](#staged-copy) com a compactação habilitada. O uso do armazenamento provisório é útil quando a largura de banda de sua rede corporativa e seus serviços do Azure são o fator limitante, e você deseja que o conjunto de dados de entrada e o DataSet de saída sejam ambos em formato descompactado.

## <a name="considerations-for-column-mapping"></a>Considerações para mapeamento de colunas

Você pode definir a Propriedade ColumnMappings em uma atividade de cópia para mapear todos ou um subconjunto das colunas de entrada para as colunas de saída. Depois do serviço de movimento de dados lê os dados da origem, tem de efetuar o mapeamento de colunas nos dados antes de ele grava os dados para o sink. Este processamento extra reduz a taxa de transferência de cópia.

Se seu arquivo de dados de origem é passível de consulta, por exemplo, se for um arquivo relacional, como a base de dados SQL ou SQL Server, ou se for um arquivo de NoSQL, como o armazenamento de tabelas ou do Azure Cosmos DB, considere enviar a filtragem de coluna e reordenação de lógica para o **consulta** propriedade em vez de usar o mapeamento de colunas. Dessa forma, a projeção ocorre enquanto o serviço de movimentação de dados lê dados do armazenamento de dados de origem, onde é muito mais eficiente.

Saiba mais no [mapeamento de esquema de atividade de cópia](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Outras considerações

Se o tamanho dos dados que você deseja copiar for grande, você poderá ajustar sua lógica de negócios para particionar ainda mais os dados. Você pode agendar a atividade de cópia para ser executada com mais frequência para reduzir o tamanho dos dados para cada atividade de cópia que é executada.

Tenha cuidado com o número de conjuntos de dados e atividades de cópia que exigem Azure Data Factory para se conectar ao mesmo armazenamento de dados ao mesmo tempo. Muitas tarefas de cópia em simultâneo poderão limitar um arquivo de dados e levar a degradação do desempenho, repetições internas do trabalho de cópia e em alguns casos, as falhas de execução.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Cenário de exemplo: Copiar de um SQL Server local para o armazenamento de BLOBs

**Cenário**: Um pipeline é criado para copiar dados de um SQL Server local para o armazenamento de BLOBs no formato CSV. Para tornar a tarefa de cópia com mais rapidez, os arquivos CSV devem ser comprimidos no formato de bzip2.

**Teste e análise**: A taxa de transferência da atividade de cópia é menor que 2 MBps, o que é muito mais lento do que o parâmetro de comparação de desempenho.

**Análise e ajuste de desempenho**: Para solucionar o problema de desempenho, vejamos como os dados são processados e movidos.

- **Ler dados**: O Integration Runtime abre uma conexão para SQL Server e envia a consulta. SQL Server responde enviando o fluxo de dados para o Integration Runtime por meio da intranet.
- **Serializar e compactar dados**: O Integration Runtime serializa o fluxo de dados para o formato CSV e compacta os dados em um fluxo bzip2.
- **Gravar dados**: O Integration Runtime carrega o fluxo do bzip2 no armazenamento de BLOBs pela Internet.

Como você pode ver, os dados são processados e movidos de forma sequencial de streaming: SQL Server > LAN > Integration Runtime > WAN > armazenamento de BLOBs. O desempenho geral é restringido pela taxa de transferência mínima no pipeline.

![Fluxo de dados](./media/copy-activity-performance/case-study-pic-1.png)

Um ou mais dos seguintes fatores podem fazer com que o afunilamento do desempenho:

* **Origem**: SQL Server tem baixa taxa de transferência devido a cargas pesadas.
* **Tempo de execução de integração auto-hospedado**:
  * **LAN**: O Integration Runtime está localizado longe da máquina SQL Server e tem uma conexão de baixa largura de banda.
  * **Tempo de execução de integração**: O Integration Runtime atingiu suas limitações de carregamento para executar as seguintes operações:
    * **Serialização**: A serialização do fluxo de dados para o formato CSV tem uma taxa de transferência lenta.
    * **Compactação**: Você escolheu um codec de compactação lenta, por exemplo, BZIP2, que é de 2,8 MBps com Core i7.
  * **WAN**: A largura de banda entre a rede corporativa e os serviços do Azure é baixa, por exemplo, T1 = 1.544 kbps; T2 = 6.312 kbps.
* **Coletor**: O armazenamento de BLOBs tem baixa taxa de transferência. Esse cenário é improvável porque seu SLA (contrato de nível de serviço) garante um mínimo de 60 MBps.

Neste caso, bzip2 compressão de dados pode ser mais lento todo o pipeline. Mudar para um codec de compressão de gzip poderá facilitar este congestionamento.

## <a name="references"></a>Referências

Aqui estão as referências de monitoramento e ajuste de desempenho para alguns dos armazenamentos de dados com suporte:

* Armazenamento do Azure, que inclui armazenamento de BLOBs e armazenamento de tabelas: [Destinos de escalabilidade de armazenamento do Azure](../storage/common/storage-scalability-targets.md) e [lista de verificação de desempenho e escalabilidade do armazenamento do Azure](../storage/common/storage-performance-checklist.md)
* Base de Dados SQL do Azure: Você pode [monitorar o desempenho](../sql-database/sql-database-single-database-monitor.md) e verificar a porcentagem de DTU (unidade de transação do banco de dados).
* SQL Data Warehouse do Azure: Seu recurso é medido em DWUs (unidades de data warehouse). Consulte [gerenciar poder de computação no Azure SQL data warehouse (visão geral)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* o Azure Cosmos DB: [Níveis de desempenho no Azure Cosmos DB](../cosmos-db/performance-levels.md).
* SQL Server local: [Monitore e ajuste o desempenho](https://msdn.microsoft.com/library/ms189081.aspx).
* Servidor de arquivos local: [Ajuste de desempenho para servidores de arquivos](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos de atividade de cópia:

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Mapeamento de esquema de atividade de cópia](copy-activity-schema-and-type-mapping.md)
- [Copie a tolerância a falhas de atividade](copy-activity-fault-tolerance.md)
