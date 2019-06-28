---
title: Copie o guia de sintonização de desempenho de atividade e no Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre os principais fatores que afetam o desempenho de movimento de dados no Azure Data Factory, quando utiliza a atividade de cópia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: jingwang
ms.openlocfilehash: 3ea89e9f6a6bb8a4c377c70bbe1b5540d3b74d44
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341254"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Copie o guia de Otimização e desempenho de atividade
> [!div class="op_single_selector" title1="Selecione a versão do Azure Data Factory, que está a utilizar:"]
> * [Versão 1](v1/data-factory-copy-activity-performance.md)
> * [Versão atual](copy-activity-performance.md)


A atividade de cópia de Azure Data Factory proporciona um primeira classe seguros, fiáveis e elevado desempenho de carregamento de dados solução. Pode usá-lo para cópia dezenas de terabytes de dados todos os dias numa variedade de avançados dos arquivos de dados na cloud e no local. Desempenho de carregamento de dados rápida é fundamental para garantir que possa se concentrar no problema de grandes volumes de dados principal: desenvolvimento de soluções de análise avançada e obtenção de informações profunda de todos esses dados.

O Azure fornece um conjunto de nível empresarial, soluções de armazém de dados e armazenamento de dados. A atividade de cópia oferece um experiência fácil de configurar e configurar o carregamento de dados altamente otimizados. Com uma atividade de cópia única, pode carregar dados para:

* Azure SQL Data Warehouse a 1,2 GBps.
* Armazenamento de Blobs do Azure a 1,0 GBps.
* Azure o Data Lake Store a 1,0 GBps.

Este artigo descreve:

* [Números de referência do desempenho](#performance-reference) para arquivos de dados de origem e sink para ajudar a planear o seu projeto de suportados.
* Funcionalidades que podem aumentar o débito de cópia em cenários diferentes, que inclui [unidades de integração de dados](#data-integration-units) (DIUs), [cópia em paralelo](#parallel-copy), e [cópia faseada](#staged-copy).
* [Guia de sintonização de desempenho](#performance-tuning-steps) sobre como otimizar o desempenho e os principais fatores que podem afetar o desempenho de cópia.

> [!NOTE]
> Se não estiver familiarizado com a atividade de cópia em geral, consulte a [copiar descrição geral da atividade](copy-activity-overview.md) antes de ler este artigo.
>

## <a name="performance-reference"></a>Referência de desempenho

Como uma referência, a tabela seguinte mostra o número do débito de cópia em MBps para a origem específica e pares de sink numa atividade de cópia única executar com base em testes internos. Para comparação, ele também demonstra como diferentes configurações de [unidades de integração de dados](#data-integration-units) ou [escalabilidade do runtime de integração de autoalojado](concepts-integration-runtime.md#self-hosted-integration-runtime) (vários nós) podem ajudar no desempenho de cópia.

![Matriz de desempenho](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> Quando a atividade de cópia é executada num runtime de integração do Azure, as unidades de integração de dados permitido mínimo (anteriormente conhecido como unidades de movimento de dados) é dois. Se não for especificado, consulte as unidades de integração de dados predefinido a ser utilizado numa [unidades de integração de dados](#data-integration-units).

**Pontos a serem observados:**

* Débito é calculado utilizando a seguinte fórmula: [ler tamanho dos dados de origem] / [duração da execução de atividade de cópia].
* Os números de referência de desempenho na tabela foram medidos utilizando um [TPC-H](http://www.tpc.org/tpch/) conjunto de dados numa execução de atividade de cópia única. Ficheiros de teste para arquivos baseados em ficheiros são vários ficheiros com 10 GB de tamanho.
* Nos arquivos de dados do Azure, a origem e sink são na mesma região do Azure.
* Para obter uma cópia entre no local e na cloud híbrida armazenamentos de dados, cada nó do runtime de integração autoalojado estava em execução num computador que estava separado do arquivo de dados com a especificação da seguinte. Quando uma única atividade estiver em execução, a operação de cópia consumido apenas uma pequena parte de CPU, memória ou largura de banda de rede da máquina de teste.
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
        <td>Interface de Internet: 10 Gbps; interface de intranet: 40 Gbps</td>
    </tr>
    </table>


> [!TIP]
> Pode alcançar um débito mais elevado, utilizando DIUs mais. Por exemplo, com 100 DIUs, pode copiar dados do armazenamento de Blobs do Azure para o Azure Data Lake Store a 1,0 GBps. Para obter mais informações sobre esta funcionalidade e o cenário suportado, consulte a [unidades de integração de dados](#data-integration-units) secção. 

## <a name="data-integration-units"></a>Unidades de integração de dados

Uma unidade de integração de dados é uma medida que representa o poder (uma combinação de CPU, memória e alocação de recursos de rede) de uma unidade única no Azure Data Factory. Unidade de integração de dados só se aplica a [runtime de integração do Azure](concepts-integration-runtime.md#azure-integration-runtime), mas não [integration runtime autoalojado](concepts-integration-runtime.md#self-hosted-integration-runtime).

Os mínimo DIUs para capacitar uma execução de atividade de cópia é dois. Se não for especificado, a tabela seguinte lista os DIUs padrão usados em cenários de cópia diferentes:

| Cenário de cópia | DIUs predefinido determinados pelo serviço |
|:--- |:--- |
| Copiar dados entre arquivos baseados em ficheiros | Entre 4 e 32 dependendo do número e tamanho dos ficheiros |
| Todos os outros cenários de cópia | 4 |

Para substituir esta predefinição, especifique um valor para o **dataIntegrationUnits** propriedade da seguinte forma. O *valores permitidos* para o **dataIntegrationUnits** propriedade é até 256. O *número real de DIUs* que a operação de cópia utiliza no tempo de execução é igual ou inferior ao valor configurado, dependendo do padrão de dados. Para obter informações sobre o nível de ganho de desempenho poderá obter ao configurar mais unidades para uma origem de cópia específico e de sink, consulte a [referência de desempenho](#performance-reference).

Pode ver os DIUs utilizados para cada cópia são executada na saída da atividade de cópia quando monitorar uma atividade executar. Para obter mais informações, consulte [copiar a monitorização da atividade](copy-activity-overview.md#monitoring).

> [!NOTE]
> Definição de DIUs maiores do que quatro atualmente aplica-se apenas quando copia vários ficheiros de armazenamento do Azure, armazenamento do Azure Data Lake, Amazon S3, Google Cloud Storage, cloud FTP ou SFTP da cloud para quaisquer outros arquivos de dados na cloud.
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

Lembre-se de que é cobrado com base no tempo total da operação de cópia. A duração total a faturação para movimento de dados é a soma da duração em DIUs. Se uma tarefa de cópia costumava levar uma hora com duas unidades de cloud e agora demora 15 minutos com oito unidades de cloud, a fatura global permanece praticamente o mesmo.

## <a name="parallel-copy"></a>Cópia paralela

Pode utilizar o **parallelCopies** propriedade para indicar o paralelismo que pretende que a atividade de cópia para utilizar. Pode pensar esta propriedade como o número máximo de threads dentro da atividade de cópia que pode ler da origem de ou escrever seus arquivos de dados de sink em paralelo.

Para cada atividade de cópia executar, o Azure Data Factory determina o número de cópias paralelas para utilizar para copiar dados a partir da origem de arquivo de dados e para os dados de destino armazenam. O número predefinido de cópias paralelos que utiliza depende do tipo de origem e sink, que utiliza.

| Cenário de cópia | Número de cópias paralela de predefinido determinado pelo serviço |
| --- | --- |
| Copiar dados entre arquivos baseados em ficheiros |Depende do tamanho dos ficheiros e o número de DIUs usado para copiar dados entre dois arquivos de dados em nuvem, ou a configuração física do computador runtime de integração autoalojado. |
| Copiar dados de qualquer arquivo de dados de origem para o armazenamento de tabelas do Azure |4 |
| Todos os outros cenários de cópia |1 |

> [!TIP]
> Quando copiar dados entre arquivos baseados em ficheiros, o comportamento padrão normalmente dá-lhe o melhor débito. O comportamento padrão é determinado por automática.

Para controlar a carga nos computadores que alojam os dados armazena, ou para otimizar o desempenho de cópia, pode substituir o valor predefinido e especifique um valor para o **parallelCopies** propriedade. O valor tem de ser um número inteiro maior que ou igual a 1. No momento de execução para o melhor desempenho, a atividade de cópia utiliza um valor que é menor ou igual ao valor que definir.

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

* Quando copiar dados entre arquivos baseados em ficheiros, **parallelCopies** determina o paralelismo em nível de arquivo. A segmentação dentro de um único arquivo acontece por baixo automática e transparente. Foi concebido para utilizar melhor segmento adequado tamanho para um tipo de arquivo de dados de origem específica carregar dados em paralelo e ortogonal à **parallelCopies**. O número real de cópias paralelas utiliza o serviço de movimento de dados para a operação de cópia em tempo de execução é não mais do que o número de ficheiros que tiver. Se for o comportamento de cópia **mergeFile**, a atividade de cópia não é possível tirar proveito do paralelismo de nível de arquivo.
* Se especificar um valor para o **parallelCopies** propriedade, considere o aumento de carga na sua origem e arquivos de dados de sink. Também deve considere o aumento de carga para o runtime de integração autoalojado se a atividade de cópia é capacitada pelo mesmo, por exemplo, para obter uma cópia híbrida. Esse aumento de carga acontece especialmente quando tem várias atividades ou execuções simultâneas de iguais às atividades que são executados em relação ao mesmo armazenamento de dados. Se notar que o arquivo de dados ou o runtime de integração autoalojado é sobrecarregado com a carga, diminuir os **parallelCopies** valor de modo a poupar a carga.
* Quando copia dados a partir de arquivos que não são baseados em ficheiros para arquivos que são baseados em ficheiros, o serviço de movimento de dados ignora a **parallelCopies** propriedade. Mesmo que o paralelismo é especificado, não é aplicado neste caso.
* O **parallelCopies** propriedade é ortogonal ao **dataIntegrationUnits**. O primeiro é contado em todas as unidades de integração de dados.

## <a name="staged-copy"></a>Cópia faseada

Quando copia dados de um arquivo de dados de origem para um arquivo de dados de sink, pode optar por utilizar o armazenamento de BLOBs como um armazenamento de teste provisório. Teste é especialmente útil nos seguintes casos:

- **Pretende ingerir dados de vários arquivos de dados no SQL Data Warehouse através do PolyBase.** O SQL Data Warehouse utiliza o PolyBase como um mecanismo de alto débito para carregar uma grande quantidade de dados para o SQL Data Warehouse. A origem de dados tem de estar no armazenamento de BLOBs ou do Azure Data Lake Store e tem de cumprir critérios adicionais. Quando carrega dados de um arquivo de dados que não seja o armazenamento de BLOBs ou do Azure Data Lake Store, pode ativar copiar por meio de armazenamento de BLOBs de teste provisório de dados. Nesse caso, o Azure Data Factory realiza as transformações de dados necessários para assegurar que cumpre os requisitos do PolyBase. Em seguida, ele utiliza o PolyBase para carregar dados para o SQL Data Warehouse com eficiência. Para obter mais informações, consulte [utilizar o PolyBase para carregar dados para o Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Por vezes, demora algum tempo para efetuar um movimento de dados híbrida (ou seja, para copiar de uma local do arquivo de dados ao arquivo de dados na cloud) através de uma ligação de rede lenta.** Para melhorar o desempenho, pode utilizar a cópia faseada para comprimir os dados no local para que ele demora menos tempo para mover dados para o arquivo de dados de teste na cloud. Em seguida, pode descomprimir os dados no arquivo de teste antes de os carregar para o arquivo de dados de destino.
- **Não quer abrir portas sem ser a porta 80 e a porta 443 na sua firewall devido a políticas de TI empresariais.** Por exemplo, quando copia dados de um arquivo de dados no local para um sink de base de dados do Azure SQL ou um coletor do Azure SQL Data Warehouse, terá de ativar a comunicação de saída de TCP na porta 1433 para a firewall do Windows e sua firewall empresarial. Neste cenário, cópia faseada pode aproveitar o runtime de integração autoalojado para primeiro copiar dados para um armazenamento de BLOBs a instância de teste através de HTTP ou HTTPS na porta 443. Em seguida, ele pode carregar os dados na base de dados SQL ou SQL Data Warehouse do teste de armazenamento de Blobs. Neste fluxo, não terá de ativar a porta 1433.

### <a name="how-staged-copy-works"></a>Funciona como faseada de cópia

Quando ativar a funcionalidade de teste, primeiro os dados são copiados do arquivo de dados de origem para o armazenamento de BLOBs de teste (traga a sua própria). Em seguida, os dados são copiados do arquivo de dados de teste para o arquivo de dados de sink. O Azure Data Factory gere automaticamente o fluxo de duas etapas para. O Azure Data Factory também limpa dados temporários, o armazenamento de teste do após o movimento de dados está concluído.

![Cópia faseada](media/copy-activity-performance/staged-copy.png)

Ao ativar o movimento de dados usando um arquivo de teste, pode especificar se pretende que os dados para ser comprimidos antes de passar dados da origem de dados armazenam para um interim ou arquivo de dados de testes e, em seguida, descompactado antes de mover dados de um intermediário ou teste de dados um arquivo para o arquivo de dados de sink.

Atualmente, não é possível copiar dados entre dois arquivos de dados no local através de um arquivo de transição.

### <a name="configuration"></a>Configuração

Configurar o **enableStaging** definir na atividade de cópia e especifique se pretende que os dados para ser preparado no armazenamento de BLOBs, antes de carregá-lo num arquivo de dados de destino. Se definir **enableStaging** para `TRUE`, especifique as propriedades adicionais listadas na tabela seguinte. Terá também de criar um armazenamento do Azure ou o armazenamento partilhado serviço ligado de assinatura de acesso para testar se não tiver uma.

| Propriedade | Descrição | Valor predefinido | Necessário |
| --- | --- | --- | --- |
| enableStaging |Especifique se pretende copiar os dados por meio de um arquivo de teste de interim. |Falso |Não |
| linkedServiceName |Especifique o nome de um [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) serviço, que se refere à instância do armazenamento que utilizar como um armazenamento de teste provisório ligado. <br/><br/> Não é possível utilizar o armazenamento com uma assinatura de acesso partilhado para carregar dados para o SQL Data Warehouse através do PolyBase. Pode usá-lo em todos os outros cenários. |N/A |Sim, quando **enableStaging** está definido como TRUE |
| caminho |Especifique o caminho de armazenamento de BLOBs para conter os dados em etapas. Se não fornecer um caminho, o serviço cria um contentor para armazenar dados temporários. <br/><br/> Especifique um caminho de apenas se utilizar o armazenamento com uma assinatura de acesso partilhado, ou se necessitar de temporários de dados numa localização específica. |N/A |Não |
| enableCompression |Especifica se os dados devem ser comprimidos antes que seja copiado para o destino. Esta definição reduz o volume de dados a serem transferidos. |Falso |Não |

>[!NOTE]
> Se usar a cópia faseada com compressão ativada, o principal de serviço ou autenticação de MSI para testar o serviço ligado do blob não é suportada.

Eis um exemplo de definição de uma atividade de cópia com as propriedades descritas na tabela anterior:

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

Será cobrado com base em dois passos: duração da cópia e copie o tipo.

* Quando utiliza durante uma cópia na cloud, que está a copiar dados de teste de um arquivo de dados na cloud para outro armazenamento de dados na cloud, ambas as fases capacitados pelo tempo de execução de integração do Azure, que lhe é cobrada a [soma de duração de cópia para o passo 1 e o passo 2] x [preço de unidade de cópia de nuvem].
* Quando utiliza o teste durante uma cópia híbrida, que está a copiar dados de um arquivo de dados no local para um arquivo de dados na cloud, uma fase capacitados por um runtime de integração autoalojado, que lhe é cobrada para [duração de cópia híbrida] x [preço da unidade de cópia híbrida] + [duração de cópia da cloud] x [preço de unidade de cópia de nuvem].

## <a name="performance-tuning-steps"></a>Etapas de ajuste de desempenho

Siga estes passos para otimizar o desempenho do seu serviço de Azure Data Factory com a atividade de cópia.

1. **Estabelece uma linha de base.** Durante a fase de desenvolvimento, teste o seu pipeline através da atividade de cópia em relação a uma amostra de dados representativos. Recolher detalhes da execução e características de desempenho a seguir [copiar a monitorização da atividade](copy-activity-overview.md#monitoring).

2. **Diagnosticar e otimizar o desempenho.** Se o desempenho que observar não cumprir as suas expectativas, identificar estrangulamentos de desempenho. Em seguida, a otimizar o desempenho para remover ou reduzir o efeito de afunilamentos.

    Em alguns casos, ao executar uma atividade de cópia no Azure Data Factory, verá uma mensagem "Dicas de ajuste de desempenho" na parte superior dos [página de monitorização de atividade de cópia](copy-activity-overview.md#monitor-visually), conforme mostrado no exemplo a seguir. A mensagem informa o afunilamento que foi identificado para a execução de cópia determinada. Ele também o orienta sobre o que altere para aumento cópia débito. As sugestões de otimização de desempenho atualmente fornecem sugestões, como:

    - Utilize o PolyBase, quando copiar dados para o Azure SQL Data Warehouse.
    - Aumente as unidades de pedido do Azure Cosmos DB ou o Azure SQL da base de dados DTUs (unidades de débito da base de dados) quando o recurso no lado do arquivo de dados é o afunilamento.
    - Remova a cópia faseada desnecessária.

    O desempenho, otimização de regras de gradualmente irá ser enriquecido também.

    **Exemplo: Copie para a base de dados do Azure SQL com sugestões de otimização de desempenho**

    Neste exemplo, durante uma cópia executar, Azure Data Factory avisos o sink de que base de dados do Azure SQL atingir a alta utilização de DTU, que pode atrasar as operações de escrita. A sugestão é aumentar o escalão de base de dados do Azure SQL com mais de DTUs. 

    ![Copie a monitorização com dicas de ajuste de desempenho](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    Além disso, seguem-se algumas considerações comuns. Uma descrição completa do diagnóstico de desempenho está além do escopo deste artigo.

   * Recursos de desempenho:
     * [Cópia paralela](#parallel-copy)
     * [Unidades de Integração de Dados](#data-integration-units)
     * [Cópia faseada](#staged-copy)
     * [Escalabilidade do runtime de integração autoalojado](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Runtime de integração autoalojado](#considerations-for-self-hosted-integration-runtime)
   * [Origem](#considerations-for-the-source)
   * [sink](#considerations-for-the-sink)
   * [Serialização e desserialização](#considerations-for-serialization-and-deserialization)
   * [Compressão](#considerations-for-compression)
   * [Mapeamento de colunas](#considerations-for-column-mapping)
   * [Outras considerações](#other-considerations)

3. **Expanda a configuração para todo o seu conjunto de dados.** Quando estiver satisfeito com os resultados de execução e o desempenho, pode expandir a definição e o pipeline para cobrir todo o seu conjunto de dados.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Considerações para o runtime de integração autoalojado

Se a sua atividade de cópia é executado num Runtime de integração autoalojado, tenha em atenção o seguinte:

**Configuração**: Recomendamos que utilize uma máquina dedicada para o runtime de integração de host. Ver [runtime de integração de autoalojado de considerações sobre a utilização](concepts-integration-runtime.md).

**Aumentar horizontalmente**: Um runtime de integração de autoalojado lógico único com um ou mais nós possa servir várias execuções de atividade de cópia ao mesmo tempo em simultâneo. Caso precise pesada no movimento de dados híbrido com um grande número de execuções de atividade de cópia em simultâneo ou com um grande volume de dados a copiar, considere [aumentar horizontalmente o runtime de integração autoalojado](create-self-hosted-integration-runtime.md#high-availability-and-scalability) para aprovisionar mais recursos para aumentar a produtividade de cópia.

## <a name="considerations-for-the-source"></a>Considerações para a origem

### <a name="general"></a>Geral

Certifique-se de que o arquivo de dados subjacente não está sobrecarregado com outras cargas de trabalho que estejam a executar num ou em relação a ele.

Para arquivos de dados da Microsoft, consulte [monitoramento e ajuste tópicos](#performance-reference) que são específicas para arquivos de dados. Estes tópicos podem ajudá-lo a compreender as características de desempenho do armazenamento de dados e como minimizar os tempos de resposta e maximizar o débito.

* Se copiar dados do armazenamento de BLOBs para o SQL Data Warehouse, considere utilizar o PolyBase para aumentar o desempenho. Para obter mais informações, consulte [utilizar o PolyBase para carregar dados para o Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Se copiar dados do HDFS para armazenamento de Blobs do Azure ou do Azure Data Lake Store, considere utilizar o DistCp para aumentar o desempenho. Para obter mais informações, consulte [utilizar DistCp para copiar dados do HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Se copiar dados de Redshift para o Azure SQL Data Warehouse, o armazenamento de Blobs do Azure ou o Azure Data Lake Store, considere utilizar UNLOAD para aumentar o desempenho. Para obter mais informações, consulte [descarregamento de utilização para copiar dados do Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Arquivos de dados baseados em ficheiros

* **Média de tamanho de ficheiro e a contagem de ficheiros**: A atividade de cópia transfere um ficheiro de dados cada vez. Com a mesma quantidade de dados para ser movida, o débito global é inferior se os dados consistam em muitos arquivos pequenos em vez de poucos arquivos grandes devido a fase de arranque de configuração para cada ficheiro. Se possível, combine ficheiros pequenos em arquivos maiores para obter um débito mais elevado.
* **Ficheiro de formato e compressão**: Para obter mais formas de melhorar o desempenho, consulte a [considerações sobre a serialização e desserialização](#considerations-for-serialization-and-deserialization) e [considerações sobre a compactação](#considerations-for-compression) secções.

### <a name="relational-data-stores"></a>Arquivos de dados relacionais

* **Padrão de dados**: A esquema da tabela afeta o débito de cópia. Um tamanho de linha grandes dá-lhe o melhor desempenho do que um tamanho de linha de pequenos para copiar a mesma quantidade de dados. O motivo é que a base de dados com mais eficiência pode obter menos lotes de dados que contêm menos linhas.
* **Consulta ou procedimento armazenado**: Otimize a lógica da consulta ou procedimento armazenado que especificar na origem de atividade de cópia para obter os dados de forma mais eficiente.

## <a name="considerations-for-the-sink"></a>Considerações para o sink

### <a name="general"></a>Geral

Certifique-se de que o arquivo de dados subjacente não está sobrecarregado com outras cargas de trabalho que estejam a executar num ou em relação a ele.

Para arquivos de dados da Microsoft, consulte [monitoramento e ajuste tópicos](#performance-reference) que são específicas para arquivos de dados. Estes tópicos podem ajudá-lo a compreender as características de desempenho do armazenamento de dados e como minimizar os tempos de resposta e maximizar o débito.

* Se copiar dados de qualquer arquivo de dados para o Azure SQL Data Warehouse, considere utilizar o PolyBase para aumentar o desempenho. Para obter mais informações, consulte [utilizar o PolyBase para carregar dados para o Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Se copiar dados do HDFS para armazenamento de Blobs do Azure ou do Azure Data Lake Store, considere utilizar o DistCp para aumentar o desempenho. Para obter mais informações, consulte [utilizar DistCp para copiar dados do HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Se copiar dados de Redshift para o Azure SQL Data Warehouse, o armazenamento de Blobs do Azure ou o Azure Data Lake Store, considere utilizar UNLOAD para aumentar o desempenho. Para obter mais informações, consulte [descarregamento de utilização para copiar dados do Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Arquivos de dados baseados em ficheiros

* **Copie o comportamento**: Se copiar dados de um arquivo de dados diferentes baseados em ficheiros, a atividade de cópia tem três opções através da **copyBehavior** propriedade. Preserva a hierarquia, nivela hierarquia ou intercala ficheiros. A preservação ou mesclar a hierarquia tem pouca ou nenhuma sobrecarga de desempenho, mas mesclar arquivos faz com que a sobrecarga de desempenho aumentar.
* **Ficheiro de formato e compressão**: Para obter mais formas de melhorar o desempenho, consulte a [considerações sobre a serialização e desserialização](#considerations-for-serialization-and-deserialization) e [considerações sobre a compactação](#considerations-for-compression) secções.

### <a name="relational-data-stores"></a>Arquivos de dados relacionais

* **Copie a implicação de comportamento e desempenho**: Existem formas diferentes para escrever dados num coletor SQL. Saiba mais a partir da [melhor prática para carregar dados para a base de dados do Azure SQL](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database).

* **Tamanho de dados padrão e batch**:
  * A esquema da tabela afeta o débito de cópia. Para copiar a mesma quantidade de dados, um tamanho de linha grandes fornece desempenho melhor do que um tamanho de linha de pequenos porque a base de dados com mais eficiência pode comprometer-se menos lotes de dados.
  * A atividade de cópia insere dados de uma série de lotes. Pode definir o número de linhas num batch ao utilizar o **writeBatchSize** propriedade. Se os dados tiverem linhas pequenas, pode definir o **writeBatchSize** propriedade com um valor mais alto para beneficiar de sobrecarga de lote menor e um débito mais elevado. Se o tamanho de linha dos seus dados é grande, tenha cuidado quando aumenta **writeBatchSize**. Um valor elevado pode levar a uma falha de cópia causada pela sobrecarga da base de dados.

### <a name="nosql-stores"></a>Arquivos NoSQL

* Para **armazenamento de tabelas**:
  * **partição**: Escrita de dados para partições intercaladas drasticamente degrada o desempenho. Ordene os dados de origem por chave de partição para que os dados são inseridos com eficiência numa partição após a outra. Em alternativa, pode ajustar a lógica para escrever os dados para uma única partição.

## <a name="considerations-for-serialization-and-deserialization"></a>Considerações sobre a serialização e desserialização

Serialização e desserialização podem ocorrer quando o seu conjunto de dados de entrada ou um conjunto de dados de saída é um ficheiro. Para obter mais informações sobre formatos de ficheiro suportados pela atividade de cópia, veja [formatos de ficheiro e de compressão suportados](supported-file-formats-and-compression-codecs.md).

**Copie o comportamento**:

* Copiar ficheiros entre arquivos de dados de ficheiros:
  * Quando a entrada e saídos conjuntos de dados de ambos os tem o mesmo ou não existem definições do formato de ficheiro, o serviço de movimento de dados executa uma *cópia binária* sem qualquer serialização ou a desserialização. Ver um débito mais elevado, em comparação com o cenário, em que as definições de formato de ficheiro de origem e sink são diferentes entre si.
  * Quando a entrada e saída conjuntos de dados os dois estão no formato de texto e apenas a codificação tipo é diferente, o serviço de movimento de dados apenas faz a conversão de codificação. Ele não faz qualquer serialização e desserialização, o que faz com que alguma sobrecarga em comparação comparado uma cópia binária de desempenho.
  * Quando a entrada e saídos conjuntos de dados de ambos os tem diferentes formatos de arquivos ou configurações diferentes, como delimitadores, o serviço de movimento de dados desserializa a origem de dados para transmitir em fluxo, transformar e, em seguida, serializá-lo para o formato de saída indicada. Esta operação resulta num desempenho muito mais significativo sobrecarga em comparação comparado outros cenários.
* Quando copia arquivos de ou para um arquivo de dados que não é o arquivo com base, por exemplo, de um arquivo de baseados em ficheiros de mensagens em fila para um armazenamento relacional, o passo de serialização ou desserialização é necessário. Este passo resulta em sobrecarga de desempenho significativo.

**Formato de ficheiro**: O formato de ficheiro que escolher pode afetar o desempenho de cópia. Por exemplo, o Avro é um formato binário compacto que armazena os metadados com dados. Ele tem um amplo suporte no ecossistema do Hadoop para consulta e processamento. Avro é mais caro para serialização e desserialização, o que resulta num débito de cópia mais baixo em comparação comparado o formato de texto. 

Faça sua escolha de formato de ficheiro em todo o fluxo de processamento holística. Começar com:

- O que formam os dados é armazenado em arquivos de dados de origem ou a ser extraído de sistemas externos.
- O melhor formato para armazenamento, processamento analítico e consultas.
- Em que formato, os dados devem ser exportados para marts de dados para as ferramentas de geração de relatórios e visualização.

Por vezes, um formato de ficheiro que é inferior ao ideal para ler e desempenho de escrita pode ser uma boa opção quando considera o processo geral analítico.

## <a name="considerations-for-compression"></a>Considerações para compressão

Quando o conjunto de dados de entrada ou de saída é um ficheiro, pode definir a atividade de cópia para efetuar a compressão ou descompressão à medida que escreve dados para o destino. Ao escolher compressão, faça uma compensação entre (e/s) de entrada/saída e CPU. Comprimir os custos de dados Extras em recursos de computação. Mas, em troca, reduz e/s de rede e armazenamento. Dependendo dos dados, poderá ver um aumento na produtividade geral da cópia.

**Codec**: Cada codec de compressão tem vantagens. Por exemplo, bzip2 tem a menor taxa de transferência de cópia, mas obtém o melhor desempenho das consultas do Hive com bzip2 como pode dividir a para processamento. GZip é a opção mais equilibrada e é utilizado mais frequentemente. Escolha o codec que melhor se adequa aos seu cenário de ponto-a-ponto.

**Nível**: Pode escolher entre duas opções para cada codec de compressão: mais rápida comprimido e comprimido ideal. A opção mais rápida comprimida comprime os dados mais rapidamente possível, mesmo que o ficheiro resultante não é compactado ideal. A opção ideal comprimida gasta mais tempo a compactação e gera uma quantidade mínima de dados. Pode testar as duas opções para ver o que fornece um melhor desempenho geral no seu caso.

**Uma consideração**: Para copiar uma grande quantidade de dados entre um arquivo no local e na cloud, considere usar [cópia faseada](#staged-copy) com compressão ativada. Utilizar o armazenamento provisório é útil quando a largura de banda de rede da sua empresa e seus serviços do Azure é o fator restritivo e pretende o conjunto de dados de entrada e saída dataset no formulário descomprimido.

## <a name="considerations-for-column-mapping"></a>Considerações para mapeamento de colunas

Pode definir o **columnMappings** propriedade numa atividade de cópia para todos os de mapa ou um subconjunto de colunas de entrada para as colunas de saída. Depois do serviço de movimento de dados lê os dados da origem, tem de efetuar o mapeamento de colunas nos dados antes de ele grava os dados para o sink. Este processamento extra reduz a taxa de transferência de cópia.

Se seu arquivo de dados de origem é passível de consulta, por exemplo, se for um arquivo relacional, como a base de dados SQL ou SQL Server, ou se for um arquivo de NoSQL, como o armazenamento de tabelas ou do Azure Cosmos DB, considere enviar a filtragem de coluna e reordenação de lógica para o **consulta** propriedade em vez de usar o mapeamento de colunas. Dessa forma, a projeção ocorre enquanto o serviço de movimento de dados lê dados de arquivo de dados de origem, em que é muito mais eficiente.

Saiba mais a partir da [copie o mapeamento de esquema de atividade](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Outras considerações

Se o tamanho dos dados que pretende copiar for grande, pode ajustar sua lógica de negócio para a partição de ainda mais os dados. Pode agendar a atividade de cópia seja executada com mais frequência para reduzir o tamanho de dados para cada atividade de cópia é executada.

Tenha cuidado sobre o número de conjuntos de dados e copiar as atividades que requerem o Azure Data Factory se ligue para o mesmo arquivo de dados ao mesmo tempo. Muitas tarefas de cópia em simultâneo poderão limitar um arquivo de dados e levar a degradação do desempenho, repetições internas do trabalho de cópia e em alguns casos, as falhas de execução.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Cenário de exemplo: Copiar de um servidor SQL no local para o armazenamento de BLOBs

**Cenário**: Baseia-se um pipeline para copiar dados de um servidor SQL no local para o armazenamento de BLOBs no formato CSV. Para tornar a tarefa de cópia com mais rapidez, os arquivos CSV devem ser comprimidos no formato de bzip2.

**Análise e teste**: O débito da atividade de cópia é inferior a 2 MBps, que é muito mais lenta do que o parâmetro de comparação do desempenho.

**Análise de desempenho e otimização**: Para resolver o problema de desempenho, vamos examinar como os dados são processados e movidos.

- **Ler dados**: O runtime de integração abre uma ligação ao SQL Server e envia a consulta. SQL Server responde ao enviar o fluxo de dados para o runtime de integração por meio da intranet.
- **Serializar e comprimir os dados**: O runtime de integração serializa o fluxo de dados em formato CSV e comprime os dados num fluxo de bzip2.
- **Escrever dados**: O runtime de integração carrega o fluxo de bzip2 para o armazenamento de BLOBs através da internet.

Como pode ver, os dados são processados e movidos de forma seqüencial transmissão em fluxo: SQL Server > LAN > runtime de integração > WAN > armazenamento de Blobs. O desempenho geral é protegido pelo débito mínimo entre o pipeline.

![Fluxo de dados](./media/copy-activity-performance/case-study-pic-1.png)

Um ou mais dos seguintes fatores podem fazer com que o afunilamento do desempenho:

* **origem**: Devido a cargas pesadas, o SQL Server em si tem baixo débito.
* **Runtime de integração autoalojado**:
  * **LAN**: Runtime de integração está localizado longe de ser máquina do SQL Server e tem uma ligação de largura de banda baixa.
  * **Runtime de integração**: Runtime de integração atingiu suas limitações de carga para realizar as seguintes operações:
    * **Serialização**: Serializar o fluxo de dados em formato CSV tem o débito lento.
    * **Compressão**: Optou por um codec de compressão lenta, por exemplo, bzip2, que é 2,8 MBps com Core i7.
  * **WAN**: A largura de banda entre a rede empresarial e seus serviços do Azure é baixa, por exemplo, T1 = 1,544 kbps; T2 = 6,312 kbps.
* **Sink**: Armazenamento de BLOBs tem baixo débito. Este cenário é improvável, porque o respetivo contrato de nível de serviço (SLA) garante um mínimo de 60 MBps.

Neste caso, bzip2 compressão de dados pode ser mais lento todo o pipeline. Mudar para um codec de compressão de gzip poderá facilitar este congestionamento.

## <a name="references"></a>Referências

Seguem-se desempenho de monitorização e otimização de referências para alguns dos arquivos de dados suportados:

* Armazenamento do Azure, que inclui o armazenamento de BLOBs e armazenamento de tabelas: [Destinos de escalabilidade do armazenamento do Azure](../storage/common/storage-scalability-targets.md) e [lista de verificação de armazenamento do Azure, desempenho e escalabilidade](../storage/common/storage-performance-checklist.md).
* Base de Dados SQL do Azure: Pode [monitorizar o desempenho](../sql-database/sql-database-single-database-monitor.md) e verificar a percentagem de unidades de transação de base de dados (DTU).
* Armazém de dados SQL do Azure: Sua capacidade é medida em unidades do Data Warehouse (DWUs). Ver [poder no Azure SQL Data Warehouse (descrição geral) de computação de gerir](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* o Azure Cosmos DB: [Níveis de desempenho no Azure Cosmos DB](../cosmos-db/performance-levels.md).
* SQL Server no local: [Monitorizar e otimizar o desempenho](https://msdn.microsoft.com/library/ms189081.aspx).
* Servidor de ficheiros no local: [Otimização de desempenho para servidores de ficheiros](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Passos Seguintes
Consulte os outros artigos de atividade de cópia:

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Mapeamento de esquema de atividade de cópia](copy-activity-schema-and-type-mapping.md)
- [Copie a tolerância a falhas de atividade](copy-activity-fault-tolerance.md)
