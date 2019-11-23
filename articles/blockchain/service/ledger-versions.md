---
title: Azure Blockchain Service ledger versions, patching, & upgrade
description: Overview of the supported ledgers versions in Azure Blockchain Service, including policies regarding systems patching and system-managed and user-managed upgrades.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 8d4a77699dd80743acfadd4d72d6d75bc1939b3b
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325177"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Supported Azure Blockchain Service ledger versions

Azure Blockchain Service uses the Ethereum-based [Quorum](https://www.goquorum.com/developers) ledger designed for the processing of private transactions within a group of known participants, identified as a consortium in Azure Blockchain Service.

Currently, Azure Blockchain Service supports [Quorum version 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) and [Tessera transaction manager](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Managing updates and upgrades

Versioning in Quorum is done through a major, minor, and patch releases. For example, if the Quorum version is 2.0.1, release type would be categorized as follows:

|Principal | Minor  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure Blockchain Service automatically updates patch releases of Quorum to existing running members within 30 days of being made available from Quorum.

## <a name="availability-of-new-ledger-versions"></a>Availability of new ledger versions

Azure Blockchain Service provides the latest major and minor versions of the Quorum ledger within 60 days of being available from the Quorum manufacturer. A maximum of four minor releases are provided for consortia to choose from when provisioning a new member and consortium. Upgrading from to a major or minor release is currently not supported. For example, if you are running version 2.x, an upgrade to version 3.x is currently not supported. Similarly, if you are running version 2.2, an upgrade to version 2.3 is currently not supported.

## <a name="next-steps"></a>Passos seguintes

[Limits in Azure Blockchain Service](limits.md)
