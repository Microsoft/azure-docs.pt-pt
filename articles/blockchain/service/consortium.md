---
title: O serviço do Azure Blockchain Consortium
description: ''
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: e745a4ee4789ef46a61b5cb0bbf806c41ef631ec
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027919"
---
# <a name="azure-blockchain-service-consortium"></a>O serviço do Azure Blockchain Consortium

Utilizar o serviço do Azure Blockchain, pode criar consortium privada blockchain redes em que cada rede de blockchain pode ser limitado aos participantes específicos na rede. Somente os participantes na rede de blockchain consortium privada podem ver e interagir com o blockchain. Redes de Consortium no serviço do Azure Blockchain podem conter dois tipos de funções de participantes de membro:

* **Administrador** -privilegiado participantes que podem realizar ações de gestão de consortium e podem participar em transações de blockchain.

* **Utilizador** -participantes que não é possível efetuar qualquer ação de gestão de consortium, mas podem participar em transações de blockchain.

Redes de Consortium podem ser uma combinação de funções de participantes e podem ter um número arbitrário de cada tipo de função. Tem de existir pelo menos um administrador.

O diagrama seguinte mostra uma rede de consórcio com vários participantes:

![Diagrama de rede de consórcio privada](./media/consortium/network-diagram.png)

Com a gestão de consortium no serviço do Azure Blockchain, pode gerenciar os participantes na rede de consórcio. Gestão do consortium baseia-se no modelo de consenso da rede. Na versão de pré-visualização atual, o serviço do Azure Blockchain fornece um modelo de consenso centralizado para a gestão de consortium. Qualquer participante com privilégios com uma função de administer pode executar ações de gestão de consortium, como adicionar ou remover os participantes a partir de uma rede.

## <a name="roles"></a>Funções

Os participantes um consórcio podem ser indivíduos ou organizações e podem ser atribuídos uma função de utilizador ou uma função de administrador. A tabela seguinte lista das principais diferenças entre as duas funções:

| Ação | Função de utilizador | Função de administrador
|--------|:----:|:------------:|
| Criar novo membro | Sim | Sim |
| Convidar novos membros | Não | Sim |
| Definir ou alterar a função de membro participante | Não | Sim |
| Alterar nome de exibição de membro | Apenas para o próprio membro | Apenas para o próprio membro |
| Remover membros | Apenas para o próprio membro | Sim |
| Participar em transações de blockchain | Sim | Sim |

### <a name="user-role"></a>Função de utilizador

Os utilizadores são participantes de consórcio com nenhuma capacidades de administrador. Elas não poderão participar no gerenciamento de membros relacionado com o consortium. Os utilizadores podem alterar o nome a apresentar de membro e podem remover-se de um consórcio.

### <a name="administrator"></a>Administrador

Um administrador pode gerir membros do consortium. Um administrador pode convidar os membros, remover membros ou atualizar funções de membros no consortium.
Tem de existir sempre, pelo menos, um administrador dentro de um consórcio. O último administrador tem de especificar outro participante como uma função de administrador antes de deixarem um consórcio.

## <a name="managing-members"></a>Gerir membros

Apenas os administradores podem convidar outros participantes para o consortium. Os administradores convidar participantes utilizando o respetivo ID de subscrição do Azure.

Depois de convidado, os participantes poderão ingressar o consortium de blockchain ao implementar um novo membro no serviço do Azure Blockchain. Para ver e Junte-se a convidada consortium, tem de especificar o mesmo ID de subscrição do Azure utilizado no convite pelo administrador de rede.

Os administradores podem remover qualquer participante do consórcio, incluindo outros administradores. Os membros só podem remover-se de um consórcio.

## <a name="consortium-management-smart-contract"></a>Contrato de inteligente de gestão de consórcio

Gerenciamento de Consortium no serviço do Azure Blockchain é feito por meio de contratos inteligentes de gerenciamento de consortium. Os contratos inteligentes são implementados automaticamente para os nós ao implementar um novo membro de blockchain.

O endereço do contrato inteligente do consórcio gestão de raiz pode ser visualizado no portal do Azure. O **RootContract endereço** está na seção de visão geral do membro de blockchain.

![Endereço de RootContract](./media/consortium/rootcontract-address.png)

Pode interagir com o contrato inteligente de gestão de consortium usando o gerenciamento de consórcio [módulo do PowerShell](manage-consortium-powershell.md), Azure portal, ou diretamente através do contrato inteligente com o serviço do Azure Blockchain gerado Ethereum conta.

## <a name="ethereum-account"></a>Conta de Ethereum

Quando um membro é criado, é criada uma chave de conta Ethereum. O serviço do Azure Blockchain usa a chave a criar transações relacionadas com a gestão de consortium. A chave da conta Ethereum é gerida pelo serviço do Azure Blockchain automaticamente.

A conta de membro pode ser visualizada no portal do Azure. A conta de membro está na seção de visão geral do membro de blockchain.

![Conta do membro](./media/consortium/member-account.png)

Pode repor a sua conta de Ethereum ao clicar na sua conta de membro e introduzir uma nova palavra-passe. O endereço da conta Ethereum e a palavra-passe serão repostas.  

## <a name="next-steps"></a>Passos Seguintes

[Como gerir os membros no serviço do Azure Blockchain com o PowerShell](manage-consortium-powershell.md)
