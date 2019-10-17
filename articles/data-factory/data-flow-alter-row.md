---
title: Azure Data Factory mapeando a transformação alterar linha do fluxo de dados
description: Como atualizar o destino do banco de dados usando Azure Data Factory mapeando a transformação alterar linha
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: fff08b3e046161fbedefdc55f4e6a39a7f965f80
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387272"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Transformação alterar linha Azure Data Factory

Use a transformação alterar linha para definir políticas de inserção, exclusão, atualização e Upsert em linhas. Você pode adicionar condições um-para-muitos como expressões. Essas condições devem ser especificadas em ordem de prioridade, pois cada linha será marcada com a política correspondente à primeira expressão correspondente. Cada uma dessas condições pode resultar em uma linha (ou linhas) sendo inserida, atualizada, excluída ou introduzida. ALTER Row pode produzir as ações de DDL & DML em seu banco de dados.



![Alterar configurações de linha](media/data-flow/alter-row1.png "Alterar configurações de linha")

> [!NOTE]
> As transformações de alteração de linha só funcionarão em coletores de banco de dados em seu fluxo. As ações que você atribui a linhas (inserir, atualizar, excluir, Upsert) não ocorrerão durante as sessões de depuração. Você deve adicionar uma tarefa Executar fluxo de dados a um pipeline e usar depuração de pipeline ou gatilhos para aplicarr as políticas de alteração de linha em suas tabelas de banco de dados.

## <a name="indicate-a-default-row-policy"></a>Indicar uma política de linha padrão

Crie uma transformação ALTER Row e especifique uma política de linha com uma condição de `true()`. Cada linha que não atende a nenhuma das expressões definidas anteriormente será marcada para a política de linha especificada. Por padrão, cada linha que não atende a nenhuma expressão condicional será marcada para `Insert`.

![Alterar uma política de linha única](media/data-flow/alter-row4.png "Alterar uma política de linha única")

> [!NOTE]
> Para marcar todas as linhas com uma política, você pode criar uma condição para essa política e especificar a condição como `true()`.

## <a name="view-policies"></a>Exibir políticas

Ative o modo de depuração de fluxo de dados para exibir os resultados de suas políticas de alteração de linha no painel de visualização de dados. A execução de uma alteração de linha no modo de depuração do fluxo de dados não produzirá ações DDL ou DML em relação ao seu destino. Para que essas ações ocorram, execute o fluxo de dados dentro de uma atividade executar fluxo de dados em um pipeline.

![Alterar políticas de linha](media/data-flow/alter-row3.png "Alterar políticas de linha")

Isso permitirá que você verifique e exiba o estado de cada linha com base em suas condições. Há ícones representativos para cada ação de inserção, atualização, exclusão e Upsert que ocorrerá em seu fluxo de dados, indicando qual ação ocorrerá quando você executar o fluxo de dados dentro de um pipeline.

## <a name="sink-settings"></a>Configurações do coletor

Você deve ter um tipo de coletor de banco de dados para alterar linha para funcionar. Nas configurações do coletor, você deve definir cada ação correspondente às condições de alteração de linha a serem permitidas.

![Alterar coletor de linha](media/data-flow/alter-row2.png "Alterar coletor de linha")

O comportamento padrão no fluxo de dados do ADF com coletores de banco de dado é inserir linhas. Se você deseja permitir atualizações, upserts e exclusões também, também deve marcar essas caixas no coletor para permitir as ações.

> [!NOTE]
> Se suas inserções, atualizações ou upserts modificarem o esquema da tabela de destino no coletor, o fluxo de dados falhará. Para modificar o esquema de destino em seu banco de dados, você deve escolher a opção "recriar tabela" no coletor. Isso removerá e recriará sua tabela com a nova definição de esquema.

## <a name="next-steps"></a>Passos seguintes

Após a transformação alterar linha, talvez você queira [coletar seus dados em um armazenamento de dados de destino](data-flow-sink.md).
