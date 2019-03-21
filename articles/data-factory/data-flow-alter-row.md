---
title: Mapeamento de transformação de linha de Alter de fluxo de dados de fábrica de dados do Azure
description: Como atualizar o destino de base de dados através do Azure Data Factory mapeamento fluxo Alter linha transformação de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: d842898ca700490ae99b46140be6609622a144df
ms.sourcegitcommit: 5f41e855d415cfa741d8f710792ea486480df5cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58133166"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Transformação de linha de Alter de fábrica de dados do Azure

Utilize a transformação de linha de Alter para definir políticas de insert, delete, atualização e upsert nas linhas. Pode adicionar condições de um-para-muitos como expressões. Cada uma destas condições pode resultar numa linha (ou linhas) a ser inserido, atualizado, eliminado ou upsert. ALTER linha pode produzir DDL & DML ações na base de dados.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Alterar as definições de linha](media/data-flow/alter-row1.png "alterar definições de linha")

> [!NOTE]
> Transformações de linha de ALTER só vão operar Coletores de base de dados no seu fluxo de dados. As ações que são atribuídas a linhas (insert, update, delete, upsert) não irão ocorrer durante a sessões de depuração. Tem de adicionar uma tarefa de executar o fluxo de dados para um pipeline e utilizar a depuração de pipeline ou acionadores adotar as políticas de linha de alter num suas tabelas de base de dados.

## <a name="view-policies"></a>Ver políticas

Mudar o modo de dados de fluxo de depuração no e, em seguida, ver os resultados das suas políticas de linha de alterar no painel de pré-visualização de dados. Uma linha de alter em execução no modo de dados de fluxo de depuração não produzirá DDL ou DML ações contra o destino. Para ter essas ações que ocorrem, execute o fluxo de dados dentro de uma atividade de executar o fluxo de dados dentro de um pipeline.

![Alterar as políticas de linha](media/data-flow/alter-row3.png "alterar políticas de linha")

Isso permitirá que verificar e ver o estado de cada linha com base em suas condições. Existem ícone representa para cada insert, update, delete e upsert ação que irá ocorrer no seu fluxo de dados, que indica qual a ação irá ocorrer quando executar o fluxo de dados dentro de um pipeline.

## <a name="sink-settings"></a>Definições de sink

Tem de ter uma base de dados sink o tipo de linha de Alter trabalhar. O coletor de definições, tem de definir cada ação seja permitido.

![Alterar o sink de linha](media/data-flow/alter-row2.png "Alter Sink de linha")

O comportamento padrão no fluxo de dados do ADF com Coletores de base de dados é inserir linhas. Se quiser permitir atualizações e eliminações também upserts, também tem de verificar essas caixas de sink para permitir as ações.

> [!NOTE]
> Se sua inserções, atualizações ou upserts modificar o esquema de tabela de destino no coletor, o fluxo de dados irá falhar. Para modificar o esquema de destino na base de dados, tem de escolher a opção "Recrie a tabela" no coletor. Isto irá remover e recriar as tabelas com a nova definição de esquema.

## <a name="next-steps"></a>Passos Seguintes

Após a transformação de linha de Alter, talvez queira [seus dados de sink para um arquivo de dados de destino](data-flow-sink.md).
