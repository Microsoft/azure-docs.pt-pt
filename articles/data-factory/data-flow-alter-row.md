---
title: Alterar a transformação da linha no fluxo de dados de mapeamento
description: Como atualizar o alvo da base de dados utilizando a transformação da linha de alteração no fluxo de dados de mapeamento
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/06/2020
ms.openlocfilehash: c3858756a0140481c0ab249e29c95f76c4b90da5
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982654"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Alterar a transformação da linha no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a transformação da Alter Row para definir as políticas de inserção, eliminar, atualizar e aumentar as políticas nas linhas. Pode adicionar condições de um a muitas como expressões. Estas condições devem ser especificadas por ordem de prioridade, uma vez que cada linha será marcada com a política correspondente à expressão de primeira correspondência. Cada uma dessas condições pode resultar na inserção, atualização, anotada ou upserted de cada uma dessas condições. Alter Row pode produzir ambas as ações DDL & DML na sua base de dados.

![Alterar as definições da linha](media/data-flow/alter-row1.png "Alterar definições de linha")

As transformações da Alter Row só funcionarão na base de dados ou na cosmosDB afunda-se no fluxo de dados. As ações que atribui às linhas (insira, atualize, elimine, upsert) não ocorrerão durante as sessões de depuração. Execute uma atividade de Fluxo de Dados execute num oleoduto para decretar as políticas de alteração de linha nas tabelas da base de dados.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vJYc]

## <a name="specify-a-default-row-policy"></a>Especificar uma política de linha padrão

Crie uma transformação alter-row e `true()`especifique uma política de linha com uma condição de . Cada linha que não corresponda a nenhuma das expressões previamente definidas será marcada para a política de linha especificada. Por padrão, cada linha que não corresponda a `Insert`qualquer expressão condicional será marcada para .

![Alterar a política de linha](media/data-flow/alter-row4.png "Alterar a política de linha")

> [!NOTE]
> Para marcar todas as linhas com uma política, pode criar uma `true()`condição para essa política e especificar a condição como .

## <a name="view-policies-in-data-preview"></a>Ver políticas na pré-visualização de dados

Utilize o [modo de depuração](concepts-data-flow-debug-mode.md) para visualizar os resultados das suas políticas de alter row no painel de pré-visualização de dados. Uma pré-visualização de dados de uma transformação de linha alterada não produzirá ações DDL ou DML contra o seu alvo.

![Alterar políticas de linha](media/data-flow/alter-row3.png "Alterar políticas de linha")

Cada política de alter row é representada por um ícone que indica se uma inserção, atualização, upsert ou ação eliminada ocorrerá. O cabeçalho superior mostra quantas linhas são afetadas por cada política na pré-visualização.

## <a name="allow-alter-row-policies-in-sink"></a>Permitir alterar políticas de linha na pia

Para que as políticas de alter row funcionem, o fluxo de dados deve escrever para uma base de dados ou afundar cosmos. No separador **Definições** na pia, ative as políticas de alteração da linha para esse lavatório.

![Alter a pia da linha](media/data-flow/alter-row2.png "Alter Row Sink")

O comportamento padrão é apenas permitir inserções. Para permitir atualizações, upserts ou eliminações, verifique a caixa no lavatório correspondente a essa condição. Se estiverem ativadas atualizações, upserts ou eliminações, deve especificar quais as colunas-chave da pia para combinar.

> [!NOTE]
> Se as suas inserções, atualizações ou upserts modificarem o esquema da tabela-alvo na pia, o fluxo de dados falhará. Para modificar o esquema de destino na sua base de dados, escolha a **tabela Recriar** como ação de mesa. Isto vai cair e recriar a sua tabela com a nova definição de esquema.

A transformação do lavatório requer uma única chave ou uma série de chaves para identificação de linha única na sua base de dados alvo. Para os lavatórios SQL, coloque as teclas no separador de definições do lavatório. Para a CosmosDB, detete a chave de partição nas definições e também detete te o campo do sistema CosmosDB "id" no seu mapeamento de pia. Para a CosmosDB, é obrigatório incluir a coluna do sistema "id" para atualizações, upserts e eliminações.

## <a name="merges-and-upserts-with-azure-sql-database-and-synapse"></a>Fusíveis e upserts com Base de Dados Azure SQL e Synapse

A ADF Data Flows suporta as fusões contra a Base de Dados Azure SQL e o pool de bases de dados Synapse (armazém de dados) com a opção upsert.

No entanto, poderá encontrar cenários em que o seu esquema de base de dados alvo utilizou a propriedade de identidade das colunas-chave. A ADF requer que identifique as teclas que utilizará para corresponder aos valores da linha para atualizações e upserts. Mas se a coluna de destino tiver o conjunto de propriedades de identidade e estiver a usar a política de upsert, a base de dados de destino não lhe permitirá escrever para a coluna. Também pode ter erros quando tentar enfrentar a coluna de distribuição de uma tabela distribuída.

Aqui estão as formas de corrigir isto:

1. Vá para as Definições de transformação da pia e deseme as colunas de teclas "Saltar para a escrita". Isto dirá à ADF para não escrever a coluna que selecionou como o valor-chave para o seu mapeamento.

2. Se essa coluna de teclas não for a coluna que está a causar o problema das colunas de identidade, então pode utilizar a opção SQL de transformação de sink antes do processamento: ```SET IDENTITY_INSERT tbl_content ON```. Em seguida, desligue-o com a propriedade ```SET IDENTITY_INSERT tbl_content OFF```SQL pós-processamento: .

3. Tanto para o caso de identidade como para a caixa da coluna de distribuição, pode mudar a sua lógica de Upsert para utilizar uma condição de atualização separada e uma condição de inserção separada utilizando uma transformação de Divisão Condicional. Desta forma, pode definir o mapeamento no caminho da atualização para ignorar o mapeamento da coluna da chave.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>Exemplo

O exemplo abaixo é uma `CleanData` transformação de `SpecifyUpsertConditions` alter row chamada que toma um fluxo de entrada e cria três condições de alter row. Na transformação anterior, `alterRowCondition` é calculada uma coluna nomeada que determina se uma linha é ou não inserida, atualizada ou eliminada na base de dados. Se o valor da coluna tiver um valor de cadeia que corresponda à regra da linha de alteração, é atribuída essa política.

Na Fábrica de Dados UX, esta transformação parece a imagem abaixo:

![Alterar exemplo de linha](media/data-flow/alter-row4.png "Alterar exemplo de linha")

O script de fluxo de dados para esta transformação está no corte abaixo:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Passos seguintes

Após a transformação da Alter Row, é melhor [afundá-lo numa loja](data-flow-sink.md)de dados de destino.
