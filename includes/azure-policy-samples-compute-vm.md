---
title: incluir ficheiro
description: incluir ficheiro
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: b6587a3928c2ddf0d00c90e07643525314690e42
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155536"
---
### <a name="virtual-machines"></a>Virtual Machines

|  |  |
|---------|---------|
| [Permitir que a imagem de VM personalizada seja de um Grupo de Recursos](../articles/governance/policy/samples/allow-custom-vm-image.md) |  Requer que as imagens personalizadas sejam provenientes de um grupo de recursos aprovado. Especifica o nome do grupo de recursos aprovado. |
| [SKUs Permitidos para Contas de Armazenamento e Máquinas Virtuais](../articles/governance/policy/samples/allowed-skus-storage.md) | Requer que as contas de armazenamento e as máquinas virtuais utilizem SKUs aprovados. Utiliza políticas incorporadas para garantir SKUs aprovados. Especifique uma matriz de SKUs de máquinas virtuais aprovadas e uma matriz de SKUs de contas de armazenamento aprovadas. |
| [Imagens de VM aprovadas](../articles/governance/policy/samples/allowed-custom-images.md) | Requer que sejam implementadas apenas imagens personalizadas aprovadas no seu ambiente. Especifica uma matriz de IDs de imagens aprovadas. |
| [Auditar se a extensão não existir](../articles/governance/policy/samples/audit-extension-not-exist.md) | Faz uma auditoria se uma extensão não estiver implementada numa máquina virtual. Especifica o publicador da extensão e o tipo para verificar se foi implementada. |
| [Extensões de VM não permitidas](../articles/governance/policy/samples/not-allowed-vm-extension.md) | Proíbe a utilização de extensões especificadas. Especifica uma matriz que contém os tipos de extensão proibidos. |