---
title: Guia de desempenho e otimização da Atividade de Cópia
description: Conheça os factores-chave que afetam o desempenho do movimento de dados na Fábrica de Dados Azure quando utiliza a Atividade de Cópia.
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
ms.openlocfilehash: c4ca328aa0ddc61d86a435b93fe775f294287b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527389"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Guia de desempenho e otimização da Atividade de Cópia

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-copy-activity-performance.md)
> * [Versão 2 (versão atual)](../copy-activity-performance.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o desempenho da atividade do Copy e o guia de [afinação para data factory](../copy-activity-performance.md).

A Azure Data Factory Copy Activity oferece uma solução de carregamento de dados segura, fiável e de alto desempenho. Permite-lhe copiar dezenas de terabytes de dados todos os dias através de uma rica variedade de lojas de dados em nuvem e no local. O desempenho de carregamento rápido de dados é fundamental para garantir que pode focar-se no problema principal dos "big data": construir soluções de análise avançadas e obter informações profundas de todos esses dados.

O Azure fornece um conjunto de soluções de armazenamento de dados de qualidade empresarial e de armazenamento de dados, e a Copy Activity oferece uma experiência de carregamento de dados altamente otimizada que é fácil de configurar e configurar. Com apenas uma única atividade de cópia, pode conseguir:

* Carregar dados no Armazém de **Dados Azure SQL** a **1,2 GBps**. Para um passeio com uma caixa de utilização, consulte a Carregue 1 TB no Armazém de [Dados Azure SQL em menos de 15 minutos com](data-factory-load-sql-data-warehouse.md)a Azure Data Factory .
* Carregar dados no **armazenamento da Blob Azure** a **1.0 GBps**
* Carregar dados na **Azure Data Lake Store** a **1.0 GBps**

Este artigo descreve:

* [Números de referência de desempenho](#performance-reference) para lojas de dados de origem suportada e afundar para ajudá-lo a planear o seu projeto;
* Funcionalidades que podem aumentar a entrada de cópia em diferentes cenários, incluindo unidades de movimento de [dados em nuvem,](#cloud-data-movement-units) [cópia paralela,](#parallel-copy)e [cópia encenada;](#staged-copy)
* [Orientação de afinação](#performance-tuning-steps) de desempenho sobre como afinar o desempenho e os factores-chave que podem impactar o desempenho da cópia.

> [!NOTE]
> Se não estiver familiarizado com a Atividade de Cópia em geral, consulte [os dados do Move utilizando a Atividade](data-factory-data-movement-activities.md) de Cópia antes de ler este artigo.
>

## <a name="performance-reference"></a>Referência de desempenho

Como referência, a tabela abaixo mostra o número de entrada de cópia em MBps para a fonte determinada e pares de pia com base em testes internos. Para comparação, também demonstra como diferentes configurações de [unidades](#cloud-data-movement-units) de movimento de dados em nuvem ou escalabilidade gateway de [gestão](data-factory-data-management-gateway-high-availability-scalability.md) de dados (vários nós de gateway) podem ajudar na cópia do desempenho.

![Matriz de desempenho](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>Na versão 1 da Azure Data Factory, as unidades mínimas de movimento de dados em nuvem para cópia cloud-to-cloud são duas. Se não especificadas, consulte as unidades de movimento de dados predefinidos utilizadas nas [unidades](#cloud-data-movement-units)de movimento de dados em nuvem .

**Pontos a notar:**
* A entrada é calculada utilizando a seguinte fórmula: [o tamanho dos dados lidos a partir da fonte]/[Copy Activity executar duração].
* Os números de referência de desempenho na tabela foram medidos utilizando o conjunto de dados [TPC-H](http://www.tpc.org/tpch/) numa única execução de atividade de cópia.
* Nas lojas de dados Azure, a fonte e o lavatório encontram-se na mesma região do Azure.
* Para cópias híbridas entre as lojas de dados no local e as lojas de dados em nuvem, cada nó de gateway estava a funcionar numa máquina que estava separada da loja de dados no local com especificação abaixo. Quando uma única atividade estava a funcionar no portal, a operação de cópia consumia apenas uma pequena parte do CPU, memória ou largura de banda da máquina de ensaio. Saiba mais a partir da [consideração para](#considerations-for-data-management-gateway)gateway de gestão de dados.
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
> Pode obter uma maior produção, alavancando mais unidades de movimento de dados (DMUs) do que o limite de DMUs padrão, que é 32 para uma execução de atividade de cópia cloud-to-cloud. Por exemplo, com 100 DMUs, pode obter dados de cópia do Azure Blob para a Azure Data Lake Store a **1.0GBps**. Consulte a secção [de unidades](#cloud-data-movement-units) de movimento de dados cloud para obter detalhes sobre esta funcionalidade e o cenário suportado. Contacte o [suporte da Azure](https://azure.microsoft.com/support/) para solicitar mais DMUs.

## <a name="parallel-copy"></a>Cópia paralela
Pode ler dados da fonte ou escrever dados para o destino **em paralelo dentro de uma execução**de Copy Activity . Esta funcionalidade melhora a entrada de uma operação de cópia e reduz o tempo que leva para mover dados.

Esta definição é diferente da propriedade **da moeda** na definição de atividade. A propriedade **concurrency** determina o número de **atividade copiadora simultânea corre** para processar dados de diferentes janelas de atividade (1:00 a 2 da manhã, 2:00 a 3 da manhã, 3:00 a 4 da manhã, e assim por diante). Esta capacidade é útil quando se executa uma carga histórica. A capacidade de cópia paralela aplica-se a uma **única execução**de atividade .

Vamos ver um cenário de amostra. No exemplo seguinte, várias fatias do passado precisam de ser processadas. Data Factory executa uma instância de Copy Activity (uma execução de atividade) para cada fatia:

* A fatia de dados da primeira janela de atividade (1:00 a 2:00) ==> A atividade executar 1
* A fatia de dados da segunda janela de atividade (2:00 a 3 da manhã) ==> Execução de Atividade 2
* A fatia de dados da segunda janela de atividade (3:00 a 4 da manhã) ==> Execução de atividade 3

E assim sucessivamente.

Neste exemplo, quando o valor da **conmoeda** é definido para 2, **a Atividade executa 1** e a Atividade **executa 2** dados de cópia de duas janelas de atividade **simultaneamente** para melhorar o desempenho do movimento de dados. No entanto, se vários ficheiros estiverem associados à Execução 1 da Atividade, o serviço de movimento de dados copia ficheiros da fonte para o destino um ficheiro de cada vez.

### <a name="cloud-data-movement-units"></a>Unidades de movimento de dados em nuvem
Uma unidade de movimento de **dados em nuvem (DMU)** é uma medida que representa a potência (uma combinação de CPU, memória e alocação de recursos de rede) de uma única unidade na Fábrica de Dados. A DMU é aplicável para operações de cópia cloud-to-cloud, mas não numa cópia híbrida.

**As unidades mínimas de movimento de dados em nuvem para capacitar a execução da Atividade de Cópia são duas.** Se não especificado, a tabela a seguir enumera as DMUs predefinidas utilizadas em diferentes cenários de cópia:

| Cenário de cópia | DMUs padrão determinados por serviço |
|:--- |:--- |
| Copiar dados entre lojas baseadas em ficheiros | Entre 4 e 16, dependendo do número e tamanho dos ficheiros. |
| Todos os outros cenários de cópia | 4 |

Para anular este padrão, especifique um valor para a propriedade **cloudDataMovementUnits** da seguinte forma. Os **valores permitidos** para a **propriedade cloudDataMovementUnits** são 2, 4, 8, 16, 32. O **número real de DMUs em nuvem** que a operação de cópia utiliza no tempo de execução é igual ou inferior ao valor configurado, dependendo do seu padrão de dados. Para obter informações sobre o nível de ganho de desempenho que poderá obter quando configurar mais unidades para uma fonte de cópia específica e afundar, consulte a referência de [desempenho](#performance-reference).

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
> Se precisar de mais DMUs em nuvem para uma maior entrada, contacte o [suporte azure](https://azure.microsoft.com/support/). A definição de 8 e acima funciona atualmente apenas quando **copia vários ficheiros do armazenamento Blob/Data Lake Store/Amazon S3/cloud FTP/cloud SFTP para blob storage/Data Lake Store/Azure SQL Database**.
>

### <a name="parallelcopies"></a>paraleloCópias
Pode utilizar a propriedade **parallelCopy** para indicar o paralelismo que pretende utilizar a Copy Activity. Pode pensar nesta propriedade como o número máximo de fios dentro da Atividade de Cópia que podem ler a partir da sua fonte ou escrever para as suas lojas de dados de sumidouro em paralelo.

Para cada execução da Atividade de Cópia, a Data Factory determina o número de cópias paralelas a utilizar para copiar dados da loja de dados de origem e para a loja de dados de destino. O número predefinido de cópias paralelas que utiliza depende do tipo de fonte e pia que está a utilizar.

| Fonte e pia | Contagem de cópias paralelas padrão determinada pelo serviço |
| --- | --- |
| Copiar dados entre lojas baseadas em ficheiros (armazenamento blob; Data Lake Store; Amazon S3; um sistema de ficheiros no local; um HDFS no local) |Entre 1 e 32. Depende do tamanho dos ficheiros e do número de unidades de movimento de dados em nuvem (DMUs) utilizadas para copiar dados entre duas lojas de dados em nuvem, ou da configuração física da máquina Gateway utilizada para uma cópia híbrida (para copiar dados de ou para uma loja de dados no local). |
| Copiar dados de qualquer loja de **dados de origem para armazenamento de tabela Seletiva Azure** |4 |
| Todos os outros pares de fontes e pia |1 |

Normalmente, o comportamento padrão deve dar-lhe a melhor entrada. No entanto, para controlar a carga nas máquinas que hospedam as suas lojas de dados, ou para afinar o desempenho da cópia, poderá optar por anular o valor predefinido e especificar um valor para a propriedade **paralelaCopy.** O valor deve estar entre 1 e 32 (ambos incluídos). No tempo de execução, para o melhor desempenho, a Copy Activity utiliza um valor inferior ou igual ao valor que definiu.

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

* Quando copia dados entre lojas baseadas em ficheiros, as **Cópias paralelas** determinam o paralelismo ao nível dos ficheiros. O pedaço dentro de um único ficheiro aconteceria por baixo de forma automática e transparente, e foi concebido para usar o melhor tamanho adequado para um determinado tipo de loja de dados de origem para carregar dados em paralelo e ortogonal para cópias paralelas. O número real de cópias paralelas que o serviço de movimento de dados utiliza para a operação de cópia em tempo de execução não é mais do que o número de ficheiros que tem. Se o comportamento da cópia for **fundidoFile**, Copy Activity não pode tirar partido do paralelismo ao nível do ficheiro.
* Quando especificar um valor para a propriedade **parallelCopies,** considere o aumento de carga nas suas lojas de dados de origem e pia, e para gateway se se trata de uma cópia híbrida. Isto acontece especialmente quando se tem múltiplas atividades ou executações simultâneas das mesmas atividades que se opõem à mesma loja de dados. Se notar que a loja de dados ou gateway está sobrecarregada com a carga, diminua o valor **paraleloCópias** para aliviar a carga.
* Quando copia dados de lojas que não são baseadas em ficheiros em lojas baseadas em ficheiros, o serviço de movimento de dados ignora a propriedade **paralelaCopy.** Mesmo que o paralelismo seja especificado, não é aplicado neste caso.

> [!NOTE]
> Deve utilizar a versão 1.11 do Portal de Gestão de Dados para utilizar a funcionalidade **ParallelCopy** quando fizer uma cópia híbrida.
>
>

Para melhor utilizar estas duas propriedades e para melhorar a sua produção de movimento de dados, consulte os casos de utilização da amostra. Não é necessário configurar **paralelismoCópias** para tirar partido do comportamento padrão. Se configurar e **paralelamente Cópias** é muito pequena, vários DMUs em nuvem podem não estar totalmente utilizados.

### <a name="billing-impact"></a>Impacto da faturação
É **importante** lembrar que é cobrado com base no tempo total da operação de cópia. Se um trabalho de cópia usado para levar uma hora com uma unidade de nuvem e agora leva 15 minutos com quatro unidades de nuvem, a conta geral permanece quase a mesma. Por exemplo, usa-se quatro unidades de nuvem. A primeira unidade de nuvem passa 10 minutos, o segundo, 10 minutos, o terceiro, 5 minutos, e o quarto, 5 minutos, tudo numa corrida de Atividade de Cópia. É cobrado pelo tempo total de cópia (movimento de dados), que é 10 + 10 + 5 + 5 = 30 minutos. Utilizar **o paraleloCópias** não afeta a faturação.

## <a name="staged-copy"></a>Cópia encenada
Quando copia dados de uma loja de dados de origem para uma loja de dados de sumidouro, pode optar por utilizar o armazenamento Blob como uma loja de encenação provisória. A encenação é especialmente útil nos seguintes casos:

1. **Deseja ingerir dados de várias lojas de dados no Armazém de Dados SQL via PolyBase**. O SQL Data Warehouse utiliza a PolyBase como um mecanismo de alta carga para carregar uma grande quantidade de dados no Armazém de Dados SQL. No entanto, os dados de origem devem estar no armazenamento da Blob, e devem satisfazer critérios adicionais. Quando carrega dados de uma loja de dados que não seja o armazenamento blob, pode ativar a cópia de dados através do armazenamento de blob de encenação provisória. Nesse caso, a Data Factory realiza as transformações de dados necessárias para garantir que satisfaz os requisitos da PolyBase. Em seguida, utiliza a PolyBase para carregar dados no Armazém de Dados SQL. Para mais detalhes, consulte Use PolyBase para carregar dados no Armazém de [Dados Azure SQL](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). Para um passeio com uma caixa de utilização, consulte a Carregue 1 TB no Armazém de [Dados Azure SQL em menos de 15 minutos com](data-factory-load-sql-data-warehouse.md)a Azure Data Factory .
2. **Por vezes demora algum tempo a realizar um movimento de dados híbrido (isto é, copiar entre uma loja de dados no local e uma loja de dados em nuvem) sobre uma ligação lenta à rede**. Para melhorar o desempenho, pode comprimir os dados no local para que leve menos tempo a mover dados para a loja de dados de encenação na nuvem. Em seguida, pode descomprimir os dados na loja de preparação antes de os colocar na loja de dados de destino.
3. Não quer abrir portas que não sejam a **porta 80 e a porta 443 na sua firewall, por causa das políticas de TI corporativas.** Por exemplo, quando copia dados de uma loja de dados no local para um lavatório de base de dados Azure SQL ou um depósito de dados Azure SQL, precisa de ativar a comunicação TCP de saída na porta 1433 para a firewall do Windows e para a sua firewall corporativa. Neste cenário, aproveite a porta de entrada para primeiro copiar dados para uma instância de armazenamento Blob sobre HTTP ou HTTPS na porta 443. Em seguida, carregue os dados na Base de Dados SQL ou no Armazém de Dados SQL a partir da encenação de armazenamento blob. Neste fluxo, não é necessário ativar a porta 1433.

### <a name="how-staged-copy-works"></a>Como funciona a cópia encenada
Quando ativa a função de encenação, primeiro os dados são copiados da loja de dados de origem para a loja de dados de encenação (traga o seu próprio). Em seguida, os dados são copiados da loja de dados de encenação para a loja de dados do lavatório. A Data Factory gere automaticamente o fluxo de dois estágios para si. Data Factory também limpa dados temporários do armazenamento de encenação após o movimento de dados estar concluído.

No cenário de cópia em nuvem (tanto as lojas de dados de origem como a pia estão na nuvem), o gateway não é usado. O serviço Data Factory realiza as operações de cópia.

![Cópia encenada: Cenário de nuvem](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

No cenário de cópia híbrida (a fonte está no local e a pia está na nuvem), o gateway move dados da loja de dados de origem para uma loja de dados de encenação. O serviço Data Factory transfere dados da loja de dados de encenação para a loja de dados do lavatório. Copiar dados de uma loja de dados em nuvem para uma loja de dados no local através da encenação também é suportado com o fluxo invertido.

![Cópia encenada: Cenário híbrido](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Quando ativa o movimento de dados utilizando uma loja de encenação, pode especificar se pretende que os dados sejam comprimidos antes de transferir dados da loja de dados de origem para uma loja provisória ou de desativação de dados, e depois descomprimido antes de mover dados de um dados provisórios ou de encenação armazenar para a loja de dados da pia.

Atualmente, não é possível copiar dados entre duas lojas de dados no local utilizando uma loja de encenação. Esperamos que esta opção esteja disponível em breve.

### <a name="configuration"></a>Configuração
Configure a definição **de habilitação** na Atividade de Cópia para especificar se pretende que os dados sejam encenados no armazenamento blob antes de os carregar numa loja de dados de destino. Quando definir **o habilitação** para true, especifique as propriedades adicionais listadas na tabela seguinte. Se não tiver um, também precisa de criar um serviço de acesso partilhado azure Storage ou Storage para a realização.

| Propriedade | Descrição | Valor predefinido | Necessário |
| --- | --- | --- | --- |
| **habilitarEncenação** |Especifique se pretende copiar dados através de uma loja provisória. |Falso |Não |
| **linkedServiceName** |Especifique o nome de um serviço ligado ao [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [AzureStorageSas,](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) que se refere à instância de Armazenamento que utiliza como uma loja de encenação provisória. <br/><br/> Não é possível utilizar o Armazenamento com uma assinatura de acesso partilhado para carregar dados no Armazém de Dados SQL através da PolyBase. Podeusá-lo em todos os outros cenários. |N/D |Sim, quando **o habilitar É** definido para TRUE |
| **caminho** |Especifique o caminho de armazenamento blob que pretende conter os dados encenados. Se não fornecer um caminho, o serviço cria um recipiente para armazenar dados temporários. <br/><br/> Especifique um caminho apenas se utilizar o Armazenamento com uma assinatura de acesso partilhado, ou se necessitar de dados temporários para estar num local específico. |N/D |Não |
| **enableCompression** |Especifica se os dados devem ser comprimidos antes de serem copiados para o destino. Esta definição reduz o volume de dados transferidos. |Falso |Não |

Aqui está uma definição de amostra de Atividade de Cópia com as propriedades descritas na tabela anterior:

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

* Quando utiliza a encenação durante uma cópia em nuvem (copiando dados de uma loja de dados em nuvem para outra loja de dados em nuvem), é-lhe cobrada a [soma da duração da cópia para o passo 1 e passo 2] x [preço unitário de cópia em nuvem].
* Quando utiliza a encenação durante uma cópia híbrida (copiando dados de uma loja de dados no local para uma loja de dados em nuvem), é cobrado pela duração [da cópia híbrida] x [preço unitário de cópia híbrida] + [duração da cópia em nuvem] x [preço unitário de cópia em nuvem].

## <a name="performance-tuning-steps"></a>Passos de afinação de desempenho
Sugerimos que tome estas medidas para afinar o desempenho do seu serviço data factory com a Atividade de Cópia:

1. **Estabelecer uma linha de base.** Durante a fase de desenvolvimento, teste o seu pipeline utilizando a Atividade de Cópia contra uma amostra representativa de dados. Pode utilizar o [modelo](data-factory-scheduling-and-execution.md) de corte da Fábrica de Dados para limitar a quantidade de dados com que trabalha.

   Colete o tempo de execução e as características de desempenho utilizando a App de **Monitorização e Gestão.** Escolha **monitor & Gerir** na sua página inicial da Fábrica de Dados. Na vista da árvore, escolha o conjunto de dados de **saída**. Na lista **De Atividades Windows,** escolha a execução da Atividade de Cópia. **A Atividade Windows** lista a duração da Atividade de Cópia e o tamanho dos dados copiados. A entrada está listada no **Activity Window Explorer**. Para saber mais sobre a aplicação, consulte monitor [e gereos oleodutos Azure Data Factory utilizando a App de Monitorização e Gestão.](data-factory-monitor-manage-app.md)

   ![Detalhes da execução da atividade](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Mais tarde no artigo, pode comparar o desempenho e a configuração do seu cenário com a referência de [desempenho](#performance-reference) da Copy Activity dos nossos testes.
2. **Diagnosticar e otimizar o desempenho.** Se o desempenho que observa não corresponder às suas expectativas, precisa identificar estrangulamentos de desempenho. Em seguida, otimize o desempenho para remover ou reduzir o efeito dos estrangulamentos. Uma descrição completa do diagnóstico de desempenho está fora do âmbito deste artigo, mas aqui estão algumas considerações comuns:

   * Características de desempenho:
     * [Cópia paralela](#parallel-copy)
     * [Unidades de movimento de dados em nuvem](#cloud-data-movement-units)
     * [Cópia encenada](#staged-copy)
     * [Escalabilidade do Gateway de Gestão de Dados](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Data Management Gateway](#considerations-for-data-management-gateway)
   * [Origem](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Serialização e desserialização](#considerations-for-serialization-and-deserialization)
   * [Compressão](#considerations-for-compression)
   * [Mapeamento de colunas](#considerations-for-column-mapping)
   * [Outras considerações](#other-considerations)
3. **Expanda a configuração para todo o seu conjunto de dados**. Quando estiver satisfeito com os resultados de execução e o desempenho, pode expandir a definição e o período ativo do pipeline para cobrir todo o seu conjunto de dados.

## <a name="considerations-for-data-management-gateway"></a>Considerações para Gateway de Gestão de Dados
**Configuração gateway**: Recomendamos que utilize uma máquina dedicada para hospedar gateway de gestão de dados. Consulte [considerações para utilizar](data-factory-data-management-gateway.md#considerations-for-using-gateway)gateway de gestão de dados .

**Monitorização do gateway e escala/saída**: Um único portal lógico com um ou mais nós de gateway pode servir várias executações de Copy Activity ao mesmo tempo simultaneamente. Pode ver instantâneos quase em tempo real de utilização de recursos (CPU, memória, rede (in/out), etc.) numa máquina de gateway, bem como o número de empregos simultâneos em funcionamento versus limite no portal Azure, ver [Portal monitor no portal.](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal) Se tiver uma forte necessidade de movimentação de dados híbridos, quer com um grande número de executações de copios simultâneas, quer com um grande volume de dados para copiar, considere [aumentar ou escalar](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) o gateway de modo a utilizar melhor o seu recurso ou fornecer mais recursos para capacitar a cópia.

## <a name="considerations-for-the-source"></a>Considerações para a fonte
### <a name="general"></a>Geral
Certifique-se de que a loja de dados subjacente não está sobrecarregada por outras cargas de trabalho que estão a decorrer sobre ou contra.

Para as lojas de dados da Microsoft, consulte tópicos de [monitorização e afinação específicos](#performance-reference) das lojas de dados e ajude-o a compreender as características de desempenho da loja de dados, minimizar os tempos de resposta e maximizar a produção.

Se copiar dados do armazenamento Blob para o SQL Data Warehouse, considere utilizar o **PolyBase** para aumentar o desempenho. Consulte [a Utilização da PolyBase para carregar dados no Armazém de Dados Azure SQL](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter mais detalhes. Para um passeio com uma caixa de utilização, consulte a Carregue 1 TB no Armazém de [Dados Azure SQL em menos de 15 minutos com](data-factory-load-sql-data-warehouse.md)a Azure Data Factory .

### <a name="file-based-data-stores"></a>Lojas de dados baseadas em ficheiros
*(Inclui armazenamento blob, Data Lake Store, Amazon S3, sistemas de ficheiros no local e HDFS no local)*

* Tamanho médio do **ficheiro e contagem de ficheiros:** Copy Activity transfere dados de um ficheiro de cada vez. Com a mesma quantidade de dados a serem movidos, a entrada global é menor se os dados forem compostos por muitos pequenos ficheiros em vez de alguns ficheiros grandes devido à fase de armadilha de botas para cada ficheiro. Portanto, se possível, combine pequenos ficheiros em ficheiros maiores para obter uma maior entrada.
* **Formato de ficheiros e compressão**: Para mais formas de melhorar o desempenho, consulte as [Considerações para a serialização e desserialização](#considerations-for-serialization-and-deserialization) e considerações para as secções de [compressão.](#considerations-for-compression)
* Para o cenário do **sistema de ficheiros no local,** no qual é necessário o Gateway de Gestão de **Dados,** consulte a secção [Considerações para a Gestão](#considerations-for-data-management-gateway) de Dados gateway.

### <a name="relational-data-stores"></a>Lojas de dados relacionais
*(Inclui base de dados SQL; Armazém de Dados SQL; Amazon Redshift; Bases de dados do Servidor SQL; e oracle, MySQL, DB2, Teradata, Sybase e PostgreSQL bases, etc.)*

* **Padrão de dados**: O seu esquema de tabela afeta a entrada de cópias. Um grande tamanho da linha dá-lhe um desempenho melhor do que o tamanho da linha pequena, para copiar a mesma quantidade de dados. A razão é que a base de dados pode recuperar mais eficientemente menos lotes de dados que contêm menos linhas.
* **Consulta ou procedimento armazenado**: Otimize a lógica da consulta ou do procedimento armazenado que especifica na fonte de Copy Activity para obter dados de forma mais eficiente.
* Para **bases de dados relacionais no local**, como o SQL Server e o Oracle, que requerem a utilização do Portal de Gestão de **Dados,** consulte as considerações para a secção Gateway de Gestão de Dados.

## <a name="considerations-for-the-sink"></a>Considerações para o lavatório
### <a name="general"></a>Geral
Certifique-se de que a loja de dados subjacente não está sobrecarregada por outras cargas de trabalho que estão a decorrer sobre ou contra.

Para as lojas de dados da Microsoft, consulte tópicos de [monitorização e afinação específicos](#performance-reference) das lojas de dados. Estes tópicos podem ajudá-lo a entender as características de desempenho da loja de dados e como minimizar os tempos de resposta e maximizar a produção.

Se estiver a copiar dados do **armazenamento blob** para **o SQL Data Warehouse,** considere utilizar a **PolyBase** para aumentar o desempenho. Consulte [a Utilização da PolyBase para carregar dados no Armazém de Dados Azure SQL](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter mais detalhes. Para um passeio com uma caixa de utilização, consulte a Carregue 1 TB no Armazém de [Dados Azure SQL em menos de 15 minutos com](data-factory-load-sql-data-warehouse.md)a Azure Data Factory .

### <a name="file-based-data-stores"></a>Lojas de dados baseadas em ficheiros
*(Inclui armazenamento blob, Data Lake Store, Amazon S3, sistemas de ficheiros no local e HDFS no local)*

* **Comportamento de cópia**: Se copiar dados de uma loja de dados baseada em ficheiros diferentes, a Copy Activity tem três opções através da propriedade **copyBehavior.** Preserva a hierarquia, achata a hierarquia ou funde ficheiros. Ou a preservação ou a achatamento da hierarquia tem pouca ou nenhuma sobrecarga de desempenho, mas a fusão de ficheiros faz com que a sobrecarga de desempenho aumente.
* **Formato de ficheiros e compressão**: Consulte as [considerações para a serialização e desserialização](#considerations-for-serialization-and-deserialization) e [considerações para](#considerations-for-compression) as secções de compressão para obter mais formas de melhorar o desempenho.
* **Armazenamento blob**: Atualmente, o armazenamento Blob suporta apenas blocos blobs para transferência e saída de dados otimizados.
* Para **cenários de sistemas de ficheiros no local** que exijam a utilização do Gateway de Gestão de **Dados,** consulte a secção [Considerações para gateway](#considerations-for-data-management-gateway) de gestão de dados.

### <a name="relational-data-stores"></a>Lojas de dados relacionais
*(Inclui base de dados SQL, Armazém de Dados SQL, bases de dados do Servidor SQL e bases de dados oracle)*

* **Comportamento**de cópia : Dependendo das propriedades que definiu para **sqlSink**, Copy Activity escreve dados para a base de dados de destino de diferentes maneiras.
  * Por predefinição, o serviço de movimento de dados utiliza a API copy a granel para inserir dados no modo apêndice, que proporciona o melhor desempenho.
  * Se configurar um procedimento armazenado na pia, a base de dados aplica os dados uma linha de cada vez em vez de uma carga a granel. O desempenho diminui significativamente. Se o seu conjunto de dados for grande, quando aplicável, considere mudar para a utilização da propriedade **sqlWriterCleanupScript.**
  * Se configurar a propriedade **sqlWriterCleanupScript** para cada execução de Copy Activity, o serviço aciona o script e, em seguida, utiliza a API copy a granel para inserir os dados. Por exemplo, para substituir toda a tabela com os dados mais recentes, pode especificar um script para primeiro apagar todos os registos antes de carregar a granel os novos dados a partir da fonte.
* **Padrão de dados e tamanho do lote:**
  * O seu esquema de mesa afeta a entrada de cópias. Para copiar a mesma quantidade de dados, um grande tamanho da linha dá-lhe um melhor desempenho do que um pequeno tamanho de linha porque a base de dados pode mais eficientemente cometer menos lotes de dados.
  * A Atividade de Cópia insere os dados numa série de lotes. Pode definir o número de linhas num lote utilizando a propriedade **writeBatchSize.** Se os seus dados têm linhas pequenas, pode definir a propriedade **writeBatchSize** com um valor mais elevado para beneficiar de sobrecargas de lote mais baixas e maior produção. Se o tamanho da linha dos seus dados for grande, tenha cuidado quando aumentar o **writeBatchSize**. Um valor elevado pode levar a uma falha de cópia causada pela sobrecarga da base de dados.
* Para **bases de dados relacionais no local** como o SQL Server e o Oracle, que requerem a utilização do Portal de Gestão de **Dados,** consulte a secção [Considerações para gateway](#considerations-for-data-management-gateway) de gestão de dados.

### <a name="nosql-stores"></a>Lojas NoSQL
*(Inclui armazenamento de mesa e Azure Cosmos DB)*

* Para **armazenamento de mesas:**
  * **Partição**: Escrever dados para divisórias intercaladas degrada drasticamente o desempenho. Separe os dados de origem através da chave de partição para que os dados sejam inseridos de forma eficiente numa divisória após a outra, ou ajuste a lógica para escrever os dados numa única divisória.
* Para **Azure Cosmos DB:**
  * **Tamanho do lote**: A propriedade **writeBatchSize** define o número de pedidos paralelos ao serviço Azure Cosmos DB para criar documentos. Você pode esperar um melhor desempenho quando você aumentar **writeBatchSize** porque mais pedidos paralelos são enviados para Azure Cosmos DB. No entanto, tenha cuidado com a estrangulamento quando escrever para o Azure Cosmos DB (a mensagem de erro é "Taxa de pedido é grande"). Vários fatores podem causar estrangulamento, incluindo o tamanho do documento, o número de termos nos documentos e a política de indexação da coleção de alvos. Para obter uma maior cópia de entrada, considere usar uma melhor coleção, por exemplo, S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Considerações para a serialização e desserialização
A serialização e a desserialização podem ocorrer quando o conjunto de dados de entrada ou o conjunto de dados de saída for um ficheiro. Consulte [formatos de ficheiros suportados e de compressão](data-factory-supported-file-and-compression-formats.md) com detalhes sobre formatos de ficheirosuportados pela Copy Activity.

**Comportamento de cópia:**

* Copiar ficheiros entre lojas de dados baseadas em ficheiros:
  * Quando os conjuntos de dados de entrada e de saída têm as mesmas definições de formato de ficheiro ou não, o serviço de movimento de dados executa uma cópia binária sem qualquer serialização ou desserialização. Você vê uma maior potência em comparação com o cenário, em que as definições de formato de ficheiro de origem e pia são diferentes umas das outras.
  * Quando os conjuntos de dados de entrada e de saída estão ambos em formato de texto e apenas o tipo de codificação é diferente, o serviço de movimento de dados apenas codifica a conversão. Não faz nenhuma serialização e desserialização, o que causa alguma sobrecarga de desempenho em comparação com uma cópia binária.
  * Quando os conjuntos de dados de entrada e de saída têm formatos de ficheiros diferentes ou configurações diferentes, como delimitadores, o serviço de movimento de dados desserializa os dados de origem para transmitir, transformar e, em seguida, serializá-los no formato de saída que indicou. Esta operação resulta numa sobrecarga de desempenho muito mais significativa em comparação com outros cenários.
* Quando copia ficheiros de/para uma loja de dados que não seja baseada em ficheiros (por exemplo, de uma loja baseada em ficheiros para uma loja relacional), é necessário o passo de serialização ou desserialização. Este passo resulta em sobrecarga significativa de desempenho.

**Formato de ficheiro**: O formato de ficheiro que escolher pode afetar o desempenho da cópia. Por exemplo, o Avro é um formato binário compacto que armazena metadados com dados. Tem um amplo apoio no ecossistema Hadoop para o processamento e consulta. No entanto, o Avro é mais caro para a serialização e desserialização, o que resulta em uma cópia mais baixa em comparação com o formato de texto. Faça a sua escolha de formato de ficheiro em todo o fluxo de processamento de forma holisticamente. Comece com a forma como os dados são armazenados, armazéns de dados de origem ou para serem extraídos de sistemas externos; o melhor formato para armazenamento, processamento analítico e consulta; e em que formato os dados devem ser exportados para data marts para ferramentas de reporte e visualização. Por vezes, um formato de ficheiro que é subideal para o desempenho de leitura e escrita pode ser uma boa escolha quando se considera o processo analítico geral.

## <a name="considerations-for-compression"></a>Considerações de compressão
Quando o seu conjunto de dados de entrada ou saída for um ficheiro, pode definir a Atividade da Cópia para realizar a compressão ou descompressão à medida que escreve dados para o destino. Ao escolher a compressão, faça uma compensação entre a entrada/saída (I/O) e a CPU. Comprimir os dados custa extra em recursos computacionais. Mas em troca, reduz a rede de I/O e o armazenamento. Dependendo dos seus dados, poderá ver um aumento na entrada de cópias globais.

**Codec**: Copy Activity suporta tipos de zip, bzip2 e desinchar. O Azure HDInsight pode consumir os três tipos para processamento. Cada codificador de compressão tem vantagens. Por exemplo, bzip2 tem a mais baixa entrada de cópia, mas você obtém o melhor desempenho de consulta da Hive com bzip2 porque você pode dividi-lo para processamento. Gzip é a opção mais equilibrada, e é usada com mais frequência. Escolha o codec que melhor se adequa ao seu cenário de ponta a ponta.

**Nível:** Pode escolher entre duas opções para cada codec de compressão: comprimido mais rápido e devidamente comprimido. A opção mais rápida comprimida comprime os dados o mais rapidamente possível, mesmo que o ficheiro resultante não esteja devidamente comprimido. A opção idealmente comprimido gasta mais tempo em compressão e produz uma quantidade mínima de dados. Pode testar ambas as opções para ver o que proporciona um melhor desempenho geral no seu caso.

**Uma consideração**: Para copiar uma grande quantidade de dados entre uma loja no local e a nuvem, considere utilizar o armazenamento provisório de blob com compressão. A utilização do armazenamento provisório é útil quando a largura de banda da sua rede corporativa e dos seus serviços Azure é o fator limitativo, e pretende que o conjunto de dados de entrada e o conjunto de dados de saída estejam ambos sob forma descomprimidos. Mais especificamente, pode quebrar uma única atividade de cópia em duas atividades de cópia. A primeira cópia copia a fonte para uma bolha provisória ou de encenação sob forma comprimido. A segunda atividade copia os dados comprimidos da encenação e, em seguida, descomprime-se enquanto escreve para a pia.

## <a name="considerations-for-column-mapping"></a>Considerações para mapeamento de colunas
Pode definir a propriedade **columnMappings** na Copy Activity para mapear a toda ou um subconjunto das colunas de entrada para as colunas de saída. Depois de o serviço de movimento de dados ler os dados da fonte, tem de realizar o mapeamento da coluna nos dados antes de escrever os dados para a pia. Este processamento extra reduz a entrada de cópia.

Se a sua loja de dados de origem for consultada, por exemplo, se for uma loja relacional como a Base de Dados SQL ou o Servidor SQL, ou se for uma loja NoSQL como o armazenamento de mesa ou o Azure Cosmos DB, considere empurrar a tecnologia de filtragem e reordenação da coluna para a propriedade de **consulta** em vez de usar o mapeamento de colunas. Desta forma, a projeção ocorre enquanto o serviço de movimento de dados lê os dados da loja de dados de origem, onde é muito mais eficiente.

## <a name="other-considerations"></a>Outras considerações
Se o tamanho dos dados que pretende copiar for grande, pode ajustar a sua lógica de negócio para continuar a dividir os dados utilizando o mecanismo de corte na Data Factory. Em seguida, agendar a Atividade de Cópia para executar com mais frequência para reduzir o tamanho dos dados para cada execução de Copy Activity.

Tenha cuidado com o número de conjuntos de dados e atividades de cópia que exigem que a Data Factory conecte a mesma loja de dados ao mesmo tempo. Muitos trabalhos de cópia simultâneas podem estrangular uma loja de dados e levar a um desempenho degradado, copiar retenta internas de trabalho e, em alguns casos, falhas de execução.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Cenário da amostra: Cópia de um SQL Server no local para armazenamento blob
**Cenário**: Um pipeline foi construído para copiar dados de um Servidor SQL no local para armazenamento Blob em formato CSV. Para tornar o trabalho de cópia mais rápido, os ficheiros CSV devem ser comprimidos em formato bzip2.

**Teste e análise**: A entrada da Atividade de Cópia é inferior a 2 MBps, o que é muito mais lento do que o referencial de desempenho.

**Análise de desempenho e afinação**: Para resolver problemas com a questão do desempenho, vamos ver como os dados são processados e movidos.

1. **Leia os dados**: Gateway abre uma ligação ao SQL Server e envia a consulta. O SQL Server responde enviando o fluxo de dados para Gateway através da intranet.
2. **Serialize e comprima dados**: Gateway serializa o fluxo de dados para o formato CSV e comprime os dados para um fluxo bzip2.
3. **Escrever dados**: Gateway envia o fluxo bzip2 para o armazenamento Blob através da Internet.

Como pode ver, os dados estão a ser processados e movidos de forma sequencial de streaming: SQL Server > LAN > Gateway > wan > blob. **O desempenho global é fechado pela entrada mínima através do gasoduto**.

![Fluxo de dados](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Um ou mais dos seguintes fatores podem causar o estrangulamento do desempenho:

* **Fonte**: O próprio SQL Server tem baixa de entrada devido a cargas pesadas.
* **Gateway de Gestão de Dados:**
  * **LAN**: Gateway está localizado longe da máquina SQL Server e tem uma ligação de baixa largura de banda.
  * **Gateway**: Gateway atingiu as suas limitações de carga para realizar as seguintes operações:
    * **Serialização**: Serializar o fluxo de dados para o formato CSV tem uma entrada lenta.
    * **Compressão:** Escolheu um código de compressão lento (por exemplo, bzip2, que é de 2,8 MBps com Core i7).
  * **WAN**: A largura de banda entre a rede corporativa e os seus serviços Azure é baixa (por exemplo, T1 = 1.544 kbps; T2 = 6.312 kbps).
* **Pia**: O armazenamento de bolhas tem baixa entrada. (Este cenário é improvável porque o seu SLA garante um mínimo de 60 MBps.)

Neste caso, a compressão de dados bzip2 pode estar a abrandar todo o oleoduto. Mudar para um código de compressão gzip pode aliviar este estrangulamento.

## <a name="sample-scenarios-use-parallel-copy"></a>Cenários de amostra: Utilize cópia paralela
**Cenário I:** Copie 1.000 ficheiros de 1 MB do sistema de ficheiros no local para o armazenamento blob.

**Análise e afinação**de desempenho : Por exemplo, se tiver instalado o gateway numa máquina quad core, data Factory utiliza 16 cópias paralelas para mover ficheiros do sistema de ficheiros para o armazenamento Blob simultaneamente. Esta execução paralela deve resultar em alta sueste. Também pode especificar explicitamente a contagem de cópias paralelas. Quando copia muitos ficheiros pequenos, cópias paralelas ajudam dramaticamente a ser repostas utilizando recursos de forma mais eficaz.

![Cenário 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Cenário II**: Copiar 20 blobs de 500 MB cada, desde o armazenamento blob até ao Data Lake Store Analytics, e depois afinar o desempenho.

**Análise e afinação**de desempenho : Neste cenário, a Data Factory copia os dados do armazenamento blob para data lake Store utilizando unidades de movimento de dados de uma única cópia **(paralelas A** 1) e unidades de movimento de dados de nuvem única. A entrada observada será próxima da descrita na secção de referência de [desempenho](#performance-reference).

![Cenário 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Cenário III**: O tamanho individual dos ficheiros é superior a dezenas de MBs e o volume total é grande.

**Análise e viragem de desempenho**: Aumentar o **paraleloCópias** não resulta num melhor desempenho de cópia devido às limitações de recursos de um DMU de uma nuvem única. Em vez disso, deve especificar mais DMUs em nuvem para obter mais recursos para realizar o movimento de dados. Não especifique um valor para a propriedade **parallelCopies.** Data Factory trata do paralelismo para si. Neste caso, se definir **cloudDataMovementUnits** para 4, ocorre uma entrada de cerca de quatro vezes.

![Cenário 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Referência
Aqui estão as referências de monitorização e afinação de desempenho para algumas das lojas de dados suportadas:

* Armazenamento Azure Blob: Metas de [escalabilidade e desempenho para armazenamento blob](../../storage/blobs/scalability-targets.md) e performance e lista de verificação de [escalabilidade para armazenamento Blob](../../storage/blobs/storage-performance-checklist.md).
* Armazenamento de mesa azul: Metas de [escalabilidade e desempenho para armazenamento](../../storage/tables/scalability-targets.md) de mesa e performance e lista de verificação de [escalabilidade para armazenamento](../../storage/tables/storage-performance-checklist.md)de mesa .
* Base de Dados Azure SQL: Pode [monitorizar o desempenho](../../sql-database/sql-database-single-database-monitor.md) e verificar a percentagem da unidade de transações de base de dados (DTU)
* Armazém de Dados Azure SQL: A sua capacidade é medida em unidades de armazém de dados (DWUs); ver Gerir a potência da computação no Armazém de [Dados Azure SQL (visão geral)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [Níveis de desempenho em Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* On-premiseS SQL Server: [Monitor e sintonização para desempenho](https://msdn.microsoft.com/library/ms189081.aspx)
* Servidor de ficheiros no local: [Sintonização de desempenho para servidores](https://msdn.microsoft.com/library/dn567661.aspx) de ficheiros
