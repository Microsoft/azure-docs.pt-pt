---
title: Mapeamento de transformação de origem do fluxo de dados de fábrica de dados do Azure
description: Mapeamento de transformação de origem do fluxo de dados de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 80f0aa3158ce4f3eeafaeb5c7834dc820e8ba75b
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886600"
---
# <a name="mapping-data-flow-source-transformation"></a>Transformação de origem de fluxo de dados de mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A transformação de origem configura uma origem de dados que deseja usar para trazer dados para o seu fluxo de dados. Talvez tenha mais do que uma transformação de origem num único fluxo de dados. Sempre comece criando seus fluxos de dados com uma origem.

> [!NOTE]
> Cada fluxo de dados requer, pelo menos, uma transformação de origem. Adicione tantas outras fontes que forem necessárias para concluir as transformações de dados. Pode associar essas origens em conjunto com uma associação ou transformação Union.

![Opções de transformação de origem](media/data-flow/source.png "origem")

Cada transformação de origem do fluxo de dados tem de estar associada a exatamente um Dataset de fábrica de dados, que define a forma e a localização dos seus dados para escrever ou ler a partir do. Pode utilizar listas de carateres universais e os ficheiros na sua origem para trabalhar com mais de um arquivo cada vez.

## <a name="data-flow-staging-areas"></a>Áreas de preparo de fluxo de dados

Fluxo de dados funciona com conjuntos de dados "testes", que estão todos no Azure. Estes conjuntos de dados de fluxo de dados são utilizados para dados de teste para efetuar as transformações de dados. Data Factory tem acesso a quase 80 conectores nativos diferentes. Para incluir dados de outras origens esses em seu fluxo de dados, primeiro testar esses dados em uma dessas áreas de teste do conjunto de dados de fluxo de dados através da atividade de cópia.

## <a name="options"></a>Opções

### <a name="allow-schema-drift"></a>Permitir que os descompassos de esquema
Selecione permitir Descompassos de esquema se as colunas de origem serão alterados com freqüência. Esta definição permitirá que todos os campos de entrada da sua origem a ser enviados por meio de transformações para o Sink.

### <a name="validate-schema"></a>Validar o esquema

![Fonte públicos](media/data-flow/source1.png "fonte públicos 1")

Em seguida, se a versão de entrada dos dados de origem não corresponde um esquema definido, a execução de fluxo de dados irá falhar.

### <a name="sampling"></a>Amostragem
Utilize amostragem para limitar o número de linhas da sua origem.  Isto é útil quando precisa apenas uma amostra dos seus dados de origem para teste e fins de depuração.

## <a name="define-schema"></a>Definir esquema

![Transformação de origem](media/data-flow/source2.png "2 de origem")

Para tipos de ficheiro de origem que não estão com rigidez de tipos (ficheiros simples, ou seja, em vez de ficheiros Parquet) deve definir os tipos de dados para cada campo aqui na transformação de origem. Pode, posteriormente, alterar os nomes das colunas numa transformação selecione e os tipos de dados numa transformação Derived Column. 

![Transformação de origem](media/data-flow/source003.png "tipos de dados")

Para origens de rigidez de tipos, pode modificar os tipos de dados numa transformação selecione subsequente. 

### <a name="optimize"></a>Otimizar

![Partições de origem](media/data-flow/sourcepart.png "criação de partições")

No separador de otimização para a transformação de origem, verá um tipo de criação de partições adicional, chamado de "Origem". Isto irá apenas light-up quando tiver selecionado a BD SQL do Azure da sua origem. Isto acontece porque o ADF pretende colocar em paralelo ligações para executar consultas grandes na sua origem de BD SQL do Azure.

A criação de partições de dados na sua origem de BD SQL é opcional, mas é útil para consultas grandes. Tem duas opções:

### <a name="column"></a>Coluna

Selecione uma coluna para a partição da sua tabela de origem. Também tem de definir o número máximo de ligações.

### <a name="query-condition"></a>Condição de consulta

Opcionalmente, pode optar por ligações com base numa consulta de partição. Para esta opção, coloque o conteúdo de um predicado WHERE. Por exemplo, ano > 1980

## <a name="source-file-management"></a>Gestão de ficheiros de origem
![Novas definições de origem](media/data-flow/source2.png "novas definições")

* Caminho de caráter universal para escolher uma série de ficheiros da sua pasta de origem que correspondem a um padrão. Isto irá substituir qualquer ficheiro que definiu na sua definição de conjunto de dados.
* Lista de ficheiros. Mesmo que um conjunto de ficheiros. Aponte para um arquivo de texto que criar uma lista de ficheiros de caminho relativo a processar.
* Coluna para armazenar o nome de ficheiro irá armazenar o nome do ficheiro da origem numa coluna nos seus dados. Introduza um novo nome aqui para armazenar a cadeia de caracteres de nome de ficheiro.
* Após a conclusão (pode optar por não fazer nada com o ficheiro de origem depois do fluxo de dados é executado, elimine o ficheiro ou ficheiros de origem ou mover os ficheiros de origem. Os caminhos para mudança são caminhos relativos.

### <a name="sql-datasets"></a>Conjuntos de dados SQL

Quando estiver a utilizar o BD SQL do Azure ou o Azure SQL DW da sua origem, terá opções adicionais.

* Consulta: Introduza uma consulta SQL para a sua origem. Definir uma consulta irá substituir qualquer tabela que escolheu no conjunto de dados. Tenha em atenção que Order By não são suportadas cláusulas aqui.

* Tamanho do lote: Introduza um tamanho de lote para colocar partes de dados grandes em leituras de tamanho de lote.

> [!NOTE]
> As definições de operação de ficheiro só serão executado quando o fluxo de dados é executado a partir de uma execução de pipeline (depuração de pipeline ou execução execute) usando a atividade de executar o fluxo de dados num pipeline. Operações de arquivo não executado no modo de depuração de fluxo de dados.

### <a name="projection"></a>Projeção

![Projeção](media/data-flow/source3.png "projeção")

Semelhante aos esquemas em conjuntos de dados, a projeção na origem define as colunas de dados, os tipos de dados e formatos de dados de origem de dados. Se possui um arquivo de texto com um esquema definido, clique em "Detetar tipo de dados" para fazer o ADF para tentar de exemplo e inferir os tipos de dados. Pode definir os dados de predefinição formatos para detetar automaticamente com o botão "Definir o formato predefinido". Pode modificar os tipos de dados de coluna numa transformação Derived Column subsequente. Os nomes das colunas podem ser modificados usando a transformação selecione.

![Padrão de formatos](media/data-flow/source2.png "predefinido formatos")

## <a name="next-steps"></a>Passos Seguintes

Comece a criar a sua transformação de dados com [Derived Column](data-flow-derived-column.md) e [selecione](data-flow-select.md).
