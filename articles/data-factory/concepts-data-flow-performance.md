---
title: Mapeando o desempenho do fluxo de dados e o guia de afinação
description: Conheça os factores-chave que afetam o desempenho dos fluxos de dados de mapeamento na Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 05/21/2020
ms.openlocfilehash: 327fffd807d93fda67ff650954ece65e5db58e63
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798104"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Guia de dados de mapeamento de fluxos de dados e afinação

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mapeamento de Fluxos de Dados em Azure Data Factory fornecem uma interface sem código para projetar, implementar e orquestrar transformações de dados em escala. Se não estiver familiarizado com os fluxos de dados de mapeamento, consulte a visão geral do fluxo de dados de [mapeamento](concepts-data-flow-overview.md).

Ao conceber e testar fluxos de dados do ADF UX, certifique-se de ligar o modo de depuração para executar os seus fluxos de dados em tempo real sem esperar que um cluster se aqueça. Para mais informações, consulte [debug mode](concepts-data-flow-debug-mode.md).

Este vídeo mostra alguns timings de amostra que transformam dados com fluxos de dados:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>Monitorização do desempenho do fluxo de dados

Ao conceber fluxos de dados de mapeamento, pode fazer um teste de cada transformação clicando no separador de pré-visualização de dados no painel de configuração. Uma vez verificado a sua lógica, teste o fluxo de dados de ponta a ponta como uma atividade num pipeline. Adicione uma atividade de Fluxo de Dados executar e utilize o botão Debug para testar o desempenho do fluxo de dados. Para abrir o plano de execução e o perfil de desempenho do seu fluxo de dados, clique no ícone dos óculos em 'ações' no separador de saída do seu pipeline.

![Monitor de Fluxo de Dados](media/data-flow/mon002.png "Monitor de Fluxo de Dados 2")

 Pode utilizar estas informações para estimar o desempenho do fluxo dos seus dados contra fontes de dados de diferentes dimensões. Para obter mais informações, consulte [monitorização dos fluxos](concepts-data-flow-monitoring.md)de dados de mapeamento .

![Monitorização do fluxo de dados](media/data-flow/mon003.png "Monitor de Fluxo de Dados 3")

 Para as corridas de depuração do gasoduto, cerca de um minuto de tempo de configuração do cluster nos seus cálculos globais de desempenho é necessário para um cluster quente. Se estiver a inicializar o tempo de execução de integração azul padrão, o tempo de centrifugação pode demorar cerca de 5 minutos.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Aumento do tamanho da computação no Tempo de Execução da Integração Azure

Um Tempo de Integração com mais núcleos aumenta o número de nós nos ambientes de computação Spark e fornece mais poder de processamento para ler, escrever e transformar os seus dados. A Df Data Flows utiliza a Spark para o motor de computação. O ambiente Spark funciona muito bem com recursos otimizados pela memória.
* Experimente um cluster **Compute Otimizado** se quiser que a sua taxa de processamento seja superior à sua taxa de entrada.
* Experimente um cluster **Otimizado** de Memória se quiser cache mais dados na memória. A memória otimizada tem um preço-ponto por núcleo mais elevado do que o Compute Otimizado, mas provavelmente resultará em velocidades de transformação mais rápidas. Se experimentar erros de memória ao executar os seus fluxos de dados, mude para uma configuração de Iv-Fi otimizada pela memória.

![Novo IR](media/data-flow/ir-new.png "Novo IR")

Para mais informações sobre como criar um Tempo de Funcionação de Integração, consulte o Tempo de [Funcionação da Integração na Fábrica de Dados Azure.](concepts-integration-runtime.md)

### <a name="increase-the-size-of-your-debug-cluster"></a>Aumente o tamanho do seu aglomerado de depuração

Por predefinição, ligar o depurado utilizará o tempo de funcionação padrão da Integração Azure que é criado automaticamente para cada fábrica de dados. Este Inversor De indevido azure está definido para oito núcleos, quatro para um nó de condutor e quatro para um nó de trabalhador, utilizando propriedades da Calcule Geral. À medida que testa com dados maiores, pode aumentar o tamanho do seu cluster de depuração criando um Azure IR com configurações maiores e escolher este novo Azure IR quando ligar o depurado. Isto instruirá a ADF a utilizar este IR Azure para pré-visualização de dados e depuração de gasodutos com fluxos de dados.

### <a name="decrease-cluster-compute-start-up-time-with-ttl"></a>Diminuir o tempo de arranque do cluster compute com tTL

Existe uma propriedade no Azure IR no âmbito da Data Flow Properties que lhe permitirá levantar um conjunto de recursos de computação cluster para a sua fábrica. Com este pool, pode submeter sequencialmente atividades de fluxo de dados para execução. Uma vez estabelecida a piscina, cada trabalho subsequente levará 1-2 minutos para o cluster Spark a pedido executar o seu trabalho. A configuração inicial do conjunto de recursos levará cerca de 6 minutos. Especifique o tempo que pretende manter o conjunto de recursos na definição de tempo de vida (TTL).

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse-synapse"></a>Otimização para Base de Dados Azure SQL e Azure SQL Data Warehouse Synapse

### <a name="partitioning-on-source"></a>Partição na fonte

1. Vá ao separador **Otimize** e selecione **Partição de Conjuntos**
1. Selecione **Origem**.
1. Em **número de divisórias,** detete o número máximo de ligações ao seu Azure SQL DB. Pode tentar uma definição mais elevada para obter ligações paralelas à sua base de dados. No entanto, alguns casos podem resultar num desempenho mais rápido com um número limitado de ligações.
1. Selecione se deve dividir por uma coluna de mesa específica ou por uma consulta.
1. Se selecionar **coluna,** escolha a coluna de partição.
1. Se selecionar **Consulta,** insira uma consulta que corresponda ao esquema de partilha da sua tabela de dados. Esta consulta permite que o motor de base de dados de origem aproveite a eliminação da divisória. As suas tabelas de bases de dados não precisam de ser divididas. Se a sua fonte ainda não estiver dividida, a ADF continuará a utilizar a partilha de dados no ambiente de transformação de Spark com base na chave que seleciona na transformação da Origem.

![Parte fonte](media/data-flow/sourcepart3.png "Parte fonte")

> [!NOTE]
> Um bom guia para ajudá-lo a escolher o número de divisórias para a sua fonte baseia-se no número de núcleos que definiu para o seu Tempo de Integração Azure e multiplique esse número por cinco. Assim, por exemplo, se estiver a transformar uma série de ficheiros nas suas pastas ADLS e utilizar um Azure IR de 32 núcleos, o número de divisórias que você teria como alvo é de 32 x 5 = 160 divisórias.

### <a name="source-batch-size-input-and-isolation-level"></a>Tamanho do lote de origem, entrada e nível de isolamento

No âmbito **das Opções de Origem** na transformação da fonte, as seguintes definições podem afetar o desempenho:

* O tamanho do lote instrui a ADF a armazenar dados em conjuntos na memória Spark em vez de fila a linha. O tamanho do lote é uma configuração opcional e você pode ficar sem recursos nos nós de computação se não forem dimensionados corretamente. Não configurar esta propriedade utilizará as predefinições do lote de cache spark.
* Definir uma consulta pode permitir-lhe filtrar linhas na fonte antes de chegarem ao Fluxo de Dados para serem processados. Isto pode tornar a aquisição inicial de dados mais rápida. Se utilizar uma consulta, pode adicionar dicas de consulta opcionais para o seu Azure SQL DB, como READ UNCOMMITTED.
* Ler não comprometido fornecerá resultados de consulta mais rápidos sobre a transformação de Origem

![Origem](media/data-flow/source4.png "Origem")

### <a name="sink-batch-size"></a>Tamanho do lote de pia

Para evitar o processamento em linha dos seus fluxos de dados, detete o tamanho do **lote** no separador Definições para a pia de DW Azure SQL DB e Azure SQL DW. Se o tamanho do lote estiver definido, a base de dados de processos da ADF escreve em lotes com base no tamanho fornecido. Não configurar esta propriedade utilizará as predefinições do lote de cache spark.

![Sink](media/data-flow/sink4.png "Sink")

### <a name="partitioning-on-sink"></a>Partição na pia

Mesmo que não tenha os seus dados divididos nas tabelas de destino, recomenda-se que os seus dados sejam divididos na transformação do lavatório. Os dados divididos resultam frequentemente numa carga muito mais rápida, forçando todas as ligações a utilizarem um único nó/partição. Vá ao separador Otimize da sua pia e selecione divisórias *Round Robin* para selecionar o número ideal de divisórias para escrever na pia.

### <a name="disable-indexes-on-write"></a>Desativar índices por escrito

No seu pipeline, adicione uma atividade de [Procedimento Armazenado](transform-data-using-stored-procedure.md) antes da sua atividade de Fluxo de Dados que desativa os índices nas tabelas-alvo escritas a partir da pia. Após a sua atividade de Fluxo de Dados, adicione outra atividade de Procedimento Armazenado que permite esses índices. Ou utilizar os scripts de pré-processamento e pós-processamento numa pia de base de dados.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Aumente o tamanho do seu Azure SQL DB e DW

Agende uma redimensionamento da sua fonte e afunde o Azure SQL DB e dW antes da execução do seu oleoduto para aumentar a entrada e minimizar a aceleração do Azure assim que atingir os limites dTU. Depois de concluída a execução do gasoduto, redimensione as suas bases de dados de volta à sua taxa normal de execução.

* A tabela de origem SQL DB com 887k linhas e 74 colunas para uma tabela SQL DB com uma única transformação de coluna derivada leva cerca de 3 minutos de ponta a ponta usando iRs de depuração de 80 núcleos otimizados.

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Azure Synapse SQL DW apenas] Utilize a encenação para carregar dados a granel através da Polybase

Para evitar inserções de fila a linha no seu DW, verifique **a preparação do ativar** nas definições do lavatório para que a ADF possa utilizar [o PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). A PolyBase permite que a ADF carregue os dados a granel.
* Quando executar a sua atividade de fluxo de dados a partir de um pipeline, terá de selecionar um local de armazenamento Blob ou ADLS Gen2 para encenar os seus dados durante o carregamento a granel.

* A fonte de ficheiro de ficheiro sinuoso de 421Mb com 74 colunas para uma tabela Synapse e uma única transformação de coluna derivada leva cerca de 4 minutos de ponta a ponta usando iRs de depuração de 80 núcleos otimizados de memória.

## <a name="optimizing-for-files"></a>Otimização para ficheiros

Em cada transformação, pode definir o esquema de partição que deseja que a fábrica de dados utilize no separador Otimize. É uma boa prática testar pias baseadas em ficheiros mantendo as divisórias e otimizações padrão.

* Para ficheiros mais pequenos, pode descobrir que escolher menos divisórias pode, por vezes, funcionar melhor e mais rápido do que pedir à Spark para dividir os seus pequenos ficheiros.
* Se não tiver informações suficientes sobre os seus dados de origem, escolha a partilha *round robin* e detetetete o número de divisórias.
* Se os seus dados têm colunas que podem ser boas teclas de hash, escolha *a divisão hash*.

* A fonte de ficheiros com a pia de ficheiro de um ficheiro de 421Mb com 74 colunas e uma única transformação de coluna derivada demora cerca de 2 minutos de ponta a ponta usando iRs de depuração de 80 núcleos otimizados.

Quando depuramos na pré-visualização de dados e na depuração do gasoduto, os tamanhos limite e de amostragem dos conjuntos de dados baseados em ficheiros aplicam-se apenas ao número de linhas devolvidas, e não ao número de linhas lidas. Isto pode afetar o desempenho das suas execuções de depuração e possivelmente fazer com que o fluxo falhe.
* Os clusters de depuração são pequenos clusters de nó único por padrão e recomendamos a utilização de pequenos ficheiros para depuração. Vá às Definições de Debug e aponte para um pequeno subconjunto dos seus dados usando um ficheiro temporário.

    ![Definições de depuração](media/data-flow/debugsettings3.png "Definições de depuração")

### <a name="file-naming-options"></a>Opções de nomeação de ficheiros

A forma mais comum de escrever dados transformados em fluxos de dados de mapeamento escrevendo blob ou loja de ficheiros ADLS. Na pia, deve selecionar um conjunto de dados que aponte para um recipiente ou pasta, e não para um ficheiro nomeado. Como o fluxo de dados de mapeamento utiliza a Spark para a execução, a sua saída é dividida em vários ficheiros com base no seu esquema de partição.

Um esquema comum de partição é escolher a _Saída para um ficheiro único,_ que une todos os ficheiros PARTE de saída num único ficheiro na pia. Esta operação requer que a saída reduz a uma única divisória num único nó de cluster. Pode ficar sem recursos de nó de cluster se estiver a combinar muitos ficheiros de grande fonte num único ficheiro de saída.

Para evitar esgotar os recursos do nó computacional, mantenha o esquema predefinido e otimizado no fluxo de dados e adicione uma Atividade de Cópia no seu pipeline que funde todos os ficheiros PART da pasta de saída para um novo ficheiro único. Esta técnica separa a ação de transformação da fusão de ficheiros e obtém o mesmo resultado que a definição _de Saída para ficheiro único_.

### <a name="looping-through-file-lists"></a>Looping através de listas de ficheiros

Um fluxo de dados de mapeamento executará melhor quando a transformação da Origem iterates sobre vários ficheiros em vez de circular através da atividade Para Cada. Recomendamos a utilização de wildcards ou listas de ficheiros na sua transformação de origem. O processo de Fluxo de Dados será executado mais rapidamente, permitindo que o looping ocorra dentro do cluster Spark. Para mais informações, consulte [wildcarding em Transformação de Origem.](connector-azure-data-lake-storage.md#mapping-data-flow-properties)

Por exemplo, se tiver uma lista de ficheiros de dados a partir de julho de 2019 que deseja processar numa pasta no Blob Storage, abaixo está um wildcard que pode utilizar na sua transformação Fonte.

```DateFiles/*_201907*.txt```

Ao utilizar wildcarding, o seu pipeline conterá apenas uma atividade de Fluxo de Dados. Isto funcionará melhor do que um Lookup contra a Blob Store que, em seguida, iterates em todos os ficheiros combinados usando um ForEach com uma atividade de Fluxo de Dados executar no seu interior.

O gasoduto Para Cada em modo paralelo irá gerar vários clusters girando-se em clusters de trabalho para cada atividade de fluxo de dados executada. Isto pode causar estrangulamento do serviço Azure com um elevado número de execuções simultâneas. No entanto, a utilização do Fluxo de Dados de Execução dentro de um For Each com conjunto sequencial no gasoduto evitará estrangulamentos e exaustão de recursos. Isto obrigará a Data Factory a executar cada um dos seus ficheiros contra um fluxo de dados sequencialmente.

Recomenda-se que, se utilizar para cada um com um fluxo de dados em sequência, utilize a definição TTL no Tempo de Funcionamento da Integração Azure. Isto porque cada ficheiro incorrerá num tempo completo de arranque de cluster de 5 minutos dentro do seu iterator.

### <a name="optimizing-for-cosmosdb"></a>Otimização para CosmosDB

A fixação de propriedades de produção e lote em sumidouros CosmosDB só produzirá efeitos durante a execução desses fluxos de dados a partir de uma atividade de fluxo de dados de gasodutos. As definições de recolha originais serão honradas pela CosmosDB após a execução do fluxo de dados.

* Tamanho do lote: Calcule o tamanho da linha áspera dos seus dados e certifique-se de que o tamanho do lote de linha tamanho * é inferior a dois milhões. Se for, aumente o tamanho do lote para obter uma melhor produção
* Entrada: Defina uma definição de entrada mais alta aqui para permitir que os documentos escrevam mais rapidamente para cosmosDB. Por favor, tenha em mente os custos de RU mais elevados com base numa configuração de alta entrada.
*   Escreva O Orçamento de Produção: Utilize um valor menor do que o total de RUs por minuto. Se tiver um fluxo de dados com um elevado número de divisórias Spark, a definição de um resultado orçamental permitirá um maior equilíbrio entre essas divisórias.

## <a name="join-performance"></a>Junte-se ao desempenho

Gerir o desempenho de juntas no fluxo de dados é uma operação muito comum que irá realizar ao longo do ciclo de vida das suas transformações de dados. Na ADF, os fluxos de dados não requerem que os dados sejam classificados antes de aderirem, uma vez que estas operações são realizadas à medida que o haxixe se junta à Spark. No entanto, pode beneficiar de um melhor desempenho com a otimização "Broadcast" Join que se aplica às transformações de Joins, Exists e Lookup.

Isto evitará baralhadas no voo empurrando para baixo o conteúdo de ambos os lados da sua relação de união para o nó de Spark. Isto funciona bem para tabelas mais pequenas que são usadas para procurar referências. Tabelas maiores que podem não caber na memória do nó não são bons candidatos para otimização de transmissão.

A configuração recomendada para fluxos de dados com muitas operações de adesão é manter o conjunto de otimização para "Auto" para "Broadcast" e usar uma configuração de tempo de execução de integração azure otimizada de memória. Se estiver a experimentar erros de memória ou a tempo de transmissão durante as execuções de fluxo de dados, pode desligar a otimização da transmissão. No entanto, isto resultará em fluxos de dados de desempenho mais lentos. Opcionalmente, pode instruir o fluxo de dados para empurrar para baixo apenas o lado esquerdo ou direito da união, ou ambos.

![Definições de transmissão](media/data-flow/newbroad.png "Definições de transmissão")

Outra otimização de Join é construir as suas juntas de forma a evitar a tendência da Spark para implementar juntas cruzadas. Por exemplo, quando incluir valores literais nas suas condições de adesão, a Spark pode ver isso como um requisito para executar primeiro um produto cartesiano completo, em seguida, filtrar os valores unidos. Mas se garantir que tem valores de coluna em ambos os lados da sua condição de união, pode evitar este produto cartesiano induzido pela Spark e melhorar o desempenho das suas juntas e fluxos de dados.

## <a name="next-steps"></a>Passos seguintes

Consulte outros artigos do Fluxo de Dados relacionados com o desempenho:

- [Separador otimizado de fluxo de dados](concepts-data-flow-overview.md#optimize)
- [Atividade de Fluxo de Dados](control-flow-execute-data-flow-activity.md)
- [Monitorizar o desempenho do Fluxo de Dados](concepts-data-flow-monitoring.md)
