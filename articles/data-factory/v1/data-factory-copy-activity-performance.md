---
title: Copie o guia de Otimização e desempenho de atividade
description: Saiba mais sobre os principais fatores que afetam o desempenho de movimento de dados no Azure Data Factory, quando usa a atividade de cópia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 88094e7ade688505bb971dd85505ddfacb1d8859
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926790"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Copie o guia de Otimização e desempenho de atividade

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-copy-activity-performance.md)
> * [Versão 2 (versão atual)](../copy-activity-performance.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Guia de desempenho e ajuste da atividade de cópia para data Factory](../copy-activity-performance.md).

Atividade de cópia da fábrica de dados do Azure fornece um primeira classe seguros, fiáveis e elevado desempenho de carregamento de dados solução. Permite a cópia dezenas de terabytes de dados todos os dias numa variedade de avançados de nuvem e no local arquivos de dados. Desempenho de carregamento de dados de incrivelmente rápida são a chave para garantir que possa se concentrar no problema de "grandes volumes de dados" core: desenvolvimento de soluções de análise avançada e obtenção de informações profunda de todos esses dados.

O Azure fornece um conjunto de nível empresarial de soluções de armazém de dados e armazenamento de dados e atividade de cópia oferece um experiência fácil de configurar e configurar o carregamento de dados altamente otimizados. Com apenas uma atividade de cópia única, pode obter:

* A carregar dados para o **do Azure SQL Data Warehouse** na **1,2 GBps**. Para obter instruções com um caso de utilização, consulte [carregar 1 TB para o Azure SQL Data Warehouse menos de 15 minutos com o Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* A carregar dados para o **armazenamento de Blobs do Azure** em **de 1,0 GBps**
* A carregar dados para o **do Azure Data Lake Store** em **de 1,0 GBps**

Este artigo descreve:

* [Números de referência do desempenho](#performance-reference) para arquivos de dados de origem e sink para ajudar a planear o seu projeto; de suportados
* Recursos que podem impulsionar a taxa de transferência de cópia em diferentes cenários, incluindo [unidades de movimentação de dados na nuvem](#cloud-data-movement-units), [cópia paralela](#parallel-copy)e [cópia em etapas](#staged-copy);
* [Guia de sintonização de desempenho](#performance-tuning-steps) sobre como otimizar o desempenho e os principais fatores que podem afetar o desempenho de cópia.

> [!NOTE]
> Se você não estiver familiarizado com a atividade de cópia em geral, consulte [mover dados usando a atividade de cópia](data-factory-data-movement-activities.md) antes de ler este artigo.
>

## <a name="performance-reference"></a>Referência de desempenho

Como referência, a tabela abaixo mostra o número da taxa de transferência de cópia em MBps para os pares de origem e coletor especificados com base no teste interno. Para comparação, ele também demonstra como as diferentes configurações de [unidades de movimentação de dados na nuvem](#cloud-data-movement-units) ou a [escalabilidade do gateway de gerenciamento de dados](data-factory-data-management-gateway-high-availability-scalability.md) (vários nós de gateway) podem ajudar no desempenho da cópia.

![Matriz de desempenho](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>No Azure Data Factory versão 1, as unidades de movimentação de dados de nuvem mínimas para a cópia da nuvem para a nuvem são duas. Se não for especificado, consulte unidades de movimentação de dados padrão sendo usadas em [unidades de movimentação de dados de nuvem](#cloud-data-movement-units).

**Pontos a serem observados:**
* Débito é calculado utilizando a seguinte fórmula: [ler tamanho dos dados de origem] / [duração de execução da atividade cópia].
* Os números de referência de desempenho na tabela foram medidos usando o conjunto de dados [TPC-H](http://www.tpc.org/tpch/) em uma única execução da atividade de cópia.
* Nos arquivos de dados do Azure, a origem e sink são na mesma região do Azure.
* Para a cópia híbrida entre armazenamentos de dados locais e na nuvem, cada nó de gateway estava em execução em um computador separado do armazenamento de dados local com a especificação abaixo. Quando uma única atividade estava em execução no gateway, a operação de cópia consumiu apenas uma pequena parte da CPU, da memória ou da largura de banda da rede do computador de teste. Saiba mais em [consideração para gerenciamento de dados gateway](#considerations-for-data-management-gateway).
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
> Você pode obter uma taxa de transferência mais alta aproveitando mais DMUs (unidades de movimentação de dados) que o máximo de DMUs padrão, que é 32 para uma execução da atividade de cópia da nuvem para a nuvem. Por exemplo, com 100 DMUs, você pode obter a cópia de dados do blob do Azure em Azure Data Lake Store a **1,0 Gbps**. Consulte a seção [unidades de movimentação de dados de nuvem](#cloud-data-movement-units) para obter detalhes sobre esse recurso e o cenário com suporte. Contate o [suporte do Azure](https://azure.microsoft.com/support/) para solicitar mais DMUs.

## <a name="parallel-copy"></a>Cópia paralela
Você pode ler dados da origem ou gravar dados **no destino em paralelo dentro de uma execução da atividade de cópia**. Esse recurso aprimora a taxa de transferência de uma operação de cópia e reduz o tempo necessário para mover os dados.

Essa configuração é diferente da propriedade **Concurrency** na definição da atividade. A propriedade **Concurrency** determina o número de **execuções de atividade de cópia simultâneas** para processar dados de diferentes janelas de atividades (das 9h às 2h, às 2h das 3h, das 3h às 2h e assim por diante). Esse recurso é útil quando você executa uma carga histórica. O recurso de cópia paralela aplica-se a uma **única execução de atividade**.

Vejamos um cenário de exemplo. No exemplo a seguir, várias fatias do passado precisam ser processadas. Data Factory executa uma instância da atividade de cópia (uma execução de atividade) para cada fatia:

* A fatia de dados da primeira janela de atividade (9h às 2 AM) = = > execução de atividade 1
* A fatia de dados da segunda janela de atividade (às 3h) = = > execução da atividade 2
* A fatia de dados da segunda janela de atividade (entre 3 e 4 AM) = = > execução da atividade 3

E assim sucessivamente.

Neste exemplo, quando o valor de **simultaneidade** é definido como 2, a **execução da atividade 1** e a **execução da atividade 2** copiam dados de duas janelas de atividade **simultaneamente** para melhorar o desempenho da movimentação de dados. No entanto, se vários arquivos estiverem associados à execução de atividade 1, o serviço de movimentação de dados copiará arquivos da origem para o destino um arquivo de cada vez.

### <a name="cloud-data-movement-units"></a>Unidades de movimentação de dados na nuvem
Uma **DMU (unidade de movimentação de dados de nuvem)** é uma medida que representa a potência (uma combinação de CPU, memória e alocação de recursos de rede) de uma única unidade no data Factory. DMU é aplicável para operações de cópia da nuvem para nuvem, mas não em uma cópia híbrida.

**As unidades de movimentação de dados de nuvem mínimas para capacitar a execução da atividade de cópia são duas.** Se não for especificado, a tabela a seguir listará o DMUs padrão usado em diferentes cenários de cópia:

| Cenário de cópia | DMUs padrão determinado pelo serviço |
|:--- |:--- |
| Copiar dados entre arquivos baseados em ficheiros | Entre 4 e 16, dependendo do número e do tamanho dos arquivos. |
| Todos os outros cenários de cópia | 4 |

Para substituir esse padrão, especifique um valor para a propriedade **cloudDataMovementUnits** da seguinte maneira. Os **valores permitidos** para a propriedade **cloudDataMovementUnits** são 2, 4, 8, 16, 32. O **número real de DMUs de nuvem** que a operação de cópia usa em tempo de execução é igual ou menor que o valor configurado, dependendo do seu padrão de dados. Para obter informações sobre o nível de ganho de desempenho poderá obter ao configurar mais unidades para uma origem de cópia específico e de sink, consulte a [referência de desempenho](#performance-reference).

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

> [!NOTE]
> Se você precisar de mais DMUs de nuvem para uma taxa de transferência maior, entre em contato com o [suporte do Azure](https://azure.microsoft.com/support/). A configuração 8 e superior atualmente funciona somente quando você **copia vários arquivos do armazenamento de blob/data Lake Store/Amazon S3/FTP em nuvem/cloud SFTP para armazenamento de blob/data Lake Store/banco de dados SQL do Azure**.
>

### <a name="parallelcopies"></a>parallelCopies
Pode utilizar o **parallelCopies** propriedade para indicar o paralelismo que pretende que a atividade de cópia para utilizar. Pode pensar esta propriedade como o número máximo de threads na atividade de cópia pode ler da origem de ou escrever seus arquivos de dados de sink em paralelo.

Para cada atividade de cópia executar, o Data Factory determina o número de cópias paralelas para utilizar para copiar dados a partir da origem de arquivo de dados e para os dados de destino armazenam. O número padrão de cópias paralelas que ele usa depende do tipo de fonte e do coletor que você está usando.

| Origem e coletor | Número de cópias paralela de predefinido determinado pelo serviço |
| --- | --- |
| Copiar dados entre armazenamentos baseados em arquivo (armazenamento de BLOBs; Data Lake Store; Amazon S3; um sistema de arquivos local; um HDFS local) |Entre 1 e 32. Depende do tamanho dos arquivos e do número de DMUs (unidades de movimentação de dados de nuvem) usadas para copiar dados entre dois armazenamentos de dados de nuvem ou a configuração física do computador do gateway usada para uma cópia híbrida (para copiar dados de ou para um armazenamento de dados local). |
| Copiar dados de **qualquer armazenamento de dados de origem para o armazenamento de tabelas do Azure** |4 |
| Todos os outros pares de origem e coletor |1 |

Normalmente, o comportamento padrão deve fornecer a melhor taxa de transferência. No entanto, para controlar a carga em computadores que hospedam seus armazenamentos de dados ou para ajustar o desempenho da cópia, você pode optar por substituir o valor padrão e especificar um valor para a propriedade **parallelCopies** . O valor deve estar entre 1 e 32 (ambos incluídos). No momento de execução para um melhor desempenho, atividade de cópia utiliza um valor que é menor ou igual ao valor que definir.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 8
        }
    }
]
```
Pontos a serem observados:

* Quando copiar dados entre arquivos baseados em ficheiros, o **parallelCopies** determinar o paralelismo em nível de arquivo. A segmentação dentro de um único arquivo aconteceria abaixo de forma automática e transparente e foi concebido para utilizar o melhor tamanho de bloco adequado para um tipo de arquivo de dados de origem específica para carregar os dados em paralelo e ortogonal ao parallelCopies. O número real de cópias paralelas utiliza o serviço de movimento de dados para a operação de cópia em tempo de execução é não mais do que o número de ficheiros que tiver. Se for o comportamento de cópia **mergeFile**, atividade de cópia não é possível tirar proveito do paralelismo de nível de arquivo.
* Quando você especificar um valor para a propriedade **parallelCopies** , considere o aumento de carga nos armazenamentos de dados de origem e de coletor, e para o gateway se ele for uma cópia híbrida. Isso acontece especialmente quando tem várias atividades ou execuções simultâneas de iguais às atividades que são executados em relação ao mesmo armazenamento de dados. Se você observar que o armazenamento de dados ou o gateway está sobrecarregado com a carga, diminua o valor de **parallelCopies** para aliviar a carga.
* Quando copiar dados a partir de arquivos que não são baseados em ficheiros para arquivos que são baseados em ficheiros, o serviço de movimento de dados ignora a **parallelCopies** propriedade. Mesmo que o paralelismo é especificado, não é aplicado neste caso.

> [!NOTE]
> Você deve usar Gerenciamento de Dados gateway versão 1,11 ou posterior para usar o recurso **parallelCopies** ao fazer uma cópia híbrida.
>
>

Para usar melhor essas duas propriedades e aprimorar sua taxa de transferência de movimentação de dados, consulte os casos de uso de exemplo. Você não precisa configurar o **parallelCopies** para aproveitar o comportamento padrão. Se você configurar e **parallelCopies** for muito pequeno, várias DMUs de nuvem poderão não ser totalmente utilizadas.

### <a name="billing-impact"></a>Impacto de cobrança
Ele possui **importante** lembrar-se de que é cobrado com base no tempo total da operação de cópia. Se um trabalho de cópia usado para levar uma hora com uma unidade de nuvem e agora levar 15 minutos com quatro unidades de nuvem, a cobrança geral permanecerá quase a mesma. Por exemplo, você usa quatro unidades de nuvem. A primeira unidade de nuvem passa 10 minutos, a segunda, 10 minutos, a terceira, 5 minutos e a quarta, 5 minutos, tudo em uma atividade de cópia executada. Você é cobrado pelo tempo total de cópia (movimentação de dados), que é 10 + 10 + 5 + 5 = 30 minutos. O uso de **parallelCopies** não afeta a cobrança.

## <a name="staged-copy"></a>Cópia faseada
Quando copia dados de um arquivo de dados de origem para um arquivo de dados de sink, pode optar por utilizar o armazenamento de BLOBs como um armazenamento de teste provisório. Teste é especialmente útil nos seguintes casos:

1. **Pretende ingerir dados de vários arquivos de dados no SQL Data Warehouse através do PolyBase**. O SQL Data Warehouse utiliza o PolyBase como um mecanismo de alto débito para carregar uma grande quantidade de dados para o SQL Data Warehouse. No entanto, os dados de origem devem estar no armazenamento de BLOBs e devem atender a critérios adicionais. Ao carregar dados de um armazenamento de dados diferente do armazenamento de BLOBs, você pode ativar a cópia de dados por meio do armazenamento de blobs de preparo provisório. Nesse caso, o Data Factory realiza as transformações de dados necessários para assegurar que cumpre os requisitos do PolyBase. Em seguida, ele utiliza o PolyBase para carregar dados para o SQL Data Warehouse. Para obter mais detalhes, consulte [usar o polybase para carregar dados no Azure SQL data warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). Para obter instruções com um caso de utilização, consulte [carregar 1 TB para o Azure SQL Data Warehouse menos de 15 minutos com o Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **Às vezes, demora um pouco para executar uma movimentação de dados híbrido (ou seja, copiar entre um armazenamento de dados local e um armazenamento de dados de nuvem) em uma conexão de rede lenta**. Para melhorar o desempenho, você pode compactar os dados locais para que leve menos tempo para mover dados para o armazenamento de dados de preparo na nuvem. Em seguida, você pode descompactar os dados no armazenamento de preparo antes de carregá-los no armazenamento de dados de destino.
3. **Não deseja abrir portas sem ser a porta 80 e a porta 443 na firewall, devido a políticas de TI empresariais**. Por exemplo, quando copia dados de um arquivo de dados no local para um sink de base de dados do Azure SQL ou um coletor do Azure SQL Data Warehouse, terá de ativar a comunicação de saída de TCP na porta 1433 para a firewall do Windows e sua firewall empresarial. Nesse cenário, aproveite o gateway para primeiro copiar dados para uma instância de preparo de armazenamento de BLOBs por HTTP ou HTTPS na porta 443. Em seguida, carregue os dados no banco de dados SQL ou SQL Data Warehouse do preparo do armazenamento de BLOBs. Neste fluxo, não terá de ativar a porta 1433.

### <a name="how-staged-copy-works"></a>Funciona como faseada de cópia
Quando você ativa o recurso de preparo, primeiro os dados são copiados do armazenamento de dados de origem para o armazenamento de dados de preparo (Traga seu próprio). Em seguida, os dados são copiados do arquivo de dados de teste para o arquivo de dados de sink. Data Factory gere automaticamente o fluxo de duas etapas para. Fábrica de dados limpa também dados temporários, o armazenamento de teste do após o movimento de dados está concluído.

No cenário de cópia de nuvem (os armazenamentos de dados de origem e de coletor estão na nuvem), o gateway não é usado. O serviço de Data Factory executa as operações de cópia.

![Cópia em etapas: cenário de nuvem](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

No cenário de cópia híbrida (a origem é local e o coletor está na nuvem), o gateway move os dados do armazenamento de dados de origem para um armazenamento de dados de preparo. Data Factory serviço move dados do armazenamento de dados de preparo para o armazenamento de dados do coletor. A cópia de dados de um armazenamento de dados de nuvem para um armazenamento de dados local por meio de preparo também tem suporte com o fluxo invertido.

![Cópia em etapas: cenário híbrido](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Ao ativar o movimento de dados usando um arquivo de teste, pode especificar se pretende que os dados para ser comprimidos antes de mover dados do arquivo de dados de origem para um arquivo de dados intermediárias ou teste e, em seguida, descompactado antes de mover dados de um interim ou dados de teste armazenar para o arquivo de dados de sink.

Atualmente, não é possível copiar dados entre dois arquivos de dados no local através de um arquivo de transição. Esperamos que essa opção esteja disponível em breve.

### <a name="configuration"></a>Configuração
Configurar o **enableStaging** definição na atividade de cópia para especificar se pretende que os dados para ser preparado no armazenamento de BLOBs, antes de carregá-lo num arquivo de dados de destino. Quando você definir **enableStaging** como true, especifique as propriedades adicionais listadas na tabela a seguir. Se não tiver um, terá também de criar um armazenamento do Azure ou o armazenamento partilhado serviço ligado de assinatura de acesso para preparação.

| Propriedade | Descrição | Valor predefinido | Obrigatório |
| --- | --- | --- | --- |
| **enableStaging** |Especifique se pretende copiar os dados por meio de um arquivo de teste de interim. |Falso |Não |
| **linkedServiceName** |Especifique o nome de um serviço vinculado [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) , que se refere à instância de armazenamento que você usa como um armazenamento de preparo provisório. <br/><br/> Não é possível utilizar o armazenamento com uma assinatura de acesso partilhado para carregar dados para o SQL Data Warehouse através do PolyBase. Pode usá-lo em todos os outros cenários. |N/A |Sim, quando **enableStaging** está definido como TRUE |
| **path** |Especifique o caminho de armazenamento de BLOBs para conter os dados em etapas. Se não fornecer um caminho, o serviço cria um contentor para armazenar dados temporários. <br/><br/> Especifique um caminho de apenas se utilizar o armazenamento com uma assinatura de acesso partilhado, ou se necessitar de temporários de dados numa localização específica. |N/A |Não |
| **enableCompression** |Especifica se os dados devem ser comprimidos antes que seja copiado para o destino. Esta definição reduz o volume de dados a serem transferidos. |Falso |Não |

Eis um exemplo de definição de atividade de cópia com as propriedades descritas na tabela anterior:

```json
"activities":[
{
    "name": "Sample copy activity",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDBOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlSink"
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob",
            "path": "stagingcontainer/path",
            "enableCompression": true
        }
    }
}
]
```

### <a name="billing-impact"></a>Impacto de cobrança
É cobrado com base em dois passos: duração da cópia e copie o tipo.

* Ao usar o preparo durante uma cópia de nuvem (copiando dados de um armazenamento de dados de nuvem para outro armazenamento de dados de nuvem), você será cobrado pela [soma da duração da cópia para a etapa 1 e etapa 2] x [preço unitário da cópia de nuvem].
* Ao usar o preparo durante uma cópia híbrida (copiando dados de um armazenamento de dados local para um armazenamento de dados de nuvem), você será cobrado por [duração da cópia híbrida] x [preço unitário da cópia híbrida] + [duração da cópia da nuvem] x [preço unitário da cópia de nuvem].

## <a name="performance-tuning-steps"></a>Etapas de ajuste de desempenho
Sugerimos que siga estes passos para otimizar o desempenho do seu serviço do Data Factory com atividade de cópia:

1. **Estabelecer uma linha de base**. Durante a fase de desenvolvimento, teste o seu pipeline através da atividade de cópia em relação a uma amostra de dados representativos. Você pode usar o [modelo de divisão](data-factory-scheduling-and-execution.md) de data Factory para limitar a quantidade de dados com os quais você trabalha.

   Colete características de desempenho e tempo de execução usando o **aplicativo de monitoramento e gerenciamento**. Escolha **monitorar & gerenciar** em seu home page de data Factory. No modo de exibição de árvore, escolha o **conjunto de resultados de saída**. Na lista **janelas de atividades** , escolha a execução da atividade de cópia. **Janelas de atividades** lista a duração da atividade de cópia e o tamanho dos dados que são copiados. A taxa de transferência é listada no **Gerenciador de janelas de atividades**. Para saber mais sobre o aplicativo, consulte [monitorar e gerenciar Azure data Factory pipelines usando o aplicativo de monitoramento e gerenciamento](data-factory-monitor-manage-app.md).

   ![Detalhes da execução da atividade](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Posteriormente neste artigo, você pode comparar o desempenho e a configuração do seu cenário para copiar a [referência de desempenho](#performance-reference) da atividade de nossos testes.
2. **Diagnosticar e otimizar o desempenho**. Se o desempenho que observar não cumprir as suas expectativas, precisa identificar afunilamentos de desempenho. Em seguida, a otimizar o desempenho para remover ou reduzir o efeito de afunilamentos. Uma descrição completa do diagnóstico de desempenho está além do escopo deste artigo, mas aqui estão algumas considerações comuns:

   * Recursos de desempenho:
     * [Cópia paralela](#parallel-copy)
     * [Unidades de movimentação de dados na nuvem](#cloud-data-movement-units)
     * [Cópia faseada](#staged-copy)
     * [Escalabilidade do gateway de Gerenciamento de Dados](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Data Management Gateway](#considerations-for-data-management-gateway)
   * [Origem](#considerations-for-the-source)
   * [sink](#considerations-for-the-sink)
   * [Serialização e desserialização](#considerations-for-serialization-and-deserialization)
   * [Compressão](#considerations-for-compression)
   * [Mapeamento de colunas](#considerations-for-column-mapping)
   * [Outras considerações](#other-considerations)
3. **Expanda a configuração para o seu conjunto de dados inteiro**. Quando estiver satisfeito com os resultados e o desempenho da execução, você poderá expandir a definição e o período ativo do pipeline para abranger todo o seu conjunto de dados.

## <a name="considerations-for-data-management-gateway"></a>Considerações para Gerenciamento de Dados gateway
**Configuração do gateway**: Recomendamos que você use um computador dedicado para hospedar gerenciamento de dados gateway. Consulte [considerações para usar o gateway de gerenciamento de dados](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Monitoramento e expansão/saída do gateway**: um único gateway lógico com um ou mais nós de gateway pode atender a várias execuções de atividade de cópia ao mesmo tempo simultaneamente. Você pode exibir o instantâneo quase em tempo real da utilização de recursos (CPU, memória, rede (entrada/saída), etc.) em um computador de gateway, bem como o número de trabalhos simultâneos em execução versus limite no portal do Azure, consulte [monitorar gateway no portal](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Se você tiver muita necessidade de movimentação de dados híbridos com um grande número de execuções simultâneas da atividade de cópia ou com grandes volumes de dados a serem copiados, considere a possibilidade de [escalar verticalmente ou escalar horizontalmente o gateway](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) para utilizar melhor seu recurso ou para provisionar mais recursos para capacitar a cópia.

## <a name="considerations-for-the-source"></a>Considerações para a origem
### <a name="general"></a>Geral
Certifique-se de que o arquivo de dados subjacente não está assoberbado com outras cargas de trabalho que estejam a executar num ou em relação a ele.

Para arquivos de dados da Microsoft, consulte [monitoramento e ajuste tópicos](#performance-reference) que são específicas para arquivos de dados e ajudam a compreender dados armazenam características de desempenho, minimizar os tempos de resposta e maximizar o débito.

Se você copiar dados do armazenamento de BLOBs para SQL Data Warehouse, considere o uso do **polybase** para impulsionar o desempenho. Ver [utilizar o PolyBase para carregar dados para o Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter detalhes. Para obter instruções com um caso de utilização, consulte [carregar 1 TB para o Azure SQL Data Warehouse menos de 15 minutos com o Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Arquivos de dados baseados em ficheiros
*(Inclui armazenamento de BLOBs, Data Lake Store, Amazon S3, sistemas de arquivos locais e HDFS local)*

* **Média de tamanho de ficheiro e a contagem de ficheiros**: atividade de cópia transfere um ficheiro de dados cada vez. Com a mesma quantidade de dados para ser movida, o débito global é inferior se os dados consistam em muitos arquivos pequenos em vez de poucos arquivos grandes devido a fase de arranque de configuração para cada ficheiro. Por conseguinte, se possível, combine o arquivos pequenos em arquivos maiores para obter um débito mais elevado.
* **Formato e compressão de ficheiros**: para obter mais formas de melhorar o desempenho, consulte a [considerações de serialização e desserialização](#considerations-for-serialization-and-deserialization) e [considerações sobre a compactação](#considerations-for-compression) secções.
* Para o cenário do **sistema de arquivos local** , no qual **Gerenciamento de dados gateway** é necessário, consulte a seção [considerações para o gateway de gerenciamento de dados](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Arquivos de dados relacionais
*(Inclui banco de dados SQL; SQL Data Warehouse; Amazon redshift; Bancos de dados SQL Server; e bancos de dados Oracle, MySQL, DB2, Teradata, Sybase e PostgreSQL, etc.)*

* **Padrão de dados**: seu esquema de tabela afeta o débito de cópia. Um tamanho de linha grandes dá-lhe um desempenho melhor do que o tamanho de linha de pequenos, para copiar a mesma quantidade de dados. O motivo é que a base de dados com mais eficiência pode obter menos lotes de dados que contêm menos linhas.
* **Consulta ou procedimento armazenado**: otimizar a lógica da consulta ou procedimento armazenado que especificar na origem de atividade de cópia para obter os dados de forma mais eficiente.
* Para **bancos de dados relacionais locais**, como SQL Server e Oracle, que exigem o uso do **Gateway de gerenciamento de dados**, consulte a seção considerações para o gateway de gerenciamento de dados.

## <a name="considerations-for-the-sink"></a>Considerações para o sink
### <a name="general"></a>Geral
Certifique-se de que o arquivo de dados subjacente não está assoberbado com outras cargas de trabalho que estejam a executar num ou em relação a ele.

Para arquivos de dados da Microsoft, consulte [monitoramento e ajuste tópicos](#performance-reference) que são específicas para arquivos de dados. Estes tópicos podem ajudá-lo a compreender as características de desempenho do armazenamento de dados e como minimizar os tempos de resposta e maximizar o débito.

Se você estiver copiando dados do **armazenamento de BLOBs** para **SQL data warehouse**, considere usar o **polybase** para aumentar o desempenho. Ver [utilizar o PolyBase para carregar dados para o Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter detalhes. Para obter instruções com um caso de utilização, consulte [carregar 1 TB para o Azure SQL Data Warehouse menos de 15 minutos com o Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Arquivos de dados baseados em ficheiros
*(Inclui armazenamento de BLOBs, Data Lake Store, Amazon S3, sistemas de arquivos locais e HDFS local)*

* **Copie o comportamento**: Se copiar dados de um arquivo de dados diferentes baseados em ficheiros, a atividade de cópia tem três opções por meio do **copyBehavior** propriedade. Preserva a hierarquia, nivela hierarquia ou intercala ficheiros. A preservação ou mesclar a hierarquia tem pouca ou nenhuma sobrecarga de desempenho, mas mesclar arquivos faz com que a sobrecarga de desempenho aumentar.
* **Formato e compressão de ficheiros**: consulte as [considerações de serialização e desserialização](#considerations-for-serialization-and-deserialization) e [considerações sobre a compactação](#considerations-for-compression) secções para obter mais maneiras de melhorar o desempenho.
* **Armazenamento de BLOBs**: atualmente, o armazenamento de BLOBs dá suporte apenas a blobs de blocos para transferência e produtividade de dados otimizadas.
* Para cenários de **sistemas de arquivos locais** que exigem o uso de **Gerenciamento de dados gateway**, consulte a seção [considerações para o gateway de gerenciamento de dados](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Arquivos de dados relacionais
*(Inclui o banco de dados SQL, o SQL Data Warehouse, os bancos de dados SQL Server e os bancos de dados Oracle)*

* **Copie o comportamento**: consoante as propriedades que tiver definido para **sqlSink**, atividade de cópia escreve dados na base de dados de destino de formas diferentes.
  * Por predefinição, as utilizações de serviço de movimento de dados a API de cópia em massa para inserir dados em acréscimo modo, que fornece o melhor desempenho.
  * Se configurar um procedimento armazenado no coletor, a base de dados aplica-se a uma linha de dados cada vez, em vez de como um carregamento em massa. Desempenho cai significativamente. Se o conjunto de dados for grande, quando aplicável, considere a possibilidade de alternar para o uso da propriedade **sqlWriterCleanupScript** .
  * Se você configurar a propriedade **sqlWriterCleanupScript** para cada execução da atividade de cópia, o serviço disparará o script e, em seguida, você usará a API de cópia em massa para inserir os dados. Por exemplo, para substituir a tabela inteira com os dados mais recentes, pode especificar um script para eliminar primeiro todos os registos antes dos novos dados da origem de carregamento em massa.
* **Tamanho de dados padrão e batch**:
  * A esquema da tabela afeta o débito de cópia. Para copiar a mesma quantidade de dados, um tamanho de linha grandes fornece desempenho melhor do que um tamanho de linha de pequenos porque a base de dados com mais eficiência pode comprometer-se menos lotes de dados.
  * Atividade de cópia insere dados de uma série de lotes. Pode definir o número de linhas num batch ao utilizar o **writeBatchSize** propriedade. Se os dados tiverem linhas pequenas, pode definir o **writeBatchSize** propriedade com um valor mais alto para beneficiar de sobrecarga de lote menor e um débito mais elevado. Se o tamanho de linha dos seus dados é grande, tenha cuidado quando aumenta **writeBatchSize**. Um valor elevado pode levar a uma falha de cópia causada pela sobrecarga da base de dados.
* Para **bancos de dados relacionais locais** como SQL Server e Oracle, que exigem o uso do gateway de **Gerenciamento de dados**, consulte a seção [considerações para o gateway de gerenciamento de dados](#considerations-for-data-management-gateway) .

### <a name="nosql-stores"></a>Arquivos NoSQL
*(Inclui armazenamento de tabela e Azure Cosmos DB)*

* Para **armazenamento de tabelas**:
  * **Partição**: escrever dados para partições intercaladas drasticamente degrada o desempenho. Ordenar os dados de origem por chave de partição para que os dados são inseridos com eficiência numa partição após a outra ou ajustar a lógica para escrever os dados para uma única partição.
* Por **Azure Cosmos DB**:
  * **Tamanho do lote**: a propriedade **writeBatchSize** define o número de solicitações paralelas para o serviço Azure Cosmos DB para criar documentos. Você pode esperar um melhor desempenho ao aumentar o **writeBatchSize** porque mais solicitações paralelas são enviadas para Azure Cosmos DB. No entanto, observe a limitação ao gravar em Azure Cosmos DB (a mensagem de erro é "a taxa de solicitação é grande"). Vários fatores podem causar limitação, incluindo o tamanho do documento, o número de termos nos documentos e a política de indexação da coleção de destino. Para obter uma taxa de transferência de cópia mais alta, considere usar uma coleção melhor, por exemplo, S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Considerações sobre a serialização e desserialização
Serialização e desserialização podem ocorrer quando o seu conjunto de dados de entrada ou um conjunto de dados de saída é um ficheiro. Ver [formatos de ficheiro e de compressão suportados](data-factory-supported-file-and-compression-formats.md) com detalhes sobre os formatos de ficheiro suportados pela atividade de cópia.

**Copie o comportamento**:

* Copiar ficheiros entre arquivos de dados de ficheiros:
  * Quando os conjuntos de dados de entrada e saída têm as mesmas ou nenhuma configuração de formato de arquivo, o serviço de movimentação de dados executa uma cópia binária sem nenhuma serialização ou desserialização. Ver um débito mais elevado, em comparação com o cenário, em que as definições de formato de ficheiro de origem e sink são diferentes entre si.
  * Quando a entrada e saída conjuntos de dados de ambos os estão no formato de texto e apenas a codificação tipo é diferente, o serviço de movimento de dados apenas faz a conversão de codificação. Ele não faz qualquer serialização e desserialização, o que faz com que alguma sobrecarga em comparação comparado uma cópia binária de desempenho.
  * Quando a entrada e saídos conjuntos de dados de ambos os tem diferentes formatos de arquivos ou configurações diferentes, como delimitadores, o serviço de movimento de dados desserializa a origem de dados para transmitir em fluxo, transformar e, em seguida, serializá-lo para o formato de saída indicada. Esta operação resulta num desempenho muito mais significativo sobrecarga em comparação comparado outros cenários.
* Quando copia arquivos de/para um arquivo de dados que não seja baseado em ficheiros (por exemplo, a partir de um arquivo de baseados em ficheiros de mensagens em fila para um armazenamento relacional), o passo de serialização ou desserialização é necessário. Este passo resulta em sobrecarga de desempenho significativo.

**Formato de ficheiro**: O formato de ficheiro que escolher pode afetar o desempenho de cópia. Por exemplo, o Avro é um formato binário compacto que armazena os metadados com dados. Ele tem um amplo suporte no ecossistema do Hadoop para consulta e processamento. No entanto, o Avro é mais caro para serialização e desserialização, o que resulta num débito de cópia mais baixo em comparação comparado o formato de texto. Faça sua escolha de formato de ficheiro em todo o fluxo de processamento holística. Começar com o que formam os dados é armazenado em arquivos de dados de origem ou a ser extraído de sistemas externos; o melhor formato para armazenamento, processamento analítico e consulta; e, em que formato os dados devem ser exportados para o marts de dados para as ferramentas de geração de relatórios e visualização. Por vezes, um formato de ficheiro que é inferior ao ideal para ler e desempenho de escrita pode ser uma boa opção quando considera o processo geral analítico.

## <a name="considerations-for-compression"></a>Considerações para compressão
Quando o seu conjunto de dados de entrada ou de saída é um arquivo, pode definir a atividade de cópia para efetuar a compressão ou descompressão à medida que escreve dados para o destino. Ao escolher compressão, faça uma compensação entre (e/s) de entrada/saída e CPU. Comprimir os custos de dados Extras em recursos de computação. Mas, em troca, reduz e/s de rede e armazenamento. Dependendo dos dados, poderá ver um aumento na produtividade geral da cópia.

**Codec**: a atividade de cópia dá suporte a tipos de compactação Gzip, BZIP2 e deflate. O Azure HDInsight pode consumir todos os três tipos para processamento. Cada codec de compactação tem vantagens. Por exemplo, bzip2 tem a menor taxa de transferência de cópia, mas obtém o melhor desempenho das consultas do Hive com bzip2 como pode dividir a para processamento. GZip é a opção mais equilibrada e é utilizado mais frequentemente. Escolha o codec que melhor se adequa aos seu cenário de ponto-a-ponto.

**Nível**: pode escolher entre duas opções para cada codec de compressão: mais rápida comprimido e comprimido ideal. A opção mais rápida comprimida comprime os dados mais rapidamente possível, mesmo que o ficheiro resultante não será compactado ideal. A opção ideal comprimida gasta mais tempo a compactação e gera uma quantidade mínima de dados. Pode testar as duas opções para ver o que fornece um melhor desempenho geral no seu caso.

**Uma consideração**: para copiar uma grande quantidade de dados entre um repositório local e a nuvem, considere o uso do armazenamento de BLOBs provisórios com compactação. Utilizar o armazenamento provisório é útil quando a largura de banda de rede da sua empresa e seus serviços do Azure é o fator restritivo e quiser que o conjunto de dados de entrada e o conjunto de dados de saída ambos os ter formato não comprimido. Mais especificamente, você pode dividir uma única atividade de cópia em duas atividades de cópia. A primeira atividade de cópia copia da origem para um blob provisório ou de preparo em formato compactado. A segunda atividade de cópia copia os dados compactados do preparo e, em seguida, descompacta enquanto grava no coletor.

## <a name="considerations-for-column-mapping"></a>Considerações para mapeamento de colunas
Pode definir o **columnMappings** propriedade na atividade de cópia para todos os de mapa ou um subconjunto de colunas de entrada para as colunas de saída. Depois do serviço de movimento de dados lê os dados da origem, tem de efetuar o mapeamento de colunas nos dados antes de ele grava os dados para o sink. Este processamento extra reduz a taxa de transferência de cópia.

Se seu arquivo de dados de origem é passível de consulta, por exemplo, se for um arquivo relacional, como a base de dados SQL ou SQL Server, ou se for um arquivo de NoSQL, como o armazenamento de tabelas ou do Azure Cosmos DB, considere enviar a filtragem de coluna e reordenação de lógica para o **consulta** propriedade em vez de usar o mapeamento de colunas. Dessa forma, a projeção ocorre enquanto o serviço de movimento de dados lê dados de arquivo de dados de origem, em que é muito mais eficiente.

## <a name="other-considerations"></a>Outras considerações
Se o tamanho dos dados que você deseja copiar for grande, você poderá ajustar sua lógica de negócios para particionar ainda mais os dados usando o mecanismo de divisão em Data Factory. Em seguida, Agende a atividade de cópia para executar com mais frequência para reduzir o tamanho dos dados para cada execução da atividade de cópia.

Tenha cuidado com o número de conjuntos de dados e atividades de cópia que exigem Data Factory para o conector para o mesmo armazenamento de dados ao mesmo tempo. Muitas tarefas de cópia em simultâneo poderão limitar um arquivo de dados e levar a degradação do desempenho, repetições internas do trabalho de cópia e em alguns casos, as falhas de execução.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Cenário de exemplo: cópia de um servidor de SQL no local para o armazenamento de BLOBs
**Cenário**: baseia-se um pipeline para copiar dados de um servidor de SQL no local para o armazenamento de BLOBs no formato CSV. Para tornar a tarefa de cópia com mais rapidez, os arquivos CSV devem ser comprimidos no formato de bzip2.

**Análise e teste**: O débito de atividade de cópia é inferior a 2 MBps, que é muito mais lenta do que o parâmetro de comparação do desempenho.

**Análise de desempenho e otimização**: para resolver o problema de desempenho, vamos dar uma olhada em como os dados são processados e movidos.

1. **Ler dados**: gateway abre uma conexão para SQL Server e envia a consulta. SQL Server responde enviando o fluxo de dados para o gateway por meio da intranet.
2. **Serializar e compactar dados**: o gateway serializa o fluxo de dados para o formato CSV e compacta os dados em um fluxo bzip2.
3. **Gravar dados**: o gateway carrega o fluxo de bzip2 no armazenamento de BLOBs pela Internet.

Como você pode ver, os dados estão sendo processados e movidos de maneira sequencial de streaming: SQL Server > LAN > gateway > WAN > armazenamento de BLOBs. **O desempenho geral é protegido pelo débito mínimo entre o pipeline**.

![Fluxo de dados](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Um ou mais dos seguintes fatores podem fazer com que o afunilamento do desempenho:

* **Origem**: próprio SQL Server tem baixo débito devido a cargas pesadas.
* **Gateway de gerenciamento de dados**:
  * **LAN**: o gateway está localizado longe da máquina SQL Server e tem uma conexão de baixa largura de banda.
  * **Gateway**: o gateway atingiu suas limitações de carga para executar as seguintes operações:
    * **Serialização**: serializar o fluxo de dados em formato CSV tem o débito lento.
    * **Compressão**: escolher um codec de compressão lenta (por exemplo, bzip2, que é 2,8 MBps com Core i7).
  * **WAN**: A largura de banda entre a rede empresarial e seus serviços do Azure é baixa (por exemplo, T1 = 1,544 kbps; T2 = 6,312 kbps).
* **Sink**: armazenamento de BLOBs tem baixo débito. (Este cenário é pouco provável que uma vez que o SLA garante um mínimo de 60 MBps.)

Neste caso, bzip2 compressão de dados pode ser mais lento todo o pipeline. Mudar para um codec de compressão de gzip poderá facilitar este congestionamento.

## <a name="sample-scenarios-use-parallel-copy"></a>Cenários de exemplo: usar cópia paralela
**Cenário I:** Copie os arquivos 1.000 1-MB do sistema de arquivos local para o armazenamento de BLOBs.

**Análise e ajuste de desempenho**: por exemplo, se você tiver instalado o gateway em um computador Quad Core, o data Factory usará 16 cópias paralelas para mover arquivos do sistema de arquivos para o armazenamento de BLOBs simultaneamente. Essa execução paralela deve resultar em alta taxa de transferência. Você também pode especificar explicitamente a contagem de cópias paralelas. Quando você copia muitos arquivos pequenos, as cópias paralelas ajudam drasticamente a taxa de transferência usando recursos com mais eficiência.

![Cenário 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Cenário II**: Copie 20 blobs de 500 MB cada do armazenamento de BLOBs para a análise de data Lake Store e, em seguida, ajuste o desempenho.

**Análise e ajuste de desempenho**: nesse cenário, data Factory copia os dados do armazenamento de BLOBs para data Lake Store usando uma cópia única (**parallelCopies** definida como 1) e unidades de movimentação de dados de nuvem única. A taxa de transferência que você observará será próxima da descrita na [seção de referência de desempenho](#performance-reference).

![Cenário 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Cenário III**: o tamanho do arquivo individual é maior que dezenas de MBS e o volume total é grande.

**Análise e desempenho desativados**: o aumento de **parallelCopies** não resulta em melhor desempenho de cópia devido às limitações de recursos de um DMU de nuvem única. Em vez disso, você deve especificar mais DMUs de nuvem para obter mais recursos para executar a movimentação de dados. Não especifique um valor para a propriedade **parallelCopies** . Data Factory lida com o paralelismo para você. Nesse caso, se você definir **cloudDataMovementUnits** como 4, uma taxa de transferência de aproximadamente quatro vezes ocorrerá.

![Cenário 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Referência
Aqui estão as referências de monitoramento e ajuste de desempenho para alguns dos armazenamentos de dados com suporte:

* O armazenamento do Azure (incluindo o armazenamento de BLOBs e armazenamento de tabelas): [metas de escalabilidade do armazenamento do Azure](../../storage/common/storage-scalability-targets.md) e [lista de verificação de armazenamento do Azure, desempenho e escalabilidade](../../storage/common/storage-performance-checklist.md)
* Base de dados SQL do Azure: Pode [monitorizar o desempenho](../../sql-database/sql-database-single-database-monitor.md) e verificar a percentagem de unidade (DTU) de transação de base de dados
* O Azure SQL Data Warehouse: O seu recurso é medido em unidades do data warehouse (DWUs); consulte [gerir computação power no Azure SQL Data Warehouse (descrição geral)](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [níveis de desempenho no Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* SQL Server no local: [monitorizar e otimizar desempenho](https://msdn.microsoft.com/library/ms189081.aspx)
* Servidor de ficheiros no local: [otimização de desempenho para servidores de ficheiros](https://msdn.microsoft.com/library/dn567661.aspx)
