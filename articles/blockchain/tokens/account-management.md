---
title: Gerenciamento de conta dos tokens Blockchain do Azure
description: Usando o gerenciamento de contas do Azure Blockchain tokens, você pode criar grupos e vincular contas do Blockchain para controlar o acesso às ações do Blockchain.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326106"
---
# <a name="azure-blockchain-tokens-account-management"></a>Gerenciamento de conta dos tokens Blockchain do Azure

[!INCLUDE [Preview note](./includes/preview.md)]

Para uma solução blockchain, os usuários podem exigir diferentes níveis de acesso aos tokens que são criados com o serviço de tokens Blockchain do Azure. Na maioria dos cenários de blockchain, você precisa planejar e implantar diferentes contas de blockchain que existem no razão. Você também precisa gerenciar o acesso entre os participantes. Usando o gerenciamento de contas do Azure Blockchain tokens, você pode criar grupos e vincular contas do Blockchain para controlar o acesso às ações do Blockchain.

## <a name="blockchain-networks"></a>Redes Blockchain

Os tokens Blockchain do Azure permitem a implantação e o gerenciamento de tokens em um conjunto de redes Blockchain. Você pode conectar uma única razão blockchain ou vários razões blockchain para o serviço.

## <a name="accounts"></a>Contas

Para redes blockchain conectadas aos tokens Blockchain do Azure, o serviço cria e gerencia os pares de chave privada-pública da conta e executa a assinatura e o envio da transação. Os tokens Blockchain do Azure também fornecem mapeamento de identidade para corresponder contas com a identidade de chave pública no razão.

## <a name="groups"></a>Grupos

Os grupos permitem que você gerencie um grande número de contas de blockchain em redes conectadas. Você pode acompanhar e auditar quais aplicativos e usuários no diretório têm a capacidade de usar contas por meio de APIs de tokens Blockchain do Azure. Por exemplo, você pode agrupar um conjunto de contas que representam diferentes linhas de negócios ou funções diferentes e acesso a tokens blockchain.

Você também pode associar um grupo a um usuário Azure Active Directory ou a uma entidade de serviço e essa entidade de segurança tem permissões para o grupo e suas contas associadas.  

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre os [modelos de tokens do Azure Blockchain](templates.md)disponíveis.
