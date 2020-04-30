---
title: O que é Azure Blockchain Tokens
description: Azure Blockchain Tokens é uma plataforma como serviço (PaaS) para emissão e gestão simbólicas.
ms.date: 11/04/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: e7c106ffee8ea06c7c0ce738aa8be0aba96a38ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79240283"
---
# <a name="what-is-azure-blockchain-tokens"></a>O que é o Azure Blockchain Tokens?

[!INCLUDE [Preview note](./includes/preview.md)]

Azure Blockchain Tokens é uma plataforma como serviço (PaaS) para emissão e gestão de tokens padronizados em livros blockchain em Azure.

Utilizando tokens Azure Blockchain, pode criar fichas padronizadas para a sua solução blockchain utilizando um modelo de token pré-construído. Também pode compor o seu próprio modelo de ficha utilizando o serviço. Uma vez criado, use Os Tokens Azure Blockchain para ligar e emitir os tokens numa blockchain. Uma vez emitido, pode então gerir os tokens através de várias redes blockchain.

## <a name="templates"></a>Modelos

Utilize tokens Azure Blockchain para selecionar um modelo de ficha pré-construído ou criar o seu próprio modelo de ficha. O Azure Blockchain Tokens suporta a composição do modelo de ficha que lhe permite criar o seu próprio modelo de token com base em comportamentos suportados. Os modelos de token podem ser usados para a maioria das soluções blockchain, uma vez que mapeiam para os tokens mais utilizados. Pode começar com um modelo, personalizá-lo e implementar os tokens para a sua solução.

Para obter mais informações sobre os modelos de Tokens Azure Blockchain, consulte [os modelos De Tokens Azure Blockchain](templates.md).

## <a name="management"></a>Gestão

A Azure Blockchain Tokens fornece a gestão de portais azure e APIs para se conectar a uma rede blockchain existente. Atualmente, pode ligar-se ao [Azure Blockchain Service](../service/overview.md) ou a outro blockchain familiar Ethereum.

Uma vez ligado a uma ou múltiplas redes blockchain, pode utilizar APIs De Tokens Azure Blockchain para emitir e gerir tokens para uso na sua solução blockchain. Utilizando APIs, pode integrar a gestão de fichas nas suas aplicações de negócio e lógica. Por exemplo, pode utilizar a API REST para gerir fichas em vez de gerir fichas diretamente na blockchain.

## <a name="blockchains-and-accounts"></a>Blockchains e contas

A Azure Blockchain Tokens fornece a gestão de portais azure e APIs para criar novos grupos e novas contas blockchain em redes blockchain conectadas. Pode criar novas contas diretamente nas suas redes conectadas, e o Azure Blockchain Tokens gere as chaves privadas da sua conta em seu nome. Utilizando grupos, pode agrupar diferentes contas blockchain de várias redes e gerir o controlo de acesso através dos grupos.

Para obter mais informações sobre a gestão da conta Azure Blockchain Tokens, consulte a gestão da [conta Azure Blockchain Tokens](account-management.md).

## <a name="token-taxonomy-framework"></a>Quadro de Taxonomia Simbólica

Azure Blockchain Tokens é construído sobre uma fundação baseada em padrões chamada Token Taxonomy Framework (TTF). TTF é um conjunto de entregas criadas pelo grupo de trabalho [Token Taxonomy Initiative](https://entethalliance.org/participate/token-taxonomy-initiative/) (TTI). O grupo de trabalho da TTI define uma taxonomia empresarial para tokens e seus comportamentos que podem ser aplicados em todos os principais livros, incluindo Ethereum, Quorum, Corda e Hyperledger Fabric. O objetivo do grupo de trabalho é criar um quadro que normalize a utilização de fichas numa perspetiva empresarial para impulsionar a simplificação e democratizar o desenvolvimento baseado em tokens. Ao deixar a indústria definir estes tokens e o seu comportamento a nível de negócio, a implementação detalhada dos tokens é abstrata da lógica do negócio que manipula os tokens.

## <a name="support-and-feedback"></a>Suporte e comentários

Para notícias azure Blockchain, visite o [blog Azure Blockchain](https://azure.microsoft.com/blog/topics/blockchain/) para se manter atualizado sobre as ofertas de serviços blockchain e informações da equipa de engenharia Azure Blockchain.

Para ter acesso à pré-visualização do Azure Blockchain Tokens, [contacte a equipa azure Blockchain Tokens](https://aka.ms/PreviewForm).

Para fornecer feedback do produto ou para solicitar novas funcionalidades, poste de voto para uma ideia através do fórum de [feedback Azure para blockchain](https://aka.ms/blockchainuservoice).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os modelos disponíveis de [Tokens Azure Blockchain.](templates.md)
