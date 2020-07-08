---
title: Mapeamento do desempenho do fluxo de dados e guia de afinação
description: Conheça os fatores-chave que afetam o desempenho dos fluxos de dados de mapeamento na Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 07/06/2020
ms.openlocfilehash: 1c63568418f21da0556ced0d004e04e7909118fb
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042633"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Mapeamento de dados flui desempenho e guia de afinação

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mapear fluxos de dados na Azure Data Factory fornece uma interface livre de códigos para conceber, implementar e orquestrar transformações de dados em escala. Se não estiver familiarizado com os fluxos de dados de mapeamento, consulte a Visão Geral do [Fluxo de Dados de Mapeamento](concepts-data-flow-overview.md).

Ao conceber e testar fluxos de dados do ADF UX, certifique-se de ligar o modo de depuração para executar os fluxos de dados em tempo real sem esperar que um cluster aqueça. Para mais informações, consulte [o Modo Debug](concepts-data-flow-debug-mode.md).

Este vídeo mostra alguns timings de amostra transformando dados com fluxos de dados:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>Monitorização do desempenho do fluxo de dados

Ao conceber fluxos de dados de mapeamento, pode testar cada transformação clicando no separador de pré-visualização de dados no painel de configuração. Assim que verificar a sua lógica, teste o fluxo de dados de ponta a ponta como uma atividade num oleoduto. Adicione uma atividade de Fluxo de Dados executar e use o botão Debug para testar o desempenho do seu fluxo de dados. Para abrir o plano de execução e o perfil de desempenho do seu fluxo de dados, clique no ícone de óculos em "ações" no separador de saída do seu pipeline.

![Monitor de fluxo de dados](media/data-flow/mon002.png "Monitor de fluxo de dados 2")

 Pode utilizar esta informação para estimar o desempenho do seu fluxo de dados contra fontes de dados de diferentes dimensões. Para obter mais informações, consulte [monitorando os fluxos de dados de mapeamento](concepts-data-flow-monitoring.md).

![Monitorização do fluxo de dados](media/data-flow/mon003.png "Monitor de fluxo de dados 3")

 Para o depuração do gasoduto, é necessário cerca de um minuto de tempo de configuração do cluster nos seus cálculos globais de desempenho para um cluster quente. Se estiver a rubricar o tempo de funcionamento da integração Azure padrão, o tempo de rotação pode demorar cerca de 5 minutos.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Aumento do tamanho do cálculo no tempo de execução da integração Azure

Um Tempo de Integração com mais núcleos aumenta o número de nós nos ambientes de computação Spark e fornece mais poder de processamento para ler, escrever e transformar os seus dados. A ADF Data Flows utiliza a Spark para o motor de computação. O ambiente Spark funciona muito bem em recursos otimizados pela memória.

Recomendamos a utilização **de Memory Optimized** para a maior parte das cargas de trabalho. Poderá armazenar mais dados na memória e minimizar erros fora de memória. A memória otimizada tem um preço-ponto por núcleo mais elevado do que o Compute Optimized, mas provavelmente resultará em velocidades de transformação mais rápidas e oleodutos mais bem sucedidos. Se experimentar erros de memória ao executar os seus fluxos de dados, mude para uma configuração Azure IR otimizada de memória.

**Compute Optimized** pode ser suficiente para depurar e visualizar dados de um número limitado de linhas de dados. Compute Optimized provavelmente não funcionará tão bem com cargas de trabalho de produção.

![Novo IR](media/data-flow/ir-new.png "Novo IR")

Para obter mais informações sobre como criar um tempo de execução de [integração, consulte o tempo de execução da integração na Azure Data Factory.](concepts-integration-runtime.md)

### <a name="increase-the-size-of-your-debug-cluster"></a>Aumente o tamanho do seu cluster de depuração

Por predefinição, ligar o depurador utilizará o tempo de funcionamento padrão da Integração Azure que é criado automaticamente para cada fábrica de dados. Este Azure IR padrão está definido para oito núcleos, quatro para um nó condutor e quatro para um nó de trabalhador, usando propriedades da General Compute. À medida que testa com dados maiores, pode aumentar o tamanho do seu cluster de depurador criando um Azure IR com configurações maiores e escolher este novo Azure IR quando ligar o depurador. Isto instruirá a ADF a utilizar este Azure IR para visualização de dados e depuração de gasodutos com fluxos de dados.

### <a name="decrease-cluster-compute-start-up-time-with-ttl"></a>Diminuir o tempo de arranque do cálculo do cluster com o TTL

Existe uma propriedade no Azure IR em Data Flow Properties que lhe permitirá levantar um conjunto de recursos de computação de cluster para a sua fábrica. Com este pool, você pode submeter sequencialmente atividades de fluxo de dados para execução. Uma vez que a piscina esteja estabelecida, cada trabalho subsequente levará 1-2 minutos para o cluster Spark on-demand executar o seu trabalho. A configuração inicial do conjunto de recursos levará cerca de 6 minutos. Especifique o tempo que deseja manter o conjunto de recursos na definição de tempo de vida (TTL).

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse-synapse"></a>Otimização para Azure SQL Database e Azure SQL Data Warehouse Synapse

### <a name="partitioning-on-source"></a>Partição na fonte

1. Vá ao separador **Otimizar** e selecione **Definir Partição**
1. **Selecione Fonte**.
1. Em **Número de divisórias,** descreva o número máximo de ligações ao seu Azure SQL DB. Pode tentar uma configuração mais alta para obter ligações paralelas à sua base de dados. No entanto, alguns casos podem resultar num desempenho mais rápido com um número limitado de ligações.
1. Selecione se a divisão por uma coluna de mesa específica ou uma consulta.
1. Se selecionar **coluna,** escolha a coluna de partição.
1. Se selecionar **a Consulta,** insira uma consulta que corresponda ao esquema de partição da sua tabela de bases de dados. Esta consulta permite que o motor da base de dados de origem aproveite a eliminação da partição. As suas tabelas de bases de dados não precisam de ser divididas. Se a sua fonte ainda não estiver dividida, a ADF continuará a utilizar a divisão de dados no ambiente de transformação de Faíscas com base na chave que seleciona na transformação Source.

![Parte de Origem](media/data-flow/sourcepart3.png "Parte de Origem")

> [!NOTE]
> Um bom guia para ajudá-lo a escolher o número de divisórias para a sua fonte baseia-se no número de núcleos que definiu para o seu Tempo de Execução de Integração Azure e multiplica esse número por cinco. Assim, por exemplo, se estiver a transformar uma série de ficheiros nas suas pastas ADLS e utilizar um Azure IR de 32 núcleos, o número de divisórias que teria como alvo é de 32 x 5 = 160 divisições.

### <a name="source-batch-size-input-and-isolation-level"></a>Tamanho do lote de origem, entrada e nível de isolamento

De acordo com **as opções de origem** na transformação da fonte, as seguintes definições podem afetar o desempenho:

* O tamanho do lote instrui a ADF a armazenar dados em conjuntos na memória Spark em vez de linha a linha. O tamanho do lote é uma definição opcional e pode ficar sem recursos nos nós de computação se não forem dimensionados corretamente. Não configurar esta propriedade utilizará os predefinidos do lote de caching Spark.
* A definição de uma consulta pode permitir filtrar linhas na fonte antes de chegarem ao Data Flow para processamento. Isto pode tornar a aquisição inicial de dados mais rapidamente. Se utilizar uma consulta, pode adicionar sugestões de consulta opcionais para o seu DB Azure SQL, como READ UNCOMMITTEDED.
* Ler não comprometido fornecerá resultados de consulta mais rápidos na transformação da fonte

![Origem](media/data-flow/source4.png "Origem")

### <a name="sink-batch-size"></a>Tamanho do lote de pia

Para evitar o processamento de linha a linha dos fluxos de dados, defina o tamanho do **lote** no separador Definições para pias Azure SQL DB e Azure SQL DW. Se o tamanho do lote for definido, a base de dados de processos ADF escreve em lotes com base no tamanho fornecido. Não configurar esta propriedade utilizará os predefinidos do lote de caching Spark.

![Sink](media/data-flow/sink4.png "Sink")

### <a name="partitioning-on-sink"></a>Partição na pia

Mesmo que não tenha os seus dados divididos nas suas tabelas de destino, recomenda-se que os seus dados tenham dividido na transformação do lavatório. Os dados divididos resultam frequentemente em carregamentos muito mais rápidos sobre a obrigato dos ligações a utilizar um único nó/partição. Vá ao separador Otimize da pia e selecione a partição *Round Robin* para selecionar o número ideal de divisórias para escrever na pia.

### <a name="disable-indexes-on-write"></a>Desativar índices na escrita

No seu pipeline, adicione uma [atividade de Procedimento Armazenado](transform-data-using-stored-procedure.md) antes da sua atividade de Fluxo de Dados que desativa os índices nas tabelas-alvo escritas a partir da pia. Após a sua atividade de Fluxo de Dados, adicione outra atividade do Procedimento Armazenado que permita esses índices. Ou utilize os scripts de pré-processamento e pós-processamento numa pia de base de dados.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Aumente o tamanho do seu Azure SQL DB e DW

Agende um redimensionamento da sua fonte e afunde o Azure SQL DB e o DW antes do seu gasoduto funcionar para aumentar a potência e minimizar o estrangulamento do Azure assim que atingir os limites de DTU. Depois da execução do seu oleoduto estar concluída, redimensione as suas bases de dados de volta à sua taxa normal de funcionação.

* A tabela de origem SQL DB com 887k linhas e 74 colunas para uma tabela DB SQL com uma única transformação de coluna derivada leva cerca de 3 minutos de ponta a ponta usando memória otimizada 80 núcleos de depuração IRs.

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Azure Synapse SQL DW apenas] Use a encenação para carregar dados a granel através da Polybase

Para evitar inserções de linha a linha no seu DW, verifique **ativar a paragem** nas definições do lavatório para que a ADF possa utilizar [o PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). A PolyBase permite que a ADF carregue os dados a granel.
* Quando executar a sua atividade de fluxo de dados a partir de um oleoduto, terá de selecionar um local de armazenamento Blob ou ADLS Gen2 para encenar os seus dados durante o carregamento a granel.

* A fonte de ficheiro de 421Mb com 74 colunas para uma tabela Synapse e uma única transformação de coluna derivada demora cerca de 4 minutos de ponta a ponta usando memória otimizada 80 núcleos de 80 núcleos de IRs de depuração.

## <a name="optimizing-for-files"></a>Otimização para ficheiros

Em cada transformação, pode definir o esquema de partição que deseja que a fábrica de dados utilize no separador Otimize. É uma boa prática testar primeiro os lavatórios baseados em ficheiros mantendo a partição padrão e otimizações. Deixar a divisão para a "partição atual" na Pia para um destino de ficheiro permitirá à Spark definir uma divisória padrão adequada para as suas cargas de trabalho. A partição predefinida utiliza 128Mb por partição.

* Para ficheiros mais pequenos, pode encontrar menos divisórias que por vezes funcionam melhor e mais rápido do que pedir ao Spark para dividir os seus pequenos ficheiros.
* Se não tiver informações suficientes sobre os seus dados de origem, escolha a divisão *Round Robin* e desempeciu o número de divisórias.
* Se os seus dados mentem colunas que podem ser boas teclas de haxixe, escolha *a partição hash*.

* Fonte de ficheiro com sumidouro de ficheiro de um ficheiro de 421Mb com 74 colunas e uma única transformação de coluna derivada demora cerca de 2 minutos de ponta a ponta usando memória otimizada 80 núcleo de depuração IRs.

Ao depurar na pré-visualização de dados e na depuragem de gasodutos, os tamanhos limite e de amostragem para conjuntos de dados de origem baseados em ficheiros aplicam-se apenas ao número de linhas devolvidas, e não ao número de linhas lidas. Isto pode afetar o desempenho das suas execuções de depurar e possivelmente causar o fluxo a falhar.
* Os clusters de depurg são pequenos aglomerados de nó único por padrão e recomendamos a utilização de pequenos ficheiros de amostra para depuração. Vá às Definições de Debug e aponte para um pequeno subconjunto dos seus dados utilizando um ficheiro temporário.

    ![Definições de depurar](media/data-flow/debugsettings3.png "Definições de depurar")

### <a name="file-naming-options"></a>Opções de nomeação de ficheiros

A forma mais comum de escrever dados transformados em fluxos de dados de mapeamento escrevendo a loja de ficheiros Blob ou ADLS. Na pia, deve selecionar um conjunto de dados que aponte para um recipiente ou pasta, e não para um ficheiro nomeado. À medida que o fluxo de dados de mapeamento utiliza o Spark para a execução, a sua saída é dividida em vários ficheiros com base no seu esquema de partição.

Um esquema comum de partição é escolher _a Saída para um único ficheiro,_ que combina todos os ficheiros PART de saída num único ficheiro na pia. Esta operação requer que a saída reduza para uma única partição num único nó de cluster. Pode ficar sem recursos de nó de cluster se estiver a combinar muitos ficheiros de origem num único ficheiro de saída.

Para evitar esgotamento dos recursos do nó de computação, mantenha o esquema padrão otimizado no fluxo de dados e adicione uma Atividade de Cópia no seu pipeline que funde todos os ficheiros PART da pasta de saída para um novo ficheiro único. Esta técnica separa a ação de transformação da fusão de ficheiros e obtém o mesmo resultado que a definição _da Saída para um único ficheiro_.

### <a name="looping-through-file-lists"></a>Looping através de listas de ficheiros

Um fluxo de dados de mapeamento executará melhor quando a transformação de Origem iterates sobre vários ficheiros em vez de circular através da atividade For Each. Recomendamos a utilização de wildcards ou listas de ficheiros na sua transformação de origem. O processo Data Flow executará mais rapidamente, permitindo que o looping ocorra dentro do cluster Spark. Para obter mais informações, consulte [Wildcarding in Source Transformation](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

Por exemplo, se tiver uma lista de ficheiros de dados a partir de julho de 2019 que pretende processar numa pasta no Blob Storage, abaixo está um wildcard que pode usar na sua transformação Source.

```DateFiles/*_201907*.txt```

Ao utilizar o wildcarding, o seu pipeline conterá apenas uma atividade de Fluxo de Dados. Isto irá funcionar melhor do que um Lookup contra a Blob Store que, em seguida, itera em todos os ficheiros combinados usando um ForEach com uma atividade de Fluxo de dados executar dentro.

O gasoduto For Each em modo paralelo irá gerar múltiplos clusters através de agrupamentos de trabalho giratórios para cada atividade de fluxo de dados executado. Isto pode causar estrangulamento de serviço Azure com um elevado número de execuções simultâneas. No entanto, a utilização do Fluxo de Dados executar dentro de um For Each com conjunto sequencial no oleoduto evitará estrangulamentos e exaustão de recursos. Isto forçará a Data Factory a executar cada um dos seus ficheiros contra um fluxo de dados sequencialmente.

Recomenda-se que, se utilizar para cada um com um fluxo de dados em sequência, utilize a definição de TTL no tempo de execução da integração Azure. Isto porque cada ficheiro incorrerá num tempo de arranque de cluster de 5 minutos dentro do seu iterador.

### <a name="optimizing-for-cosmosdb"></a>Otimização para CosmosDB

A definição de propriedades de produção e lote nos lavatórios CosmosDB só produz efeito durante a execução desse fluxo de dados a partir de uma atividade de fluxo de dados de gasoduto. As configurações originais da recolha serão honradas pela CosmosDB após a execução do fluxo de dados.

* Tamanho do lote: Calcule o tamanho da linha áspera dos seus dados e certifique-se de que o tamanho do lote de tamanho de linha * seja inferior a dois milhões. Se for, aumente o tamanho do lote para obter melhor produção
* Produção: Defina aqui uma definição de produção mais alta para permitir que os documentos escrevam mais rapidamente para o CosmosDB. Por favor, tenha em mente os custos ru mais elevados com base numa configuração de produção elevada.
*   Write Throughput Budget: Use um valor inferior ao total de RUs por minuto. Se tiver um fluxo de dados com um elevado número de divisórias Spark, definir uma produção orçamental permitirá um maior equilíbrio entre essas divisórias.

## <a name="join-and-lookup-performance"></a>Junte-se e procure desempenho

Gerir o desempenho das juntas no seu fluxo de dados é uma operação muito comum que irá realizar ao longo do ciclo de vida das suas transformações de dados. No ADF, os fluxos de dados não requerem que os dados sejam classificados antes da junção, uma vez que estas operações são realizadas como junções de haxixe na Spark. No entanto, pode beneficiar-se de um melhor desempenho com a otimização "Broadcast" que se aplica às transformações de Junção, Existência e Procura.

Isto evitará baralhar-se no voo empurrando para baixo o conteúdo de ambos os lados da sua relação de união no nó spark. Isto funciona bem para mesas mais pequenas que são usadas para procuras de referência. Tabelas maiores que podem não caber na memória do nó não são bons candidatos para a otimização da transmissão.

A configuração recomendada para fluxos de dados com muitas operações de junção é manter o conjunto de otimização para "Auto" para "Broadcast" e usar uma configuração de tempo de execução de integração ***azure otimizada*** de memória. Se estiver a experimentar erros de memória ou tempo limites de transmissão durante as execuções do fluxo de dados, pode desligar a otimização da transmissão. No entanto, isto resultará numa execução mais lenta dos fluxos de dados. Opcionalmente, pode instruir o fluxo de dados para empurrar apenas o lado esquerdo ou direito da junta, ou ambos.

![Definições de transmissão](media/data-flow/newbroad.png "Definições de transmissão")

Outra otimização de Join é construir as suas juntas de forma a evitar a tendência de Spark para implementar juntas cruzadas. Por exemplo, quando você inclui valores literais nas suas condições de união, a Spark pode ver isso como um requisito para executar um produto cartesiano completo primeiro, em seguida, filtrar os valores unidos. Mas se garantir que tem valores de coluna em ambos os lados da sua condição de união, pode evitar este produto cartesiano induzido por Faísca e melhorar o desempenho das suas juntas e fluxos de dados.

## <a name="next-steps"></a>Próximos passos

Consulte outros artigos do Fluxo de Dados relacionados com o desempenho:

- [Separador otimizado do fluxo de dados](concepts-data-flow-overview.md#optimize)
- [Atividade do Fluxo de Dados](control-flow-execute-data-flow-activity.md)
- [Monitor De fluxo de dados](concepts-data-flow-monitoring.md)
