---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 327e86642041a607ada7c1173bb053b12a41832c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011726"
---
Por padrão, as ações de ficheiros padrão podem abranger apenas até 5 TiB, embora o limite de ações possa ser aumentado para 100 TiB. Para isso, a grande funcionalidade *de partilha de ficheiros* deve ser ativada ao nível da conta de armazenamento. As contas de armazenamento premium (Contas de armazenamento *FileStorage)* não têm o grande pavilhão de funcionalidades de partilha de ficheiros, uma vez que todas as ações de ficheiros premium já estão habilitados para o provisionamento até à capacidade total de 100 TiB.

Só é possível permitir grandes ações de ficheiros em contas de armazenamento padrão redundantes ou redundantes locais ou na zona. Uma vez ativado a bandeira de recurso de grande porção, não pode alterar o nível de redundância para armazenamento geo-redundante ou de zona geo-redundante.

Para ativar grandes ações de ficheiros numa conta de armazenamento existente, navegue para a vista **de Configuração** na tabela de conteúdos da conta de armazenamento e altere o grande interruptor de partilha de ficheiros para ativar:

![Uma imagem do interruptor de partilha de ficheiros grande ativado no portal Azure](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Também pode ativar 100 partilhas de ficheiros TiB através do [`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) cmdlet PowerShell e do [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) comando Azure CLI. Para obter instruções detalhadas sobre a ativação de partilhas de ficheiros grandes, consulte [ativar e criar grandes partilhas de ficheiros](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Para saber mais sobre como criar ações de ficheiros em novas contas de armazenamento, consulte [a criação de uma partilha de ficheiros Azure](../articles/storage/files/storage-how-to-create-file-share.md).