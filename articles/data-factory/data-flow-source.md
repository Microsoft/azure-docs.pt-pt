---
title: Configurar uma transformação de origem na funcionalidade de mapeamento de fluxo de dados do Azure Data Factory
description: Saiba como configurar uma transformação de origem no mapeamento de fluxo de dados.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 5b53819c1d30f6cd62c5941d4b44d70a4996daad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67117880"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Transformação de origem para mapeamento de fluxo de dados 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Uma transformação de origem configura a origem de dados para o fluxo de dados. Um fluxo de dados pode incluir mais do que uma transformação de origem. Quando estruturar dados fluxos, sempre começam com uma transformação de origem.

Cada fluxo de dados requer a transformação de pelo menos uma origem. Adicione origens de tantos conforme necessário para concluir as transformações de dados. Pode associar essas origens em conjunto com uma transformação de associação ou de uma transformação union.

> [!NOTE]
> Ao depurar o fluxo de dados, os dados são lidos da fonte, utilizando a definição de amostragem ou os limites de origem de depuração. Para escrever dados para um sink, tem de executar o fluxo de dados a partir de um pipeline de atividade de fluxo de dados. 

![Opções de transformação no separador de definições da origem de origem](media/data-flow/source.png "origem")

Associe a sua transformação de origem do fluxo de dados com exatamente um conjunto de dados do Data Factory. O conjunto de dados define a forma e a localização dos dados que pretende escrever ou ler a partir de. Pode utilizar carateres universais e ficheiro de lista na sua origem para trabalhar com mais de um arquivo cada vez.

## <a name="data-flow-staging-areas"></a>Áreas de preparo de fluxo de dados

Fluxo de dados funciona com *teste* conjuntos de dados que estão todos no Azure. Utilize estes conjuntos de dados para quando estamos a transformar os dados de teste. 

O Data Factory tem acesso a conectores nativos quase 80. Para incluir dados de outras origens no seu fluxo de dados, utilize a ferramenta de atividade de cópia para testar esses dados em uma das áreas de transição de conjunto de dados de fluxo de dados.

## <a name="options"></a>Opções

Escolha as opções de esquema e de amostragem para os seus dados.

### <a name="allow-schema-drift"></a>Permitir que os descompassos de esquema
Selecione **permitir que os descompassos de esquema** se as colunas de origem serão alterados com freqüência. Esta definição permite que todos os campos de origem de entrada a ser enviados por meio de transformações para o sink.

### <a name="validate-schema"></a>Validar o esquema

Se a versão de entrada dos dados de origem não corresponder um esquema definido, o fluxo de dados falhará executar.

![Definições da origem público, que mostra as opções para validar esquema, permitir descompassos de esquema e amostragem](media/data-flow/source1.png "fonte públicos 1")

### <a name="sample-the-data"></a>Os dados de exemplo
Ativar **amostragem** para limitar o número de linhas da sua origem. Utilize esta definição quando testar ou dados de exemplo da sua origem para fins de depuração.

## <a name="define-schema"></a>Definir esquema

Quando os arquivos de origem não são fortemente tipados (por exemplo, arquivos simples em vez de ficheiros Parquet), defina os tipos de dados para cada campo aqui na transformação de origem.  

![No separador de esquema de definir as definições de transformação de origem](media/data-flow/source2.png "2 de origem")

Pode, posteriormente, alterar os nomes das colunas numa transformação selecione. Utilize uma transformação de coluna derivada para alterar os tipos de dados. Para origens com rigidez de tipos, pode modificar os tipos de dados numa transformação selecione mais tarde. 

![Tipos de dados numa transformação selecione](media/data-flow/source003.png "tipos de dados")

### <a name="optimize-the-source-transformation"></a>Otimizar a transformação de origem

Sobre o **otimizar** separador para a transformação de origem, poderá ver um **origem** tipo de partição. Esta opção só está disponível quando a origem é a base de dados do Azure SQL. Isto acontece porque o Data Factory tenta estabelecer ligações paralela para executar consultas grandes em sua origem de base de dados SQL.

![Definições de partição de origem](media/data-flow/sourcepart3.png "criação de partições")

Não é necessário criar partições de dados na sua origem de base de dados SQL, mas as partições são úteis para consultas grandes. Pode basear sua partição numa coluna ou uma consulta.

### <a name="use-a-column-to-partition-data"></a>Utilizar uma coluna para criar partições de dados

Da sua tabela de origem, selecione uma coluna de partição. Também defina o número de partições.

### <a name="use-a-query-to-partition-data"></a>Utilizar uma consulta para criar partições de dados

Pode optar por ligações com base numa consulta de partição. Introduza simplesmente o conteúdo de um predicado WHERE. Por exemplo, introduza o ano > 1980.

## <a name="source-file-management"></a>Gestão de ficheiros de origem

Escolha as definições para gerir os ficheiros na sua origem. 

![Novas definições de origem](media/data-flow/source2.png "novas definições")

* **Caminho de carateres universais**: A partir da sua pasta de origem, escolha uma série de ficheiros que correspondem a um padrão. Esta definição substitui qualquer ficheiro na sua definição de conjunto de dados.

Exemplos de carateres universais:

* ```*``` Representa qualquer conjunto de carateres
* ```**``` Representa o aninhamento de diretório recursiva
* ```?``` Substitui um caráter
* ```[]``` Corresponde a um dos mais carateres entre colchetes

* ```/data/sales/**/*.csv``` Obtém todos os ficheiros de csv em /data/sales
* ```/data/sales/20??/**``` Obtém todos os ficheiros no século 20
* ```/data/sales/2004/*/12/[XY]1?.csv``` Obtém todos os ficheiros csv em 2004 em Dezembro, começando com X ou Y como prefixadas por um número de dígitos de 2

Contentor tem de ser especificado no conjunto de dados. Seu caminho de caráter universal, por conseguinte, tem também de incluir o caminho da pasta a partir da pasta de raiz.

* **Lista de ficheiros**: Este é um conjunto de ficheiros. Crie um ficheiro de texto que inclui uma lista de ficheiros de caminho relativo a processar. Apontar para este ficheiro de texto.
* **Coluna para armazenar o nome de ficheiro**: Store o nome do ficheiro de origem numa coluna nos seus dados. Introduza um novo nome aqui para armazenar a cadeia de caracteres de nome de ficheiro.
* **Após a conclusão**: Opte por não fazer nada com o ficheiro de origem depois dos dados de execuções de fluxo, elimine o ficheiro de origem ou mover o ficheiro de origem. Os caminhos para a movimentação são relativos.

Para mover ficheiros de origem para outro pós-processamento da localização, primeiro selecione "Mover" para a operação de ficheiro. Em seguida, defina o diretório "de". Se não estiver a utilizar quaisquer carateres universais para o seu caminho, o "de" definição vai ser a mesma pasta que sua pasta de origem.

Se tiver um caminho de origem wildcarded, por exemplo:

```/data/sales/20??/**/*.csv```

Pode especificar "de" como

```/data/sales```

E "para" como

```/backup/priorSales```

Neste caso, todos os subdiretórios abaixo /data/sales que foram origem são movidos em relação ao /backup/priorSales.

### <a name="sql-datasets"></a>Conjuntos de dados SQL

Se a origem está na base de dados SQL ou SQL Data Warehouse, tem opções adicionais para gestão de ficheiros de origem.

* **Consulta**: Introduza uma consulta SQL para a sua origem. Esta definição substitui qualquer tabela que escolheu no conjunto de dados. Tenha em atenção que **Order By** cláusulas não são suportadas aqui, mas pode definir uma instrução SELECT FROM completa. Também pode utilizar as funções de tabela definido pelo utilizador. **Selecionar * de udfGetData()** é uma UDF do SQL que devolve uma tabela. Esta consulta irá produzir uma tabela de origem que pode utilizar no seu fluxo de dados.
* **Tamanho do lote**: Introduza um tamanho de lote para colocar partes de dados grandes em leituras.

> [!NOTE]
> As operações de arquivo executadas apenas quando iniciar o fluxo de dados a partir de um (uma depuração de pipeline ou execução execute) de execução do pipeline que utiliza a atividade de executar o fluxo de dados num pipeline. As operações de ficheiros *não* executar no modo de depuração do fluxo de dados.

### <a name="projection"></a>Projeção

Como esquemas em conjuntos de dados, a projeção de uma origem define as colunas de dados, tipos e formatos de dados de origem. 

![As definições no separador de projeção](media/data-flow/source3.png "projeção")

Se o ficheiro de texto tiver definidas de esquemas, selecione **detetar tipo de dados** para que a fábrica de dados será de exemplo e inferir os tipos de dados. Selecione **formato de padrão de definir** automaticamente os dados de predefinição formatos. 

Pode modificar os tipos de dados de coluna numa transformação de coluna derivada mais tarde. Use uma transformação Selecione para modificar os nomes das colunas.

![Definições para formatos de dados predefinida](media/data-flow/source2.png "predefinido formatos")

## <a name="next-steps"></a>Passos Seguintes

Começar a criar uma [transformação de coluna derivada](data-flow-derived-column.md) e uma [selecione transformação](data-flow-select.md).
