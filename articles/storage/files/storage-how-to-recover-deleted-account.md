---
title: Use an Azure file share with Azure Storage | Microsoft Docs
description: Saiba como utilizar uma partilha de ficheiros do Azure com o Windows e o Windows Server.
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: e1e581134b4e3821659c43bb7f55003239594bd9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233795"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>How to recover a deleted storage account

Azure Storage provides data resiliency through automated replicas, but doesn't prevent users or application code from corrupting data, whether accidentally or maliciously. Maintaining data fidelity during instances of application or user error requires more advanced techniques, such as copying the data to a secondary storage location with an audit log.

The following table provides overview of the scope of Storage Account Recovery depending on the replication strategy.

| |LRS|ZRS|GRS|RA - GRS|
|---|---|---|---|---|
|Storage Account Azure Resource Manager|Sim|Sim|Sim|Sim|
|Storage Account Classic|Sim|Sim|Sim|Sim|

Gather the following information and file a support request with Microsoft Support:

* Nome da conta de armazenamento
* Date of deletion
* Storage account region
* How was the storage account deleted?
* What method deleted the storage account? (Portal, PowerShell, etc.)

Important Points

* It can generally take up to 15 days from the time of deletion for the storage service to perform garbage collection, so storage accounts recovery may not be recovered with an SLA.
* Microsoft Support will try to recover the Container/Account on a best-effort basis and cannot guarantee the recovery.

> [!NOTE]
> The recovery may not be successful if the account has been re-created. If you have already re-created the account, you must delete it first before recovery can be attempted.
