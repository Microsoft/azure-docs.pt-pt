---
title: Gestão de conta Azure Blockchain Tokens
description: Utilizando a gestão de conta Azure Blockchain Tokens, pode criar grupos e ligar contas blockchain para controlar o acesso a ações blockchain.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74326106"
---
# <a name="azure-blockchain-tokens-account-management"></a>Gestão de conta Azure Blockchain Tokens

[!INCLUDE [Preview note](./includes/preview.md)]

Para uma solução blockchain, os utilizadores podem exigir diferentes níveis de acesso aos tokens que são criados com o serviço Azure Blockchain Tokens. Na maioria dos cenários blockchain, você precisa planear e implementar diferentes contas blockchain que existem no livro de contabilidade. Também precisa de gerir o acesso entre os participantes.Utilizando a gestão de conta Azure Blockchain Tokens, pode criar grupos e ligar contas blockchain para controlar o acesso a ações blockchain.

## <a name="blockchain-networks"></a>Redes blockchain

O Azure Blockchain Tokens permite a implementação e gestão de fichas através de um conjunto de redes blockchain. Pode ligar um único livro de livros blockchain ou vários livros blockchain ao serviço.

## <a name="accounts"></a>Contas

Para redes blockchain ligadas a Tokens Azure Blockchain, o serviço cria e gere os pares de chaves público-públicos e executa a assinatura e submissão de transações. Azure Blockchain Tokens também fornece mapeamento de identidade para combinar contas com a identidade chave pública no livro de contabilidade.

## <a name="groups"></a>Grupos

Os grupos permitem-lhe gerir um grande número de contas blockchain através de redes conectadas. Pode rastrear e auditar quais as aplicações e utilizadores do diretório que têm a capacidade de utilizar contas através de APIs Tokens Azure Blockchain. Por exemplo, você poderia agrupar um conjunto de contas que representam diferentes linhas de negócio ou diferentes papéis e acesso a tokens blockchain.

Também pode associar um grupo a um utilizador ou diretor de serviço do Azure Ative Diretório e este principal tem permissões ao grupo e às suas contas associadas.  

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os modelos disponíveis de [Tokens Azure Blockchain.](templates.md)
