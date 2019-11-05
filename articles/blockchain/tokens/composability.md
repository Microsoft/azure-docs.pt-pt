---
title: Capacidade de composição dos tokens Blockchain do Azure
description: A capacidade de composição dos tokens do Azure Blockchain fornece flexibilidade para criar tokens para cenários avançados.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: fe932d3ae1874e7a35abb9729a0b80e4fa3512eb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512304"
---
# <a name="azure-blockchain-tokens-composability"></a>Capacidade de composição dos tokens Blockchain do Azure

[!INCLUDE [Preview note](./includes/preview.md)]

A capacidade de composição de token fornece flexibilidade para criar tokens para cenários avançados. Você pode ter um cenário complexo que não pode ser implementado usando os [quatro modelos de token pré-criados](templates.md#base-token-types). A composição de tokens permite que você projete seus próprios modelos de token adicionando ou removendo comportamentos definidos para criar seu próprio modelo de token. Ao criar um novo modelo de token, os tokens de Blockchain do Azure verificam todas as regras de gramática de token. Modelos compostos são salvos no serviço tokens Blockchain do Azure para emissão em redes Blockchain conectadas.

Você pode usar os [comportamentos de token](templates.md#token-behaviors) nas seções a seguir para criar seu modelo de token.

## <a name="burnable-b"></a>Gravável (b)

Capacidade de remover os tokens do fornecimento.

Por exemplo, quando você resgatar pontos de cartão de crédito online para um cartão de presente, os pontos de cartão de crédito são gravados.

## <a name="delegable-g"></a>Delegáveis (g)

Capacidade de delegar as ações executadas no token que você possui.

O delegado pode executar ações como o proprietário do token. Por exemplo, você pode usar um token delegáveis para implementar um voto. Um token delegáveis permite que o proprietário do token de votação faça com que outra pessoa vote em seu nome.

## <a name="logable-l"></a>Logable (l)

Capacidade de fazer logon.

Por exemplo, você pode emitir um token logable para uma distribuição de filme para cada teatro mostrando um filme específico. Para que o filme seja reproduzido, a exibição deve registrar uma transação para cada mostrada porque os pagamentos de royalties são exibidos durante a execução do filme. A compilação dos atores pode usar os tokens de filme para validar pagamentos por filme mostrados por teatro na distribuição.

## <a name="mint-able-m"></a>Com capacidade de menta (m)

Capacidade de mentar tokens adicionais para a classe de token. A função de mentais inclui o comportamento mentable.

Por exemplo, uma empresa de varejo, que deseja implementar um programa de fidelidade, pode usar tokens mentais para seu programa de fidelidade. Eles podem mentar pontos de fidelidade adicionais para seus clientes à medida que sua base de clientes cresce.  

## <a name="non-subdividable-or-whole-d"></a>Não subdividable ou total (~ d)

Restrição para impedir que um token seja dividido em partes menores.

Por exemplo, uma única pintura de arte não pode ser subdividida em várias partes menores. 

## <a name="non-transferable-t"></a>Não transferível (~ t)

Restrição para impedir uma alteração de Propriedade do proprietário do token inicial.

Por exemplo, um diploma de Universidade é um token não transferível. Depois que um diploma é dado a uma forma de graduação, ele não pode ser transferido do graduação para outra pessoa.

## <a name="roles-r"></a>Funções (r)

Capacidade de definir funções dentro da classe de modelo de token para comportamentos específicos.

Você pode fornecer uma lista de nomes de função que um token dá suporte no momento da criação do token. Quando as funções são especificadas, o usuário pode atribuir funções a esses comportamentos. Atualmente, há suporte apenas para a função de mentador.

## <a name="singleton-s"></a>Singleton (s)

Restrição para permitir um fornecimento de um token.

Por exemplo, um artefato de museu é um token singleton. Os artefatos do Museu são exclusivos. Um token que representa um artefato tem apenas um único item no fornecimento.

## <a name="subdividable-d"></a>Subdividable (d)

Capacidade de dividir um token em partes menores.

Por exemplo, um dólar pode ser subdividido em centavos.

## <a name="transferable-t"></a>Transferível (t)

Capacidade de transferir a propriedade do token.

Por exemplo, um título de propriedade é um token transferível, que pode ser transferido de uma pessoa para outra quando a propriedade é vendida.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [Gerenciamento de contas do Azure Blockchain tokens](account-management.md).
