---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
author: MarkusVi
ms.service: active-directory
ms.topic: include
ms.date: 01/14/2020
ms.author: markvi
ms.custom: include file
ms.openlocfilehash: 7100a28479e6c9b5e36a36e6400622793e4cd13b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006981"
---
Ativar uma identidade gerida atribuída pelo sistema é uma experiência de um clique. Pode ative-lo durante a criação de um VM ou nas propriedades de um VM existente.

![O screenshot mostra o separador do Sistema atribuído a uma máquina virtual onde pode ligar o estado atribuído ao Sistema.](./media/active-directory-msi-tut-enable/identity.png)


**Para permitir uma identidade gerida atribuída pelo sistema num novo VM:** 

1. [Inscreva-se no portal Azure](https://portal.azure.com)

2. [Criar uma máquina virtual com identidade atribuída ao sistema](../articles/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)