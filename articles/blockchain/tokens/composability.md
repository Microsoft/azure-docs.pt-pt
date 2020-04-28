---
title: Composição azure Blockchain Tokens
description: A compability Azure Blockchain Tokens proporciona flexibilidade para criar tokens para cenários avançados.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: a3fe1b290917de20b7c3af31fe386ed93580d850
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74325124"
---
# <a name="azure-blockchain-tokens-composability"></a>Composição azure Blockchain Tokens

[!INCLUDE [Preview note](./includes/preview.md)]

A composição simbólica proporciona flexibilidade para criar fichas para cenários avançados. Pode ter um cenário complexo que não pode ser implementado usando os quatro modelos de [ficha pré-construídos](templates.md#base-token-types). A composição token permite-lhe desenhar os seus próprios modelos de token adicionando ou removendo comportamentos definidos para construir o seu próprio modelo de token. Ao criar um novo modelo simbólico, o Azure Blockchain Tokens verifica todas as regras da gramática simbólica. Os modelos compostos são guardados no serviço Azure Blockchain Tokens para emissão em redes blockchain conectadas.

Pode utilizar os [comportamentos simbólicos](templates.md#token-behaviors) nas seguintes secções para desenhar o seu modelo de ficha.

## <a name="burnable-b"></a>Queime (b)

Capacidade de remover as fichas da alimentação.

Por exemplo, quando resgata pontos de cartão de crédito on-line para um cartão presente, os pontos do cartão de crédito são queimados.

## <a name="delegable-g"></a>Delegável (g)

Capacidade de delegar as ações tomadas no símbolo que possui.

O delegado pode realizar ações como proprietário do símbolo. Por exemplo, poderia usar um símbolo deslegível para implementar uma votação. Um símbolo delegável permite que o dono do símbolo do voto tenha outra pessoa a votar em seu nome.

## <a name="logable-l"></a>Logable (l)

Capacidade de log.

Por exemplo, você pode emitir um sinal de logable para uma distribuição de filme para cada teatro mostrando um filme específico. Para que o filme seja reproduzido, a exibição deve registar uma transação para cada exibição, porque os pagamentos de royalties são por exibição durante a execução do filme. Os atores constroem podem usar as fichas do filme para validar os pagamentos por filme exibido por cinema na distribuição.

## <a name="mint-able-m"></a>Menta (m)

Capacidade de cunhar fichas adicionais para a classe simbólica. O papel minter inclui o comportamento de menta.

Por exemplo, uma empresa de retalho, que quer implementar um programa de fidelização, pode usar fichas de menta para o seu programa de fidelização. Podem cunhar pontos de fidelização adicionais para os seus clientes à medida que a sua base de clientes cresce.  

## <a name="non-subdividable-or-whole-d"></a>Não subdividável ou inteiro (~d)

Restrição para evitar que um símbolo seja dividido em partes menores.

Por exemplo, uma única pintura de arte não pode ser subdividida em várias partes menores. 

## <a name="non-transferable-t"></a>Não transferível (~t)

Restrição para evitar uma mudança de propriedade do proprietário do símbolo inicial.

Por exemplo, um diploma universitário é um símbolo não transferível. Uma vez que um diploma é dado a um graduado, não pode ser transferido do graduado para outra pessoa.

## <a name="roles-r"></a>Funções (r)

Capacidade de definir papéis dentro da classe de modelo de token para comportamentos específicos.

Você pode fornecer uma lista de nomes de papéis que um símbolo suporta no tempo de criação simbólica. Quando as funções são especificadas, o utilizador pode atribuir funções a estes comportamentos. Atualmente, apenas o papel do MINTER é apoiado.

## <a name="singleton-s"></a>Singleton (s)

Restrição para permitir o fornecimento de um símbolo.

Por exemplo, um artefacto de museu é um símbolo singleton. Os artefactos do museu são únicos. Um símbolo que representa um artefacto só tem um único item no fornecimento.

## <a name="subdividable-d"></a>Subdividável d

Capacidade de dividir um símbolo em partes menores.

Por exemplo, um dólar pode ser subdividido em cêntimos.

## <a name="transferable-t"></a>Transferível (t)

Capacidade de transferir a propriedade do símbolo.

Por exemplo, um título de propriedade é um símbolo transferível, que pode ser transferido de uma pessoa para outra quando a propriedade é vendida.

## <a name="next-steps"></a>Passos seguintes

Conheça a [gestão da conta Azure Blockchain Tokens.](account-management.md)
