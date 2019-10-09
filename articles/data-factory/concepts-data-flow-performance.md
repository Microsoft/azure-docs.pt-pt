---
title: Mapeando o guia de desempenho e ajuste do fluxo de dados no Azure Data Factory | Microsoft Docs
description: Saiba mais sobre os principais fatores que afetam o desempenho do mapeamento de fluxos de dados em Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 10/07/2019
ms.openlocfilehash: 9db1b96cb495fd0de452091da79ab61f7ae59118
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030684"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Mapeando o guia de desempenho e ajuste do fluxo de dados

O mapeamento de fluxos de dados no Azure Data Factory fornece uma interface sem código para projetar, implantar e orquestrar transformações de dados em escala. Se você não estiver familiarizado com o mapeamento de fluxos de dados, consulte a [visão geral do fluxo de dados de mapeamento](concepts-data-flow-overview.md).

Ao projetar e testar fluxos de dados do UX do ADF, certifique-se de alternar no modo de depuração para executar seus fluxos de dados em tempo real sem esperar que um cluster fique quente. Para obter mais informações, consulte [modo de depuração](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flow-performance"></a>Monitorando o desempenho do fluxo de dados

Ao criar fluxos de dados de mapeamento, você pode testar cada transformação clicando na guia Visualização de dados no painel de configuração. Depois de verificar sua lógica, teste seu fluxo de dados de ponta a ponta como uma atividade em um pipeline. Adicione uma atividade executar fluxo de dados e use o botão depurar para testar o desempenho do fluxo de dados. Para abrir o plano de execução e o perfil de desempenho do fluxo de dados, clique no ícone de óculos em ' ações ' na guia saída do pipeline.

Monitor de fluxo de dados do ![Monitor de fluxo de dados](media/data-flow/mon002.png "2")

 Você pode usar essas informações para estimar o desempenho do fluxo de dados em fontes de dados de tamanhos diferentes. Para obter mais informações, consulte [monitoramento de fluxos de dados de mapeamento](concepts-data-flow-monitoring.md).

Monitor de fluxo de dados de ![monitoramento de fluxo de dados](media/data-flow/mon003.png "3")

 Para execuções de depuração de pipeline, cerca de um minuto de tempo de configuração de cluster em seus cálculos de desempenho geral são necessários para um cluster quente. Se você estiver inicializando o Azure Integration Runtime padrão, o tempo de rotação poderá levar cerca de 5 minutos.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Aumentando o tamanho da computação no Azure Integration Runtime

Um Integration Runtime com mais núcleos aumenta o número de nós nos ambientes de computação do Spark e fornece mais capacidade de processamento para ler, gravar e transformar seus dados.
* Experimente um cluster **otimizado para computação** se você quiser que sua taxa de processamento seja maior do que a sua taxa de entrada
* Experimente um cluster com **otimização de memória** se você quiser armazenar em cache mais dados na memória.

![Novo](media/data-flow/ir-new.png "ir novo") ir

Para obter mais informações sobre como criar um Integration Runtime, consulte [Integration Runtime em Azure data Factory](concepts-integration-runtime.md).

### <a name="increase-the-size-of-your-debug-cluster"></a>Aumentar o tamanho do cluster de depuração

Por padrão, a ativação da depuração usará o tempo de execução de integração do Azure padrão criado automaticamente para cada data factory. Essa Azure IR padrão é definida para oito núcleos, quatro para um nó de driver e quatro para um nó de trabalho, usando propriedades de computação gerais. Ao testar com dados maiores, você pode aumentar o tamanho do cluster de depuração criando um Azure IR com configurações maiores e escolher essa nova Azure IR quando você alternar para depuração. Isso instruirá o ADF a usar essa Azure IR para visualização de dados e depuração de pipeline com fluxos de dados.

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Otimizando para o banco de dados SQL do Azure e o Azure SQL Data Warehouse

### <a name="partitioning-on-source"></a>Particionamento na origem

1. Vá para a guia **otimizar** e selecione **definir particionamento**
1. Selecione **origem**.
1. Em **número de partições**, defina o número máximo de conexões com o banco de BD SQL do Azure. Você pode tentar uma configuração mais alta para obter conexões paralelas com seu banco de dados. No entanto, alguns casos podem resultar em um desempenho mais rápido com um número limitado de conexões.
1. Selecione se deseja particionar por uma coluna de tabela ou consulta específica.
1. Se você selecionou **coluna**, escolha a coluna partição.
1. Se você selecionou **consulta**, insira uma consulta que corresponda ao esquema de particionamento de sua tabela de banco de dados. Essa consulta permite que o mecanismo de banco de dados de origem Aproveite a eliminação de partição. As tabelas do banco de dados de origem não precisam ser particionadas. Se sua fonte já não estiver particionada, o ADF ainda usará o particionamento de dados no ambiente de transformação do Spark com base na chave que você selecionar na transformação origem.

![](media/data-flow/sourcepart3.png "Parte de origem") da parte de origem

### <a name="source-batch-size-input-and-isolation-level"></a>Tamanho do lote de origem, entrada e nível de isolamento

Em **Opções de origem** na transformação origem, as configurações a seguir podem afetar o desempenho:

* O tamanho do lote instrui o ADF a armazenar dados em conjuntos na memória, em vez de linha por linha. O tamanho do lote é uma configuração opcional e você pode ficar sem recursos nos nós de computação se eles não forem dimensionados corretamente.
* A definição de uma consulta pode permitir que você filtre linhas na origem antes que elas cheguem ao fluxo de dados para processamento. Isso pode tornar a aquisição de dados inicial mais rápida. Se você usar uma consulta, poderá adicionar dicas de consulta opcionais para seu banco de BD SQL do Azure, como leitura não confirmada.
* A leitura não confirmada fornecerá resultados de consulta mais rápidos na transformação de origem

![Origem](media/data-flow/source4.png "Origem") do

### <a name="sink-batch-size"></a>Tamanho do lote do coletor

Para evitar o processamento de linha por linha de seus fluxos de dados, defina o **tamanho do lote** na guia Configurações para o BD SQL do Azure e coletores do Azure SQL DW. Se o tamanho do lote for definido, o ADF processará gravações de banco de dados em lotes com base no tamanho fornecido.

![Coletor](media/data-flow/sink4.png "Coletor")

### <a name="partitioning-on-sink"></a>Particionamento no coletor

Mesmo que você não tenha seus dados particionados em suas tabelas de destino, é recomendável ter seus dados particionados na transformação do coletor. Os dados particionados geralmente resultam em um carregamento muito mais rápido ao forçar todas as conexões a usar um único nó/partição. Vá para a guia otimizar do coletor e selecione particionamento *Round Robin* para selecionar o número ideal de partições a serem gravadas no coletor.

### <a name="disable-indexes-on-write"></a>Desabilitar índices na gravação

Em seu pipeline, adicione uma [atividade de procedimento armazenado](transform-data-using-stored-procedure.md) antes de sua atividade de fluxo de dados que desabilita índices em suas tabelas de destino gravadas de seu coletor. Após a atividade de fluxo de dados, adicione outra atividade de procedimento armazenado que habilite esses índices.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Aumentar o tamanho do seu BD SQL do Azure e do DW

Agende um redimensionamento da origem e do coletor do banco de BD SQL do Azure e do DW antes de executar o pipeline para aumentar a taxa de transferência e minimizar a limitação do Azure depois de atingir os limites de DTU. Depois que a execução do pipeline for concluída, redimensione os bancos de dados de volta à sua taxa de execução normal.

### <a name="azure-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Somente SQL DW do Azure] Usar preparo para carregar dados em massa por meio do polybase

Para evitar inserções de linha por linha em seu DW, marque **habilitar o preparo** nas configurações do coletor para que o ADF possa usar o [polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). O polybase permite que o ADF carregue os dados em massa.
* Ao executar a atividade de fluxo de dados de um pipeline, você precisará selecionar um BLOB ou ADLS Gen2 local de armazenamento para preparar seus dados durante o carregamento em massa.

## <a name="optimizing-for-files"></a>Otimizando para arquivos

Em cada transformação, você pode definir o esquema de particionamento que deseja que data factory use na guia otimizar.
* Para arquivos menores, você pode achar que selecionar *uma única partição* pode, às vezes, funcionar melhor e mais rápido do que pedir ao Spark para particionar seus arquivos pequenos.
* Se você não tiver informações suficientes sobre seus dados de origem, escolha particionamento *Round Robin* e defina o número de partições.
* Se seus dados tiverem colunas que podem ser boas chaves de hash, escolha *particionamento de hash*.

Durante a depuração na visualização de dados e na depuração de pipeline, os tamanhos de limite e amostragem para DataSets de origem baseados em arquivo se aplicam apenas ao número de linhas retornadas, e não ao número de linhas lidas. Isso pode afetar o desempenho de suas execuções de depuração e possivelmente causar falha no fluxo.
* Os clusters de depuração são pequenos clusters de nó único por padrão e recomendamos o uso de arquivos pequenos de exemplo para depuração. Vá para configurações de depuração e aponte para um pequeno subconjunto de dados usando um arquivo temporário.

    Configurações ![da depuração configurações]de(media/data-flow/debugsettings3.png "depuração")

### <a name="file-naming-options"></a>Opções de nomenclatura de arquivo

A maneira mais comum de gravar dados transformados no mapeamento de fluxos de dados gravando BLOB ou repositório de arquivos ADLS. Em seu coletor, você deve selecionar um conjunto de um que aponte para um contêiner ou pasta, não um arquivo nomeado. Como o fluxo de dados de mapeamento usa o Spark para execução, sua saída é dividida em vários arquivos com base em seu esquema de particionamento.

Um esquema de particionamento comum é escolher a _saída para um único arquivo_, que mescla todos os arquivos da parte de saída em um único arquivo em seu coletor. Essa operação requer que a saída seja reduzida para uma única partição em um único nó de cluster. Você poderá ficar sem recursos de nó de cluster se estiver combinando muitos arquivos de origem grandes em um único arquivo de saída.

Para evitar esgotar os recursos do nó de computação, mantenha o esquema padrão otimizado no fluxo de dados e adicione uma atividade de cópia em seu pipeline que mescla todos os arquivos da parte da pasta de saída para um novo arquivo único. Essa técnica separa a ação de transformação da mesclagem de arquivos e Obtém o mesmo resultado que a configuração _de saída para um único arquivo_.

### <a name="looping-through-file-lists"></a>Loop por meio de listas de arquivos

Um fluxo de dados de mapeamento será executado melhor quando a transformação de origem iterar em vários arquivos em vez de executar um loop por meio de cada atividade. É recomendável usar caracteres curinga ou listas de arquivos em sua transformação de origem. O processo de fluxo de dados será executado mais rapidamente, permitindo que o loop ocorra dentro do cluster do Spark. Para obter mais informações, consulte [curinga na transformação origem](data-flow-source.md#file-based-source-options).

Por exemplo, se você tiver uma lista de arquivos de dados de julho de 2019 que deseja processar em uma pasta no armazenamento de BLOBs, abaixo está um caractere curinga que você pode usar em sua transformação de origem.

```DateFiles/*_201907*.txt```

Usando o curinga, seu pipeline conterá apenas uma atividade de fluxo de dados. Isso terá um desempenho melhor do que uma pesquisa no repositório de BLOB que, em seguida, itera em todos os arquivos correspondentes usando um ForEach com uma atividade executar fluxo de dados dentro do.

## <a name="next-steps"></a>Passos seguintes

Consulte outros artigos de fluxo de dados relacionados ao desempenho:

- [Guia otimizar fluxo de dados](concepts-data-flow-overview.md#optimize)
- [Atividade de fluxo de dados](control-flow-execute-data-flow-activity.md)
- [Monitorar o desempenho do fluxo de dados](concepts-data-flow-monitoring.md)
