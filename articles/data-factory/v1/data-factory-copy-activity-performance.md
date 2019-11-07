---
title: Guia de desempenho e ajuste da atividade de cópia
description: Saiba mais sobre os principais fatores que afetam o desempenho da movimentação de dados em Azure Data Factory quando você usa a atividade de cópia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c1cabbe3587079fa3fa5947eddbcf6cecaff3b98
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682925"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Guia de desempenho e ajuste da atividade de cópia

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-copy-activity-performance.md)
> * [Versão 2 (versão atual)](../copy-activity-performance.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Guia de desempenho e ajuste da atividade de cópia para data Factory](../copy-activity-performance.md).

Azure Data Factory atividade de cópia fornece uma solução de carregamento de dados segura, confiável e de alto desempenho de primeira classe. Ele permite que você copie dezenas de terabytes de dados todos os dias em uma grande variedade de armazenamentos de dados locais e na nuvem. O desempenho de carregamento de dados mais rápido é a chave para garantir que você possa se concentrar no principal problema de "Big Data": criar soluções de análise avançada e obter informações aprofundadas de todos esses dados.

O Azure fornece um conjunto de soluções de armazenamento e data warehouse de dados de nível empresarial, e a atividade de cópia oferece uma experiência de carregamento de dados altamente otimizada que é fácil de configurar e configurar. Com apenas uma única atividade de cópia, você pode obter:

* Carregando dados no **Azure SQL data warehouse** a **1,2 Gbps**. Para obter instruções com um caso de uso, consulte [carregar 1 TB no Azure SQL data warehouse menos de 15 minutos com Azure data Factory](data-factory-load-sql-data-warehouse.md).
* Carregando dados no **armazenamento de BLOBs do Azure** a **1,0 Gbps**
* Carregando dados em **Azure data Lake Store** a **1,0 Gbps**

Este artigo descreve:

* [Números de referência de desempenho](#performance-reference) para armazenamentos de dados de origem e coletor com suporte para ajudá-lo a planejar seu projeto;
* Recursos que podem impulsionar a taxa de transferência de cópia em diferentes cenários, incluindo [unidades de movimentação de dados na nuvem](#cloud-data-movement-units), [cópia paralela](#parallel-copy)e [cópia em etapas](#staged-copy);
* [Diretrizes de ajuste de desempenho](#performance-tuning-steps) sobre como ajustar o desempenho e os fatores principais que podem afetar o desempenho da cópia.

> [!NOTE]
> Se você não estiver familiarizado com a atividade de cópia em geral, consulte [mover dados usando a atividade de cópia](data-factory-data-movement-activities.md) antes de ler este artigo.
>

## <a name="performance-reference"></a>Referência de desempenho

Como referência, a tabela abaixo mostra o número da taxa de transferência de cópia em MBps para os pares de origem e coletor especificados com base no teste interno. Para comparação, ele também demonstra como as diferentes configurações de [unidades de movimentação de dados na nuvem](#cloud-data-movement-units) ou a [escalabilidade do gateway de gerenciamento de dados](data-factory-data-management-gateway-high-availability-scalability.md) (vários nós de gateway) podem ajudar no desempenho da cópia.

![Matriz de desempenho](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>No Azure Data Factory versão 1, as unidades de movimentação de dados de nuvem mínimas para a cópia da nuvem para a nuvem são duas. Se não for especificado, consulte unidades de movimentação de dados padrão sendo usadas em [unidades de movimentação de dados de nuvem](#cloud-data-movement-units).

**Pontos a serem observados:**
* A taxa de transferência é calculada usando a seguinte fórmula: [tamanho dos dados lidos da origem]/[duração da execução da atividade de cópia].
* Os números de referência de desempenho na tabela foram medidos usando o conjunto de dados [TPC-H](http://www.tpc.org/tpch/) em uma única execução da atividade de cópia.
* Nos armazenamentos de dados do Azure, a origem e o coletor estão na mesma região do Azure.
* Para a cópia híbrida entre armazenamentos de dados locais e na nuvem, cada nó de gateway estava em execução em um computador separado do armazenamento de dados local com a especificação abaixo. Quando uma única atividade estava em execução no gateway, a operação de cópia consumiu apenas uma pequena parte da CPU, da memória ou da largura de banda da rede do computador de teste. Saiba mais em [consideração para gerenciamento de dados gateway](#considerations-for-data-management-gateway).
    <table>
    <tr>
        <td>CPU</td>
        <td>32 núcleos 2,20 GHz Intel Xeon E5-2660 v2</td>
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
| Copiar dados entre armazenamentos baseados em arquivo | Entre 4 e 16, dependendo do número e do tamanho dos arquivos. |
| Todos os outros cenários de cópia | 4 |

Para substituir esse padrão, especifique um valor para a propriedade **cloudDataMovementUnits** da seguinte maneira. Os **valores permitidos** para a propriedade **cloudDataMovementUnits** são 2, 4, 8, 16, 32. O **número real de DMUs de nuvem** que a operação de cópia usa em tempo de execução é igual ou menor que o valor configurado, dependendo do seu padrão de dados. Para obter informações sobre o nível de obtenção de desempenho que você pode obter ao configurar mais unidades para uma origem de cópia específica e um coletor, consulte a [referência de desempenho](#performance-reference).

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
Você pode usar a propriedade **parallelCopies** para indicar o paralelismo que você deseja que a atividade de cópia use. Você pode considerar essa propriedade como o número máximo de threads na atividade de cópia que pode ler de sua origem ou gravar em armazenamentos de dados de coletor em paralelo.

Para cada execução de atividade de cópia, Data Factory determina o número de cópias paralelas a serem usadas para copiar dados do armazenamento de dados de origem e para o armazenamento de dados de destino. O número padrão de cópias paralelas que ele usa depende do tipo de fonte e do coletor que você está usando.

| Origem e coletor | Contagem de cópia paralela padrão determinada pelo serviço |
| --- | --- |
| Copiar dados entre armazenamentos baseados em arquivo (armazenamento de BLOBs; Data Lake Store; Amazon S3; um sistema de arquivos local; um HDFS local) |Entre 1 e 32. Depende do tamanho dos arquivos e do número de DMUs (unidades de movimentação de dados de nuvem) usadas para copiar dados entre dois armazenamentos de dados de nuvem ou a configuração física do computador do gateway usada para uma cópia híbrida (para copiar dados de ou para um armazenamento de dados local). |
| Copiar dados de **qualquer armazenamento de dados de origem para o armazenamento de tabelas do Azure** |4 |
| Todos os outros pares de origem e coletor |1 |

Normalmente, o comportamento padrão deve fornecer a melhor taxa de transferência. No entanto, para controlar a carga em computadores que hospedam seus armazenamentos de dados ou para ajustar o desempenho da cópia, você pode optar por substituir o valor padrão e especificar um valor para a propriedade **parallelCopies** . O valor deve estar entre 1 e 32 (ambos incluídos). No tempo de execução, para obter o melhor desempenho, a atividade de cópia usa um valor menor ou igual ao valor que você definiu.

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

* Quando você copia dados entre repositórios baseados em arquivo, o **parallelCopies** determina o paralelismo no nível de arquivo. O agrupamento dentro de um único arquivo ocorreria abaixo de forma automática e transparente, e ele foi projetado para usar o melhor tamanho de bloco adequado para um determinado tipo de armazenamento de dados de origem para carregar dados em paralelo e ortogonal em parallelCopies. O número real de cópias paralelas que o serviço de movimentação de dados usa para a operação de cópia em tempo de execução não é mais do que o número de arquivos que você tem. Se o comportamento de cópia for **mergefile**, a atividade de cópia não poderá tirar proveito do paralelismo de nível de arquivo.
* Quando você especificar um valor para a propriedade **parallelCopies** , considere o aumento de carga nos armazenamentos de dados de origem e de coletor, e para o gateway se ele for uma cópia híbrida. Isso acontece especialmente quando você tem várias atividades ou execuções simultâneas das mesmas atividades executadas no mesmo armazenamento de dados. Se você observar que o armazenamento de dados ou o gateway está sobrecarregado com a carga, diminua o valor de **parallelCopies** para aliviar a carga.
* Quando você copia dados de armazenamentos que não são baseados em arquivo para armazenamentos que são baseados em arquivo, o serviço de movimentação de dados ignora a propriedade **parallelCopies** . Mesmo se o paralelismo for especificado, ele não será aplicado nesse caso.

> [!NOTE]
> Você deve usar Gerenciamento de Dados gateway versão 1,11 ou posterior para usar o recurso **parallelCopies** ao fazer uma cópia híbrida.
>
>

Para usar melhor essas duas propriedades e aprimorar sua taxa de transferência de movimentação de dados, consulte os casos de uso de exemplo. Você não precisa configurar o **parallelCopies** para aproveitar o comportamento padrão. Se você configurar e **parallelCopies** for muito pequeno, várias DMUs de nuvem poderão não ser totalmente utilizadas.

### <a name="billing-impact"></a>Impacto de cobrança
É **importante** lembrar que você é cobrado com base no tempo total da operação de cópia. Se um trabalho de cópia usado para levar uma hora com uma unidade de nuvem e agora levar 15 minutos com quatro unidades de nuvem, a cobrança geral permanecerá quase a mesma. Por exemplo, você usa quatro unidades de nuvem. A primeira unidade de nuvem passa 10 minutos, a segunda, 10 minutos, a terceira, 5 minutos e a quarta, 5 minutos, tudo em uma atividade de cópia executada. Você é cobrado pelo tempo total de cópia (movimentação de dados), que é 10 + 10 + 5 + 5 = 30 minutos. O uso de **parallelCopies** não afeta a cobrança.

## <a name="staged-copy"></a>Cópia em etapas
Ao copiar dados de um armazenamento de dados de origem para um armazenamento de dados de coletor, você pode optar por usar o armazenamento de BLOBs como um armazenamento de preparo provisório. O preparo é especialmente útil nos seguintes casos:

1. **Você deseja ingerir dados de vários armazenamentos de dados para SQL data warehouse por meio do polybase**. O SQL Data Warehouse usa o polybase como um mecanismo de alta taxa de transferência para carregar uma grande quantidade de dados em SQL Data Warehouse. No entanto, os dados de origem devem estar no armazenamento de BLOBs e devem atender a critérios adicionais. Ao carregar dados de um armazenamento de dados diferente do armazenamento de BLOBs, você pode ativar a cópia de dados por meio do armazenamento de blobs de preparo provisório. Nesse caso, Data Factory executa as transformações de dados necessárias para garantir que ele atenda aos requisitos do polybase. Em seguida, ele usa o polybase para carregar dados em SQL Data Warehouse. Para obter mais detalhes, consulte [usar o polybase para carregar dados no Azure SQL data warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). Para obter instruções com um caso de uso, consulte [carregar 1 TB no Azure SQL data warehouse menos de 15 minutos com Azure data Factory](data-factory-load-sql-data-warehouse.md).
2. **Às vezes, demora um pouco para executar uma movimentação de dados híbrido (ou seja, copiar entre um armazenamento de dados local e um armazenamento de dados de nuvem) em uma conexão de rede lenta**. Para melhorar o desempenho, você pode compactar os dados locais para que leve menos tempo para mover dados para o armazenamento de dados de preparo na nuvem. Em seguida, você pode descompactar os dados no armazenamento de preparo antes de carregá-los no armazenamento de dados de destino.
3. **Você não quer abrir portas que não sejam a porta 80 e a porta 443 em seu firewall, devido a políticas de ti corporativas**. Por exemplo, ao copiar dados de um armazenamento de dados local para um coletor de banco de dados SQL do Azure ou um coletor de SQL Data Warehouse do Azure, você precisa ativar a comunicação TCP de saída na porta 1433 para o Firewall do Windows e o firewall corporativo. Nesse cenário, aproveite o gateway para primeiro copiar dados para uma instância de preparo de armazenamento de BLOBs por HTTP ou HTTPS na porta 443. Em seguida, carregue os dados no banco de dados SQL ou SQL Data Warehouse do preparo do armazenamento de BLOBs. Nesse fluxo, você não precisa habilitar a porta 1433.

### <a name="how-staged-copy-works"></a>Como funciona a cópia em etapas
Quando você ativa o recurso de preparo, primeiro os dados são copiados do armazenamento de dados de origem para o armazenamento de dados de preparo (Traga seu próprio). Em seguida, os dados são copiados do armazenamento de dados de preparo para o armazenamento de dados do coletor. Data Factory gerencia automaticamente o fluxo de duas etapas para você. Data Factory também limpa dados temporários do armazenamento de preparo após a conclusão da movimentação de dados.

No cenário de cópia de nuvem (os armazenamentos de dados de origem e de coletor estão na nuvem), o gateway não é usado. O serviço de Data Factory executa as operações de cópia.

![Cópia em etapas: cenário de nuvem](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

No cenário de cópia híbrida (a origem é local e o coletor está na nuvem), o gateway move os dados do armazenamento de dados de origem para um armazenamento de dados de preparo. Data Factory serviço move dados do armazenamento de dados de preparo para o armazenamento de dados do coletor. A cópia de dados de um armazenamento de dados de nuvem para um armazenamento de dados local por meio de preparo também tem suporte com o fluxo invertido.

![Cópia em etapas: cenário híbrido](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Ao ativar a movimentação de dados usando um armazenamento de preparo, você pode especificar se deseja que os dados sejam compactados antes de mover dados do armazenamento de dados de origem para um armazenamento de dados provisório ou de preparo e, em seguida, descompactados antes de mover dados de dados provisórios ou de preparo armazenar no repositório de dados do coletor.

No momento, não é possível copiar dados entre dois armazenamentos de dados locais usando um armazenamento de preparo. Esperamos que essa opção esteja disponível em breve.

### <a name="configuration"></a>Configuração
Defina a configuração **enableStaging** na atividade de cópia para especificar se deseja que os dados sejam preparados no armazenamento de BLOBs antes de carregá-los em um armazenamento de dados de destino. Quando você definir **enableStaging** como true, especifique as propriedades adicionais listadas na tabela a seguir. Se você não tiver uma, também precisará criar um armazenamento do Azure ou um serviço vinculado à assinatura de acesso compartilhado de armazenamento para preparo.

| Propriedade | Descrição | Valor predefinido | Necessário |
| --- | --- | --- | --- |
| **enableStaging** |Especifique se deseja copiar dados por meio de um repositório de preparo provisório. |Falso |Não |
| **linkedServiceName** |Especifique o nome de um serviço vinculado [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) , que se refere à instância de armazenamento que você usa como um armazenamento de preparo provisório. <br/><br/> Você não pode usar o armazenamento com uma assinatura de acesso compartilhado para carregar dados em SQL Data Warehouse por meio do polybase. Você pode usá-lo em todos os outros cenários. |N/D |Sim, quando **enableStaging** é definido como true |
| **Multi-Path** |Especifique o caminho de armazenamento de BLOBs que você deseja que contenha os dados preparados. Se você não fornecer um caminho, o serviço criará um contêiner para armazenar dados temporários. <br/><br/> Especifique um caminho somente se você usar o armazenamento com uma assinatura de acesso compartilhado ou se precisar que dados temporários estejam em um local específico. |N/D |Não |
| **enableCompression** |Especifica se os dados devem ser compactados antes de serem copiados para o destino. Essa configuração reduz o volume de dados que está sendo transferido. |Falso |Não |

Aqui está um exemplo de definição de atividade de cópia com as propriedades descritas na tabela anterior:

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
Você é cobrado com base em duas etapas: copiar duração e copiar tipo.

* Ao usar o preparo durante uma cópia de nuvem (copiando dados de um armazenamento de dados de nuvem para outro armazenamento de dados de nuvem), você será cobrado pela [soma da duração da cópia para a etapa 1 e etapa 2] x [preço unitário da cópia de nuvem].
* Ao usar o preparo durante uma cópia híbrida (copiando dados de um armazenamento de dados local para um armazenamento de dados de nuvem), você será cobrado por [duração da cópia híbrida] x [preço unitário da cópia híbrida] + [duração da cópia da nuvem] x [preço unitário da cópia de nuvem].

## <a name="performance-tuning-steps"></a>Etapas de ajuste de desempenho
Sugerimos que você siga estas etapas para ajustar o desempenho do seu serviço de Data Factory com a atividade de cópia:

1. **Estabelecer uma linha de base**. Durante a fase de desenvolvimento, teste seu pipeline usando a atividade de cópia em um exemplo de dados representativos. Você pode usar o [modelo de divisão](data-factory-scheduling-and-execution.md) de data Factory para limitar a quantidade de dados com os quais você trabalha.

   Colete características de desempenho e tempo de execução usando o **aplicativo de monitoramento e gerenciamento**. Escolha **monitorar & gerenciar** em seu home page de data Factory. No modo de exibição de árvore, escolha o **conjunto de resultados de saída**. Na lista **janelas de atividades** , escolha a execução da atividade de cópia. **Janelas de atividades** lista a duração da atividade de cópia e o tamanho dos dados que são copiados. A taxa de transferência é listada no **Gerenciador de janelas de atividades**. Para saber mais sobre o aplicativo, consulte [monitorar e gerenciar Azure data Factory pipelines usando o aplicativo de monitoramento e gerenciamento](data-factory-monitor-manage-app.md).

   ![Detalhes da execução da atividade](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Posteriormente neste artigo, você pode comparar o desempenho e a configuração do seu cenário para copiar a [referência de desempenho](#performance-reference) da atividade de nossos testes.
2. **Diagnostique e otimize o desempenho**. Se o desempenho que você observar não atender às suas expectativas, você precisará identificar gargalos de desempenho. Em seguida, otimize o desempenho para remover ou reduzir o efeito dos afunilamentos. Uma descrição completa do diagnóstico de desempenho está além do escopo deste artigo, mas aqui estão algumas considerações comuns:

   * Recursos de desempenho:
     * [Cópia paralela](#parallel-copy)
     * [Unidades de movimentação de dados na nuvem](#cloud-data-movement-units)
     * [Cópia em etapas](#staged-copy)
     * [Escalabilidade do gateway de Gerenciamento de Dados](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Data Management Gateway](#considerations-for-data-management-gateway)
   * [Origem](#considerations-for-the-source)
   * [Coletar](#considerations-for-the-sink)
   * [Serialização e desserialização](#considerations-for-serialization-and-deserialization)
   * [Çã](#considerations-for-compression)
   * [Mapeamento de colunas](#considerations-for-column-mapping)
   * [Outras considerações](#other-considerations)
3. **Expanda a configuração para todo o conjunto de dados**. Quando estiver satisfeito com os resultados e o desempenho da execução, você poderá expandir a definição e o período ativo do pipeline para abranger todo o seu conjunto de dados.

## <a name="considerations-for-data-management-gateway"></a>Considerações para Gerenciamento de Dados gateway
**Configuração do gateway**: Recomendamos que você use um computador dedicado para hospedar gerenciamento de dados gateway. Consulte [considerações para usar o gateway de gerenciamento de dados](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Monitoramento e expansão/saída do gateway**: um único gateway lógico com um ou mais nós de gateway pode atender a várias execuções de atividade de cópia ao mesmo tempo simultaneamente. Você pode exibir o instantâneo quase em tempo real da utilização de recursos (CPU, memória, rede (entrada/saída), etc.) em um computador de gateway, bem como o número de trabalhos simultâneos em execução versus limite no portal do Azure, consulte [monitorar gateway no portal](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Se você tiver muita necessidade de movimentação de dados híbridos com um grande número de execuções simultâneas da atividade de cópia ou com grandes volumes de dados a serem copiados, considere a possibilidade de [escalar verticalmente ou escalar horizontalmente o gateway](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) para utilizar melhor seu recurso ou para provisionar mais recursos para capacitar CopiarObjeto.

## <a name="considerations-for-the-source"></a>Considerações para a origem
### <a name="general"></a>Geral
Certifique-se de que o armazenamento de dados subjacente não esteja sobrecarregado por outras cargas de trabalho em execução no ou em relação a ele.

Para armazenamentos de dados da Microsoft, consulte [Tópicos de monitoramento e ajuste](#performance-reference) específicos para armazenamentos de dados e ajude você a entender as características de desempenho do armazenamento de dados, minimizar os tempos de resposta e maximizar a taxa de transferência.

Se você copiar dados do armazenamento de BLOBs para SQL Data Warehouse, considere o uso do **polybase** para impulsionar o desempenho. Consulte [usar o polybase para carregar dados no Azure SQL data warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter detalhes. Para obter instruções com um caso de uso, consulte [carregar 1 TB no Azure SQL data warehouse menos de 15 minutos com Azure data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Armazenamentos de dados baseados em arquivo
*(Inclui armazenamento de BLOBs, Data Lake Store, Amazon S3, sistemas de arquivos locais e HDFS local)*

* **Tamanho médio do arquivo e contagem de arquivos**: a atividade de cópia transfere dados um arquivo por vez. Com a mesma quantidade de dados a serem movidos, a taxa de transferência geral é menor se os dados consistem em muitos arquivos pequenos, em vez de alguns arquivos grandes devido à fase de inicialização de cada arquivo. Portanto, se possível, combine arquivos pequenos em arquivos maiores para obter uma taxa de transferência mais alta.
* **Formato e compactação de arquivo**: para obter mais maneiras de melhorar o desempenho, consulte as seções [Considerações sobre serialização e desserialização](#considerations-for-serialization-and-deserialization) e [considerações para compactação](#considerations-for-compression) .
* Para o cenário do **sistema de arquivos local** , no qual **Gerenciamento de dados gateway** é necessário, consulte a seção [considerações para o gateway de gerenciamento de dados](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Armazenamentos de dados relacionais
*(Inclui banco de dados SQL; SQL Data Warehouse; Amazon redshift; Bancos de dados SQL Server; e bancos de dados Oracle, MySQL, DB2, Teradata, Sybase e PostgreSQL, etc.)*

* **Padrão de dados**: o esquema de tabela afeta a produtividade da cópia. Um tamanho de linha grande oferece um desempenho melhor do que o tamanho de linha pequeno, para copiar a mesma quantidade de dados. O motivo é que o banco de dados pode recuperar com mais eficiência menos lotes de dado que contenham menos linhas.
* **Consulta ou procedimento armazenado**: Otimize a lógica da consulta ou procedimento armazenado que você especificar na origem da atividade de cópia para buscar dados com mais eficiência.
* Para **bancos de dados relacionais locais**, como SQL Server e Oracle, que exigem o uso do **Gateway de gerenciamento de dados**, consulte a seção considerações para o gateway de gerenciamento de dados.

## <a name="considerations-for-the-sink"></a>Considerações para o coletor
### <a name="general"></a>Geral
Certifique-se de que o armazenamento de dados subjacente não esteja sobrecarregado por outras cargas de trabalho em execução no ou em relação a ele.

Para armazenamentos de dados da Microsoft, consulte os [Tópicos de monitoramento e ajuste](#performance-reference) específicos para armazenamentos de dados. Esses tópicos podem ajudá-lo a entender as características de desempenho do armazenamento de dados e como minimizar os tempos de resposta e maximizar a taxa de transferência.

Se você estiver copiando dados do **armazenamento de BLOBs** para **SQL data warehouse**, considere usar o **polybase** para aumentar o desempenho. Consulte [usar o polybase para carregar dados no Azure SQL data warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter detalhes. Para obter instruções com um caso de uso, consulte [carregar 1 TB no Azure SQL data warehouse menos de 15 minutos com Azure data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Armazenamentos de dados baseados em arquivo
*(Inclui armazenamento de BLOBs, Data Lake Store, Amazon S3, sistemas de arquivos locais e HDFS local)*

* **Comportamento de cópia**: se você copiar dados de um armazenamento de dados baseado em arquivo diferente, a atividade de cópia terá três opções por meio da propriedade **copyBehavior** . Preserva a hierarquia, nivela a hierarquia ou mescla arquivos. A preservação ou o nivelamento da hierarquia tem pouca ou nenhuma sobrecarga de desempenho, mas a mesclagem de arquivos faz com que a sobrecarga de desempenho aumente.
* **Formato e compactação de arquivo**: Veja as seções [Considerações sobre serialização e desserialização](#considerations-for-serialization-and-deserialization) e [considerações para compactação](#considerations-for-compression) para obter mais maneiras de melhorar o desempenho.
* **Armazenamento de BLOBs**: atualmente, o armazenamento de BLOBs dá suporte apenas a blobs de blocos para transferência e produtividade de dados otimizadas.
* Para cenários de **sistemas de arquivos locais** que exigem o uso de **Gerenciamento de dados gateway**, consulte a seção [considerações para o gateway de gerenciamento de dados](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Armazenamentos de dados relacionais
*(Inclui o banco de dados SQL, o SQL Data Warehouse, os bancos de dados SQL Server e os bancos de dados Oracle)*

* **Comportamento de cópia**: dependendo das propriedades que você definiu para **sqlsink**, a atividade de cópia grava dados no banco de dado de destino de diferentes maneiras.
  * Por padrão, o serviço de movimentação de dados usa a API de cópia em massa para inserir dados no modo de acréscimo, o que fornece o melhor desempenho.
  * Se você configurar um procedimento armazenado no coletor, o banco de dados aplicará o dado uma linha por vez, em vez de um carregamento em massa. O desempenho cai significativamente. Se o conjunto de dados for grande, quando aplicável, considere a possibilidade de alternar para o uso da propriedade **sqlWriterCleanupScript** .
  * Se você configurar a propriedade **sqlWriterCleanupScript** para cada execução da atividade de cópia, o serviço disparará o script e, em seguida, você usará a API de cópia em massa para inserir os dados. Por exemplo, para substituir a tabela inteira com os dados mais recentes, você pode especificar um script para primeiro excluir todos os registros antes de carregar em massa os novos dados da origem.
* **Padrão de dados e tamanho do lote**:
  * O esquema de tabela afeta a produtividade da cópia. Para copiar a mesma quantidade de dados, um tamanho de linha grande oferece um desempenho melhor do que um pequeno tamanho de linha, pois o banco de dados pode confirmar com mais eficiência menos lotes de dado.
  * A atividade de cópia insere dados em uma série de lotes. Você pode definir o número de linhas em um lote usando a propriedade **writeBatchSize** . Se os dados tiverem linhas pequenas, você poderá definir a propriedade **writeBatchSize** com um valor mais alto para se beneficiar da sobrecarga de lote inferior e da taxa de transferência mais alta. Se o tamanho da linha de seus dados for grande, tenha cuidado ao aumentar o **writeBatchSize**. Um valor alto pode levar a uma falha de cópia causada pela sobrecarga do banco de dados.
* Para **bancos de dados relacionais locais** como SQL Server e Oracle, que exigem o uso do gateway de **Gerenciamento de dados**, consulte a seção [considerações para o gateway de gerenciamento de dados](#considerations-for-data-management-gateway) .

### <a name="nosql-stores"></a>Repositórios NoSQL
*(Inclui armazenamento de tabela e Azure Cosmos DB)*

* Para **armazenamento de tabelas**:
  * **Partição**: gravar dados em partições intercaladas degrada drasticamente o desempenho. Classifique os dados de origem por chave de partição para que os dados sejam inseridos com eficiência em uma partição após a outra ou ajuste a lógica para gravar os dados em uma única partição.
* Por **Azure Cosmos DB**:
  * **Tamanho do lote**: a propriedade **writeBatchSize** define o número de solicitações paralelas para o serviço Azure Cosmos DB para criar documentos. Você pode esperar um melhor desempenho ao aumentar o **writeBatchSize** porque mais solicitações paralelas são enviadas para Azure Cosmos DB. No entanto, observe a limitação ao gravar em Azure Cosmos DB (a mensagem de erro é "a taxa de solicitação é grande"). Vários fatores podem causar limitação, incluindo o tamanho do documento, o número de termos nos documentos e a política de indexação da coleção de destino. Para obter uma taxa de transferência de cópia mais alta, considere usar uma coleção melhor, por exemplo, S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Considerações sobre serialização e desserialização
A serialização e a desserialização podem ocorrer quando o conjunto de dados de entrada ou o conjunto de dados de saída é um arquivo. Consulte [formatos de arquivo e compactação com suporte](data-factory-supported-file-and-compression-formats.md) com detalhes sobre formatos de arquivo com suporte por atividade de cópia.

**Comportamento da cópia**:

* Copiando arquivos entre armazenamentos de dados baseados em arquivo:
  * Quando os conjuntos de dados de entrada e saída têm as mesmas ou nenhuma configuração de formato de arquivo, o serviço de movimentação de dados executa uma cópia binária sem nenhuma serialização ou desserialização. Você verá uma taxa de transferência mais alta em comparação com o cenário, no qual as configurações de formato de arquivo de origem e de coletor são diferentes umas das outras.
  * Quando os conjuntos de dados de entrada e saída estiverem no formato de texto e apenas o tipo de codificação for diferente, o serviço de movimentação de dados apenas fará a conversão de codificação. Ele não faz nenhuma serialização e desserialização, o que causa alguma sobrecarga de desempenho em comparação com uma cópia binária.
  * Quando os conjuntos de dados de entrada e saída têm diferentes formatos de arquivo ou configurações diferentes, como delimitadores, o serviço de movimentação de dados desserializa os dados de origem para transmitir, transformar e, em seguida, serializa-os no formato de saída que você indicou. Essa operação resulta em uma sobrecarga de desempenho muito mais significativa em comparação com outros cenários.
* Quando você copia arquivos de/para um armazenamento de dados que não é baseado em arquivo (por exemplo, de um armazenamento baseado em arquivo para um relational store), a etapa de serialização ou desserialização é necessária. Essa etapa resulta em uma sobrecarga de desempenho significativa.

**Formato de arquivo**: o formato de arquivo escolhido pode afetar o desempenho da cópia. Por exemplo, Avro é um formato binário compacto que armazena metadados com dados. Ele tem amplo suporte no ecossistema do Hadoop para processamento e consulta. No entanto, o Avro é mais caro para serialização e desserialização, o que resulta em uma taxa de transferência de cópia mais baixa em comparação com o formato de texto. Faça sua escolha de formato de arquivo em todo o fluxo de processamento de forma holística. Comece com em que formato os dados são armazenados, armazenamentos de dados de origem ou que serão extraídos de sistemas externos; o melhor formato para armazenamento, processamento analítico e consulta; e em que formato os dados devem ser exportados para marts de dados para ferramentas de relatório e visualização. Às vezes, um formato de arquivo inferior ao desempenho de leitura e gravação pode ser uma boa opção quando você considera o processo analítico geral.

## <a name="considerations-for-compression"></a>Considerações sobre compactação
Quando o conjunto de dados de entrada ou saída é um arquivo, você pode definir a atividade de cópia para executar a compactação ou descompactação à medida que ele grava dados no destino. Ao escolher a compactação, você faz uma compensação entre entrada/saída (e/s) e CPU. Compactação dos custos de dados extra em recursos de computação. Mas, em retorno, reduz a e/s de rede e O armazenamento. Dependendo de seus dados, você poderá ver um aumento na produtividade geral da cópia.

**Codec**: a atividade de cópia dá suporte a tipos de compactação Gzip, BZIP2 e deflate. O Azure HDInsight pode consumir todos os três tipos para processamento. Cada codec de compactação tem vantagens. Por exemplo, bzip2 tem a menor taxa de transferência de cópia, mas você obtém o melhor desempenho de consulta de Hive com bzip2, pois você pode dividi-lo para processamento. Gzip é a opção mais equilibrada e é usado com mais frequência. Escolha o codec que melhor se adapta a seu cenário de ponta a ponta.

**Nível**: você pode escolher entre duas opções para cada codec de compactação: mais rápido compactado e compactado de maneira ideal. A opção compactada mais rápida compacta os dados o mais rápido possível, mesmo que o arquivo resultante não seja compactado de maneira ideal. A opção de compactação ideal passa mais tempo na compactação e produz uma quantidade mínima de dados. Você pode testar as duas opções para ver quais fornecem melhor desempenho geral no seu caso.

**Uma consideração**: para copiar uma grande quantidade de dados entre um repositório local e a nuvem, considere o uso do armazenamento de BLOBs provisórios com compactação. O uso do armazenamento provisório é útil quando a largura de banda de sua rede corporativa e seus serviços do Azure são o fator de limitação e você deseja que o conjunto de dados de entrada e o conjunto de dados de saída sejam ambos no formato descompactado. Mais especificamente, você pode dividir uma única atividade de cópia em duas atividades de cópia. A primeira atividade de cópia copia da origem para um blob provisório ou de preparo em formato compactado. A segunda atividade de cópia copia os dados compactados do preparo e, em seguida, descompacta enquanto grava no coletor.

## <a name="considerations-for-column-mapping"></a>Considerações sobre o mapeamento de coluna
Você pode definir a propriedade **ColumnMappings** na atividade de cópia para mapear todos ou um subconjunto das colunas de entrada para as colunas de saída. Depois que o serviço de movimentação de dados lê os dados da origem, ele precisa executar o mapeamento de coluna nos dados antes de gravar os dados no coletor. Esse processamento extra reduz a taxa de transferência de cópia.

Se o seu armazenamento de dados de origem for passível de consulta, por exemplo, se for um relational store como o SQL Database ou SQL Server, ou se for um repositório NoSQL como armazenamento de tabelas ou Azure Cosmos DB, considere enviar a filtragem de coluna e reordenar a lógica para a propriedade de **consulta** em vez de usar o mapeamento de coluna. Dessa forma, a projeção ocorre enquanto o serviço de movimentação de dados lê dados do armazenamento de dados de origem, onde é muito mais eficiente.

## <a name="other-considerations"></a>Outras considerações
Se o tamanho dos dados que você deseja copiar for grande, você poderá ajustar sua lógica de negócios para particionar ainda mais os dados usando o mecanismo de divisão em Data Factory. Em seguida, Agende a atividade de cópia para executar com mais frequência para reduzir o tamanho dos dados para cada execução da atividade de cópia.

Tenha cuidado com o número de conjuntos de dados e atividades de cópia que exigem Data Factory para o conector para o mesmo armazenamento de dados ao mesmo tempo. Muitos trabalhos de cópia simultâneos podem limitar um armazenamento de dados e levar a degradar o desempenho, copiar repetições internas de trabalho e, em alguns casos, falhas de execução.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Cenário de exemplo: copiar de um SQL Server local para o armazenamento de BLOBs
**Cenário**: um pipeline é criado para copiar dados de um SQL Server local para o armazenamento de BLOBs no formato CSV. Para tornar o trabalho de cópia mais rápido, os arquivos CSV devem ser compactados no formato bzip2.

**Teste e análise**: a taxa de transferência da atividade de cópia é menor que 2 Mbps, o que é muito mais lento do que o parâmetro de comparação de desempenho.

**Análise e ajuste de desempenho**: para solucionar o problema de desempenho, vejamos como os dados são processados e movidos.

1. **Ler dados**: gateway abre uma conexão para SQL Server e envia a consulta. SQL Server responde enviando o fluxo de dados para o gateway por meio da intranet.
2. **Serializar e compactar dados**: o gateway serializa o fluxo de dados para o formato CSV e compacta os dados em um fluxo bzip2.
3. **Gravar dados**: o gateway carrega o fluxo de bzip2 no armazenamento de BLOBs pela Internet.

Como você pode ver, os dados estão sendo processados e movidos de maneira sequencial de streaming: SQL Server > LAN > gateway > WAN > armazenamento de BLOBs. **O desempenho geral é restringido pela taxa de transferência mínima no pipeline**.

![Fluxo de dados](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Um ou mais dos seguintes fatores podem causar o afunilamento de desempenho:

* **Fonte**: SQL Server em si tem baixa taxa de transferência devido a cargas pesadas.
* **Gateway de gerenciamento de dados**:
  * **LAN**: o gateway está localizado longe da máquina SQL Server e tem uma conexão de baixa largura de banda.
  * **Gateway**: o gateway atingiu suas limitações de carga para executar as seguintes operações:
    * **Serialização**: a serialização do fluxo de dados para o formato CSV tem uma taxa de transferência lenta.
    * **Compactação**: você escolheu um codec de compactação lenta (por exemplo, BZIP2, que é de 2,8 Mbps com Core i7).
  * **Wan**: a largura de banda entre a rede corporativa e os serviços do Azure é baixa (por exemplo, T1 = 1.544 kbps; T2 = 6.312 kbps).
* **Coletor**: o armazenamento de BLOBs tem baixa taxa de transferência. (Esse cenário é improvável porque seu SLA garante um mínimo de 60 MBps.)

Nesse caso, a compactação de dados bzip2 pode estar diminuindo o pipeline inteiro. Alternar para um codec de compactação Gzip pode facilitar esse afunilamento.

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

* Armazenamento do Azure (incluindo armazenamento de BLOB e armazenamento de tabelas): lista de verificação de [metas de escalabilidade de armazenamento](../../storage/common/storage-scalability-targets.md) do Azure e [desempenho do armazenamento do Azure](../../storage/common/storage-performance-checklist.md)
* Banco de dados SQL do Azure: você pode [monitorar o desempenho](../../sql-database/sql-database-single-database-monitor.md) e verificar a porcentagem de DTU (unidade de transação do banco de dados)
* SQL Data Warehouse do Azure: seu recurso é medido em unidades de data warehouse (DWUs); consulte [gerenciar poder de computação no Azure SQL data warehouse (visão geral)](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [níveis de desempenho no Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* SQL Server local: [monitorar e ajustar o desempenho](https://msdn.microsoft.com/library/ms189081.aspx)
* Servidor de arquivos local: [ajuste de desempenho para servidores de arquivos](https://msdn.microsoft.com/library/dn567661.aspx)
