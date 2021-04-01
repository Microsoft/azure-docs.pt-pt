---
title: Alterar a transformação da linha no fluxo de dados de mapeamento
description: Como atualizar o alvo da base de dados usando a transformação da linha de alteração no fluxo de dados de mapeamento
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/06/2020
ms.openlocfilehash: c3858756a0140481c0ab249e29c95f76c4b90da5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "82982654"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Alterar a transformação da linha no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a transformação alter row para definir as políticas de inserção, exclusão, atualização e atualização em linhas. Pode adicionar uma a muitas condições como expressões. Estas condições devem ser especificadas por ordem prioritária, uma vez que cada linha será marcada com a política correspondente à expressão de primeira correspondência. Cada uma dessas condições pode resultar na inserção, na atualização, na sua atualização ou na atualização de uma linha. A Alter Row pode produzir as ações DDL & DML na sua base de dados.

![Alterar as definições de linha](media/data-flow/alter-row1.png "Alterar definições de linha")

As transformações da Alter Row só funcionarão na base de dados ou nos lavatórios CosmosDB no fluxo de dados. As ações que atribui às linhas (inserir, atualizar, apagar, intensificar) não ocorrerão durante as sessões de depuração. Execute uma atividade de Fluxo de Dados executado num oleoduto para decretar as políticas de alteração de linha nas suas tabelas de bases de dados.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vJYc]

## <a name="specify-a-default-row-policy"></a>Especificar uma política de linha padrão

Crie uma transformação alter row e especifique uma política de linha com uma condição de `true()` . Cada linha que não corresponda a nenhuma das expressões previamente definidas será marcada para a política de linha especificada. Por predefinição, cada linha que não corresponda a qualquer expressão condicional será marcada para `Insert` .

![Alterar a política de linha](media/data-flow/alter-row4.png "Alterar a política de linha")

> [!NOTE]
> Para marcar todas as linhas com uma política, pode criar uma condição para essa política e especificar a condição como `true()` .

## <a name="view-policies-in-data-preview"></a>Ver políticas na pré-visualização de dados

Utilize [o modo de depurador](concepts-data-flow-debug-mode.md) para visualizar os resultados das suas políticas de alter linha no painel de pré-visualização de dados. Uma pré-visualização de dados de uma transformação de linha alterada não produzirá ações de DDL ou DML contra o seu alvo.

![Alterar políticas de linha](media/data-flow/alter-row3.png "Alterar políticas de linha")

Cada política de linha alterada é representada por um ícone que indica se ocorrerá uma ação de inserção, atualização, atualização ou ação eliminada. O cabeçalho superior mostra quantas linhas são afetadas por cada política na pré-visualização.

## <a name="allow-alter-row-policies-in-sink"></a>Permitir alterar políticas de linha no lavatório

Para que as políticas de alteração da linha funcionem, o fluxo de dados deve escrever para uma base de dados ou para a cosmia cosmos. No **separador Definições** na pia, ativar quais as políticas de alteração da linha para a pia.

![Alterar pia de linha](media/data-flow/alter-row2.png "Sumidouro de Alter Row")

O comportamento padrão é apenas permitir inserções. Para permitir atualizações, enertes ou eliminações, verifique a caixa na pia correspondente a essa condição. Se forem ativadas atualizações, atualizações ou eliminações, deve especificar quais as colunas-chave da pia para combinar.

> [!NOTE]
> Se as suas inserções, atualizações ou atualizações modificarem o esquema da tabela alvo na pia, o fluxo de dados falhará. Para modificar o esquema-alvo na sua base de dados, escolha **a tabela 'Recriar'** como ação de tabela. Isto vai cair e recriar a sua mesa com a nova definição de esquema.

A transformação do lavatório requer uma única chave ou uma série de chaves para identificação de linha única na base de dados alvo. Para os lavatórios SQL, coloque as teclas no separador de definições da pia. Para o CosmosDB, defina a chave de partição nas definições e também coloque o campo do sistema CosmosDB "id" no seu mapeamento da pia. Para o CosmosDB, é obrigatório incluir a coluna do sistema "id" para atualizações, atualizações e eliminações.

## <a name="merges-and-upserts-with-azure-sql-database-and-synapse"></a>Fundições e enertes com base de dados Azure SQL e Sinapse

ADF Data Flows suporta as fusões contra a Base de Dados Azure SQL e o conjunto de bases de dados Synapse (data warehouse) com a opção upsert.

No entanto, poderá encontrar cenários em que o seu esquema de base de dados-alvo utilizasse a propriedade identitária das colunas-chave. A ADF exige que identifique as teclas que utilizará para corresponder aos valores de linha para atualizações e atualizações. Mas se a coluna-alvo tiver o conjunto de propriedade de identidade e estiver a usar a política de upsert, a base de dados-alvo não permitirá que escreva para a coluna. Pode também encontrar erros quando tentar enuerá-lo contra a coluna de distribuição de uma mesa distribuída.

Eis as formas de corrigir isto:

1. Vá para as Definições de transformação do lavatório e defina "Saltar colunas-chave de escrita". Isto dirá à ADF para não escrever a coluna que selecionou como o valor-chave para o seu mapeamento.

2. Se essa coluna-chave não for a coluna que está a causar o problema das colunas de identidade, então pode utilizar a opção SQL de transformação de sumidouro: ```SET IDENTITY_INSERT tbl_content ON``` . Em seguida, desligue-o com a propriedade SQL pós-processamento: ```SET IDENTITY_INSERT tbl_content OFF``` .

3. Tanto para o caso da identidade como para o caso da coluna de distribuição, pode mudar a sua lógica de Upsert para uma condição de atualização separada e uma condição de inserção separada utilizando uma transformação de Divisão Condicional. Desta forma, pode definir o mapeamento no caminho da atualização para ignorar o mapeamento da coluna-chave.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Syntax

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

O exemplo abaixo é uma transformação de linha alterada nomeada `CleanData` que toma um fluxo de entrada e cria três `SpecifyUpsertConditions` condições de linha alteradas. Na transformação anterior, é calculada uma coluna nomeada `alterRowCondition` que determina se uma linha é ou não inserida, atualizada ou eliminada na base de dados. Se o valor da coluna tiver um valor de corda que corresponda à regra da linha de alteração, é atribuída essa política.

No Data Factory UX, esta transformação parece a imagem abaixo:

![Alterar exemplo de linha](media/data-flow/alter-row4.png "Alterar exemplo de linha")

O roteiro do fluxo de dados para esta transformação está no snippet abaixo:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Passos seguintes

Após a transformação de Alter Row, poderá querer [afundar os seus dados numa loja de dados de destino.](data-flow-sink.md)
