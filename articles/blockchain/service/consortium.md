---
title: Azure Blockchain Service consortium
description: Overview of how Azure Blockchain service implements consortium blockchain networks.
ms.date: 11/21/2019
ms.topic: conceptual
ms.reviewer: zeyadr
ms.openlocfilehash: 7b8885ba08d35db20d1eb7e75141cb173913b386
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455770"
---
# <a name="azure-blockchain-service-consortium"></a>Azure Blockchain Service consortium

Using Azure Blockchain Service, you can create private consortium blockchain networks where each blockchain network can be limited to specific participants in the network. Only participants in the private consortium blockchain network can view and interact with the blockchain. Consortium networks in Azure Blockchain Service can contain two types of member participant roles:

* **Administrator** - Privileged participants who can take consortium management actions and can participate in blockchain transactions.

* **User** -  Participants who cannot take any consortium management action but can participate in blockchain transactions.

Consortium networks can be a mix of participant roles and can have an arbitrary number of each role type. There must be at least one administrator.

The following diagram shows a consortium network with multiple participants:

![Private consortium network diagram](./media/consortium/network-diagram.png)

With consortium management in Azure Blockchain Service, you can manage participants in the consortium network. Management of the consortium is based on the consensus model of the network. In the current preview release, Azure Blockchain Service provides a centralized consensus model for consortium management. Any privileged participant with an administer role can take consortium management actions, such as adding or removing participants from a network.

## <a name="roles"></a>Funções

Participants in a consortium can be individuals or organizations and can be assigned a user role or an administrator role. The following table lists the high-level differences between the two roles:

| Ação | User role | Administrator role
|--------|:----:|:------------:|
| Create new member | Sim | Sim |
| Invite new members | Não | Sim |
| Set or change member participant role | Não | Sim |
| Change member display name | Only for own member | Only for own member |
| Remove members | Only for own member | Sim |
| Participate in blockchain transactions | Sim | Sim |

### <a name="user-role"></a>User role

Users are consortium participants with no administrator capabilities. They cannot participate in managing members related to the consortium. Users can change their member display name and can remove themselves from a consortium.

### <a name="administrator"></a>Administrador

An administrator can manage members within the consortium. An administrator can invite members, remove members, or update members roles within the consortium.
There must always be at least one administrator within a consortium. The last administrator must specify another participant as an administrator role before leaving a consortium.

## <a name="managing-members"></a>Managing members

Only administrators can invite other participants to the consortium. Administrators invite participants using their Azure subscription ID.

Once invited, participants can join the blockchain consortium by deploying a new member in Azure Blockchain Service. To view and join the invited consortium, you must specify the same Azure subscription ID used in the invite by the network administrator.

Administrators can remove any participant from the consortium, including other administrators. Members can only remove themselves from a consortium.

## <a name="consortium-management-smart-contract"></a>Consortium management smart contract

Consortium management in Azure Blockchain Service is done via consortium management smart contracts. The smart contracts are automatically deployed to your nodes when you deploy a new blockchain member.

The address of the root consortium management smart contract can be viewed in the Azure portal. The **RootContract address** is in blockchain member's overview section.

![RootContract address](./media/consortium/rootcontract-address.png)

You can interact with the consortium management smart contract using the consortium management [PowerShell module](manage-consortium-powershell.md), Azure portal, or directly through the smart contract using the Azure Blockchain Service generated Ethereum account.

## <a name="ethereum-account"></a>Ethereum account

When a member is created, an Ethereum account key is created. Azure Blockchain Service uses the key to create transactions related to consortium management. The Ethereum account key is managed by Azure Blockchain Service automatically.

The member account can be viewed in the Azure portal. The member account is in blockchain member's overview section.

![Member account](./media/consortium/member-account.png)

You can reset your Ethereum account by clicking on your member account and entering a new password. Both the Ethereum account address and the password will be reset.  

## <a name="next-steps"></a>Passos seguintes

Consortium management actions can be accessed through PowerShell. For more information, see [Manage consortium members in Azure Blockchain Service using PowerShell](manage-consortium-powershell.md).
