---
title: Mapeamento de transformação de Sink de fluxo de dados de fábrica de dados do Azure
description: Mapeamento de transformação de Sink de fluxo de dados de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dba043721c2d81b7fe2c254f62328e54bb959cdc
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729377"
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

### <a name="output-settings"></a>Definições de saída

Substituir irá truncar a tabela se existir, em seguida, recriá-lo e carregar os dados. Acrescentar irá inserir novas linhas. Se a tabela a partir do nome de tabela do conjunto de dados não existe nenhuma no destino ADW, fluxo de dados irá criar a tabela, em seguida, carregue os dados.

Se desmarcar "Mapear automática", pode mapear os campos à sua tabela de destino manualmente.

![Opções de ADW de sink](media/data-flow/adw2.png "adw sink")

#### <a name="field-mapping"></a>Mapeamento de campos

No separador de mapeamento da sua transformação de Sink, pode mapear as colunas de entrada (lado esquerdo) para o destino (lado direito). Quando fluxos de dados de sink para ficheiros, o ADF sempre escreve novos ficheiros para uma pasta. Quando mapear para um conjunto de dados do banco de dados, pode optar por gerar uma nova tabela com esse esquema (definido guardar a política de "substituir") ou inserir novas linhas para um existente uma tabela e mapeiam os campos para o esquema existente.

Pode utilizar seleção múltipla na tabela de mapeamento para ligar a várias colunas com um só clique, Delink várias colunas ou mapear várias linhas para o mesmo nome de coluna.

![Mapeamento de campo](media/data-flow/multi1.png "várias opções")

Se pretende repor os mapeamentos de colunas, prima o botão "Mapear novamente" para repor os mapeamentos.

![Ligações](media/data-flow/maxcon.png "ligações")

### <a name="updates-to-sink-transformation-for-adf-v2-ga-version"></a>Atualizações para o Sink de transformação para a versão de disponibilidade geral do ADF V2

![Opções de sink](media/data-flow/sink1.png "um Sink")

![Opções de sink](media/data-flow/sink2.png "Coletores de")

* Permitir que as opções de esquema Descompassos e validar esquema estão agora disponíveis no Sink. Isso permitirá que instruir o ADF para totalmente aceite as definições de esquema flexível (esquema Descompassos) ou falhar o Sink se o esquema for alterado (validar esquema).

* Limpe a pasta. ADF irá truncar o conteúdo da pasta sink antes de gravar os ficheiros de destino nessa pasta de destino.

* Opções de nome de ficheiro

   * Predefinição: Permitir que o Spark para nomear arquivos com base nas predefinições de parte
   * Pattern: Introduza um nome para os seus ficheiros de saída
   * Por partição: Introduza um nome de ficheiro por partição
   * Como os dados na coluna: Definir o ficheiro de saída para o valor de uma coluna

> [!NOTE]
> Operações de arquivo só serão executado quando estiver a executar a atividade executar fluxo de dados, não enquanto estiver no modo de dados de fluxo de depuração

Com os tipos de sink do SQL, pode definir:

* Truncar a tabela
* Recrie a tabela (executa drop/criar)
* Tamanho do lote para grandes cargas de dados. Introduza um número a escritas de bucket em segmentos.

![Mapeamento de campos](media/data-flow/sql001.png "opções de SQL")

## <a name="next-steps"></a>Passos Seguintes

Agora que criou o fluxo de dados, adicione uma [atividade de executar o fluxo de dados para o seu pipeline](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-overview).
