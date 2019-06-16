---
title: Copie o guia de sintonização de desempenho de atividade e | Documentos da Microsoft
description: Saiba mais sobre os principais fatores que afetam o desempenho de movimento de dados no Azure Data Factory, quando usa a atividade de cópia.
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
ms.openlocfilehash: ec8c58e4ced0d8df958e242b9c1671aeed8c2ee6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60488225"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Copie o guia de Otimização e desempenho de atividade

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory, que está a utilizar:"]
> * [Versão 1](data-factory-copy-activity-performance.md)
> * [Versão 2 (versão atual)](../copy-activity-performance.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [copie o desempenho e atividade de guia de ajuste do Data Factory](../copy-activity-performance.md).

Atividade de cópia da fábrica de dados do Azure fornece um primeira classe seguros, fiáveis e elevado desempenho de carregamento de dados solução. Permite a cópia dezenas de terabytes de dados todos os dias numa variedade de avançados de nuvem e no local arquivos de dados. Desempenho de carregamento de dados de incrivelmente rápida são a chave para garantir que possa se concentrar no problema de "grandes volumes de dados" core: desenvolvimento de soluções de análise avançada e obtenção de informações profunda de todos esses dados.

O Azure fornece um conjunto de nível empresarial de soluções de armazém de dados e armazenamento de dados e atividade de cópia oferece um experiência fácil de configurar e configurar o carregamento de dados altamente otimizados. Com apenas uma atividade de cópia única, pode obter:

* A carregar dados para o **do Azure SQL Data Warehouse** na **1,2 GBps**. Para obter instruções com um caso de utilização, consulte [carregar 1 TB para o Azure SQL Data Warehouse menos de 15 minutos com o Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* A carregar dados para o **armazenamento de Blobs do Azure** em **de 1,0 GBps**
* A carregar dados para o **do Azure Data Lake Store** em **de 1,0 GBps**

Este artigo descreve:

* [Números de referência do desempenho](#performance-reference) para arquivos de dados de origem e sink para ajudar a planear o seu projeto; de suportados
* Funcionalidades que podem aumentar o débito de cópia em cenários diferentes, incluindo [unidades de movimento de dados da cloud](#cloud-data-movement-units), [cópia em paralelo](#parallel-copy), e [cópia faseada](#staged-copy);
* [Guia de sintonização de desempenho](#performance-tuning-steps) sobre como otimizar o desempenho e os principais fatores que podem afetar o desempenho de cópia.

> [!NOTE]
> Se não estiver familiarizado com a atividade de cópia em geral, veja [mover dados com a atividade de cópia](data-factory-data-movement-activities.md) antes de ler este artigo.
>

## <a name="performance-reference"></a>Referência de desempenho

Como uma referência, tabela a seguir mostra o número do débito de cópia em MBps para os pares de origem e sink determinados com base em testes internos. Para comparação, ele também demonstra como diferentes configurações de [unidades de movimento de dados da cloud](#cloud-data-movement-units) ou [escalabilidade do Data Management Gateway](data-factory-data-management-gateway-high-availability-scalability.md) (vários nós de gateway) podem ajudar no desempenho de cópia.

![Matriz de desempenho](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>No Azure Data Factory versão 1, as unidades de movimento de dados de cloud mínimo para a cópia na cloud para a cloud é dois. Se não for especificado, consulte as unidades de movimento de dados predefinido a ser utilizadas numa [unidades de movimento de dados da cloud](#cloud-data-movement-units).

**Pontos a serem observados:**
* Débito é calculado utilizando a seguinte fórmula: [ler tamanho dos dados de origem] / [duração de execução da atividade cópia].
* Os números de referência de desempenho na tabela foram medidos usando [TPC-H](http://www.tpc.org/tpch/) conjunto de dados de uma execução de atividade de cópia única.
* Nos arquivos de dados do Azure, a origem e sink são na mesma região do Azure.
* Para obter uma cópia entre no local e na cloud híbrida armazenamentos de dados, cada nó de gateway estava em execução num computador que estava separado do arquivo de dados no local com abaixo especificação. Quando uma única atividade estiver em execução no gateway, a operação de cópia consumido apenas uma pequena parte de CPU, memória ou largura de banda de rede da máquina de teste. Saiba mais a partir da [consideração para o Data Management Gateway](#considerations-for-data-management-gateway).
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
> Pode alcançar um débito mais elevado ao tirar partido de mais unidades de movimento de dados (DMUs) que não o predefinido DMUs máximos, o que é de 32 para uma atividade de cópia de cloud-para-cloud executar. Por exemplo, com 100 DMUs, pode conseguir copiar dados de Blobs do Azure para o Azure Data Lake Store na **1.0GBps**. Consulte a [unidades de movimento de dados da Cloud](#cloud-data-movement-units) secção para obter detalhes sobre esta funcionalidade e o cenário suportado. Contacte [suporte do Azure](https://azure.microsoft.com/support/) para solicitar mais DMUs.

## <a name="parallel-copy"></a>Cópia paralela
Pode ler dados a partir da origem de ou escrever dados para o destino **em paralelo dentro de uma execução de atividade de cópia**. Esta funcionalidade melhora o débito de uma operação de cópia e reduz o tempo que demora a mover dados.

Esta definição é diferente da **simultaneidade** propriedade na definição da atividade. O **simultaneidade** propriedade determina o número de **execuções de atividade de cópia simultâneos** para processar dados de janelas de atividade diferente (AM de 1 para 2 AM, 2 AM 3 AM, AM de 3 a 4 AM e assim por diante). Esta capacidade é útil quando executar uma carga histórica. A capacidade de cópia paralela aplica-se para uma **única da execução da atividade**.

Vamos examinar um cenário de exemplo. No exemplo a seguir, é necessário processar vários setores do passado. Fábrica de dados executa uma instância da atividade de cópia (executar uma atividade) para cada setor:

* O setor de dados da primeira janela de atividade (AM de 1 para 2 AM) = = > 1 da execução da atividade
* O setor de dados da segunda janela de atividade (AM de 2 a 3 AM) = = > 2 da execução da atividade
* O setor de dados da segunda janela de atividade (AM de 3 a 4 AM) = = > 3 da execução da atividade

E assim sucessivamente.

Neste exemplo, quando o **simultaneidade** valor está definido como 2, **1 da execução da atividade** e **2 da execução da atividade** copiar dados de duas janelas de atividade **simultaneamente** para melhorar o desempenho dos movimentos de dados. No entanto, se vários ficheiros estão associadas à execução de 1 de atividade, o serviço de movimento de dados copia ficheiros da origem para o ficheiro de um destino de cada vez.

### <a name="cloud-data-movement-units"></a>Unidades de movimento de dados na cloud
R **unidade de movimento de dados de cloud (DMU)** é uma medida que representa o poder (uma combinação de CPU, memória e alocação de recursos de rede) de uma única unidade no Data Factory. DMU é aplicável para operações de cópia na cloud para a cloud, mas não numa cópia híbrida.

**As unidades de movimento de dados de cloud mínimo para capacitar a execução de atividade de cópia é dois.** Se não for especificado, a tabela seguinte lista os DMUs padrão usados em cenários de cópia diferentes:

| Cenário de cópia | DMUs predefinido determinados pelo serviço |
|:--- |:--- |
| Copiar dados entre arquivos baseados em ficheiros | Entre 4 e 16 dependendo do número e tamanho dos ficheiros. |
| Todos os outros cenários de cópia | 4 |

Para substituir esta predefinição, especifique um valor para o **cloudDataMovementUnits** propriedade da seguinte forma. O **valores permitidos** para o **cloudDataMovementUnits** propriedade são 2, 4, 8, 16 e 32. O **número real de cloud DMUs** que a operação de cópia utiliza no tempo de execução é igual ou inferior ao valor configurado, dependendo do padrão de dados. Para obter informações sobre o nível de ganho de desempenho poderá obter ao configurar mais unidades para uma origem de cópia específico e de sink, consulte a [referência de desempenho](#performance-reference).

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
> Se precisar de mais cloud DMUs um débito mais elevado, contacte [suporte do Azure](https://azure.microsoft.com/support/). Definição de 8 e superior trabalha, atualmente, apenas quando **copiar vários ficheiros de Blob storage/Data Lake Store/Amazon S3/nuvem SFTP de FTP/na cloud para BLOBs de armazenamento/Data Lake Store/Azure SQL Database**.
>

### <a name="parallelcopies"></a>parallelCopies
Pode utilizar o **parallelCopies** propriedade para indicar o paralelismo que pretende que a atividade de cópia para utilizar. Pode pensar esta propriedade como o número máximo de threads na atividade de cópia pode ler da origem de ou escrever seus arquivos de dados de sink em paralelo.

Para cada atividade de cópia executar, o Data Factory determina o número de cópias paralelas para utilizar para copiar dados a partir da origem de arquivo de dados e para os dados de destino armazenam. O número predefinido de cópias paralelos que utiliza depende do tipo de origem e sink, que está a utilizar.

| Origem e sink | Número de cópias paralela de predefinido determinado pelo serviço |
| --- | --- |
| Copiar dados entre arquivos baseados em ficheiros (armazenamento de BLOBs; Data Lake Store; Amazon S3; um sistema de ficheiros no local; um HDFS no local) |Entre 1 e 32. Depende do tamanho dos ficheiros e o número de unidades de movimento de dados na cloud (DMUs), usado para copiar dados entre dois arquivos de dados em nuvem ou de configuração física da máquina de Gateway utilizada para obter uma cópia híbrida (para copiar dados de ou para um arquivo de dados no local). |
| Copiar dados de **armazenar de qualquer origem de dados para o armazenamento de tabelas do Azure** |4 |
| Todos os outros pares de origem e sink |1 |

Normalmente, o comportamento padrão deve dar a o melhor débito. No entanto controlar a carga nos computadores que alojam os dados armazena, ou para otimizar o desempenho de cópia, pode optar por substituir o valor predefinido e especifique um valor para o **parallelCopies** propriedade. O valor tem de ser entre 1 e 32 (ambos, inclusive). No momento de execução para um melhor desempenho, atividade de cópia utiliza um valor que é menor ou igual ao valor que definir.

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
* Se especificar um valor para o **parallelCopies** propriedade, considere o aumento de carga em seus arquivos de dados de origem e de sink e ao gateway, se se trata de uma cópia híbrida. Isso acontece especialmente quando tem várias atividades ou execuções simultâneas de iguais às atividades que são executados em relação ao mesmo armazenamento de dados. Se notar que o arquivo de dados ou o Gateway está assoberbado com a carga, diminuir os **parallelCopies** valor de modo a poupar a carga.
* Quando copiar dados a partir de arquivos que não são baseados em ficheiros para arquivos que são baseados em ficheiros, o serviço de movimento de dados ignora a **parallelCopies** propriedade. Mesmo que o paralelismo é especificado, não é aplicado neste caso.

> [!NOTE]
> Tem de utilizar o Data Management Gateway versão 1.11 ou posterior para utilizar o **parallelCopies** funcionalidade quando o fizer uma cópia híbrida.
>
>

Para melhor usar essas duas propriedades e para melhorar o débito do movimento de dados, consulte o exemplo de casos de utilização. Não precisa de configurar **parallelCopies** para aproveitar o comportamento padrão. Se configurar e **parallelCopies** é demasiado pequena, vários cloud DMUs poderá não ser totalmente utilizada.

### <a name="billing-impact"></a>Impacto de faturação
Ele possui **importante** lembrar-se de que é cobrado com base no tempo total da operação de cópia. Se uma tarefa de cópia costumava levar uma hora com a unidade de uma cloud e agora demora 15 minutos com quatro unidades de cloud, a fatura global permanece praticamente o mesmo. Por exemplo, utilizar quatro unidades de cloud. A primeira unidade gastar 10 minutos, o um segundo, 10 minutos, o um terceiro, 5 minutos e o quarto, 5 minutos, tudo numa atividade Copy executar. É-lhe cobrada a hora de total da cópia (movimento de dados), que é 10 + 10 + 5 + 5 = 30 minutos. Usando **parallelCopies** não afetam a faturação.

## <a name="staged-copy"></a>Cópia faseada
Quando copia dados de um arquivo de dados de origem para um arquivo de dados de sink, pode optar por utilizar o armazenamento de BLOBs como um armazenamento de teste provisório. Teste é especialmente útil nos seguintes casos:

1. **Pretende ingerir dados de vários arquivos de dados no SQL Data Warehouse através do PolyBase**. O SQL Data Warehouse utiliza o PolyBase como um mecanismo de alto débito para carregar uma grande quantidade de dados para o SQL Data Warehouse. No entanto, tem de ser a origem de dados no armazenamento de BLOBs e tem de cumprir critérios adicionais. Quando carrega dados de um arquivo de dados que não seja o armazenamento de BLOBs, pode ativar copiar por meio de armazenamento de BLOBs de teste provisório de dados. Nesse caso, o Data Factory realiza as transformações de dados necessários para assegurar que cumpre os requisitos do PolyBase. Em seguida, ele utiliza o PolyBase para carregar dados para o SQL Data Warehouse. Para obter mais detalhes, consulte [utilizar o PolyBase para carregar dados para o Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). Para obter instruções com um caso de utilização, consulte [carregar 1 TB para o Azure SQL Data Warehouse menos de 15 minutos com o Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **Por vezes, demora algum tempo para efetuar um movimento de dados híbrida (ou seja, para copiar entre os dados no local de arquivo e um cloud arquivo de dados) através de uma ligação de rede lenta**. Para melhorar o desempenho, é possível compactar os dados no local para que ele demora menos tempo para mover dados para o arquivo de dados de teste na cloud. Em seguida, pode descomprimir os dados no arquivo de teste antes de os carregar para o arquivo de dados de destino.
3. **Não deseja abrir portas sem ser a porta 80 e a porta 443 na firewall, devido a políticas de TI empresariais**. Por exemplo, quando copia dados de um arquivo de dados no local para um sink de base de dados do Azure SQL ou um coletor do Azure SQL Data Warehouse, terá de ativar a comunicação de saída de TCP na porta 1433 para a firewall do Windows e sua firewall empresarial. Neste cenário, tire partido do gateway para o primeiro de cópia de dados para uma instância de teste de armazenamento de BLOBs através de HTTP ou HTTPS na porta 443. Em seguida, carregar os dados na base de dados SQL ou SQL Data Warehouse do teste de armazenamento de Blobs. Neste fluxo, não terá de ativar a porta 1433.

### <a name="how-staged-copy-works"></a>Funciona como faseada de cópia
Quando ativar a funcionalidade de teste, primeiro os dados são copiados do arquivo de dados de origem para o arquivo de dados de teste (traga a sua própria). Em seguida, os dados são copiados do arquivo de dados de teste para o arquivo de dados de sink. Data Factory gere automaticamente o fluxo de duas etapas para. Fábrica de dados limpa também dados temporários, o armazenamento de teste do após o movimento de dados está concluído.

No cenário de cópia na cloud (origem e sink dados arquivos estão na cloud), o gateway não é utilizado. O serviço Data Factory realiza as operações de cópia.

![Cópia faseada: Cenário de nuvem](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

No cenário de cópia híbrida (origem está no local e sink é na cloud), o gateway move dados do arquivo de dados de origem para um arquivo de dados de teste. Serviço do Data Factory move os dados do arquivo de dados de teste para o arquivo de dados de sink. Copiar dados do arquivo de dados na cloud para um arquivo de dados no local através de teste também é suportado com o fluxo revertido.

![Cópia faseada: Cenário híbrido](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Ao ativar o movimento de dados usando um arquivo de teste, pode especificar se pretende que os dados para ser comprimidos antes de mover dados do arquivo de dados de origem para um arquivo de dados intermediárias ou teste e, em seguida, descompactado antes de mover dados de um interim ou dados de teste armazenar para o arquivo de dados de sink.

Atualmente, não é possível copiar dados entre dois arquivos de dados no local através de um arquivo de transição. Esperamos que esta opção para estar disponível em breve.

### <a name="configuration"></a>Configuração
Configurar o **enableStaging** definição na atividade de cópia para especificar se pretende que os dados para ser preparado no armazenamento de BLOBs, antes de carregá-lo num arquivo de dados de destino. Se definir **enableStaging** para verdadeiro, especifique as propriedades adicionais listadas na tabela seguinte. Se não tiver um, terá também de criar um armazenamento do Azure ou o armazenamento partilhado serviço ligado de assinatura de acesso para preparação.

| Propriedade | Descrição | Valor predefinido | Necessário |
| --- | --- | --- | --- |
| **enableStaging** |Especifique se pretende copiar os dados por meio de um arquivo de teste de interim. |Falso |Não |
| **linkedServiceName** |Especifique o nome de um [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) serviço, que se refere à instância do armazenamento que utilizar como um armazenamento de teste provisório ligado. <br/><br/> Não é possível utilizar o armazenamento com uma assinatura de acesso partilhado para carregar dados para o SQL Data Warehouse através do PolyBase. Pode usá-lo em todos os outros cenários. |N/A |Sim, quando **enableStaging** está definido como TRUE |
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

### <a name="billing-impact"></a>Impacto de faturação
É cobrado com base em dois passos: duração da cópia e copie o tipo.

* Quando utiliza o teste durante uma cópia na cloud (copiar dados do arquivo de dados na cloud para outro arquivo de dados na cloud), é cobrado a [soma de duração de cópia para o passo 1 e o passo 2] x [preço de unidade de cópia de nuvem].
* Quando utiliza o teste durante uma cópia híbrida (copiar dados de um arquivo de dados no local para um arquivo de dados na cloud), é-lhe cobrada [duração de cópia híbrida] x [preço da unidade de cópia híbrida] + [duração de cópia da cloud] x [preço de unidade de cópia de nuvem].

## <a name="performance-tuning-steps"></a>Etapas de ajuste de desempenho
Sugerimos que siga estes passos para otimizar o desempenho do seu serviço do Data Factory com atividade de cópia:

1. **Estabelecer uma linha de base**. Durante a fase de desenvolvimento, teste o seu pipeline através da atividade de cópia em relação a uma amostra de dados representativos. Pode utilizar o Data Factory [modelo de fragmentação](data-factory-scheduling-and-execution.md) para limitar a quantidade de dados a trabalhar com.

   Recolher o tempo de execução e características de desempenho, utilizando o **monitorização e gestão de aplicações**. Escolher **monitorizar e gerir** na sua home page da fábrica de dados. Na vista de árvore, escolha o **conjunto de dados de saída**. Na **atividade Windows** lista, escolha a execução de atividade de cópia. **Atividade Windows** lista a duração da atividade de cópia e o tamanho dos dados copiados. O débito está listado na **Explorador de janelas de atividade**. Para saber mais sobre a aplicação, veja [monitorizar e gerir pipelines do Azure Data Factory com a aplicação de gestão e monitorização](data-factory-monitor-manage-app.md).

   ![Detalhes da execução da atividade](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Posteriormente neste artigo, pode comparar o desempenho e a configuração do seu cenário para a atividade de cópia [referência de desempenho](#performance-reference) dos nossos testes.
2. **Diagnosticar e otimizar o desempenho**. Se o desempenho que observar não cumprir as suas expectativas, precisa identificar afunilamentos de desempenho. Em seguida, a otimizar o desempenho para remover ou reduzir o efeito de afunilamentos. Uma descrição completa do diagnóstico de desempenho está além do escopo deste artigo, mas aqui estão algumas considerações comuns:

   * Recursos de desempenho:
     * [Cópia paralela](#parallel-copy)
     * [Unidades de movimento de dados na cloud](#cloud-data-movement-units)
     * [Cópia faseada](#staged-copy)
     * [Escalabilidade do Gateway de gestão de dados](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Data Management Gateway](#considerations-for-data-management-gateway)
   * [Origem](#considerations-for-the-source)
   * [sink](#considerations-for-the-sink)
   * [Serialização e desserialização](#considerations-for-serialization-and-deserialization)
   * [Compressão](#considerations-for-compression)
   * [Mapeamento de colunas](#considerations-for-column-mapping)
   * [Outras considerações](#other-considerations)
3. **Expanda a configuração para o seu conjunto de dados inteiro**. Quando estiver satisfeito com os resultados de execução e o desempenho, pode expandir a definição e o período ativo do pipeline para cobrir o seu conjunto de dados inteiro.

## <a name="considerations-for-data-management-gateway"></a>Considerações para o Data Management Gateway
**Configuração do gateway**: Recomendamos que utilize uma máquina dedicada para alojar o Data Management Gateway. Ver [considerações sobre como utilizar o Data Management Gateway](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Monitorização de gateway e aumentar verticalmente/horizontalmente**: Um gateway lógico único com um ou mais nós de gateway pode servir várias execuções de atividade de cópia ao mesmo tempo em simultâneo. Pode ver o instantâneo de quase em tempo real de utilização de recursos (CPU, memória, network(in/out), etc.) numa máquina de gateway, bem como o número de tarefas simultâneas em execução em comparação com o limite no portal do Azure, consulte [gateway Monitor no portal do](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Caso precise pesada no movimento de dados híbrido com grande número de execuções de atividade de cópia em simultâneo ou com grande volume de dados para copiar, considere [aumentar verticalmente ou horizontalmente gateway](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) de modo a utilizar melhor o seu recurso ou para aprovisionar mais recursos para aumentar a produtividade de cópia.

## <a name="considerations-for-the-source"></a>Considerações para a origem
### <a name="general"></a>Geral
Certifique-se de que o arquivo de dados subjacente não está assoberbado com outras cargas de trabalho que estejam a executar num ou em relação a ele.

Para arquivos de dados da Microsoft, consulte [monitoramento e ajuste tópicos](#performance-reference) que são específicas para arquivos de dados e ajudam a compreender dados armazenam características de desempenho, minimizar os tempos de resposta e maximizar o débito.

Se copiar dados do armazenamento de BLOBs para o SQL Data Warehouse, considere utilizar **PolyBase** para aumentar o desempenho. Ver [utilizar o PolyBase para carregar dados para o Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter detalhes. Para obter instruções com um caso de utilização, consulte [carregar 1 TB para o Azure SQL Data Warehouse menos de 15 minutos com o Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Arquivos de dados baseados em ficheiros
*(Inclui o armazenamento de BLOBs, Data Lake Store, Amazon S3, sistemas de ficheiros no local e HDFS no local)*

* **Média de tamanho de ficheiro e a contagem de ficheiros**: Atividade de cópia transfere um ficheiro de dados cada vez. Com a mesma quantidade de dados para ser movida, o débito global é inferior se os dados consistam em muitos arquivos pequenos em vez de poucos arquivos grandes devido a fase de arranque de configuração para cada ficheiro. Por conseguinte, se possível, combine o arquivos pequenos em arquivos maiores para obter um débito mais elevado.
* **Ficheiro de formato e compressão**: Para obter mais formas de melhorar o desempenho, consulte a [considerações sobre a serialização e desserialização](#considerations-for-serialization-and-deserialization) e [considerações sobre a compactação](#considerations-for-compression) secções.
* Para o **sistema de ficheiros no local** cenário, em que **Data Management Gateway** é necessário, consulte a [considerações para o Data Management Gateway](#considerations-for-data-management-gateway) secção.

### <a name="relational-data-stores"></a>Arquivos de dados relacionais
*(Inclui a base de dados do SQL; O SQL Data Warehouse; Amazon Redshift; Bases de dados do SQL Server; e o Oracle, MySQL, DB2, Teradata, Sybase e PostgreSQL bases de dados, etc.)*

* **Padrão de dados**: A esquema da tabela afeta o débito de cópia. Um tamanho de linha grandes dá-lhe um desempenho melhor do que o tamanho de linha de pequenos, para copiar a mesma quantidade de dados. O motivo é que a base de dados com mais eficiência pode obter menos lotes de dados que contêm menos linhas.
* **Consulta ou procedimento armazenado**: Otimize a lógica da consulta ou procedimento armazenado que especificar na origem de atividade de cópia para obter os dados de forma mais eficiente.
* Para **bases de dados relacionais locais**, tal como o SQL Server e Oracle, que requerem a utilização de **Data Management Gateway**, veja as considerações para a secção de Gateway de gestão de dados.

## <a name="considerations-for-the-sink"></a>Considerações para o sink
### <a name="general"></a>Geral
Certifique-se de que o arquivo de dados subjacente não está assoberbado com outras cargas de trabalho que estejam a executar num ou em relação a ele.

Para arquivos de dados da Microsoft, consulte [monitoramento e ajuste tópicos](#performance-reference) que são específicas para arquivos de dados. Estes tópicos podem ajudá-lo a compreender as características de desempenho do armazenamento de dados e como minimizar os tempos de resposta e maximizar o débito.

Se estiver a copiar dados a partir **armazenamento de BLOBs** ao **SQL Data Warehouse**, considere a utilização **PolyBase** para aumentar o desempenho. Ver [utilizar o PolyBase para carregar dados para o Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter detalhes. Para obter instruções com um caso de utilização, consulte [carregar 1 TB para o Azure SQL Data Warehouse menos de 15 minutos com o Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Arquivos de dados baseados em ficheiros
*(Inclui o armazenamento de BLOBs, Data Lake Store, Amazon S3, sistemas de ficheiros no local e HDFS no local)*

* **Copie o comportamento**: Se copiar dados de um arquivo de dados diferentes baseados em ficheiros, a atividade de cópia tem três opções através da **copyBehavior** propriedade. Preserva a hierarquia, nivela hierarquia ou intercala ficheiros. A preservação ou mesclar a hierarquia tem pouca ou nenhuma sobrecarga de desempenho, mas mesclar arquivos faz com que a sobrecarga de desempenho aumentar.
* **Ficheiro de formato e compressão**: Consulte a [considerações sobre a serialização e desserialização](#considerations-for-serialization-and-deserialization) e [considerações sobre a compactação](#considerations-for-compression) secções para obter mais maneiras de melhorar o desempenho.
* **Armazenamento de BLOBs**: Atualmente, o armazenamento de BLOBs suporta apenas blobs de blocos para transferência de dados otimizada e débito.
* Para **sistemas de ficheiros no local** cenários que requerem a utilização de **Data Management Gateway**, consulte a [considerações para o Data Management Gateway](#considerations-for-data-management-gateway) secção.

### <a name="relational-data-stores"></a>Arquivos de dados relacionais
*(Inclui a base de dados SQL, o SQL Data Warehouse, bases de dados do SQL Server e bancos de dados Oracle)*

* **Copie o comportamento**: Consoante as propriedades que tiver definido para **sqlSink**, atividade de cópia escreve dados na base de dados de destino de formas diferentes.
  * Por predefinição, as utilizações de serviço de movimento de dados a API de cópia em massa para inserir dados em acréscimo modo, que fornece o melhor desempenho.
  * Se configurar um procedimento armazenado no coletor, a base de dados aplica-se a uma linha de dados cada vez, em vez de como um carregamento em massa. Desempenho cai significativamente. Se o seu conjunto de dados é grande, quando aplicável, considere mudar para utilizar o **sqlWriterCleanupScript** propriedade.
  * Se configurar o **sqlWriterCleanupScript** executar de propriedade para cada atividade de cópia, o serviço aciona o script e, em seguida, utilize a API de cópia em massa para inserir os dados. Por exemplo, para substituir a tabela inteira com os dados mais recentes, pode especificar um script para eliminar primeiro todos os registos antes dos novos dados da origem de carregamento em massa.
* **Tamanho de dados padrão e batch**:
  * A esquema da tabela afeta o débito de cópia. Para copiar a mesma quantidade de dados, um tamanho de linha grandes fornece desempenho melhor do que um tamanho de linha de pequenos porque a base de dados com mais eficiência pode comprometer-se menos lotes de dados.
  * Atividade de cópia insere dados de uma série de lotes. Pode definir o número de linhas num batch ao utilizar o **writeBatchSize** propriedade. Se os dados tiverem linhas pequenas, pode definir o **writeBatchSize** propriedade com um valor mais alto para beneficiar de sobrecarga de lote menor e um débito mais elevado. Se o tamanho de linha dos seus dados é grande, tenha cuidado quando aumenta **writeBatchSize**. Um valor elevado pode levar a uma falha de cópia causada pela sobrecarga da base de dados.
* Para **bases de dados relacionais locais** como o SQL Server e Oracle, que requerem a utilização de **Data Management Gateway**, consulte o [considerações para o Data Management Gateway](#considerations-for-data-management-gateway)secção.

### <a name="nosql-stores"></a>Arquivos NoSQL
*(Inclui o armazenamento de tabelas e o Azure Cosmos DB)*

* Para **armazenamento de tabelas**:
  * **partição**: Escrita de dados para partições intercaladas drasticamente degrada o desempenho. Ordenar os dados de origem por chave de partição para que os dados são inseridos com eficiência numa partição após a outra ou ajustar a lógica para escrever os dados para uma única partição.
* Para **do Azure Cosmos DB**:
  * **Tamanho do lote**: O **writeBatchSize** propriedade define o número de pedidos paralelos para o serviço do Azure Cosmos DB para criar documentos. Pode esperar um melhor desempenho quando aumenta **writeBatchSize** como mais pedidos paralelos são enviados para o Azure Cosmos DB. No entanto, procure limitação ao escrever para o Azure Cosmos DB (a mensagem de erro é "taxa de pedidos é grande"). Vários fatores podem causar a limitação, incluindo o tamanho do documento, o número de termos em documentos e a política de indexação da coleção de destino. Para obter um maior débito de cópia, considere a utilização de uma coleção melhor, por exemplo, S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Considerações sobre a serialização e desserialização
Serialização e desserialização podem ocorrer quando o seu conjunto de dados de entrada ou um conjunto de dados de saída é um ficheiro. Ver [formatos de ficheiro e de compressão suportados](data-factory-supported-file-and-compression-formats.md) com detalhes sobre os formatos de ficheiro suportados pela atividade de cópia.

**Copie o comportamento**:

* Copiar ficheiros entre arquivos de dados de ficheiros:
  * Quando a entrada e saídos conjuntos de dados de ambos os tem o mesmo ou não existem definições do formato de ficheiro, o serviço de movimento de dados executa uma cópia binária sem qualquer serialização ou a desserialização. Ver um débito mais elevado, em comparação com o cenário, em que as definições de formato de ficheiro de origem e sink são diferentes entre si.
  * Quando a entrada e saída conjuntos de dados de ambos os estão no formato de texto e apenas a codificação tipo é diferente, o serviço de movimento de dados apenas faz a conversão de codificação. Ele não faz qualquer serialização e desserialização, o que faz com que alguma sobrecarga em comparação comparado uma cópia binária de desempenho.
  * Quando a entrada e saídos conjuntos de dados de ambos os tem diferentes formatos de arquivos ou configurações diferentes, como delimitadores, o serviço de movimento de dados desserializa a origem de dados para transmitir em fluxo, transformar e, em seguida, serializá-lo para o formato de saída indicada. Esta operação resulta num desempenho muito mais significativo sobrecarga em comparação comparado outros cenários.
* Quando copia arquivos de/para um arquivo de dados que não seja baseado em ficheiros (por exemplo, a partir de um arquivo de baseados em ficheiros de mensagens em fila para um armazenamento relacional), o passo de serialização ou desserialização é necessário. Este passo resulta em sobrecarga de desempenho significativo.

**Formato de ficheiro**: O formato de ficheiro que escolher pode afetar o desempenho de cópia. Por exemplo, o Avro é um formato binário compacto que armazena os metadados com dados. Ele tem um amplo suporte no ecossistema do Hadoop para consulta e processamento. No entanto, o Avro é mais caro para serialização e desserialização, o que resulta num débito de cópia mais baixo em comparação comparado o formato de texto. Faça sua escolha de formato de ficheiro em todo o fluxo de processamento holística. Começar com o que formam os dados é armazenado em arquivos de dados de origem ou a ser extraído de sistemas externos; o melhor formato para armazenamento, processamento analítico e consulta; e, em que formato os dados devem ser exportados para o marts de dados para as ferramentas de geração de relatórios e visualização. Por vezes, um formato de ficheiro que é inferior ao ideal para ler e desempenho de escrita pode ser uma boa opção quando considera o processo geral analítico.

## <a name="considerations-for-compression"></a>Considerações para compressão
Quando o seu conjunto de dados de entrada ou de saída é um arquivo, pode definir a atividade de cópia para efetuar a compressão ou descompressão à medida que escreve dados para o destino. Ao escolher compressão, faça uma compensação entre (e/s) de entrada/saída e CPU. Comprimir os custos de dados Extras em recursos de computação. Mas, em troca, reduz e/s de rede e armazenamento. Dependendo dos dados, poderá ver um aumento na produtividade geral da cópia.

**Codec**: Atividade de cópia suporta os tipos de compressão Deflate, bzip2 e gzip. O Azure HDInsight pode consumir todos os três tipos de processamento. Cada codec de compressão tem vantagens. Por exemplo, bzip2 tem a menor taxa de transferência de cópia, mas obtém o melhor desempenho das consultas do Hive com bzip2 como pode dividir a para processamento. GZip é a opção mais equilibrada e é utilizado mais frequentemente. Escolha o codec que melhor se adequa aos seu cenário de ponto-a-ponto.

**Nível**: Pode escolher entre duas opções para cada codec de compressão: mais rápida comprimido e comprimido ideal. A opção mais rápida comprimida comprime os dados mais rapidamente possível, mesmo que o ficheiro resultante não será compactado ideal. A opção ideal comprimida gasta mais tempo a compactação e gera uma quantidade mínima de dados. Pode testar as duas opções para ver o que fornece um melhor desempenho geral no seu caso.

**Uma consideração**: Para copiar uma grande quantidade de dados entre um arquivo no local e na cloud, considere a utilização de armazenamento de BLOBs provisória com a compactação. Utilizar o armazenamento provisório é útil quando a largura de banda de rede da sua empresa e seus serviços do Azure é o fator restritivo e quiser que o conjunto de dados de entrada e o conjunto de dados de saída ambos os ter formato não comprimido. Mais especificamente, pode quebrar uma atividade de cópia única em atividades de cópia de dois. A primeira atividade de cópia copia da origem para um blob de provisório ou teste no formato comprimido. A segunda atividade de cópia copia os dados comprimidos de teste e, em seguida, descomprime enquanto escreve para o sink.

## <a name="considerations-for-column-mapping"></a>Considerações para mapeamento de colunas
Pode definir o **columnMappings** propriedade na atividade de cópia para todos os de mapa ou um subconjunto de colunas de entrada para as colunas de saída. Depois do serviço de movimento de dados lê os dados da origem, tem de efetuar o mapeamento de colunas nos dados antes de ele grava os dados para o sink. Este processamento extra reduz a taxa de transferência de cópia.

Se seu arquivo de dados de origem é passível de consulta, por exemplo, se for um arquivo relacional, como a base de dados SQL ou SQL Server, ou se for um arquivo de NoSQL, como o armazenamento de tabelas ou do Azure Cosmos DB, considere enviar a filtragem de coluna e reordenação de lógica para o **consulta** propriedade em vez de usar o mapeamento de colunas. Dessa forma, a projeção ocorre enquanto o serviço de movimento de dados lê dados de arquivo de dados de origem, em que é muito mais eficiente.

## <a name="other-considerations"></a>Outras considerações
Se o tamanho dos dados que pretende copiar for grande, pode ajustar sua lógica de negócio para a partição ainda mais os dados com o mecanismo de slicing no Data Factory. Em seguida, agende a atividade de cópia para executar com mais frequência para reduzir o tamanho de dados para cada execução de atividade de cópia.

Tenha cuidado sobre o número de conjuntos de dados e atividades de cópia que requerem a fábrica de dados para o conector para o mesmo arquivo de dados ao mesmo tempo. Muitas tarefas de cópia em simultâneo poderão limitar um arquivo de dados e levar a degradação do desempenho, repetições internas do trabalho de cópia e em alguns casos, as falhas de execução.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Cenário de exemplo: Copiar de um servidor de SQL no local para o armazenamento de BLOBs
**Cenário**: Baseia-se um pipeline para copiar dados de um servidor de SQL no local para o armazenamento de BLOBs no formato CSV. Para tornar a tarefa de cópia com mais rapidez, os arquivos CSV devem ser comprimidos no formato de bzip2.

**Análise e teste**: O débito de atividade de cópia é inferior a 2 MBps, que é muito mais lenta do que o parâmetro de comparação do desempenho.

**Análise de desempenho e otimização**: Para resolver o problema de desempenho, vamos examinar como os dados são processados e movidos.

1. **Ler dados**: O gateway é aberta uma ligação ao SQL Server e envia a consulta. SQL Server responde ao enviar o fluxo de dados ao Gateway por meio da intranet.
2. **Serializar e comprimir os dados**: Gateway serializa o fluxo de dados em formato CSV e comprime os dados num fluxo de bzip2.
3. **Escrever dados**: Gateway carrega o fluxo de bzip2 para o armazenamento de BLOBs através da Internet.

Como pode ver, os dados estão a ser processados e movidos de forma seqüencial transmissão em fluxo: SQL Server > LAN > Gateway > WAN > armazenamento de Blobs. **O desempenho geral é protegido pelo débito mínimo entre o pipeline**.

![Fluxo de dados](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Um ou mais dos seguintes fatores podem fazer com que o afunilamento do desempenho:

* **origem**: Devido a cargas pesadas, o SQL Server em si tem baixo débito.
* **O Data Management Gateway**:
  * **LAN**: O gateway está localizado longe de ser máquina do SQL Server e tem uma ligação de largura de banda baixa.
  * **Gateway**: Gateway atingiu suas limitações de carga para realizar as seguintes operações:
    * **Serialização**: Serializar o fluxo de dados em formato CSV tem o débito lento.
    * **Compressão**: Optou por um codec de compressão lenta (por exemplo, bzip2, que é 2,8 MBps com Core i7).
  * **WAN**: A largura de banda entre a rede empresarial e seus serviços do Azure é baixa (por exemplo, T1 = 1,544 kbps; T2 = 6,312 kbps).
* **Sink**: Armazenamento de BLOBs tem baixo débito. (Este cenário é pouco provável que uma vez que o SLA garante um mínimo de 60 MBps.)

Neste caso, bzip2 compressão de dados pode ser mais lento todo o pipeline. Mudar para um codec de compressão de gzip poderá facilitar este congestionamento.

## <a name="sample-scenarios-use-parallel-copy"></a>Cenários de exemplo: Utilize a cópia paralela
**Cenário i:** Copie 1000 MB de 1 ficheiros do sistema de arquivos no local para o armazenamento de Blobs.

**Análise e a otimização de desempenho**: Por exemplo, se tiver instalado o gateway num computador quad-core, o Data Factory utiliza 16 cópias paralelas para mover ficheiros do sistema de arquivos para armazenamento de BLOBs em simultâneo. Esta execução paralela deve resultar em alto débito. Também pode especificar explicitamente o número de cópias paralela. Quando copiar muitos arquivos pequenos, cópias paralelas drasticamente ajudam a débito ao utilizar recursos com mais eficiência.

![Cenário 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Cenário II**: Copiar blobs de 20 de 500 MB de armazenamento de BLOBs para análise do Data Lake Store e, em seguida, otimizar o desempenho.

**Análise e a otimização de desempenho**: Neste cenário, Data Factory copia os dados do armazenamento de BLOBs para o Data Lake Store através de cópia única (**parallelCopies** definido como 1) e unidades de movimento de dados de cloud única. O débito observar será próximo descrito a [seção da referência de desempenho](#performance-reference).

![Cenário 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Cenário III**: Tamanho de ficheiro individual é superior a dezenas de MB e o total volume é grande.

**Análise e ativar desempenho**: O aumento **parallelCopies** não resulta num melhor desempenho de cópia devido a limitações de recursos um DMU de cloud única. Em vez disso, deve especificar DMUs para obter mais recursos para realizar o movimento de dados de cloud mais. Não especifique um valor para o **parallelCopies** propriedade. Data Factory processa o paralelismo para. Neste caso, se definir **cloudDataMovementUnits** 4, um débito de cerca de quatro vezes ocorre.

![Cenário 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Referência
Seguem-se desempenho de monitorização e otimização de referências para alguns dos arquivos de dados suportados:

* Armazenamento do Azure (incluindo o armazenamento de BLOBs e armazenamento de tabelas): [Destinos de escalabilidade do armazenamento do Azure](../../storage/common/storage-scalability-targets.md) e [lista de verificação de armazenamento do Azure, desempenho e escalabilidade](../../storage/common/storage-performance-checklist.md)
* Base de Dados SQL do Azure: Pode [monitorizar o desempenho](../../sql-database/sql-database-single-database-monitor.md) e verificar a percentagem de unidade (DTU) de transação de base de dados
* Armazém de dados SQL do Azure: Sua capacidade é medida em unidades do data warehouse (DWUs); consulte [gerir computação power no Azure SQL Data Warehouse (descrição geral)](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* o Azure Cosmos DB: [Níveis de desempenho no Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* SQL Server no local: [Monitorizar e otimizar o desempenho](https://msdn.microsoft.com/library/ms189081.aspx)
* Servidor de ficheiros no local: [Ajuste de desempenho para servidores de ficheiros](https://msdn.microsoft.com/library/dn567661.aspx)
