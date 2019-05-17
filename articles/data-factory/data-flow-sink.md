---
title: Configurar uma transformação de sink na funcionalidade de mapeamento de fluxo de dados do Azure Data Factory
description: Saiba como configurar uma transformação de sink no mapeamento de fluxo de dados.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 4341cbb0e24330d535f5211c088f0068eab33af7
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596257"
---
# <a name="sink-transformation-for-a-data-flow"></a>Transformação de um fluxo de dados de sink

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Após transformar o fluxo de dados, pode sink os dados para um conjunto de dados de destino. Na transformação de sink, escolha uma definição de conjunto de dados para os dados de saída de destino. Pode ter como transformações de sink requer que o fluxo de dados.

A conta para desvios de esquema e as alterações nos dados recebidos, os dados de saída de sink para uma pasta sem um esquema definido no conjunto de dados de saída. Pode também em conta as alterações de coluna em suas origens de selecionando **permitir que os descompassos de esquema** na origem. Em seguida, automap todos os campos no coletor.

![Opções no separador de Sink, incluindo a opção de mapa de Auto](media/data-flow/sink1.png "sink 1")

Para todos os campos de entrada de sink, ative **automática mapa**. Para escolher os campos para o sink para o destino ou para alterar os nomes dos campos no destino, desative **automática mapa**. Em seguida, abra a **mapeamento** separador para mapear campos de saída.

![Opções no separador de mapeamento](media/data-flow/sink2.png "sink 2")

## <a name="output"></a>Resultado 
Para armazenamento de Blobs do Azure ou tipos de sink de armazenamento do Data Lake, saída os dados transformados para uma pasta. Spark gera arquivos de dados de saída particionada com base no esquema de particionamento que utiliza a transformação de sink. 

Pode definir o esquema de particionamento do **otimizar** separador. Se pretender que o Data Factory para intercalar o resultado num único arquivo, selecione **única partição**.

![Opções no separador de otimização](media/data-flow/opt001.png "opções de sink")

## <a name="field-mapping"></a>Mapeamento de campos

Sobre o **mapeamento** separador da sua transformação de sink, pode mapear as colunas de entrada à esquerda para os destinos à direita. Quando fluxos de dados de sink para ficheiros, o Data Factory sempre escreve novos ficheiros para uma pasta. Quando mapear para um conjunto de dados do banco de dados, pode gerar uma nova tabela que usa esse esquema definindo **guardar política** ao **substituir**. Ou inserir novas linhas de uma tabela existente e, em seguida, mapear os campos para o esquema existente. 

![O separador de mapeamento](media/data-flow/sink2.png "Coletores de")

Na tabela de mapeamento, pode seleção múltipla para ligar a várias colunas, delink várias colunas ou mapear várias linhas para o mesmo nome de coluna.

Para mapear sempre o conjunto de entrada de campos para um destino, que são e aceitar completamente as definições de esquema flexível, selecione **permitir que os descompassos de esquema**.

![O separador de mapeamento, que mostra os campos mapeados para colunas no conjunto de dados](media/data-flow/multi1.png "várias opções")

Para repor o seu mapeamento de colunas, selecione **remapear**.

![O separador de Sink](media/data-flow/sink1.png "um Sink")

Selecione **validar esquema** falhe o sink se o esquema for alterado.

Selecione **limpar a pasta** truncar o conteúdo da pasta sink antes de gravar os ficheiros de destino nessa pasta de destino.

## <a name="file-name-options"></a>Opções de nome de ficheiro

Configure nomes de arquivos: 

   * **Predefinido**: Permitir que o Spark para nomear arquivos com base nas predefinições de parte.
   * **Padrão de**: Introduza um padrão para os seus ficheiros de saída. Por exemplo, **empréstimos [n]** criará loans1.csv loans2.csv e assim por diante.
   * **Por partição**: Introduza um nome de ficheiro por partição.
   * **Como os dados na coluna**: Defina o ficheiro de saída para o valor de uma coluna.
   * **Saída para um único arquivo**: Com esta opção, o ADF vai juntar os ficheiros de saída particionado num único ficheiro com o nome. Para utilizar esta opção, o conjunto de dados deve resolver para um nome de pasta. Além disso,. tenha em atenção que esta operação de intercalação, possivelmente, pode falhar com base no tamanho do nó.

> [!NOTE]
> Início de operações de ficheiros apenas quando estiver a executar a atividade executar fluxo de dados. Eles não são iniciadas no modo de dados de fluxo de depuração.

## <a name="database-options"></a>Opções de base de dados

Escolha as definições de base de dados:

* **Atualizar o método**: A predefinição é permitir que inserções. Limpar **insert permitir** se de que pretende parar a inserir novas linhas da sua origem. Para atualizar, upsert, ou eliminar linhas, adicione uma transformação de linha de alter para linhas de etiqueta para essas ações. 
* **Recrie a tabela**: Remova ou criar a sua tabela de destino antes dos dados de fluxo estiver concluída.
* **Tabela truncar**: Remova todas as linhas da sua tabela de destino antes dos dados de fluxo estiver concluída.
* **Tamanho do lote**: Introduza um número a escritas de bucket em segmentos. Utilize esta opção para cargas de dados grandes. 
* **Ativar o teste**: Utilize o PolyBase, quando carrega o armazém de dados do Azure como o conjunto de dados de sink.

![Separador Definições, que mostra as opções de sink do SQL](media/data-flow/alter-row2.png "opções de SQL")

> [!NOTE]
> No fluxo de dados, pode direcionar o Data Factory para criar uma nova definição de tabela na base de dados de destino. Para criar a definição da tabela, defina um conjunto de dados na transformação de sink, que tem um novo nome de tabela. No conjunto de dados SQL, abaixo do nome de tabela, selecione **editar** e introduza um novo nome de tabela. Em seguida, na transformação de sink, ativassem **permitir que os descompassos de esquema**. Definir **importar esquema** ao **nenhum**.

![Definições do conjunto de dados SQL, que mostra onde pode editar o nome da tabela](media/data-flow/dataset2.png "esquema SQL")

> [!NOTE]
> Quando atualiza ou eliminar linhas na sua sink de base de dados, tem de definir a coluna chave. Esta definição permite a transformação de linha de alter determinar a linha exclusiva na biblioteca de movimento de dados (DML).

## <a name="next-steps"></a>Passos Seguintes

Agora que criou o fluxo de dados, adicione uma [atividade de fluxo de dados para o seu pipeline](concepts-data-flow-overview.md).
