---
title: O que são tokens Blockchain do Azure
description: Os tokens Blockchain do Azure são uma plataforma como um serviço (PaaS) para emissão e gerenciamento de tokens.
ms.date: 11/04/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: c1c18298ba82775c9b10bb79c420704bd462e2b4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326091"
---
# <a name="what-is-azure-blockchain-tokens"></a>O que é o Azure Blockchain Tokens?

[!INCLUDE [Preview note](./includes/preview.md)]

Os tokens do Blockchain do Azure são uma plataforma como um serviço (PaaS) para emissão e gerenciamento de token padronizados em razões de Blockchain no Azure.

Usando tokens do Blockchain do Azure, você pode criar tokens padronizados para sua solução do Blockchain usando um modelo de token predefinido. Você também pode compor seu próprio modelo de token usando o serviço. Depois de criado, use os tokens Blockchain do Azure para se conectar e emitir os tokens em um Blockchain. Depois de emitido, você pode gerenciar os tokens em várias redes blockchain.

## <a name="templates"></a>Modelos

Use tokens Blockchain do Azure para selecionar um modelo de token pré-criado ou criar seu próprio modelo de token. Os tokens Blockchain do Azure dão suporte à capacidade de composição do modelo de token que permite que você crie seu próprio modelo de token com base em comportamentos com suporte. Os modelos de token podem ser usados para a maioria das soluções de blockchain, já que elas são mapeadas para os tokens utilizados com mais frequência. Você pode começar com um modelo, personalizá-lo e implantar os tokens para sua solução.

Para obter mais informações sobre os modelos de tokens do Azure Blockchain, consulte [modelos de tokens do Blockchain do Azure](templates.md).

## <a name="management"></a>Gestão

Os tokens Blockchain do Azure fornecem gerenciamento de portal do Azure e APIs para se conectar a uma rede Blockchain existente. No momento, você pode se conectar ao [serviço Blockchain do Azure](../service/overview.md) ou a outro Blockchain da família Ethereum.

Uma vez conectado a uma ou várias redes blockchain, você pode usar as APIs de tokens Blockchain do Azure para emitir e gerenciar tokens para uso em sua solução blockchain. Usando APIs, você pode integrar o gerenciamento de tokens em seus aplicativos de negócios e lógica. Por exemplo, você pode usar a API REST para gerenciar tokens em vez de gerenciar tokens diretamente no blockchain.

## <a name="blockchains-and-accounts"></a>Blockchains e contas

Os tokens Blockchain do Azure fornecem gerenciamento de portal do Azure e APIs para criar novos grupos e contas de Blockchain novas em redes Blockchain conectadas. Você pode criar novas contas diretamente em suas redes conectadas e os tokens Blockchain do Azure gerenciam suas chaves privadas de conta em seu nome. Usando grupos, você pode agrupar contas diferentes do blockchain de várias redes e gerenciar o controle de acesso por meio dos grupos.

Para obter mais informações sobre o gerenciamento de conta de tokens Blockchain do Azure, consulte [Gerenciamento de conta de tokens Blockchain do Azure](account-management.md).

## <a name="token-taxonomy-framework"></a>Estrutura de taxonomia do token

Os tokens Blockchain do Azure baseiam-se em uma base baseada em padrões chamada de TTF (estrutura de taxonomia de token). TTF é um conjunto de resultados finais criados no grupo de trabalho do token TTI ( [iniciativa de taxonomia de token](https://entethalliance.org/participate/token-taxonomy-initiative/) ). O grupo de trabalho TTI define uma taxonomia de negócios para tokens e seus comportamentos que podem ser aplicados em todos os principais razões, incluindo Ethereum, quorum, corda e a malha de hiperrazãor. O objetivo do grupo de trabalho é criar uma estrutura que padronize o uso de tokens de uma perspectiva de negócios para orientar a simplificação e o desenvolvimento baseado em token democratize. Ao permitir que o setor defina esses tokens e seu comportamento no nível de negócios, a implementação detalhada dos tokens é dissociada da lógica de negócios que manipula os tokens.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre os [modelos de tokens do Azure Blockchain](templates.md)disponíveis.
