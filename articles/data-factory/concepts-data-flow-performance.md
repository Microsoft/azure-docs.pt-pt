---
title: Mapeamento do desempenho do fluxo de dados e guia de afinação
description: Conheça os fatores-chave que afetam o desempenho dos fluxos de dados de mapeamento na Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 12/18/2020
ms.openlocfilehash: d23b2f65f25b704beaee12c53e47706653dcc208
ms.sourcegitcommit: 89c0482c16bfec316a79caa3667c256ee40b163f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97858591"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Mapeamento de dados flui desempenho e guia de afinação

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mapear fluxos de dados na Azure Data Factory fornece uma interface livre de códigos para conceber e executar transformações de dados em escala. Se não estiver familiarizado com os fluxos de dados de mapeamento, consulte a Visão Geral do [Fluxo de Dados de Mapeamento](concepts-data-flow-overview.md). Este artigo destaca várias formas de sintonizar e otimizar os fluxos de dados para que cumpram os seus critérios de desempenho.

Veja o vídeo abaixo para ver mostra alguns timings da amostra transformando dados com fluxos de dados.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="testing-data-flow-logic"></a>Lógica de fluxo de dados de teste

Ao conceber e testar fluxos de dados a partir do ADF UX, o modo depuração permite-lhe testar interativamente contra um cluster de Faíscas vivos. Isto permite-lhe visualizar dados e executar os seus fluxos de dados sem esperar que um cluster aqueça. Para mais informações, consulte [o Modo Debug](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flow-performance"></a>Monitorização do desempenho do fluxo de dados

Assim que verificar a sua lógica de transformação utilizando o modo de depurado, executar o fluxo de dados de ponta a ponta como uma atividade num oleoduto. Os fluxos de dados são operacionalizados num gasoduto utilizando a [atividade de fluxo de dados de execução](control-flow-execute-data-flow-activity.md). A atividade de fluxo de dados tem uma experiência de monitorização única em comparação com outras atividades da Azure Data Factory que exibe um plano de execução detalhado e perfil de desempenho da lógica de transformação. Para visualizar informações detalhadas de monitorização de um fluxo de dados, clique no ícone de óculos na produção de execução de atividade de um oleoduto. Para obter mais informações, consulte [monitorando os fluxos de dados de mapeamento](concepts-data-flow-monitoring.md).

![Monitor de fluxo de dados](media/data-flow/monitoring-details.png "Monitor de fluxo de dados 2")

Ao monitorizar o desempenho do fluxo de dados, existem quatro possíveis estrangulamentos a ter em conta:

* Tempo de arranque do cluster
* Leitura de uma fonte
* Tempo de transformação
* Escrevendo para uma pia 

![Monitorização do fluxo de dados](media/data-flow/monitoring-performance.png "Monitor de fluxo de dados 3")

O tempo de arranque do cluster é o tempo que leva para girar um aglomerado de Faíscas Apache. Este valor está localizado no canto superior direito do ecrã de monitorização. Os fluxos de dados funcionam num modelo just-in-time onde cada trabalho utiliza um cluster isolado. Este tempo de arranque geralmente demora 3-5 minutos. Para trabalhos sequenciais, este pode ser reduzido permitindo um tempo de vida. Para obter mais informações, consulte [a otimização do tempo de funcionamento da integração do Azure.](#ir)

Os fluxos de dados utilizam um otimizador Spark que reordena e executa a sua lógica de negócio em 'fases' para executar o mais rapidamente possível. Para cada pia que o seu fluxo de dados escreve, a saída de monitorização lista a duração de cada fase de transformação, juntamente com o tempo que leva para escrever dados na pia. O tempo que é o maior é provavelmente o estrangulamento do seu fluxo de dados. Se a fase de transformação que leva a maior contém uma fonte, então talvez queira olhar para otimizar ainda mais o seu tempo de leitura. Se uma transformação está a demorar muito tempo, então poderá ter de repartir ou aumentar o tamanho do seu tempo de integração. Se o tempo de processamento da pia for grande, poderá ter de aumentar a sua base de dados ou verificar se não está a ser outputing para um único ficheiro.

Depois de identificar o estrangulamento do seu fluxo de dados, utilize as estratégias de otimização abaixo para melhorar o desempenho.

## <a name="optimize-tab"></a>Otimizar o separador

O **separador Otimize** contém configurações para configurar o esquema de partição do cluster Spark. Este separador existe em todas as transformações do fluxo de dados e especifica se pretende repartir os dados **após** a transformação ter terminado. A regulação da partição proporciona controlo sobre a distribuição dos seus dados através de nós de computação e otimizações de localidade de dados que podem ter efeitos positivos e negativos no desempenho global do fluxo de dados.

![O screenshot mostra o separador Otimize, que inclui a opção de partição, o tipo de partição e o número de divisórias.](media/data-flow/optimize.png)

Por predefinição, é selecionada *a partição atual* que instrui a Azure Data Factory a manter a divisão de saída atual da transformação. Como os dados de repartição demoram tempo, *a partilha atual* é recomendada na maioria dos cenários. Os cenários em que poderá querer repartir os seus dados incluem após agregados e junta-se que distorcem significativamente os seus dados ou quando utiliza a divisão Source num DB SQL.

Para alterar a partição em qualquer transformação, selecione o **separador Otimize** e selecione o botão de rádio **De Divisão de Divisão.** É-lhe apresentada uma série de opções para a partilha. O melhor método de partição difere com base nos seus volumes de dados, chaves de candidatos, valores nulos e cardinalidade. 

> [!IMPORTANT]
> A partição única combina todos os dados distribuídos numa única partição. Esta é uma operação muito lenta que também afeta significativamente toda a transformação a jusante e escreve. A Azure Data Factory recomenda vivamente a não utilização desta opção, a menos que exista uma razão explícita para o fazer.

As seguintes opções de partição estão disponíveis em todas as transformações:

### <a name="round-robin"></a>Robin redondo 

Robin redondo distribui dados igualmente através de divisórias. Use o rodapé quando não tiver bons candidatos-chave para implementar uma estratégia sólida e inteligente de partição. Pode definir o número de divisórias físicas.

### <a name="hash"></a>Hash

A Azure Data Factory produz um haxixe de colunas para produzir divisórias uniformes de tal forma que as linhas com valores semelhantes caem na mesma partição. Quando utilizar a opção Hash, teste se possível distorcer a partição. Pode definir o número de divisórias físicas.

### <a name="dynamic-range"></a>Gama dinâmica

A gama dinâmica utiliza gamas dinâmicas Spark com base nas colunas ou expressões que fornece. Pode definir o número de divisórias físicas. 

### <a name="fixed-range"></a>Gama fixa

Construa uma expressão que forneça um intervalo fixo para valores dentro das colunas de dados divididas. Para evitar distorções de partição, deve ter uma boa compreensão dos seus dados antes de utilizar esta opção. Os valores que introduz para a expressão são utilizados como parte de uma função de partição. Pode definir o número de divisórias físicas.

### <a name="key"></a>Chave

Se tiver uma boa compreensão da cardinalidade dos seus dados, a divisão chave pode ser uma boa estratégia. A divisória de chaves cria divisórias para cada valor único na sua coluna. Não é possível definir o número de divisórias porque o número baseia-se em valores únicos nos dados.

> [!TIP]
> A definição manual do esquema de partição remodela os dados e pode compensar os benefícios do optimizador Spark. Uma boa prática é não definir manualmente a partição a menos que seja necessário.

## <a name="logging-level"></a>Nível de registo

Se não necessitar de todas as execuções do pipeline das suas atividades de fluxo de dados para registar totalmente todos os registos de telemetria verbose, pode configurar opcionalmente o seu nível de registo para "Básico" ou "Nenhum". Ao executar os fluxos de dados no modo "Verbose" (padrão), está a solicitar à ADF que faça login totalmente a cada nível de partição durante a transformação dos dados. Esta pode ser uma operação dispendiosa, por isso apenas permitir verbose quando a resolução de problemas pode melhorar o seu fluxo global de dados e desempenho do pipeline. O modo "Básico" só registará durações de transformação enquanto "Nenhum" fornecerá apenas um resumo das durações.

![Nível de registo](media/data-flow/logging.png "Definir nível de registo")

## <a name="optimizing-the-azure-integration-runtime"></a><a name="ir"></a> Otimização do tempo de funcionamento da integração do Azure

Os fluxos de dados são executados em clusters Spark que são girados no tempo de execução. A configuração para o cluster utilizado é definida no tempo de integração (IR) da atividade. Existem três considerações de desempenho a tomar ao definir o tempo de execução da sua integração: tipo de cluster, tamanho do cluster e tempo para viver.

Para obter mais informações sobre como criar um tempo de execução de [integração, consulte o tempo de execução da integração na Azure Data Factory.](concepts-integration-runtime.md)

### <a name="cluster-type"></a>Tipo de cluster

Existem três opções disponíveis para o tipo de cluster Spark: propósito geral, memória otimizada e computação otimizada.

Os clusters **de finalidade** geral são a seleção padrão e serão ideais para a maioria das cargas de trabalho do fluxo de dados. Estes tendem a ser o melhor equilíbrio de desempenho e custo.

Se o seu fluxo de dados tiver muitas junções e pesquisas, talvez queira utilizar um cluster **otimizado de memória.** Os clusters otimizados pela memória podem armazenar mais dados na memória e minimizar quaisquer erros fora de memória que possa obter. A memória otimizada tem o ponto de preço mais alto por núcleo, mas também tende a resultar em oleodutos mais bem sucedidos. Se experimentar erros de memória ao executar fluxos de dados, mude para uma configuração Azure IR otimizada de memória. 

**O compute otimizado** não é ideal para fluxos de trabalho da ETL e não são recomendados pela equipa da Azure Data Factory para a maioria das cargas de trabalho de produção. Para transformações de dados intensivas e não-memória, tais como a filtragem de dados ou a adição de colunas derivadas, os clusters otimizados pela computação podem ser usados a um preço mais barato por núcleo.

### <a name="cluster-size"></a>Tamanho do cluster

Os fluxos de dados distribuem o processamento de dados em diferentes nós num cluster Spark para realizar operações paralelamente. Um cluster spark com mais núcleos aumenta o número de nós no ambiente computacional. Mais nós aumentam o poder de processamento do fluxo de dados. Aumentar o tamanho do cluster é muitas vezes uma maneira fácil de reduzir o tempo de processamento.

O tamanho do cluster padrão é quatro nós do condutor e quatro nós de trabalhador.  À medida que processa mais dados, são recomendados agrupamentos maiores. Abaixo estão as opções possíveis de dimensionamento:

| Núcleos de trabalhadores | Núcleos de motorista | Total de núcleos | Notas |
| ------------ | ------------ | ----------- | ----- |
| 4 | 4 | 8 | Não disponível para computação otimizada |
| 8 | 8 | 16 | |
| 16 | 16 | 32 | |
| 32 | 16 | 48 | |
| 64 | 16 | 80 | |
| 128 | 16 | 144 | |
| 256 | 16 | 272 | |

Os fluxos de dados têm preços a vcore-hrs, o que significa que tanto o tamanho do cluster como o fator tempo de execução para este. À medida que aumenta, o seu custo de cluster por minuto aumentará, mas o seu tempo global diminuirá.

> [!TIP]
> Há um limite para o tamanho de um cluster que afeta o desempenho de um fluxo de dados. Dependendo do tamanho dos seus dados, há um ponto em que o aumento do tamanho de um cluster deixará de melhorar o desempenho. Por exemplo, se tiver mais nós do que divisórias de dados, adicionar nós adicionais não vai ajudar. Uma boa prática é começar pequeno e escalar para atender às suas necessidades de desempenho. 

### <a name="time-to-live"></a>Time to live

Por padrão, cada atividade de fluxo de dados gira um novo cluster com base na configuração de IR. O tempo de arranque do cluster demora alguns minutos e o processamento de dados não pode começar até que esteja completo. Se os seus oleodutos contiverem múltiplos fluxos de dados **sequenciais,** pode permitir um tempo para viver (TTL). Especificar um tempo para o valor vivo mantém um cluster vivo por um certo período de tempo após a sua execução terminar. Se um novo trabalho começar a utilizar o IR durante o tempo TTL, reutilizará o cluster existente e o tempo de arranque será muito reduzido. Após o segundo trabalho concluído, o cluster permanecerá novamente vivo durante o tempo de TTL.

Só um trabalho pode funcionar num único aglomerado de cada vez. Se houver um cluster disponível, mas dois fluxos de dados começarem, apenas um usará o cluster vivo. O segundo trabalho vai girar o seu próprio aglomerado isolado.

Se a maioria dos fluxos de dados forem executados em paralelo, não é recomendável que ative o TTL. 

> [!NOTE]
> O tempo de viver não está disponível quando se utiliza o tempo de integração de resolução automática

## <a name="optimizing-sources"></a>Fontes de otimização

Para cada fonte, exceto a Base de Dados Azure SQL, recomenda-se que continue **a utilizar a partição atual** como valor selecionado. Ao ler de todos os outros sistemas de origem, os dados fluem automaticamente os dados de forma uniforme com base no tamanho dos dados. Uma nova partição é criada para cerca de 128 MB de dados. À medida que o tamanho dos dados aumenta, o número de divisórias aumenta.

Qualquer divisória personalizada ocorre *após* a leitura da Spark nos dados e impactará negativamente o desempenho do fluxo de dados. Como os dados são igualmente divididos na leitura, isso não é recomendado. 

> [!NOTE]
> As velocidades de leitura podem ser limitadas pela produção do seu sistema de origem.

### <a name="azure-sql-database-sources"></a>Fontes de base de dados Azure SQL

A Azure SQL Database tem uma opção de partição única chamada partição 'Source'. Permitir a partição de fontes pode melhorar os seus tempos de leitura a partir do Azure SQL DB, permitindo ligações paralelas no sistema de origem. Especifique o número de divisórias e como dividir os seus dados. Use uma coluna de partição com alta cardinalidade. Também pode introduzir uma consulta que corresponda ao esquema de partição da sua tabela de origem.

> [!TIP]
> Para a divisão de origem, o I/O do SQL Server é o estrangulamento. Adicionar demasiadas divisórias pode saturar a sua base de dados de origem. Geralmente quatro ou cinco divisórias são ideais quando se utiliza esta opção.

![Partição de origem](media/data-flow/sourcepart3.png "Partição de origem")

#### <a name="isolation-level"></a>Nível de isolamento

O nível de isolamento da leitura num sistema de origem Azure SQL tem um impacto no desempenho. A escolha de 'Ler não comprometido' proporcionará o desempenho mais rápido e evitará quaisquer bloqueios de bases de dados. Para saber mais sobre os níveis de isolamento do SQL, consulte [os níveis de isolamento da Compreensão.](https://docs.microsoft.com/sql/connect/jdbc/understanding-isolation-levels)

#### <a name="read-using-query"></a>Ler usando consulta

Pode ler na Base de Dados Azure SQL utilizando uma tabela ou uma consulta SQL. Se estiver a executar uma consulta SQL, a consulta deve ser completada antes de a transformação poder começar. As queries SQL podem ser úteis para empurrar para baixo operações que podem executar mais rapidamente e reduzir a quantidade de dados lidos a partir de um Servidor SQL, tais como declarações SELECT, WHERE e JOIN. Ao impulsionar as operações, perde-se a capacidade de rastrear a linhagem e o desempenho das transformações antes que os dados entrem no fluxo de dados.

### <a name="azure-synapse-analytics-sources"></a>Fontes azure Synapse Analytics

Ao utilizar o Azure Synapse Analytics, existe uma definição chamada **Enable staging** nas opções de origem. Isto permite que a ADF leia a partir de Synapse ```Staging``` usando, o que melhora consideravelmente o desempenho da leitura. Ativar ```Staging``` requer que especifique um Azure Blob Storage ou Azure Data Lake Storage gen2 localização de localização nas definições de atividade de fluxo de dados.

![Ativar o teste](media/data-flow/enable-staging.png "Ativar o teste")

### <a name="file-based-sources"></a>Fontes baseadas em ficheiros

Embora os fluxos de dados suportem uma variedade de tipos de ficheiros, a Azure Data Factory recomenda a utilização do formato Parquet nativo-faísca para os melhores tempos de leitura e escrita.

Se estiver a executar o mesmo fluxo de dados num conjunto de ficheiros, recomendamos a leitura de uma pasta, utilizando caminhos wildcard ou lendo a partir de uma lista de ficheiros. Uma única atividade de fluxo de dados pode processar todos os seus ficheiros em lote. Mais informações sobre como definir estas definições podem ser encontradas na documentação do conector, como [o Azure Blob Storage](connector-azure-blob-storage.md#source-transformation).

Se possível, evite utilizar a atividade For-Each para executar fluxos de dados sobre um conjunto de ficheiros. Isto fará com que cada iteração do for-each rode o seu próprio cluster Spark, que muitas vezes não é necessário e pode ser caro. 

## <a name="optimizing-sinks"></a>Otimização de pias

Quando os fluxos de dados escrevem para afundar, qualquer divisória personalizada acontecerá imediatamente antes da escrita. Tal como a fonte, na maioria dos casos recomenda-se que continue **a utilizar a partição atual** como opção de partição selecionada. Os dados divididos escreverão significativamente mais rápido do que os dados não participantes, mesmo o seu destino não é dividido. Abaixo estão as considerações individuais para vários tipos de pias. 

### <a name="azure-sql-database-sinks"></a>Azure SQL Database afunda

Com a Base de Dados Azure SQL, a partição padrão deve funcionar na maioria dos casos. Existe a possibilidade de a sua pia ter demasiadas divisórias para a sua base de dados SQL manusear. Se estiver a escamar isto, reduza o número de divisórias outputadas pelo seu lavatório SQL Database.

#### <a name="impact-of-error-row-handling-to-performance"></a>Impacto do manuseamento da linha de erro para o desempenho

Quando ativar o manuseamento de linhas de erro ("continue em erro") na transformação do lavatório, a ADF dará um passo adicional antes de escrever as linhas compatíveis para a sua tabela de destino. Este passo adicional terá uma pequena penalidade de desempenho que pode estar no intervalo de 5% adicionado para este passo com um pequeno golpe de desempenho adicional também adicionado se você definir a opção também com as linhas incompatíveis para um arquivo de log.

#### <a name="disabling-indexes-using-a-sql-script"></a>Desativar índices usando um Script SQL

Desativar índices antes de uma carga numa base de dados SQL pode melhorar consideravelmente o desempenho da escrita para a tabela. Executar o comando abaixo antes de escrever para a pia SQL.

`ALTER INDEX ALL ON dbo.[Table Name] DISABLE`

Após a escrita ter terminado, reconstrua os índices utilizando o seguinte comando:

`ALTER INDEX ALL ON dbo.[Table Name] REBUILD`

Ambos podem ser feitos de forma nativa utilizando scripts Pré e Post-SQL dentro de um Azure SQL DB ou sinapse em fluxos de dados de mapeamento.

![Índices de desativação](media/data-flow/disable-indexes-sql.png "Índices de desativação")

> [!WARNING]
> Ao desativar os índices, o fluxo de dados está efetivamente a assumir o controlo de uma base de dados e é pouco provável que as consultas sejam bem sucedidas neste momento. Como resultado, muitos postos de trabalho da ETL são desencadeados a meio da noite para evitar este conflito. Para mais informações, saiba sobre os [constrangimentos dos índices incapacitantes](https://docs.microsoft.com/sql/relational-databases/indexes/disable-indexes-and-constraints)

#### <a name="scaling-up-your-database"></a>Escalonar a sua base de dados

Agende um redimensionamento da sua fonte e afunde o Azure SQL DB e o DW antes do seu gasoduto funcionar para aumentar a potência e minimizar o estrangulamento do Azure assim que atingir os limites de DTU. Depois da execução do seu oleoduto estar concluída, redimensione as suas bases de dados de volta à sua taxa normal de funcionação.

### <a name="azure-synapse-analytics-sinks"></a>Azure Synapse Analytics afunda

Ao escrever para a Azure Synapse Analytics, certifique-se de que **a encenação enable** está definida como verdadeira. Isto permite que a ADF escreva usando [o SQL Copy Command](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql) que efetivamente carrega os dados a granel. Você precisará de fazer referência a uma conta de armazenamento do Lago de Dados Azure gen2 ou Azure Blob Para a realização dos dados ao utilizar a Staging.

Além da Encenação, as mesmas boas práticas aplicam-se ao Azure Synapse Analytics como Base de Dados Azure SQL.

### <a name="file-based-sinks"></a>Pias baseadas em ficheiros 

Embora os fluxos de dados suportem uma variedade de tipos de ficheiros, a Azure Data Factory recomenda a utilização do formato Parquet nativo-faísca para os melhores tempos de leitura e escrita.

Se os dados forem distribuídos uniformemente, **utilize a partição atual** será a opção de partição mais rápida para escrever ficheiros.

#### <a name="file-name-options"></a>Opções de nome de ficheiro

Ao escrever ficheiros, tem uma escolha de opções de nomeação que cada uma tem um impacto de desempenho.

![Opções de pia](media/data-flow/file-sink-settings.png "opções de afundar")

Selecionar a opção **Predefinição** escreverá o mais rápido. Cada divisória equivale a um ficheiro com o nome padrão spark. Isto é útil se estiver apenas a ler a partir da pasta de dados.

A definição de um **Padrão** de nomeação mudará o nome de cada ficheiro de partição para um nome mais fácil de utilizar. Esta operação acontece após a escrita e é ligeiramente mais lenta do que escolher o padrão. Por partição permite nomear cada partição manualmente.

Se uma coluna corresponder à forma como deseja descodionar os dados, pode selecionar **como dados na coluna**. Isto remodela os dados e pode impactar o desempenho se as colunas não forem distribuídas uniformemente.

**A saída para um único ficheiro** combina todos os dados numa única partição. Isto leva a longos tempos de escrita, especialmente para grandes conjuntos de dados. A equipa da Azure Data Factory recomenda vivamente **não** escolher esta opção a menos que exista uma razão explícita para o fazer.

### <a name="cosmosdb-sinks"></a>CosmosDB afunda

Ao escrever para o CosmosDB, alterar o tamanho da produção e do lote durante a execução do fluxo de dados pode melhorar o desempenho. Estas alterações só têm efeito durante a atividade de fluxo de dados e voltarão às definições originais de recolha após a conclusão. 

**Tamanho do lote:** Calcule o tamanho da linha áspera dos seus dados e certifique-se de que o tamanho do lote * é inferior a dois milhões. Se for, aumente o tamanho do lote para obter melhor produção

**Produção:** Defina aqui uma definição de produção mais alta para permitir que os documentos escrevam mais rapidamente para o CosmosDB. Tenha em mente os custos ru mais elevados com base numa elevada definição de produção.

**Escrever Orçamento de Produção:** Utilize um valor inferior ao total de RUs por minuto. Se tiver um fluxo de dados com um elevado número de divisórias Spark, definir uma produção orçamental permitirá um maior equilíbrio entre essas divisórias.

## <a name="optimizing-transformations"></a>Otimização de transformações

### <a name="optimizing-joins-exists-and-lookups"></a>Otimização de Junções, Existe e Procura

#### <a name="broadcasting"></a>Radiodifusão

Em juntas, procuras e transformações existentes, se um ou ambos os fluxos de dados forem pequenos o suficiente para caber na memória do nó do trabalhador, pode otimizar o desempenho permitindo a **radiodifusão.** A radiodifusão é quando envia pequenos quadros de dados para todos os nós do cluster. Isto permite que o motor Spark execute uma junção sem remodelar os dados no grande fluxo. Por predefinição, o motor Spark decidirá automaticamente se transmite ou não um dos lados de uma junção. Se estiver familiarizado com os seus dados de entrada e souber que um fluxo será significativamente menor do que o outro, pode selecionar a radiodifusão **Fixa.** A radiodifusão fixa obriga a Spark a transmitir o fluxo selecionado. 

Se o tamanho dos dados transmitidos for demasiado grande para o nó faísca, poderá obter um erro de memória. Para evitar erros de memória, utilize agrupamentos **otimizados de memória.** Se experimentar intervalos de transmissão durante as execuções de fluxo de dados, pode desligar a otimização da transmissão. No entanto, isto resultará numa execução mais lenta dos fluxos de dados.

![Junte-se à transformação otimizar](media/data-flow/joinoptimize.png "Junte-se à otimização")

#### <a name="cross-joins"></a>Cruz junta-se

Se utilizar valores literais nas suas condições de união ou tiver vários jogos em ambos os lados de uma junção, a Spark executará a junção como uma ligação cruzada. Uma junção cruzada é um produto cartesiano completo que, em seguida, filtra os valores unidos. Isto é significativamente mais lento do que outros tipos de junção. Certifique-se de que tem referências de colunas em ambos os lados das suas condições de união para evitar o impacto do desempenho.

#### <a name="sorting-before-joins"></a>Ordenar antes de aderir

Ao contrário da fusão em ferramentas como a SSIS, a transformação de junção não é uma operação de fusão obrigatória. As chaves de junção não requerem triagem antes da transformação. A equipa da Azure Data Factory não recomenda a utilização de transformações do Sort no mapeamento dos fluxos de dados.

### <a name="window-transformation-performance"></a>Desempenho da transformação da janela

A [transformação da Janela](data-flow-window.md) partilha os seus dados por valor nas colunas que seleciona como parte da cláusula nas ```over()``` definições de transformação. Há uma série de funções agregadas e analíticas muito populares que estão expostas na transformação do Windows. No entanto, se o seu caso de utilização for gerar uma janela sobre todo o conjunto de dados para efeitos de classificação ou número de ```rank()``` ```rowNumber()``` linha, recomenda-se que utilize a [transformação de Rank](data-flow-rank.md) e a transformação da [Chave de Substituição](data-flow-surrogate-key.md). Estas transformações terão um melhor desempenho novamente operações completas de conjunto de dados utilizando essas funções.

### <a name="repartitioning-skewed-data"></a>Repartição de dados distorcidos

Certas transformações, tais como juntas e agregados, remodelam as suas divisórias de dados e podem ocasionalmente levar a dados distorcidos. Os dados distorcidos significam que os dados não são distribuídos uniformemente pelas divisórias. Dados fortemente distorcidos podem levar a transformações a jusante mais lentas e a afundar- se. Pode verificar a distorção dos seus dados em qualquer ponto de um fluxo de dados executado clicando na transformação no visor de monitorização.

![Skewness e kurtose](media/data-flow/skewness-kurtosis.png "Skewness e kurtose")

O visor de monitorização mostrará como os dados são distribuídos por cada partição juntamente com duas métricas, distorção e kurtose. **A distorção** é uma medida de como os dados são assimétricos e podem ter um valor positivo, zero, negativo ou indefinido. Distorção negativa significa que a cauda esquerda é mais comprida que a direita. **A kurtose** é a medida de se os dados são de cauda pesada ou de cauda-clara. Valores de alta kurtose não são desejáveis. As gamas ideais de distorção situam-se entre -3 e 3 e os intervalos de kurtose são inferiores a 10. Uma maneira fácil de interpretar estes números é olhar para o gráfico de partição e ver se 1 barra é significativamente maior do que o resto.

Se os seus dados não forem igualmente divididos após uma transformação, pode utilizar o [separador otimizar](#optimize-tab) para repartição. A reformulação dos dados leva tempo e pode não melhorar o desempenho do fluxo de dados.

> [!TIP]
> Se repartir os seus dados, mas tiver transformações a jusante que recomeçam os seus dados, utilize a partição de haxixe numa coluna usada como chave de união.

## <a name="using-data-flows-in-pipelines"></a>Utilização de fluxos de dados em oleodutos 

Ao construir oleodutos complexos com múltiplos fluxos de dados, o seu fluxo lógico pode ter um grande impacto no tempo e no custo. Esta secção abrange o impacto de diferentes estratégias de arquitetura.

### <a name="executing-data-flows-in-parallel"></a>Execução de fluxos de dados em paralelo

Se executar vários fluxos de dados em paralelo, a ADF gira os agrupamentos de faíscas separados para cada atividade. Isto permite que cada trabalho seja isolado e executado em paralelo, mas levará a vários clusters em execução ao mesmo tempo.

Se os fluxos de dados forem executados em paralelo, recomenda-se que não permita que o tempo de vida do Azure IV seja propriedade, pois conduzirá a várias piscinas quentes não utilizadas.

> [!TIP]
> Em vez de executar o mesmo fluxo de dados várias vezes num para cada atividade, enfirma os seus dados num lago de dados e use caminhos wildcard para processar os dados num único fluxo de dados.

### <a name="execute-data-flows-sequentially"></a>Executar fluxos de dados sequencialmente

Se executar as suas atividades de fluxo de dados em sequência, recomenda-se que desemque um TTL na configuração Azure IR. A ADF reutilizará os recursos computacional, resultando num tempo de arranque mais rápido do cluster. Cada atividade continuará isolada recebendo um novo contexto de Faísca para cada execução.

Gerir os postos de trabalho sequencialmente levará provavelmente mais tempo a executar de ponta a ponta, mas proporciona uma separação limpa das operações lógicas.

### <a name="overloading-a-single-data-flow"></a>Sobrecarga de um único fluxo de dados

Se colocar toda a sua lógica dentro de um único fluxo de dados, a ADF executará todo o trabalho numa única instância spark. Embora isto possa parecer uma forma de reduzir custos, mistura diferentes fluxos lógicos e pode ser difícil de monitorizar e depurar. Se um componente falhar, todas as outras partes do trabalho também falharão. A equipa da Azure Data Factory recomenda a organização de fluxos de dados por fluxos independentes de lógica empresarial. Se o fluxo de dados se tornar demasiado grande, dividi-lo em componentes separados facilitará a monitorização e a depuragem. Embora não exista um limite rígido para o número de transformações num fluxo de dados, ter demasiadas tornará o complexo de trabalho.

### <a name="execute-sinks-in-parallel"></a>Executar pias em paralelo

O comportamento padrão dos sumidouros de fluxo de dados é executar cada pia sequencialmente, de forma em série, e falhar o fluxo de dados quando um erro é encontrado na pia. Além disso, todos os lavatórios estão em incumprimento do mesmo grupo, a menos que você entre nas propriedades de fluxo de dados e desecorde diferentes prioridades para os lavatórios.

Os fluxos de dados permitem-lhe agrupar os sumidouros em grupos a partir do separador de propriedades de fluxo de dados no designer de UI. Ambos podem definir a ordem de execução dos seus lavatórios, bem como agrupar pias usando o mesmo número de grupo. Para ajudar a gerir grupos, pode pedir à ADF para executar pias no mesmo grupo, para correr em paralelo.

No gasoduto execute a atividade de fluxo de dados sob a secção "Propriedades do Lavatório" é uma opção para ligar o carregamento paralelo do lavatório. Quando ativa "correr em paralelo", está a instruir os fluxos de dados a escrever em pias ligadas ao mesmo tempo e não de forma sequencial. Para utilizar a opção paralela, os lavatórios devem ser agrupados e ligados ao mesmo fluxo através de um Novo Ramo ou Divisão Condicional.

## <a name="next-steps"></a>Passos seguintes

Consulte outros artigos do Fluxo de Dados relacionados com o desempenho:

- [Atividade do Fluxo de Dados](control-flow-execute-data-flow-activity.md)
- [Monitor De fluxo de dados](concepts-data-flow-monitoring.md)
