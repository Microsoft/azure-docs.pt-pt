---
title: Azure Blockchain Tokens account management
description: Using Azure Blockchain Tokens account management, you can create groups and link blockchain accounts to control access to blockchain actions.
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
# <a name="azure-blockchain-tokens-account-management"></a>Azure Blockchain Tokens account management

[!INCLUDE [Preview note](./includes/preview.md)]

For a blockchain solution, users may require different levels of access to the tokens that are created with the Azure Blockchain Tokens service. In most blockchain scenarios, you need to plan and deploy different blockchain accounts that exist on the ledger. You also need to manage access across participants. Using Azure Blockchain Tokens account management, you can create groups and link blockchain accounts to control access to blockchain actions.

## <a name="blockchain-networks"></a>Blockchain networks

Azure Blockchain Tokens enables deployment and management of tokens across a set of blockchain networks. You can connect a single blockchain ledger or several blockchain ledgers to the service.

## <a name="accounts"></a>Contas

For blockchain networks connected to Azure Blockchain Tokens, the service creates and manages the account private-public key pairs and performs transaction signing and submission. Azure Blockchain Tokens also provides identity mapping to match accounts with the public key identity on the ledger.

## <a name="groups"></a>Grupos

Groups lets you manage a large number of blockchain accounts across connected networks. You can track and audit which applications and users in the directory have the ability to use accounts through Azure Blockchain Tokens APIs. For example, you could group a set of accounts that represent different lines of business or different roles and access to blockchain tokens.

You can also associate a group to an Azure Active Directory user or service principal and this principal has permissions to the group and its associated accounts.  

## <a name="next-steps"></a>Passos seguintes

Learn more about available [Azure Blockchain Tokens templates](templates.md).
