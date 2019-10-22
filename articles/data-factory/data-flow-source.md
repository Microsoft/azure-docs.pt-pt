---
title: Transformação de origem no fluxo de dados de mapeamento-Azure Data Factory | Microsoft Docs
description: Saiba como configurar uma transformação de origem no fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: c7d18ab6e9018511915e9b77ea02ac60b1277c12
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72596486"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Transformação de origem para mapeamento de fluxo de dados 



Uma transformação de origem configura sua fonte de dados para o fluxo de dados. Ao criar fluxos de dados, sua primeira etapa sempre estará configurando uma transformação de origem. Para adicionar uma origem, clique na caixa **Adicionar origem** na tela fluxo de dados.

Cada fluxo de dados requer pelo menos uma transformação de origem, mas você pode adicionar quantas fontes forem necessárias para concluir as transformações de dados. Você pode unir essas fontes junto com uma transformação junção, pesquisa ou União.

Cada transformação de origem é associada a exatamente um conjunto de Data Factory. O DataSet define a forma e o local dos dados que você deseja gravar ou ler. Se estiver usando um conjunto de informações baseado em arquivo, você poderá usar curingas e listas de arquivos em sua fonte para trabalhar com mais de um arquivo por vez.

## <a name="supported-connectors-in-mapping-data-flow"></a>Conectores com suporte no fluxo de dados de mapeamento

O mapeamento de fluxo de dados segue uma abordagem de extração, carregamento, transformação (ELT) e funciona com conjuntos de dados de *preparo* que estão todos no Azure. Atualmente, os seguintes conjuntos de valores podem ser usados em uma transformação de origem:
    
* Armazenamento de Blobs do Azure
* Armazenamento Ger1 do Azure Data Lake
* Ger2 de Armazenamento do Azure Data Lake
* Armazém de Dados SQL do Azure
* Base de Dados SQL do Azure

Azure Data Factory tem acesso a mais de 80 conectores nativos. Para incluir dados dessas outras fontes em seu fluxo de dados, use a atividade de cópia para carregar esses dados em uma das áreas de preparo com suporte.

## <a name="source-settings"></a>Definições de origem

Depois de adicionar uma fonte, configure por meio da guia **configurações de origem** . Aqui, você pode escolher ou criar o conjunto de os pontos de origem em. Você também pode selecionar opções de esquema e amostragem para seus dados.

![Guia Configurações de origem](media/data-flow/source1.png "Guia Configurações de origem")

**Descompasso de esquema:** a [descompasso de esquema](concepts-data-flow-schema-drift.md) é a capacidade do data Factory de lidar nativamente com esquemas flexíveis em seus fluxos de dados sem a necessidade de definir explicitamente as alterações na coluna.

* Marque a caixa **permitir descompasso de esquema** se as colunas de origem forem alteradas com frequência. Essa configuração permite que todos os campos de origem de entrada fluam por meio das transformações para o coletor.

* Escolher **inferir tipos de coluna descompassos** instruirá data Factory a detectar e definir tipos de dados para cada nova coluna descoberta. Com esse recurso desativado, todas as colunas descompassos serão do tipo cadeia de caracteres.

**Validar esquema:** Se a validação de esquema for selecionada, o fluxo de dados não será executado se os dados de origem de entrada não corresponderem ao esquema definido do DataSet.

**Ignorar contagem de linhas:** O campo ignorar contagem de linhas especifica quantas linhas ignorar no início do conjunto de um.

**Amostragem:** Habilite a amostragem para limitar o número de linhas de sua origem. Use essa configuração quando você testar ou criar amostras de dados de sua fonte para fins de depuração.

**Linhas multilinha:** Selecione linhas multilinha se o arquivo de texto de origem contiver valores de cadeia de caracteres que abranjam várias linhas, ou seja, linhas novas dentro de um valor.

Para validar se a fonte está configurada corretamente, ative o modo de depuração e busque uma visualização de dados. Para obter mais informações, consulte [modo de depuração](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Quando o modo de depuração estiver ativado, a configuração de limite de linha nas configurações de depuração substituirá a configuração de amostragem na origem durante a visualização de dados.

## <a name="file-based-source-options"></a>Opções de origem baseadas em arquivo

Se você estiver usando um conjunto de um DataSet baseado em arquivo, como o armazenamento de BLOBs do Azure ou Azure Data Lake Storage, a guia **Opções de origem** permitirá que você gerencie como a origem lê os arquivos.

![Opções de origem](media/data-flow/sourceOPtions1.png "Opções de origem")

**Caminho curinga:** Usar um padrão curinga instruirá o ADF a executar um loop em cada pasta e arquivo correspondentes em uma única transformação de origem. Essa é uma maneira eficaz de processar vários arquivos dentro de um único fluxo. Adicione vários padrões de correspondência de curingas com o sinal de + que aparece ao passar o mouse sobre o padrão de curinga existente.

Em seu contêiner de origem, escolha uma série de arquivos que correspondem a um padrão. Somente o contêiner pode ser especificado no DataSet. O caminho curinga, portanto, também deve incluir o caminho da pasta da pasta raiz.

Exemplos de curinga:

* ```*``` representa qualquer conjunto de caracteres
* ```**``` representa o aninhamento de diretório recursivo
* ```?``` substitui um caractere
* ```[]``` corresponde a um ou mais caracteres entre colchetes

* ```/data/sales/**/*.csv``` obtém todos os arquivos CSV em/data/Sales
* ```/data/sales/20??/**``` obtém todos os arquivos no século 20
* ```/data/sales/2004/*/12/[XY]1?.csv``` Obtém todos os arquivos CSV em 2004 em dezembro, começando com X ou Y prefixados por um número de dois dígitos

**Caminho raiz da partição:** Se você tiver pastas particionadas em sua fonte de arquivo com um formato ```key=value``` (por exemplo, Year = 2019), poderá atribuir o nível superior dessa árvore de pastas de partição a um nome de coluna no fluxo de dados do fluxo de dados.

Primeiro, defina um curinga para incluir todos os caminhos que são as pastas particionadas mais os arquivos folha que você deseja ler.

![Configurações do arquivo de origem da partição](media/data-flow/partfile2.png "Configuração do arquivo de partição")

Use a configuração caminho raiz da partição para definir qual é o nível superior da estrutura de pastas. Ao exibir o conteúdo de seus dados por meio de uma visualização de dados, você verá que o ADF adicionará as partições resolvidas encontradas em cada um dos níveis de pasta.

![Caminho raiz da partição](media/data-flow/partfile1.png "Visualização do caminho raiz da partição")

**Lista de arquivos:** Este é um conjunto de arquivos. Crie um arquivo de texto que inclua uma lista de arquivos de caminho relativo a serem processados. Aponte para este arquivo de texto.

**Coluna para armazenar o nome do arquivo:** Armazene o nome do arquivo de origem em uma coluna em seus dados. Insira um novo nome de coluna aqui para armazenar a cadeia de caracteres de nome de arquivo.

**Após a conclusão:** Escolha não fazer nada com o arquivo de origem após a execução do fluxo de dados, exclua o arquivo de origem ou mova o arquivo de origem. Os caminhos para a movimentação são relativos.

Para mover os arquivos de origem para outro local de pós-processamento, primeiro selecione "mover" para a operação de arquivo. Em seguida, defina o diretório "de". Se você não estiver usando curingas para o caminho, a configuração "de" será a mesma pasta que a pasta de origem.

Se você tiver um caminho de origem com curinga, sua sintaxe terá a seguinte aparência:

```/data/sales/20??/**/*.csv```

Você pode especificar "from" como

```/data/sales```

E "para" como

```/backup/priorSales```

Nesse caso, todos os arquivos que foram originados em/data/Sales são movidos para/backup/priorSales.

> [!NOTE]
> As operações de arquivo são executadas somente quando você inicia o fluxo de dados de uma execução de pipeline (uma depuração de pipeline ou execução de execução) que usa a atividade executar fluxo de dados em um pipeline. As operações de arquivo *não são* executadas no modo de depuração de fluxo de dados.

**Filtrar por última modificação:** Você pode filtrar quais arquivos são processados especificando um intervalo de datas de quando eles foram modificados pela última vez. Todas as datas-hora estão em UTC. 

### <a name="add-dynamic-content"></a>Adicionar conteúdo dinâmico

Todas as configurações de origem podem ser especificadas como expressões usando a [linguagem de expressão de transformação mapear fluxo de dados](data-flow-expression-functions.md). Para adicionar conteúdo dinâmico, clique ou focalize dentro dos campos no painel configurações. Clique no hiperlink para **adicionar conteúdo dinâmico**. Isso iniciará o construtor de expressões, no qual você pode definir valores dinamicamente usando expressões, valores literais estáticos ou parâmetros.

![Parâmetros](media/data-flow/params6.png "Parâmetros")

## <a name="sql-source-options"></a>Opções de origem do SQL

Se sua fonte estiver no banco de dados SQL ou SQL Data Warehouse, configurações adicionais específicas do SQL estarão disponíveis na guia **Opções de origem** . 

**Entrada:** Selecione se você apontar sua fonte em uma tabela (equivalente a ```Select * from <table-name>```) ou inserir uma consulta SQL personalizada.

**Consulta**: se você selecionar consulta no campo de entrada, insira uma consulta SQL para sua origem. Essa configuração substitui qualquer tabela que você tenha escolhido no conjunto de um. Não há suporte para cláusulas **ordenar por** aqui, mas você pode definir uma instrução SELECT FROM completa. Você também pode usar funções de tabela definidas pelo usuário. **Select * de udfGetData ()** é um UDF no SQL que retorna uma tabela. Essa consulta produzirá uma tabela de origem que você pode usar em seu fluxo de dados.

**Tamanho do lote**: Insira um tamanho de lote para dividir dados grandes em leituras.

**Nível de isolamento**: o padrão para as fontes SQL no fluxo de dados de mapeamento é leitura não confirmada. Você pode alterar o nível de isolamento aqui para um destes valores:
* Leitura confirmada
* Leitura não confirmada
* Leitura repetida
* Serializável
* Nenhum (ignorar nível de isolamento)

![Nível de isolamento](media/data-flow/isolationlevel.png "Nível de isolamento")

## <a name="projection"></a>Projeção

Como os esquemas em conjuntos de dados, a projeção em uma fonte define as colunas, os tipos e os formatos dos dados de origem. Para a maioria dos tipos de conjunto de conjuntos, como SQL e parquet, a projeção em uma fonte é fixada para refletir o esquema definido em um conjunto de um DataSet. Quando os arquivos de origem não são fortemente tipados (por exemplo, arquivos CSV simples em vez de arquivos parquet), você pode definir os tipos de dados para cada campo na transformação de origem.

![Configurações na guia projeção](media/data-flow/source3.png "Projeção")

Se o arquivo de texto não tiver um esquema definido, selecione **detectar tipo de dados** para que data Factory obterá amostras e inferirá os tipos de dados. Selecione **definir formato padrão** para detectar automaticamente os formatos de dados padrão. 

Você pode modificar os tipos de dados de coluna em uma transformação de coluna derivada de fluxo inferior. Use uma transformação selecionar para modificar os nomes de coluna.

## <a name="optimize-the-source-transformation"></a>Otimizar a transformação de origem

Na guia **otimizar** da transformação origem, você poderá ver um tipo de partição de **origem** . Essa opção só está disponível quando sua origem é o banco de dados SQL do Azure. Isso ocorre porque Data Factory tenta fazer conexões paralelas para executar consultas grandes em sua origem do banco de dados SQL.

![Configurações de partição de origem](media/data-flow/sourcepart3.png "Particionamento")

Você não precisa Particionar dados em sua origem do banco do dados SQL, mas as partições são úteis para consultas grandes. Você pode basear sua partição em uma coluna ou em uma consulta.

### <a name="use-a-column-to-partition-data"></a>Usar uma coluna para particionar dados

Na tabela de origem, selecione uma coluna na qual particionar. Defina também o número de partições.

### <a name="use-a-query-to-partition-data"></a>Usar uma consulta para particionar dados

Você pode optar por particionar as conexões com base em uma consulta. Insira o conteúdo de um predicado WHERE. Por exemplo, digite year > 1980.

Para obter mais informações sobre a otimização no fluxo de dados de mapeamento, consulte a [guia otimizar](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Passos seguintes

Comece a criar uma [transformação de coluna derivada](data-flow-derived-column.md) e uma [transformação selecionar](data-flow-select.md).
