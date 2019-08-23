---
title: incluir ficheiro
description: incluir ficheiro
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/21/2019
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 42e965b188db2b84579ab322fbe19781000dff7e
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69894159"
---
## <a name="storage"></a>Armazenamento

|  |  |
|---------|---------|
| [SKUs Permitidos para Contas de Armazenamento e Máquinas Virtuais](../articles/governance/policy/samples/allowed-skus-storage.md) | Requer que as contas de armazenamento e as máquinas virtuais utilizem SKUs aprovados. Utiliza políticas incorporadas para garantir SKUs aprovados. Especifique uma matriz de SKUs de máquinas virtuais aprovadas e uma matriz de SKUs de contas de armazenamento aprovadas. |
| [SKUs de contas de armazenamento permitidos](../articles/governance/policy/samples/allowed-storage-account-skus.md) | Requer que as contas de armazenamento utilizem um SKU aprovado. Especifica uma matriz de SKUs aprovados. |
| [Negar camadas de acesso esporádico para contas de armazenamento](../articles/governance/policy/samples/deny-cool-access-tiering.md) | Proíbe a utilização de camadas de acesso esporádico para contas de armazenamento de blobs.  |
| [Assegurar o tráfego https apenas para contas de armazenamento](../articles/governance/policy/samples/ensure-https-storage-account.md) | Requer que as contas de armazenamento utilizem o tráfego HTTPS.  |
| [Assegurar a encriptação de ficheiros de armazenamento](../articles/governance/policy/samples/ensure-storage-file-encryption.md) | Requer que a encriptação de ficheiros esteja ativada para contas de armazenamento.  |