---
title: Guia de desempenho e escalabilidade da atividade de cópia no Azure Data Factory
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
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 701eaad8d36b352e946ae8d74204876b41ecb53d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678270"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Guia de desempenho e escalabilidade da atividade de cópia
> [!div class="op_single_selector" title1="Selecione a versão do Azure Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-copy-activity-performance.md)
> * [Versão atual](copy-activity-performance.md)

Se você deseja executar uma migração de dados em larga escala do data Lake ou Enterprise data warehouse (EDW) para o Azure ou se deseja ingerir dados em escala de diferentes fontes para o Azure para Big Data Analytics, é essencial obter um desempenho ideal e escalabilidade.  O Azure Data Factory fornece um mecanismo de desempenho, resiliente e econômico para ingerir dados em escala, tornando-o uma ótima opção para os engenheiros de dados que buscam criar pipelines de ingestão de dados altamente funcionais e escalonáveis.

Depois de ler este artigo, você poderá responder às seguintes perguntas:

- Que nível de desempenho e escalabilidade posso conseguir usando a atividade de cópia do ADF para cenários de migração de dados e de ingestão de dados?

- Quais etapas devo tomar para ajustar o desempenho da atividade de cópia do ADF?
- Quais botões de otimização de desempenho do ADF posso utilizar para otimizar o desempenho de uma única execução da atividade de cópia?
- Quais outros fatores fora do ADF a considerar ao otimizar o desempenho da cópia?

> [!NOTE]
> Se você não estiver familiarizado com a atividade de cópia em geral, consulte a [visão geral da atividade de cópia](copy-activity-overview.md) antes de ler este artigo.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Desempenho de cópia e escalabilidade atingíveis usando o ADF

O ADF oferece uma arquitetura sem servidor que permite paralelismo em diferentes níveis, o que permite aos desenvolvedores criar pipelines para utilizar totalmente a largura de banda da rede, bem como IOPS de armazenamento e largura de banda para maximizar a taxa de transferência de movimentação de dados para o seu ambiente.  Isso significa que a taxa de transferência que você pode obter pode ser estimada medindo a taxa de transferência mínima oferecida pelo armazenamento de dados de origem, o armazenamento de dados de destino e a largura de banda de rede entre a origem e o destino.  A tabela a seguir calcula a duração da cópia com base no tamanho dos dados e no limite de largura de banda do seu ambiente. 

| Tamanho dos dados/ <br/> Larga | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | mínimo de 2,7    | mínimo de 1,4   | mínimo de 0,3   | mínimo de 0,1  | mínimo de 0, 3 | mínimo de 0, 1 | mínimo de 0,0   |
| **10 GB**                   | mínimo de 27,3   | mínimo de 13,7  | mínimo de 2,7   | mínimo de 1,3  | mínimo de 0,3  | mínimo de 0,1  | mínimo de 0, 3  |
| **100 GB**                  | 4,6 horas    | 2,3 horas   | 0,5 horas   | 0,2 horas  | 0, 5 horas | 0, 2 horas | 0,0 horas   |
| **1 TB**                    | 46,6 horas   | 23,3 horas  | 4,7 horas   | 2,3 horas  | 0,5 horas  | 0,2 horas  | 0, 5 horas  |
| **10 TB**                   | 19,4 dias  | 9,7 dias  | 1,9 dias  | 0,9 dias | 0,2 dias | 0,1 dias | 0, 2 dias |
| **100 TB**                  | 194,2 dias | 97,1 dias | 19,4 dias | 9,7 dias | 1,9 dias | 1 dias   | 0,2 dias  |
| **1 PB**                    | 64,7 mo    | 32,4 mo   | 6,5 mo    | 3,2 mo   | 0,6 mo   | 0,3 mo   | 0, 6 mo   |
| **10 PB**                   | 647,3 mo   | 323,6 mo  | 64,7 mo   | 31,6 mo  | 6,5 mo   | 3,2 mo   | 0,6 mo    |

A cópia do ADF é escalonável em diferentes níveis:

![como a cópia do ADF é dimensionada](media/copy-activity-performance/adf-copy-scalability.png)

- O fluxo de controle do ADF pode iniciar várias atividades de cópia em paralelo, por exemplo, usando [loop for each](control-flow-for-each-activity.md).
- Uma única atividade de cópia pode aproveitar os recursos de computação escalonáveis: ao usar Azure Integration Runtime, você pode especificar [até 256 DIUs](#data-integration-units) para cada atividade de cópia de maneira sem servidor; ao usar o Integration Runtime auto-hospedado, você pode escalar verticalmente o computador manualmente ou escalar horizontalmente para vários computadores ([até 4 nós](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) e uma única atividade de cópia particionará seu conjunto de arquivos em todos os nós.
- Uma única atividade de cópia lê e grava no armazenamento de dados usando vários threads [em paralelo](#parallel-copy).

## <a name="performance-tuning-steps"></a>Etapas de ajuste de desempenho

Siga estas etapas para ajustar o desempenho do seu serviço de Azure Data Factory com a atividade de cópia.

1. **Pegue um conjunto de testes e estabeleça uma linha de base.** Durante a fase de desenvolvimento, teste seu pipeline usando a atividade de cópia em um exemplo de dados representativos. O conjunto de dados que você escolher deve representar seus padrões de dado típicos (estrutura de pastas, padrão de arquivo, esquema de dados, etc.) e é grande o suficiente para avaliar o desempenho de cópia, por exemplo, demora 10 minutos ou mais para que a atividade de cópia seja concluída. Coletar detalhes de execução e características de desempenho após o [monitoramento da atividade de cópia](copy-activity-overview.md#monitoring).

2. **Como maximizar o desempenho de uma única atividade de cópia**:

   Para começar, recomendamos que você primeiro maximize o desempenho usando uma única atividade de cópia.

   **Se a atividade de cópia estiver sendo executada em um Azure Integration Runtime:**

   Comece com valores padrão para [DIU (unidades de integração de dados)](#data-integration-units) e configurações de [cópia paralelas](#parallel-copy) .  Realize uma execução de teste de desempenho e anote o desempenho obtido, bem como os valores reais usados para DIUs e cópias paralelas.  Consulte [monitoramento de atividade de cópia](copy-activity-overview.md#monitoring) sobre como coletar resultados de execução e configurações de desempenho usadas.

   Agora, realize execuções de teste de desempenho adicionais, a cada vez que o valor da configuração de DIU é duplicado.  Como alternativa, se você acredita que o desempenho obtido usando a configuração padrão está muito abaixo da sua expectativa, você pode aumentar a configuração DIU de forma mais drástica na execução de teste subsequente.

   A atividade de cópia deve ser dimensionada quase perfeitamente à medida que você aumenta a configuração DIU.  Se, ao dobrar a configuração DIU, você não estiver vendo a taxa de transferência, duas coisas podem estar ocorrendo:

   - O padrão de cópia específico que você está executando não se beneficia da adição de mais DIUs.  Embora você tenha especificado um valor maior de DIU, o DIU real usado permaneceu o mesmo e, portanto, você está obtendo a mesma taxa de transferência como antes.  Se esse for o caso, maximize a taxa de transferência agregada executando várias cópias ao fazer referência à etapa 3 simultaneamente.
   - Ao adicionar mais DIUs (mais potência) e, assim, orientar a taxa mais alta de extração, transferência e carregamento de dados, o armazenamento de dados de origem, a rede entre o ou o armazenamento de dados de destino atingiu seu afunilamento e possivelmente sendo limitado.  Se esse for o caso, tente entrar em contato com o administrador do armazenamento de dados ou o administrador da rede para aumentar o limite superior ou, como alternativa, reduza a configuração DIU até que a limitação pare de ocorrer.

   **Se a atividade de cópia estiver sendo executada em um Integration Runtime hospedado automaticamente:**

   É recomendável que você use um computador dedicado separado do servidor que hospeda o repositório de dados para hospedar o Integration Runtime.

   Comece com valores padrão para a configuração de [cópia paralela](#parallel-copy) e use um único nó para o ir auto-hospedado.  Execute uma execução de teste de desempenho e anote o desempenho obtido.

   Se você quiser obter uma taxa de transferência mais alta, poderá escalar verticalmente ou escalar horizontalmente o IR do modo auto-hospedado:

   - Se a CPU e a memória disponível no nó IR de hospedagem interna não forem totalmente utilizadas, mas a execução de trabalhos simultâneos estiver atingindo o limite, você deverá escalar verticalmente aumentando o número de trabalhos simultâneos que podem ser executados em um nó.  Consulte [aqui](create-self-hosted-integration-runtime.md#scale-up) para obter instruções.
   - Se, por outro lado, a CPU estiver alta no nó IR auto-hospedado ou se a memória disponível estiver baixa, você poderá adicionar um novo nó para ajudar a escalar horizontalmente a carga entre os vários nós.  Consulte [aqui](create-self-hosted-integration-runtime.md#high-availability-and-scalability) para obter instruções.

   À medida que você escalar verticalmente ou expandir a capacidade do IR auto-hospedado, repita a execução do teste de desempenho para ver se você está obtendo uma taxa de transferência cada vez mais melhor.  Se a taxa de transferência parar de melhorar, provavelmente o armazenamento de dados de origem, a rede entre o ou o armazenamento de dados de destino atingiu seu gargalo e está começando a ser limitado. Se esse for o caso, tente entrar em contato com o administrador do armazenamento de dados ou o administrador da rede para aumentar o limite superior ou, como alternativa, volte para a configuração de dimensionamento anterior para o IR de hospedagem interna. 

3. **Como maximizar a taxa de transferência agregada executando várias cópias simultaneamente:**

   Agora que você maximizou o desempenho de uma única atividade de cópia, se ainda não tiver atingido os limites superiores da taxa de transferência do seu ambiente – rede, armazenamento de dados de origem e armazenamento de dados de destino-você pode executar várias atividades de cópia em paralelo usando o ADF construções de fluxo de controle, como [loop for each](control-flow-for-each-activity.md).

4. **Dicas de ajuste de desempenho e recursos de otimização.** Em alguns casos, ao executar uma atividade de cópia no Azure Data Factory, você verá uma mensagem "dicas de ajuste de desempenho" sobre o [monitoramento da atividade de cópia](copy-activity-overview.md#monitor-visually), conforme mostrado no exemplo a seguir. A mensagem informa o afunilamento que foi identificado para a execução de cópia fornecida. Ele também orienta você sobre o que mudar para impulsionar a produtividade da cópia. As dicas de ajuste de desempenho atualmente fornecem sugestões como:

   - Use o polybase ao copiar dados para o Azure SQL Data Warehouse.
   - Aumente Azure Cosmos DB unidades de solicitação ou DTUs do banco de dados SQL do Azure (unidades de produtividade do banco de dado) quando o recurso no lado do repositório de armazenamento for o afunilamento.
   - Remova a cópia preparada desnecessária.

   As regras de ajuste de desempenho também serão aprimoradas gradualmente.

   **Exemplo: copiar para o banco de dados SQL do Azure com dicas de ajuste de desempenho**

   Neste exemplo, durante uma execução de cópia, Azure Data Factory observa que o banco de dados SQL do Azure do coletor atinge alta utilização de DTU, o que reduz as operações de gravação. A sugestão é aumentar a camada do banco de dados SQL do Azure com mais DTUs. 

   ![Copiar monitoramento com dicas de ajuste de desempenho](media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

   Além disso, estes são alguns dos recursos de otimização de desempenho dos quais você deve estar atento:

   - [Cópia paralela](#parallel-copy)
   - [Unidades de Integração de Dados](#data-integration-units)
   - [Cópia em etapas](#staged-copy)
   - [Escalabilidade do tempo de execução de integração auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime)

5. **Expanda a configuração para todo o conjunto de seus conjuntos de seus.** Quando estiver satisfeito com os resultados e o desempenho da execução, você poderá expandir a definição e o pipeline para abranger todo o seu conjunto de todos.

## <a name="copy-performance-optimization-features"></a>Copiar recursos de otimização de desempenho

O Azure Data Factory fornece os seguintes recursos de otimização de desempenho:

- [Cópia paralela](#parallel-copy)
- [Unidades de Integração de Dados](#data-integration-units)
- [Cópia em etapas](#staged-copy)

### <a name="data-integration-units"></a>Unidades de integração de dados

Uma unidade de integração de dados é uma medida que representa a potência (uma combinação de CPU, memória e alocação de recursos de rede) de uma única unidade no Azure Data Factory. A unidade de integração de dados só se aplica ao [Integration Runtime do Azure](concepts-integration-runtime.md#azure-integration-runtime), mas não ao [tempo de execução de integração auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime).

Você será cobrado **# of used DIUs \* duração da cópia \* preço unitário/DIU-hora**. Veja os preços atuais [aqui](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/). A moeda local e a descontagem separada podem ser aplicadas por tipo de assinatura.

O DIUs permitido para capacitar uma execução da atividade de cópia é **entre 2 e 256**. Se não for especificado ou você escolher "auto" na interface do usuário, Data Factory aplicar dinamicamente a configuração ideal de DIU com base no seu par de coletor de origem e no padrão de dados. A tabela a seguir lista os DIUs padrão usados em diferentes cenários de cópia:

| Cenário de cópia | DIUs padrão determinado pelo serviço |
|:--- |:--- |
| Copiar dados entre armazenamentos baseados em arquivo | Entre 4 e 32, dependendo do número e do tamanho dos arquivos |
| Copiar dados para o banco de dados SQL do Azure ou Azure Cosmos DB |Entre 4 e 16 dependendo da camada do banco de dados SQL do Azure ou do Cosmos DB (número de DTUs/RUs) |
| Todos os outros cenários de cópia | 4 |

Para substituir esse padrão, especifique um valor para a propriedade **dataIntegrationUnits** da seguinte maneira. O *número real de DIUs* que a operação de cópia usa em tempo de execução é igual ou menor que o valor configurado, dependendo do seu padrão de dados.

Você pode ver o DIUs usado para cada execução de cópia na saída da atividade de cópia ao monitorar uma execução de atividade. Para obter mais informações, consulte [monitoramento de atividade de cópia](copy-activity-overview.md#monitoring).

> [!NOTE]
> A configuração de DIUs maior que quatro atualmente se aplica somente quando você copia vários arquivos do blob do Azure/ADLS Gen1/ADLS Gen2/Amazon S3/Google Cloud Storage/nuvem FTP/Cloud SFTP ou do armazenamento de dados relacionais de nuvem habilitado para a opção de partição (incluindo [Oracle ](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)) a qualquer outro armazenamento de dados de nuvem.

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

Você pode usar a propriedade **parallelCopies** para indicar o paralelismo que você deseja que a atividade de cópia use. Você pode considerar essa propriedade como o número máximo de threads na atividade de cópia que pode ler de sua origem ou gravar em armazenamentos de dados de coletor em paralelo.

Para cada execução de atividade de cópia, Azure Data Factory determina o número de cópias paralelas a serem usadas para copiar dados do armazenamento de dados de origem e para o armazenamento de dados de destino. O número padrão de cópias paralelas que ele usa depende do tipo de fonte e do coletor que você usa.

| Cenário de cópia | Contagem de cópia paralela padrão determinada pelo serviço |
| --- | --- |
| Copiar dados entre armazenamentos baseados em arquivo |Depende do tamanho dos arquivos e do número de DIUs usados para copiar dados entre dois armazenamentos de dados de nuvem ou a configuração física do computador do Integration Runtime de hospedagem interna. |
| Copiar do repositório de dados relacional com a opção de partição habilitada (incluindo [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [tabela SAP](connector-sap-table.md#sap-table-as-source)e [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source))|4 |
| Copiar dados de qualquer repositório de origem para o armazenamento de tabelas do Azure |4 |
| Todos os outros cenários de cópia |1 |

> [!TIP]
> Quando você copia dados entre repositórios baseados em arquivo, o comportamento padrão geralmente oferece a melhor taxa de transferência. O comportamento padrão é determinado automaticamente com base no padrão do arquivo de origem.

Para controlar a carga em computadores que hospedam seus armazenamentos de dados ou para ajustar o desempenho da cópia, você pode substituir o valor padrão e especificar um valor para a propriedade **parallelCopies** . O valor deve ser um número inteiro maior ou igual a 1. Em tempo de execução, para obter o melhor desempenho, a atividade de cópia usa um valor menor ou igual ao valor que você definiu.

**Pontos a serem observados:**

- Quando você copia dados entre repositórios baseados em arquivo, o **parallelCopies** determina o paralelismo no nível de arquivo. O agrupamento em um único arquivo ocorre abaixo de forma automática e transparente. Ele foi projetado para usar o melhor tamanho de bloco adequado para um determinado tipo de armazenamento de dados de origem para carregar dados em paralelo e ortogonal em **parallelCopies**. O número real de cópias paralelas que o serviço de movimentação de dados usa para a operação de cópia em tempo de execução não é mais do que o número de arquivos que você tem. Se o comportamento de cópia for **mergefile**, a atividade de cópia não poderá tirar proveito do paralelismo de nível de arquivo.
- Quando você copia dados de armazenamentos que não são baseados em arquivo (exceto [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [tabela SAP](connector-sap-table.md#sap-table-as-source)e conector de [Hub aberto SAP](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) como fonte com particionamento de dados habilitado) para armazenamentos que são baseados em arquivo, os dados o serviço de movimentação ignora a propriedade **parallelCopies** . Mesmo se o paralelismo for especificado, ele não será aplicado nesse caso.
- A propriedade **parallelCopies** é ortogonal a **dataIntegrationUnits**. O primeiro é contado em todas as unidades de integração de dados.
- Ao especificar um valor para a propriedade **parallelCopies** , considere o aumento de carga nos armazenamentos de dados de origem e do coletor. Considere também o aumento de carga para o tempo de execução de integração auto-hospedado se a atividade de cópia for habilitada por ti, por exemplo, para cópia híbrida. Esse aumento de carga ocorre especialmente quando você tem várias atividades ou execuções simultâneas das mesmas atividades executadas no mesmo armazenamento de dados. Se você observar que o armazenamento de dados ou o tempo de execução de integração auto-hospedado está sobrecarregado com a carga, diminua o valor de **parallelCopies** para aliviar a carga.

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

### <a name="staged-copy"></a>Cópia em etapas

Ao copiar dados de um armazenamento de dados de origem para um armazenamento de dados de coletor, você pode optar por usar o armazenamento de BLOBs como um armazenamento de preparo provisório. O preparo é especialmente útil nos seguintes casos:

- **Você deseja ingerir dados de vários armazenamentos de dados para SQL Data Warehouse por meio do polybase.** O SQL Data Warehouse usa o polybase como um mecanismo de alta taxa de transferência para carregar uma grande quantidade de dados em SQL Data Warehouse. Os dados de origem devem estar no armazenamento de BLOBs ou Azure Data Lake Store e devem atender a critérios adicionais. Ao carregar dados de um armazenamento de dados diferente do armazenamento de BLOBs ou Azure Data Lake Store, você pode ativar a cópia de dados por meio do armazenamento de blobs de preparo provisório. Nesse caso, Azure Data Factory executa as transformações de dados necessárias para garantir que ele atenda aos requisitos do polybase. Em seguida, ele usa o polybase para carregar dados em SQL Data Warehouse com eficiência. Para obter mais informações, veja [Use PolyBase to load data into Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) (Utilizar o PolyBase para carregar dados para o Azure SQL Data Warehouse).
- **Às vezes, demora um pouco para executar uma movimentação de dados híbrido (ou seja, copiar de um armazenamento de dados local para um armazenamento de dados de nuvem) em uma conexão de rede lenta.** Para melhorar o desempenho, você pode usar cópia em etapas para compactar os dados locais, de modo que leve menos tempo para mover dados para o armazenamento de dados de preparo na nuvem. Em seguida, você pode descompactar os dados no armazenamento de preparo antes de carregar no armazenamento de dados de destino.
- **Você não quer abrir portas que não sejam a porta 80 e a porta 443 em seu firewall devido a políticas corporativas de ti.** Por exemplo, ao copiar dados de um armazenamento de dados local para um coletor de banco de dados SQL do Azure ou um coletor de SQL Data Warehouse do Azure, você precisa ativar a comunicação TCP de saída na porta 1433 para o Firewall do Windows e o firewall corporativo. Nesse cenário, a cópia preparada pode aproveitar o tempo de execução de integração auto-hospedado para primeiro copiar dados para uma instância de preparo de armazenamento de BLOBs por HTTP ou HTTPS na porta 443. Em seguida, ele pode carregar os dados no banco de dados SQL ou SQL Data Warehouse do preparo do armazenamento de BLOBs. Nesse fluxo, você não precisa habilitar a porta 1433.

#### <a name="how-staged-copy-works"></a>Como funciona a cópia em etapas

Quando você ativa o recurso de preparo, primeiro os dados são copiados do armazenamento de dados de origem para o armazenamento de blob de preparo (Traga seu próprio). Em seguida, os dados são copiados do armazenamento de dados de preparo para o armazenamento de dados do coletor. Azure Data Factory gerencia automaticamente o fluxo de duas etapas para você. Azure Data Factory também limpa dados temporários do armazenamento de preparo após a conclusão da movimentação de dados.

![Cópia em etapas](media/copy-activity-performance/staged-copy.png)

Ao ativar a movimentação de dados usando um armazenamento de preparo, você pode especificar se deseja que os dados sejam compactados antes de mover os dados do armazenamento de dados de origem para um armazenamento de dados provisório ou de preparo e, em seguida, descompactados antes de mover dados de um dat provisório ou de preparo uma loja para o armazenamento de dados do coletor.

No momento, não é possível copiar dados entre dois armazenamentos de dados que estão conectados por meio do IRs hospedado internamente diferente, nem com nem sem cópia preparada. Para esse cenário, você pode configurar duas atividades de cópia encadeadas explicitamente para copiar da origem para o preparo e, em seguida, do preparo para o coletor.

#### <a name="configuration"></a>Configuração

Defina a configuração **enableStaging** na atividade de cópia para especificar se deseja que os dados sejam preparados no armazenamento de BLOBs antes de carregá-los em um armazenamento de dados de destino. Ao definir **enableStaging** como `TRUE`, especifique as propriedades adicionais listadas na tabela a seguir. Você também precisa criar um armazenamento do Azure ou um serviço vinculado à assinatura de acesso compartilhado de armazenamento para preparação, se você não tiver um.

| Propriedade | Descrição | Valor predefinido | Necessário |
| --- | --- | --- | --- |
| enableStaging |Especifique se deseja copiar dados por meio de um repositório de preparo provisório. |Falso |Não |
| linkedServiceName |Especifique o nome de um serviço vinculado [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) , que se refere à instância de armazenamento que você usa como um armazenamento de preparo provisório. <br/><br/> Você não pode usar o armazenamento com uma assinatura de acesso compartilhado para carregar dados em SQL Data Warehouse por meio do polybase. Você pode usá-lo em todos os outros cenários. |N/D |Sim, quando **enableStaging** é definido como true |
| Multi-Path |Especifique o caminho de armazenamento de BLOBs que você deseja que contenha os dados preparados. Se você não fornecer um caminho, o serviço criará um contêiner para armazenar dados temporários. <br/><br/> Especifique um caminho somente se você usar o armazenamento com uma assinatura de acesso compartilhado ou se precisar que dados temporários estejam em um local específico. |N/D |Não |
| enableCompression |Especifica se os dados devem ser compactados antes de serem copiados para o destino. Essa configuração reduz o volume de dados que está sendo transferido. |Falso |Não |

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

#### <a name="staged-copy-billing-impact"></a>Impacto de cobrança de cópia em etapas

Você é cobrado com base em duas etapas: copiar duração e copiar tipo.

* Quando você usa o preparo durante uma cópia de nuvem, que está copiando dados de um armazenamento de dados de nuvem para outro armazenamento de dados de nuvem, ambos os estágios capacitados pelo tempo de execução de integração do Azure, você é cobrado pela [soma da duração da cópia para a etapa 1 e etapa 2] x [preço unitário da cópia de nuvem].
* Quando você usa o preparo durante uma cópia híbrida, que está copiando dados de um armazenamento de dados local para um armazenamento de dados de nuvem, um estágio capacitado por um tempo de execução de integração auto-hospedado, você é cobrado pelo [duração da cópia híbrida] x [preço unitário da cópia híbrida] + [duração da cópia da nuvem] x [preço unitário da cópia de nuvem].

## <a name="references"></a>Referências

Aqui estão as referências de monitoramento e ajuste de desempenho para alguns dos armazenamentos de dados com suporte:

* Armazenamento do Azure, que inclui armazenamento de BLOBs e armazenamento de tabelas: [destinos de escalabilidade de armazenamento do Azure](../storage/common/storage-scalability-targets.md) e [lista de verificação de escalabilidade e desempenho do armazenamento do Azure](../storage/common/storage-performance-checklist.md)
* Banco de dados SQL do Azure: você pode [monitorar o desempenho](../sql-database/sql-database-single-database-monitor.md) e verificar a porcentagem de DTU (unidade de transação do banco de dados).
* SQL Data Warehouse do Azure: seu recurso é medido em unidades de data warehouse (DWUs). Consulte [gerenciar poder de computação no Azure SQL data warehouse (visão geral)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [níveis de desempenho no Azure Cosmos DB](../cosmos-db/performance-levels.md).
* SQL Server local: [monitorar e ajustar o desempenho](https://msdn.microsoft.com/library/ms189081.aspx).
* Servidor de arquivos local: [ajuste de desempenho para servidores de arquivos](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Passos seguintes
Consulte os outros artigos de atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Use Azure Data Factory para migrar dados de seu data Lake ou data warehouse para o Azure](data-migration-guidance-overview.md)
- [Migrar dados do Amazon S3 para o armazenamento do Azure](data-migration-guidance-s3-azure-storage.md)
