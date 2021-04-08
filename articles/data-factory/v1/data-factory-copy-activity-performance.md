---
title: Guia de desempenho e otimização da Atividade de Cópia
description: Conheça os fatores-chave que afetam o desempenho do movimento de dados na Azure Data Factory quando utilizar a Copy Activity.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9a890719de39a71d8336d39f9932e73f7baccf87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377215"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Guia de desempenho e otimização da Atividade de Cópia

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-copy-activity-performance.md)
> * [Versão 2 (versão atual)](../copy-activity-performance.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o desempenho da [atividade copy e o guia de afinação para a Data Factory.](../copy-activity-performance.md)

A Azure Data Factory Copy Activity oferece uma solução de carregamento de dados de primeira classe segura, fiável e de alto desempenho. Permite-lhe copiar dezenas de terabytes de dados todos os dias através de uma rica variedade de lojas de dados em nuvem e no local. O desempenho de carregamento rápido de dados é fundamental para garantir que pode focar-se no problema principal do "big data": construir soluções de análise avançadas e obter informações profundas a partir de todos esses dados.

O Azure fornece um conjunto de soluções de armazenamento de dados e armazém de dados de nível empresarial, e a Copy Activity oferece uma experiência de carregamento de dados altamente otimizada que é fácil de configurar e configurar. Com apenas uma única atividade de cópia, pode conseguir:

* A carregar dados para **a Azure Synapse Analytics** a **1,2 GBps**. Para uma passagem com uma caixa de utilização, consulte [a Carga 1 TB em Azure Synapse Analytics em menos de 15 minutos com a Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* Carregar dados no **armazenamento da Azure Blob** a **1,0 GBps**
* Carregar dados na **Azure Data Lake Store** a **1,0 GBps**

Este artigo descreve:

* [Números de referência de desempenho](#performance-reference) para lojas de dados de origem suportada e afunda-se para ajudá-lo a planear o seu projeto;
* Características que podem aumentar a produção de cópias em diferentes cenários, incluindo [unidades de movimento de dados](#cloud-data-movement-units)em nuvem, [cópia paralela,](#parallel-copy)e [cópia encenada;](#staged-copy)
* [Orientação de afinação](#performance-tuning-steps) de desempenho sobre como sintonizar o desempenho e os fatores-chave que podem impactar o desempenho da cópia.

> [!NOTE]
> Se não estiver familiarizado com a Copy Activity em geral, consulte [os dados do Movimento utilizando a Copy Activity](data-factory-data-movement-activities.md) antes de ler este artigo.
>

## <a name="performance-reference"></a>Referência de desempenho

Como referência, a tabela abaixo mostra o número de produção de cópia em MBps para os pares de fontes e pias determinados com base em testes internos. Para comparação, também demonstra como diferentes configurações de [unidades](#cloud-data-movement-units) de movimento de dados em nuvem ou [escalabilidade do Gateway de gestão de dados](data-factory-data-management-gateway-high-availability-scalability.md) (múltiplos nós de gateway) podem ajudar no desempenho da cópia.

![Matriz de desempenho](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>Na versão 1 do Azure Data Factory, as unidades mínimas de movimento de dados em nuvem para cópia nuvem-nuvem são duas. Se não for especificado, consulte as unidades de movimento de dados predefinidas que estão a ser utilizadas em [unidades de movimento de dados](#cloud-data-movement-units)em nuvem .

**Pontos a notar:**
* A produção é calculada utilizando a seguinte fórmula: [tamanho dos dados lidos a partir da fonte]/[Duração da execução da atividade de cópia].
* Os números de referência de desempenho na tabela foram medidos utilizando o conjunto de dados [TPC-H](http://www.tpc.org/tpch/) numa única execução de atividade de cópia.
* Nas lojas de dados Azure, a fonte e o lavatório encontram-se na mesma região de Azure.
* Para cópia híbrida entre as lojas de dados no local e as lojas de dados em nuvem, cada nó de gateway estava a funcionar numa máquina separada da loja de dados no local com especificações abaixo. Quando uma única atividade estava a funcionar no gateway, a operação de cópia consumia apenas uma pequena parte do CPU, memória ou largura de banda da máquina de teste. Saiba mais com [a consideração para o Data Management Gateway.](#considerations-for-data-management-gateway)
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
        <td>Interface de Internet: 10 Gbps; interface intranet: 40 Gbps</td>
    </tr>
    </table>


> [!TIP]
> Pode obter uma maior produção aproveitando mais unidades de movimento de dados (DMUs) do que as DMUs máximas predefinidos, que é 32 para uma execução de cópia nuvem-nuvem. Por exemplo, com 100 DMUs, pode obter dados de cópia de Azure Blob para Azure Data Lake Store em **1.0GBps**. Consulte a secção [de unidades de movimento de dados cloud](#cloud-data-movement-units) para obter mais detalhes sobre esta funcionalidade e sobre o cenário suportado. Contacte [o suporte da Azure](https://azure.microsoft.com/support/) para solicitar mais DMUs.

## <a name="parallel-copy"></a>Cópia paralela
Pode ler dados a partir da fonte ou escrever dados para o destino **em paralelo dentro de uma função Copy Activity**. Esta funcionalidade melhora o rendimento de uma operação de cópia e reduz o tempo necessário para mover dados.

Esta configuração é diferente da propriedade **de concurrency** na definição de atividade. A propriedade **de concuncy** determina o número de **Atividade de Cópia Simultânea que corre** para processar dados de diferentes janelas de atividade (1:00 às 2:00, 2:00 às 3:00, 3:00 às 4:00, e assim por diante). Esta capacidade é útil quando se realiza uma carga histórica. A capacidade de cópia paralela aplica-se a uma **única atividade executada**.

Vamos ver um cenário de amostra. No exemplo seguinte, várias fatias do passado precisam de ser processadas. A Data Factory executa uma instância de Copy Activity (uma atividade executada) para cada fatia:

* A fatia de dados da primeira janela de atividade (1:00 às 2:00) ==> Atividade executar 1
* A fatia de dados da segunda janela de atividade (2:00 às 3:00) ==> Atividade executar 2
* A fatia de dados da segunda janela de atividade (3:00 às 4:00) ==> Atividade executar 3

E assim sucessivamente.

Neste exemplo, quando o valor da **concuência** é definido para 2, **a Atividade funciona 1** e **a Atividade executam 2** dados de cópia de duas janelas de atividade **simultaneamente** para melhorar o desempenho do movimento de dados. No entanto, se vários ficheiros estiverem associados à Atividade executar 1, o serviço de movimento de dados copia ficheiros da fonte para o destino um ficheiro de cada vez.

### <a name="cloud-data-movement-units"></a>Unidades de movimento de dados em nuvem
Uma **unidade de movimento de dados em nuvem (DMU)** é uma medida que representa a potência (uma combinação de CPU, memória e alocação de recursos de rede) de uma única unidade na Data Factory. A DMU é aplicável para operações de cópia em nuvem,mas não numa cópia híbrida.

**As unidades mínimas de movimento de dados em nuvem para capacitar a atividade de cópia são duas.** Se não for especificado, o quadro que se segue lista as DMUs predefinidas utilizadas em diferentes cenários de cópia:

| Cenário de cópia | DMUs padrão determinados pelo serviço |
|:--- |:--- |
| Copiar dados entre lojas baseadas em ficheiros | Entre 4 e 16 dependendo do número e tamanho dos ficheiros. |
| Todos os outros cenários de cópia | 4 |

Para anular este padrão, especifique um valor para a propriedade **cloudDataMovementUnnits** da seguinte forma. Os **valores permitidos** para a **propriedade CloudDataMovementUnnits** são 2, 4, 8, 16, 32. O **número real de DMUs em nuvem** que a operação de cópia utiliza no tempo de execução é igual ou inferior ao valor configurado, dependendo do seu padrão de dados. Para obter informações sobre o nível de ganho de desempenho que poderá obter quando configurar mais unidades para uma fonte de cópia específica e afundar, consulte a referência de [desempenho](#performance-reference).

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
> Se precisar de mais DMUs em nuvem para uma produção mais elevada, contacte [o suporte do Azure](https://azure.microsoft.com/support/). A definição de 8 e superior funciona atualmente apenas quando **copia vários ficheiros do blob storage/Data Lake Store/Amazon S3/cloud FTP/cloud SFTP para blob armazenamento/Data Lake Store/Azure SQL Database**.
>

### <a name="parallelcopies"></a>paralelosCopias
Pode utilizar a propriedade **parallelCopies** para indicar o paralelismo que pretende utilizar. Pode pensar nesta propriedade como o número máximo de fios dentro da Copy Activity que podem ler a partir da sua fonte ou escrever para as suas lojas de dados de lavatórios em paralelo.

Para cada funcionação da Atividade de Cópia, a Data Factory determina o número de cópias paralelas a utilizar para copiar dados da loja de dados de origem e para a loja de dados de destino. O número predefinido de cópias paralelas que utiliza depende do tipo de fonte e pia que está a utilizar.

| Fonte e pia | Contagem de cópia paralela padrão determinada por serviço |
| --- | --- |
| Copiar dados entre lojas baseadas em ficheiros (armazenamento Blob; Data Lake Store; Amazon S3; um sistema de ficheiros no local; um HDFS no local) |Entre 1 e 32. Depende do tamanho dos ficheiros e do número de unidades de movimento de dados em nuvem (DMUs) utilizadas para copiar dados entre duas lojas de dados em nuvem, ou a configuração física da máquina Gateway utilizada para uma cópia híbrida (para copiar dados de ou para uma loja de dados no local). |
| Copiar dados de qualquer loja de **dados de origem para o armazenamento da Tabela Azure** |4 |
| Todas as outras fontes e pares de pias |1 |

Normalmente, o comportamento padrão deve dar-lhe a melhor produção. No entanto, para controlar a carga nas máquinas que acolhem as suas lojas de dados, ou para sintonizar o desempenho da cópia, pode optar por sobrepor o valor predefinido e especificar um valor para a propriedade **ParallelCopies.** O valor deve estar entre 1 e 32 (ambos inclusive). No tempo de execução, para o melhor desempenho, copy Activity usa um valor inferior ou igual ao valor que define.

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
Pontos a notar:

* Ao copiar dados entre lojas baseadas em **ficheiros, os paralelosCopia determinam** o paralelismo ao nível dos ficheiros. O chunking dentro de um único ficheiro aconteceria por baixo automaticamente e de forma transparente, e foi projetado para usar o tamanho de pedaço mais adequado para um determinado tipo de loja de dados de origem para carregar dados em paralelo e ortogonal para parallelCopies. O número real de cópias paralelas que o serviço de movimento de dados utiliza para a operação de cópia no tempo de execução não é mais do que o número de ficheiros que tem. Se o comportamento da cópia for **fusãoFile,** a Copy Activity não pode tirar partido do paralelismo ao nível do ficheiro.
* Quando especificar um valor para a propriedade **parallelCopies,** considere o aumento de carga nas suas lojas de dados de origem e afundada, e para gateway se se trata de uma cópia híbrida. Isto acontece especialmente quando você tem múltiplas atividades ou execuções simultâneas das mesmas atividades que funcionam contra a mesma loja de dados. Se notar que a loja de dados ou gateway está sobrecarregada com a carga, diminua o valor **paraleloCopias** para aliviar a carga.
* Quando copia dados de lojas que não são baseadas em ficheiros para lojas baseadas em ficheiros, o serviço de movimento de dados ignora a propriedade **parallelCopies.** Mesmo que o paralelismo seja especificado, não é aplicado neste caso.

> [!NOTE]
> Tem de utilizar a versão 1.11 ou posterior do Data Management Gateway para utilizar a função **ParallelCopies** quando fizer uma cópia híbrida.
>
>

Para melhor utilizar estas duas propriedades e melhorar a produção do seu movimento de dados, consulte os casos de utilização da amostra. Não é preciso configurar **paralelosCopias** para tirar partido do comportamento padrão. Se configurar e **paralelosCopias** são muito pequenas, várias DMUs de nuvem podem não ser totalmente utilizadas.

### <a name="billing-impact"></a>Impacto da faturação
É **importante** lembrar que é carregado com base no tempo total da operação de cópia. Se um trabalho de cópia costumava demorar uma hora com uma unidade de nuvem e agora leva 15 minutos com quatro unidades de nuvem, a conta geral permanece quase a mesma. Por exemplo, usa-se quatro unidades de nuvem. A primeira unidade de nuvem passa 10 minutos, a segunda, 10 minutos, a terceira, 5 minutos, e a quarta, 5 minutos, tudo numa atividade de cópia. É cobrado o tempo total de cópia (movimento de dados), que é 10 + 10 + 5 + 5 = 30 minutos. A utilização **de paralelosCopias** não afeta a faturação.

## <a name="staged-copy"></a>Cópia encenada
Ao copiar dados de uma loja de dados de origem para uma loja de dados de lavatórios, poderá optar por utilizar o armazenamento Blob como uma loja de preparação provisória. A encenação é especialmente útil nos seguintes casos:

1. **Pretende ingerir dados de várias lojas de dados no Azure Synapse Analytics via PolyBase**. A Azure Synapse Analytics usa a PolyBase como um mecanismo de alta produção para carregar uma grande quantidade de dados em Azure Synapse Analytics. No entanto, os dados de origem devem estar no armazenamento blob, e devem cumprir critérios adicionais. Quando carrega dados de uma loja de dados que não seja o armazenamento blob, pode ativar a cópia de dados através de uma encenação provisória do armazenamento blob. Nesse caso, a Data Factory realiza as transformações de dados necessárias para garantir que satisfaz os requisitos da PolyBase. Em seguida, utiliza a PolyBase para carregar dados no Azure Synapse Analytics. Para obter mais detalhes, consulte [o PolyBase para carregar dados no Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics). Para uma passagem com uma caixa de utilização, consulte [a Carga 1 TB em Azure Synapse Analytics em menos de 15 minutos com a Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **Por vezes demora algum tempo a realizar um movimento de dados híbrido (isto é, a copiar entre uma loja de dados no local e uma loja de dados em nuvem) sobre uma ligação de rede lenta**. Para melhorar o desempenho, pode comprimir os dados no local para que leve menos tempo a mover dados para a loja de dados de encenação na nuvem. Em seguida, pode descomprimir os dados na loja de preparação antes de os colocar na loja de dados de destino.
3. **Não pretende abrir portas que não o porto 80 e o porto 443 na sua firewall, por causa das políticas corporativas de TI.** Por exemplo, quando copia dados de uma loja de dados no local para um lavatório Azure SQL Database ou um lavatório Azure Synapse Analytics, precisa de ativar a comunicação TCP de saída na porta 1433 para a firewall do Windows e para a sua firewall corporativa. Neste cenário, aproveite a porta de entrada para copiar primeiro os dados para uma instância de armazenamento Blob sobre HTTP ou HTTPS na porta 443. Em seguida, carregue os dados na Base de Dados SQL ou na Azure Synapse Analytics a partir da paragem de armazenamento Blob. Neste fluxo, não precisa de ativar a porta 1433.

### <a name="how-staged-copy-works"></a>Como funciona a cópia encenada
Quando ativar a função de encenação, primeiro os dados são copiados da loja de dados de origem para a loja de dados de encenação (traga o seu próprio). Em seguida, os dados são copiados da loja de dados de encenação para a loja de dados da pia. A Data Factory gere automaticamente o fluxo de dois estágios para si. A Data Factory também limpa dados temporários do armazenamento de encenação após o movimento de dados estar completo.

No cenário de cópia em nuvem (tanto as lojas de dados de origem como de sumidouro estão na nuvem), o gateway não é utilizado. O serviço Data Factory realiza as operações de cópia.

![Cópia encenada: Cenário de nuvem](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

No cenário de cópia híbrida (a fonte está no local e o lavatório está na nuvem), o gateway move os dados da loja de dados de origem para uma loja de dados de encenação. O serviço Data Factory transfere os dados da loja de dados de encenação para a loja de dados da pia. A cópia de dados de uma loja de dados em nuvem para uma loja de dados no local através da encenação também é suportada com o fluxo invertido.

![Cópia encenada: Cenário híbrido](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Quando ativa o movimento de dados utilizando uma loja de preparação, pode especificar se pretende que os dados sejam comprimidos antes de transferir os dados da loja de dados de origem para uma loja de dados provisória ou de paragem, e depois descomprimido antes de mover os dados de uma loja de dados provisória ou de paragem para a loja de dados do lavatório.

Atualmente, não é possível copiar dados entre duas lojas de dados no local utilizando uma loja de encenação. Esperamos que esta opção esteja disponível em breve.

### <a name="configuration"></a>Configuração
Configure a **definição de definição de definição de ativação** na Atividade de Cópia para especificar se pretende que os dados sejam encenados no armazenamento blob antes de os colocar numa loja de dados de destino. Quando definir ativar a **definição** de TRUE, especifique as propriedades adicionais listadas na tabela seguinte. Se não tiver um, também precisa de criar um serviço de acesso partilhado azure para a realização.

| Propriedade | Descrição | Valor predefinido | Necessário |
| --- | --- | --- | --- |
| **permitir A marcação** |Especifique se pretende copiar dados através de uma loja de encenação provisória. |Falso |No |
| **linkedServiceName** |Especifique o nome de um serviço ligado [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [AzureStorageSas,](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) que se refere à instância de Armazenamento que utiliza como loja de paragem provisória. <br/><br/> Não é possível utilizar o Armazenamento com uma assinatura de acesso partilhado para carregar dados no Azure Synapse Analytics via PolyBase. Podes usá-lo em todos os outros cenários. |N/D |Sim, quando **ativar A definição de marcação** está definida para TRUE |
| **caminho** |Especifique a trajetória de armazenamento Blob que pretende conter os dados encenados. Se não fornecer um caminho, o serviço cria um recipiente para armazenar dados temporários. <br/><br/> Especifique um caminho apenas se utilizar o Armazenamento com uma assinatura de acesso partilhado, ou se necessitar de dados temporários para estar num local específico. |N/D |No |
| **permitir a compressão** |Especifica se os dados devem ser comprimidos antes de serem copiados para o destino. Esta definição reduz o volume de dados que são transferidos. |Falso |No |

Aqui está uma definição de amostra de Copy Activity com as propriedades descritas na tabela anterior:

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

### <a name="billing-impact"></a>Impacto da faturação
É cobrado com base em dois passos: duração da cópia e tipo de cópia.

* Quando utiliza a encenação durante uma cópia em nuvem (copiando dados de uma loja de dados em nuvem para outra loja de dados em nuvem), é-lhe cobrado o [soma da duração da cópia para o passo 1 e passo 2] x [preço unitário de cópia em nuvem].
* Quando utiliza a encenação durante uma cópia híbrida (copiando dados de uma loja de dados no local para uma loja de dados em nuvem), é cobrado por [duração de cópia híbrida] x [preço unitário de cópia híbrida] + [duração da cópia da nuvem] x [preço unitário da cópia da nuvem].

## <a name="performance-tuning-steps"></a>Passos de afinação de desempenho
Sugerimos que tome estas medidas para afinar o desempenho do seu serviço de Data Factory com a Copy Activity:

1. **Estabelecer uma linha de base**. Durante a fase de desenvolvimento, teste o seu pipeline utilizando a Copy Activity contra uma amostra de dados representativa. Pode utilizar o [modelo de corte da](data-factory-scheduling-and-execution.md) Data Factory para limitar a quantidade de dados com que trabalha.

   Colete o tempo de execução e as características de desempenho utilizando **a App de Monitorização e Gestão.** Escolha **Monitor & Gerir** na sua página inicial da Data Factory. Na vista da árvore, escolha o **conjunto de dados de saída**. Na lista **'Windows' de atividade,** escolha a execução da Atividade de Cópia. **O Activity Windows** lista a duração da Atividade de Cópia e o tamanho dos dados copiados. A produção está listada no **Explorador de Janelas de Atividade.** Para saber mais sobre a aplicação, consulte [o Monitor e gere os oleodutos Azure Data Factory utilizando a App de Monitorização e Gestão.](data-factory-monitor-manage-app.md)

   ![Detalhes da execução da atividade](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Mais tarde no artigo, pode comparar o desempenho e configuração do seu cenário com a referência de [desempenho](#performance-reference) da Copy Activity a partir dos nossos testes.
2. **Diagnosticar e otimizar o desempenho.** Se o desempenho que observa não corresponder às suas expectativas, tem de identificar estrangulamentos de desempenho. Em seguida, otimize o desempenho para remover ou reduzir o efeito dos estrangulamentos. Uma descrição completa do diagnóstico de desempenho está fora do âmbito deste artigo, mas aqui estão algumas considerações comuns:

   * Características de desempenho:
     * [Cópia paralela](#parallel-copy)
     * [Unidades de movimento de dados em nuvem](#cloud-data-movement-units)
     * [Cópia encenada](#staged-copy)
     * [Escalabilidade gateway de gestão de dados](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Data Management Gateway](#considerations-for-data-management-gateway)
   * [Origem](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Serialização e deserialização](#considerations-for-serialization-and-deserialization)
   * [Compressão](#considerations-for-compression)
   * [Mapeamento de colunas](#considerations-for-column-mapping)
   * [Outras considerações](#other-considerations)
3. **Expanda a configuração para todo o conjunto de dados**. Quando estiver satisfeito com os resultados e desempenho da execução, pode expandir o período ativo de definição e pipeline para cobrir todo o seu conjunto de dados.

## <a name="considerations-for-data-management-gateway"></a>Considerações para gateway de gestão de dados
**Configuração gateway**: Recomendamos que utilize uma máquina dedicada para hospedar o Data Management Gateway. Consulte [considerações para a utilização do Gateway de Gestão de Dados](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Monitorização de gateway e escala/out**: Um único gateway lógico com um ou mais nós de gateway pode servir várias operações de cópia ao mesmo tempo simultaneamente. Pode visualizar uma imagem quase real da utilização de recursos (CPU, memória, rede (in/out), etc.) numa máquina de gateway, bem como o número de empregos simultâneos em execução versus limite no portal Azure, consulte [o portal Monitor no portal](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Se tiver uma grande necessidade de movimento de dados híbridos, quer com um grande número de executações simultâneas de cópias, quer com grande volume de dados a copiar, considere [aumentar ou escalar o gateway](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) de modo a utilizar melhor o seu recurso ou a disponibilização de mais recursos para capacitar a cópia.

## <a name="considerations-for-the-source"></a>Considerações para a fonte
### <a name="general"></a>Geral
Certifique-se de que a loja de dados subjacente não está sobrecarregada por outras cargas de trabalho que estão a decorrer sobre ela ou contra ela.

Para as lojas de dados da Microsoft, consulte [tópicos de monitorização e afinação específicos](#performance-reference) das lojas de dados e ajude-o a compreender as características de desempenho da loja de dados, a minimizar os tempos de resposta e a maximizar a produção.

Se copiar dados do armazenamento blob para a Azure Synapse Analytics, considere usar **a PolyBase** para aumentar o desempenho. Consulte [o PolyBase para carregar os dados no Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) para obter mais detalhes. Para uma passagem com uma caixa de utilização, consulte [a Carga 1 TB em Azure Synapse Analytics em menos de 15 minutos com a Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Lojas de dados baseadas em ficheiros
*(Inclui armazenamento blob, Data Lake Store, Amazon S3, sistemas de ficheiros no local e HDFS no local)*

* **Tamanho médio do ficheiro e contagem de ficheiros**: A Atividade de Cópia transfere dados um ficheiro de cada vez. Com a mesma quantidade de dados a mover, a produção geral é menor se os dados forem compostos por muitos ficheiros pequenos em vez de alguns ficheiros grandes devido à fase de bootstrap para cada ficheiro. Portanto, se possível, combine pequenos ficheiros em ficheiros maiores para obter uma produção mais elevada.
* **Formato de ficheiro e compressão**: Para obter mais formas de melhorar o desempenho, consulte as [Considerações de serialização e deserialização](#considerations-for-serialization-and-deserialization) e considerações para secções [de compressão.](#considerations-for-compression)
* Para o cenário **do sistema de ficheiros no local,** no qual é necessário **o Gateway de Gestão de Dados,** consulte a secção [Considerações para a Gateway de Gestão de Dados.](#considerations-for-data-management-gateway)

### <a name="relational-data-stores"></a>Lojas de dados relacionais
*(Inclui base de dados SQL; Azure Synapse Analytics; Amazon Redshift; Bases de dados do SQL Server; e Oracle, MySQL, DB2, Teradata, Sybase e PostgreSQL bases de dados, etc.)*

* **Padrão de dados**: O seu esquema de tabela afeta a produção de cópias. Um tamanho de linha grande dá-lhe um melhor desempenho do que o tamanho da fila pequena, para copiar a mesma quantidade de dados. A razão é que a base de dados pode recuperar de forma mais eficiente menos lotes de dados que contêm menos linhas.
* **Consulta ou procedimento armazenado**: Otimize a lógica da consulta ou do procedimento armazenado que especifique na fonte de Atividade de Cópia para obter dados de forma mais eficiente.
* Para **bases de dados relacionais no local**, como o SQL Server e o Oracle, que requerem a utilização do Data Management **Gateway,** consulte a secção Considerations for Data Management Gateway.

## <a name="considerations-for-the-sink"></a>Considerações para a pia
### <a name="general"></a>Geral
Certifique-se de que a loja de dados subjacente não está sobrecarregada por outras cargas de trabalho que estão a decorrer sobre ela ou contra ela.

Para as lojas de dados da Microsoft, consulte [tópicos de monitorização e afinação específicos](#performance-reference) das lojas de dados. Estes tópicos podem ajudá-lo a compreender as características de desempenho da data store e como minimizar os tempos de resposta e maximizar a produção.

Se estiver a copiar dados do **armazenamento Blob** para **a Azure Synapse Analytics,** considere utilizar o **PolyBase** para aumentar o desempenho. Consulte [o PolyBase para carregar os dados no Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) para obter mais detalhes. Para uma passagem com uma caixa de utilização, consulte [a Carga 1 TB em Azure Synapse Analytics em menos de 15 minutos com a Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Lojas de dados baseadas em ficheiros
*(Inclui armazenamento blob, Data Lake Store, Amazon S3, sistemas de ficheiros no local e HDFS no local)*

* **Função de cópia**: Se copiar dados de uma loja de dados diferente baseada em ficheiros, a Copy Activity tem três opções através da propriedade **copyBehavior.** Preserva a hierarquia, achata a hierarquia ou funde ficheiros. Ou preservar ou aplanar a hierarquia tem pouca ou nenhuma sobrecarga de desempenho, mas a fusão dos ficheiros faz com que o desempenho aumente.
* **Formato e compressão** de ficheiros : Consulte as [Considerações de serialização e deserialização](#considerations-for-serialization-and-deserialization) e [considerações para](#considerations-for-compression) secções de compressão para obter mais formas de melhorar o desempenho.
* **Armazenamento blob**: Atualmente, o armazenamento Blob suporta apenas blobs de bloco para transferência e produção de dados otimizados.
* Para **cenários de sistemas de ficheiros no local** que exijam a utilização do Gateway de Gestão de **Dados,** consulte a secção [Considerations for Data Management Gateway.](#considerations-for-data-management-gateway)

### <a name="relational-data-stores"></a>Lojas de dados relacionais
*(Inclui base de dados SQL, Azure Synapse Analytics, bases de dados do SQL Server e bases de dados oracle)*

* **Comportamento da cópia**: Dependendo das propriedades que definiu para **sqlSink,** copy Activity escreve dados para a base de dados de destino de diferentes maneiras.
  * Por predefinição, o serviço de movimento de dados utiliza a API de cópia a granel para inserir dados no modo de apêndice, que proporciona o melhor desempenho.
  * Se configurar um procedimento armazenado na pia, a base de dados aplica os dados uma linha de cada vez em vez de como uma carga a granel. O desempenho diminui significativamente. Se o seu conjunto de dados for grande, quando aplicável, considere mudar para usar a propriedade **sqlWriterCleanUpScript.**
  * Se configurar a propriedade **sqlWriterCleanUpScript** para cada execução da Atividade de Cópia, o serviço ativa o script e, em seguida, utiliza a API de cópia a granel para inserir os dados. Por exemplo, para substituir toda a tabela com os dados mais recentes, pode especificar um script para primeiro eliminar todos os registos antes de carregar em massa os novos dados da fonte.
* **Padrão de dados e tamanho do lote:**
  * O seu esquema de mesa afeta a produção de cópias. Para copiar a mesma quantidade de dados, um grande tamanho de linha dá-lhe um melhor desempenho do que um tamanho de linha pequeno porque a base de dados pode comprometer de forma mais eficiente menos lotes de dados.
  * A Copy Activity insere dados numa série de lotes. Pode definir o número de linhas num lote utilizando a propriedade **writeBatchSize.** Se os seus dados tão pequenos utilizadores, pode definir a propriedade **writeBatchSize** com um valor mais elevado para beneficiar de uma sobrecarga de lote mais baixa e de uma produção mais elevada. Se o tamanho da linha dos seus dados for grande, tenha cuidado ao aumentar **o writeBatchSize**. Um valor elevado pode levar a uma falha de cópia causada pela sobrecarga da base de dados.
* Para **bases de dados relacionais no local,** como o SQL Server e o Oracle, que requerem a utilização do Gateway de **Gestão** de Dados, consulte a secção [Considerações para a Gateway de Gestão de Dados.](#considerations-for-data-management-gateway)

### <a name="nosql-stores"></a>Lojas NoSQL
*(Inclui armazenamento de mesa e Azure Cosmos DB)*

* Para **armazenamento de mesa:**
  * **Partição**: Escrever dados para divisórias intercaladas degrada drasticamente o desempenho. Serdene os seus dados de origem por chave de partição para que os dados seja inseridos de forma eficiente numa partição após outra, ou ajuste a lógica para escrever os dados numa única partição.
* Para **Azure Cosmos DB:**
  * **Tamanho do lote**: A propriedade **writeBatchSize** define o número de pedidos paralelos ao serviço DB Azure Cosmos para criar documentos. Pode esperar um melhor desempenho quando aumentar **a escritaBatchSize** porque mais pedidos paralelos são enviados para Azure Cosmos DB. No entanto, tenha cuidado com o estrangulamento quando escrever para Azure Cosmos DB (a mensagem de erro é "A taxa de pedido é grande"). Vários fatores podem causar estrangulamento, incluindo o tamanho do documento, o número de termos nos documentos e a política de indexação da recolha alvo. Para obter uma maior produção de cópia, considere usar uma melhor coleção, por exemplo, S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Considerações para a serialização e a desserialização
A serialização e a deserialização podem ocorrer quando o conjunto de dados de entrada ou conjunto de dados de saída é um ficheiro. Consulte [formatos de ficheiros e compressão suportados](data-factory-supported-file-and-compression-formats.md) com detalhes sobre formatos de ficheiros suportados pela Copy Activity.

**Comportamento de cópia:**

* Cópia de ficheiros entre lojas de dados baseadas em ficheiros:
  * Quando os conjuntos de dados de entrada e saída têm as mesmas definições ou não do formato de ficheiro, o serviço de movimento de dados executa uma cópia binária sem qualquer serialização ou deserialização. Você vê uma produção mais alta em comparação com o cenário, em que as definições de formato de ficheiro de origem e pia são diferentes umas das outras.
  * Quando os conjuntos de dados de entrada e saída estão ambos em formato de texto e apenas o tipo de codificação é diferente, o serviço de movimento de dados só codifica a conversão. Não faz nenhuma serialização e deserialização, o que causa alguma sobrecarga de desempenho em comparação com uma cópia binária.
  * Quando os conjuntos de dados de entrada e saída têm diferentes formatos de ficheiros ou configurações diferentes, como delimiters, o serviço de movimento de dados dessesecializa os dados de origem para transmitir, transformar e, em seguida, serializá-los no formato de saída indicado. Esta operação resulta num desempenho muito mais significativo em comparação com outros cenários.
* Quando copia ficheiros de/para uma loja de dados que não seja baseada em ficheiros (por exemplo, de uma loja baseada em ficheiros para uma loja relacional), é necessário o passo de serialização ou de dessaserialização. Este passo resulta numa sobrecarga de desempenho significativa.

**Formato de ficheiro**: O formato de ficheiro que escolher pode afetar o desempenho da cópia. Por exemplo, a Avro é um formato binário compacto que armazena metadados com dados. Tem um amplo apoio no ecossistema Hadoop para processamento e consulta. No entanto, a Avro é mais cara para a serialização e a deserialização, o que resulta numa produção de cópia mais baixa em comparação com o formato de texto. Faça a sua escolha de formato de ficheiro em todo o fluxo de processamento de forma holisticamente. Comece com a forma em que os dados são armazenados, armazenam dados de origem ou a serem extraídos de sistemas externos; o melhor formato para armazenamento, processamento analítico e consulta; e em que formato os dados devem ser exportados para data marts para ferramentas de reporte e visualização. Por vezes, um formato de ficheiro subóptima para ler e escrever desempenho pode ser uma boa escolha quando se considera o processo analítico geral.

## <a name="considerations-for-compression"></a>Considerações para a compressão
Quando o seu conjunto de dados de entrada ou saída é um ficheiro, pode definir a Copy Activity para efetuar a compressão ou descompressão à medida que escreve dados para o destino. Quando escolhe a compressão, faz uma troca entre a entrada/saída (I/O) e a CPU. Comprimir os dados custa mais em recursos computativos. Mas em troca, reduz a e-te a rede de E/S e o armazenamento. Dependendo dos seus dados, poderá ver um aumento na produção geral de cópias.

**Codec**: Copy Activity suporta tipos de gzip, bzip2 e Deflate. A azure HDInsight pode consumir todos os três tipos para o processamento. Cada códice de compressão tem vantagens. Por exemplo, o bzip2 tem a produção de cópia mais baixa, mas obtém-se o melhor desempenho de consulta de Hive com bzip2 porque pode dividi-lo para processamento. Gzip é a opção mais equilibrada, e é usada mais frequentemente. Escolha o codec que melhor se adequa ao seu cenário de ponta a ponta.

**Nível**: Pode escolher entre duas opções para cada codec de compressão: comprimido mais rápido e comprimido da melhor forma. A opção comprimida mais rápida comprime os dados o mais rapidamente possível, mesmo que o ficheiro resultante não seja perfeitamente comprimido. A opção comprimida idealmente gasta mais tempo em compressão e produz uma quantidade mínima de dados. Pode testar ambas as opções para ver o que proporciona um melhor desempenho geral no seu caso.

**Consideração**: Para copiar uma grande quantidade de dados entre uma loja no local e a nuvem, considere utilizar o armazenamento de bolhas provisória com compressão. A utilização de armazenamento provisório é útil quando a largura de banda da sua rede corporativa e dos seus serviços Azure é o fator limitativo, e pretende que o conjunto de dados de entrada e o conjunto de dados de saída estejam ambos de forma descomprimida. Mais especificamente, pode dividir uma única atividade de cópia em duas atividades de cópia. A primeira cópia da atividade copia da fonte para uma bolha provisória ou de encenação sob forma comprimida. A segunda atividade de cópia copia os dados comprimidos da encenação e, em seguida, descomprime enquanto escreve na pia.

## <a name="considerations-for-column-mapping"></a>Considerações para mapeamento de colunas
Pode definir a propriedade de mapas de **colunasMappings** na Atividade de Cópia para mapear todas ou um subconjunto das colunas de entrada para as colunas de saída. Após o serviço de movimento de dados ler os dados da fonte, precisa de realizar mapeamento de colunas nos dados antes de escrever os dados para a pia. Este processamento extra reduz a produção de cópias.

Se a sua loja de dados de origem for questionável, por exemplo, se for uma loja relacional como a SQL Database ou o SQL Server, ou se for uma loja NoSQL como o armazenamento de mesa ou o Azure Cosmos DB, considere empurrar a lógica de filtragem e reordenamento da coluna para a propriedade de **consulta** em vez de usar o mapeamento de colunas. Desta forma, a projeção ocorre enquanto o serviço de movimento de dados lê dados da loja de dados de origem, onde é muito mais eficiente.

## <a name="other-considerations"></a>Outras considerações
Se o tamanho dos dados que pretende copiar for grande, pode ajustar a sua lógica de negócio para dividir ainda mais os dados utilizando o mecanismo de corte na Data Factory. Em seguida, agende a Atividade de Cópia para executar com mais frequência para reduzir o tamanho dos dados para cada execução da Copy Activity.

Tenha cuidado com o número de conjuntos de dados e atividades de cópia que exigem que a Data Factory se conecte à mesma loja de dados ao mesmo tempo. Muitos trabalhos de cópia simultâneas podem acelerar uma loja de dados e levar a um desempenho degradado, copiar retrações internas e, em alguns casos, falhas de execução.

## <a name="sample-scenario-copy-from-a-sql-server-database-to-blob-storage"></a>Cenário da amostra: Copiar de uma base de dados do SQL Server para o armazenamento blob
**Cenário**: Um gasoduto é construído para copiar dados de uma base de dados do SQL Server para o armazenamento blob em formato CSV. Para tornar a cópia mais rápida, os ficheiros CSV devem ser comprimidos em formato bzip2.

**Teste e análise**: O resultado da Atividade de Cópia é inferior a 2 MBps, o que é muito mais lento do que o referencial de desempenho.

**Análise de desempenho e afinação**: Para resolver problemas de desempenho, vamos ver como os dados são processados e movidos.

1. **Leia os dados**: Gateway abre uma ligação ao SQL Server e envia a consulta. O SQL Server responde enviando o fluxo de dados para Gateway através da intranet.
2. **Serialize e comprima dados**: Gateway serializa o fluxo de dados para o formato CSV e comprime os dados para um fluxo bzip2.
3. **Dados de escrita**: Gateway envia o fluxo bzip2 para o armazenamento blob através da Internet.

Como pode ver, os dados estão a ser processados e movidos de forma sequencial de streaming: SQL Server > LAN > Gateway > ARMAZENAMENTO WAN > Blob. **O desempenho global é fechado pela produção mínima através do gasoduto**.

![Fluxo de dados](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Um ou mais dos seguintes fatores podem causar o estrangulamento do desempenho:

* **Fonte**: O próprio SQL Server tem baixa produção devido a cargas pesadas.
* **Gateway de Gestão de Dados:**
  * **LAN**: Gateway está localizado longe do computador SQL Server e tem uma ligação de baixa largura de banda.
  * **Gateway**: Gateway : Gateway atingiu as suas limitações de carga para executar as seguintes operações:
    * **Serialização**: Serializar o fluxo de dados para o formato CSV tem uma produção lenta.
    * **Compressão**: Escolheu um codec de compressão lenta (por exemplo, bzip2, que é de 2,8 MBps com Core i7).
  * **WAN**: A largura de banda entre a rede corporativa e os seus serviços Azure é baixa (por exemplo, T1 = 1.544 kbps; T2 = 6,312 kbps).
* **Pia**: O armazenamento de bolhas tem baixa produção. (Este cenário é improvável porque o seu SLA garante um mínimo de 60 MBps.)

Neste caso, a compressão de dados bzip2 pode estar a abrandar todo o gasoduto. Mudar para um codec de compressão gzip pode aliviar este estrangulamento.

## <a name="sample-scenarios-use-parallel-copy"></a>Cenários da amostra: Utilizar cópia paralela
**Cenário I:** Copie 1.000 ficheiros 1-MB do sistema de ficheiros no local para o armazenamento blob.

**Análise e afinação de desempenho**: Por exemplo, se instalou gateway numa máquina quad core, a Data Factory utiliza 16 cópias paralelas para mover ficheiros do sistema de ficheiros para o armazenamento blob simultaneamente. Esta execução paralela deve resultar em alta produção. Também pode especificar explicitamente a contagem de cópias paralelas. Quando copia muitos ficheiros pequenos, cópias paralelas ajudam dramaticamente a produção utilizando recursos de forma mais eficaz.

![Cenário 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Cenário II**: Copiar 20 blobs de 500 MB cada, desde o armazenamento blob até à Data Lake Store Analytics e, em seguida, sintonizar o desempenho.

**Análise e afinação** de desempenho : Neste cenário, a Data Factory copia os dados do armazenamento blob para a Data Lake Store utilizando unidades de movimento de dados de cópia única **(parallelCopies definidas** para 1) e unidades de movimento de dados de nuvem única. A produção que observar será próxima da descrita na [secção de referência](#performance-reference)de desempenho .

![Cenário 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Cenário III**: O tamanho do ficheiro individual é maior do que dezenas de MBs e o volume total é grande.

**Análise e viragem** de desempenho : Aumentar **os paralelosCopias** não resulta num melhor desempenho de cópia devido às limitações de recursos de um DMU de uma única nuvem. Em vez disso, deve especificar mais DMUs em nuvem para obter mais recursos para executar o movimento de dados. Não especifique um valor para a propriedade **parallelCopies.** A Data Factory trata do paralelismo para si. Neste caso, se definir **cloudDataMovementUnnits** para 4, ocorre uma produção de cerca de quatro vezes.

![Cenário 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Referência
Aqui estão as referências de monitorização e afinação de desempenho para algumas das lojas de dados suportadas:

* Armazenamento Azure Blob: [Objetivos de escalabilidade e desempenho para armazenamento blob](../../storage/blobs/scalability-targets.md) e [performance e escalabilidade lista de verificação para armazenamento blob](../../storage/blobs/storage-performance-checklist.md).
* Armazenamento da tabela azul: [Metas de escalabilidade e desempenho para armazenamento de mesa](../../storage/tables/scalability-targets.md) e performance e lista de [verificação de escala para armazenamento de mesa](../../storage/tables/storage-performance-checklist.md).
* Base de Dados Azure SQL: Pode [monitorizar o desempenho](../../azure-sql/database/monitor-tune-overview.md) e verificar a percentagem de transações de bases de dados (DTU)
* Azure Synapse Analytics: A sua capacidade é medida em unidades de armazém de dados (DWUs); ver [Gerir o poder de computação em Azure Synapse Analytics (Visão geral)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [Níveis de desempenho em Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* Servidor SQL no local: [Monitor e sintonização para desempenho](/sql/relational-databases/performance/monitor-and-tune-for-performance)
* Servidor de ficheiros no local: [Ajuste de desempenho para servidores de ficheiros](/previous-versions//dn567661(v=vs.85))