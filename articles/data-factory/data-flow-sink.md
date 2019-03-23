---
title: Mapeamento de transformação de Sink de fluxo de dados de fábrica de dados do Azure
description: Mapeamento de transformação de Sink de fluxo de dados de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: a56f391aa76bd1216fd51d516adb836a2093bcba
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371144"
---
# <a name="mapping-data-flow-sink-transformation"></a>Transformação de Sink de fluxo de dados de mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Opções de sink](media/data-flow/windows1.png "sink 1")

Após a conclusão da sua transformação de fluxo de dados, pode sink os dados transformados num conjunto de dados de destino. A transformação de Sink, pode escolher a definição de conjunto de dados que deseja usar para os dados de saída de destino. Pode ter tantos de Sink de transformação, à medida que necessita do seu fluxo de dados.

É uma prática comum para levar em conta a alteração de dados de entrada e levar em conta os descompassos de esquema para os dados de saída de sink para uma pasta sem um esquema definido no conjunto de dados de saída. Pode além em conta todas as alterações de coluna em suas origens ao selecionar "Permitir Descompassos de esquema" na origem e, em seguida, automap todos os campos no coletor.

Pode optar por substituir, acrescentar ou falhar o fluxo de dados quando afundando até um conjunto de dados.

Também pode escolher "automap" para todos os campos de entrada de sink. Se desejar escolhe os campos que pretende para o destino de sink, ou se gostaria de alterar os nomes dos campos no destino, escolha "Desativado" para "automap" e, em seguida, clique no separador de mapeamento para mapear campos de saída:

![Opções de sink](media/data-flow/sink2.png "sink 2")

## <a name="output-to-one-file"></a>Saída para um ficheiro
Para tipos de sink do Blob de armazenamento do Azure ou o Data Lake, produzirá os dados transformados para uma pasta. Spark irá gerar arquivos de dados de saída particionada com base no esquema de particionamento a ser utilizado na transformação de Sink. Pode definir o esquema de partição ao clicar na guia "Otimizar". Se desejar ADF para intercalar o resultado num único arquivo, clique no botão de opção "Partição única".

![Opções de sink](media/data-flow/opt001.png "opções de sink")

## <a name="field-mapping"></a>Mapeamento de campos

No separador de mapeamento da sua transformação de Sink, pode mapear as colunas de entrada (lado esquerdo) para o destino (lado direito). Quando fluxos de dados de sink para ficheiros, o ADF sempre escreve novos ficheiros para uma pasta. Quando mapear para um conjunto de dados do banco de dados, pode optar por gerar uma nova tabela com esse esquema (definido guardar a política de "substituir") ou inserir novas linhas para um existente uma tabela e mapeiam os campos para o esquema existente.

Pode utilizar seleção múltipla na tabela de mapeamento para ligar a várias colunas com um só clique, delink várias colunas ou mapear várias linhas para o mesmo nome de coluna.

Quando desejar sempre usar o conjunto de entrada de campos e mapeá-los para um destino como-é, definir a definição "Permitir Descompassos de esquema".

![Mapeamento de campo](media/data-flow/multi1.png "várias opções")

Se pretende repor os mapeamentos de colunas, prima o botão "Mapear novamente" para repor os mapeamentos.

![Opções de sink](media/data-flow/sink1.png "um Sink")

![Opções de sink](media/data-flow/sink2.png "Coletores de")

* Permitir que as opções de esquema Descompassos e validar esquema estão agora disponíveis no Sink. Isso permitirá que instruir o ADF para totalmente aceite as definições de esquema flexível (esquema Descompassos) ou falhar o Sink se o esquema for alterado (validar esquema).

* Limpe a pasta. ADF irá truncar o conteúdo da pasta sink antes de gravar os ficheiros de destino nessa pasta de destino.

## <a name="file-name-options"></a>Opções de nome de ficheiro

   * Predefinição: Permitir que o Spark para nomear arquivos com base nas predefinições de parte
   * Pattern: Introduza um padrão para os seus ficheiros de saída. Por exemplo, "empréstimos [n]" criará loans1.csv, loans2.csv,...
   * Por partição: Introduza um nome de ficheiro por partição
   * Como os dados na coluna: Definir o ficheiro de saída para o valor de uma coluna

> [!NOTE]
> Operações de arquivo só serão executado quando estiver a executar a atividade executar fluxo de dados, não enquanto estiver no modo de dados de fluxo de depuração

## <a name="database-options"></a>Opções de base de dados

* Permita insert, update, delete, upserts. A predefinição é permitir que inserções. Se desejar de atualização, upsert ou eliminação de linhas, primeiro tem de adicionar uma transformação de linha de alter para linhas de etiqueta para essas ações específicas. Desativar "Permitir insert" irá parar ADF de inserir novas linhas da sua origem.
* Truncar a tabela (remove todas as linhas da tabela de destino antes de concluir o fluxo de dados)
* Recrie a tabela (executa a queda/criação da sua tabela de destino de antes de concluir o fluxo de dados)
* Tamanho do lote para grandes cargas de dados. Introduza um número a escritas de bucket em segmentos
* Ative o teste: Isso instruirá o ADF para utilizar o Polybase ao carregar o armazém de dados do Azure como o conjunto de dados de sink

> [!NOTE]
> No fluxo de dados, pode pedir o ADF para criar uma nova definição de tabela na base de dados de destino através da definição de um conjunto de dados na transformação de Sink, que tem um novo nome de tabela. No conjunto de dados SQL, clique em "Editar" abaixo o nome da tabela e introduza um novo nome de tabela. Em seguida, na transformação de Sink, ative "Permitir Descompassos de esquema". Seth a definição de "Importar esquema" como None.

![Esquema de transformação de origem](media/data-flow/dataset2.png "esquema SQL")

![Opções de Sink do SQL](media/data-flow/alter-row2.png "opções de SQL")

> [!NOTE]
> Quando atualizar ou eliminar linhas na sua sink de base de dados, tem de definir a coluna chave. Dessa forma, a linha de Alter é capaz de determinar a linha exclusiva no DML.

## <a name="next-steps"></a>Passos Seguintes

Agora que criou o fluxo de dados, adicione uma [atividade de executar o fluxo de dados para o seu pipeline](concepts-data-flow-overview.md).
